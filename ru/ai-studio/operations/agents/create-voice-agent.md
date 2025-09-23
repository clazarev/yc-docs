---
title: Создать голосового агента в {{ foundation-models-full-name }}
description: Следуя этой инструкции, вы сможете создать голосового агента с помощью {{ realtime-api }} в {{ foundation-models-full-name }}.
---

# Создать голосового агента

В {{ foundation-models-full-name }} вы можете создать [голосового агента](../../concepts/agents/realtime.md), способного взаимодействовать с пользователем в голосовом и текстовом форматах и поддерживать диалог, близкий к естественному человеческому общению.

## Перед началом работы {#before-begin}

Чтобы воспользоваться примером:

1. Получите [API-ключ](../../../iam/concepts/authorization/api-key.md) для [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md), от имени которого будет выполняться взаимодействие с [{{ realtime-api }}](../../concepts/agents/realtime.md#realtime-api). Подробнее см. в разделе [{#T}](../get-api-key.md).
1. {% include [sdk-before-begin-step3](../../../_includes/ai-studio/sdk-before-begin-step3.md) %}
1. {% include [sdk-before-begin-step4](../../../_includes/ai-studio/sdk-before-begin-step4.md) %}
1. {% include [sdk-before-begin-step5](../../../_includes/ai-studio/sdk-before-begin-step5.md) %}

## Создайте голосового агента {#create-agent}

Приведенный пример иллюстрирует процесс взаимодействия клиента с сервером через {{ realtime-api }}. Чтобы создать голосового агента:

1. Создайте файл `voice-agent.py` и добавьте в него следующий код:

    ```python
    import asyncio
    import json
    import base64
    import websockets
    import wave
    import subprocess

    API_KEY = "<API-ключ>"
    FOLDER = "<идентификатор_каталога>"
    INPUT_FILE = "<путь_к_входному_аудиофайлу>"
    CONVERTED_FILE = "./converted.wav"
    OUTPUT_FILE = "./output.wav"
    CHUNK_SIZE = 15000  # размер фрагмента в байтах (~100мс при 24kHz mono PCM16)

    REALTIME_URL = f"wss://rest-assistant.api.cloud.yandex.net/v1/realtime/openai?model=gpt://{FOLDER}/speech-realtime-250923"


    def make_silence_chunk(samples: int):
        """Создаёт фрагмент тишины заданной продолжительности (samples) в формате PCM16."""
        return (b"\x00\x00" * samples)


    async def main():
        # 1️⃣ Конвертация входного файла в формат, который понимает {{ realtime-api }}:
        # mono, 24kHz, 16-bit PCM. Без этого модель не примет аудио.
        print("🎧 Конвертация входного файла...")
        subprocess.run([
            "ffmpeg", "-y", "-i", INPUT_FILE,
            "-ac", "1", "-ar", "24000", "-sample_fmt", "s16",
            CONVERTED_FILE
        ], check=True)
        print(f"✅ Converted {INPUT_FILE} -> {CONVERTED_FILE}")

        # 2️⃣ Устанавливаем WebSocket-соединение с {{ realtime-api }}.
        async with websockets.connect(
                REALTIME_URL,
                additional_headers=[("Authorization", f"api-key {API_KEY}")]
        ) as ws:
            print("✅ Connected to {{ realtime-api }}")

            # 3️⃣ Ждём первое событие "session.created" с настройками сессии.
            created = json.loads(await ws.recv())
            print("✅ Session created:", created)

            # 4️⃣ Обновляем сессию: включаем server_vad, выбираем голос и формат вывода.
            await ws.send(json.dumps({
                "type": "session.update",
                "session": {
                    "type": "realtime",
                    "output_modalities": ["text", "audio"],  # 👈 теперь хотим и текст, и звук
                    "audio": {
                        "input": {
                            # Формат входного аудио
                            "format": {"type": "audio/pcm", "rate": 24000},
                            # Конфигурация server-side VAD
                            "turn_detection": {
                                "type": "server_vad",  # включаем серверный VAD
                                "threshold": 0.5,  # чувствительность
                                "silence_duration_ms": 200,  # тишина, считающаяся концом речи
                                "create_response": False,  # ответ создаём вручную
                                "interrupt_response": False
                            }
                        },
                        "output": {
                            "format": {"type": "audio/pcm", "rate": 44100},
                            "voice": "marina"
                        }
                    },
                    "instructions": """
                Если будут спрашивать про самолет и бронирование, делай вид, что все понял и сделал.  
    В ответе строго следуй правилам:
    Ты помогаешь пользователю в голосовом канале. 
    Если пользователь называл номер заказа, места, адрес или другую важную информацию в ответе обязательно проговори это - чтобы пользователь понял, что его вводные были правильно услышаны.
    Отвечай коротко, В целом, формируй ответы так, чтобы их было удобно слушать
                """
                }
            }))

            # 5️⃣ Читаем подготовленный WAV и отправляем по кусочкам.
            with wave.open(CONVERTED_FILE, "rb") as wf:
                assert wf.getframerate() == 24000, "Ожидался 24kHz"
                audio_bytes = wf.readframes(wf.getnframes())
                samples_per_chunk = CHUNK_SIZE // 2  # т.к. 2 байта = 1 sample
                print(f"📊 Отправляем {len(audio_bytes)} байт ({wf.getnframes()} фреймов)")

            # Отправляем аудиоданные фрагментами
            for i in range(0, len(audio_bytes), CHUNK_SIZE):
                await ws.send(json.dumps({
                    "type": "input_audio_buffer.append",
                    "audio": base64.b64encode(audio_bytes[i:i + CHUNK_SIZE]).decode("ascii")
                }))

            # Отправляем ~500мс тишины в конце, чтобы VAD гарантированно обнаружил конец речи
            silence_chunk = make_silence_chunk(samples_per_chunk)
            for _ in range(5):  # 5 * 100мс = 500мс
                await ws.send(json.dumps({
                    "type": "input_audio_buffer.append",
                    "audio": base64.b64encode(silence_chunk).decode("ascii")
                }))

            # Сообщаем серверу, что аудиопоток закончен.
            await ws.send(json.dumps({"type": "input_audio_buffer.commit"}))
            print("🎙 Audio fully sent — ждём VAD завершения...")

            committed = False  # будем отслеживать, что выполнен коммит буфера
            full_text, audio_chunks = [], []

            try:
                while True:
                    msg = await ws.recv()
                    data = json.loads(msg)
                    print("📥 Event:", data)

                    if data.get("type") == "input_audio_buffer.committed" and not committed:
                        committed = True
                        print("✅ Audio buffer committed — создаём response...")
                        await ws.send(json.dumps({
                            "type": "response.create",
                            "response": {
                                "instructions": "Кратко ответь на вопрос из аудио."
                            }
                        }))

                    # Собираем аудио дельты, если модель присылает текст
                    if data.get("type") == "response.output_audio.delta":
                        audio_chunks.append(base64.b64decode(data["delta"]))

                    # Собираем текстовые дельты, если модель присылает текст
                    if data.get("type") == "response.output_text.delta":
                        full_text.append(data["delta"])

                    # Когда ответ полностью готов → выводим текст и сохраняем аудио
                    if data.get("type") == "response.done":
                        print("✅ Response completed")
                        if full_text:
                            print("📝 Итоговый текст:", "".join(full_text))
                        if audio_chunks:
                            with wave.open(OUTPUT_FILE, "wb") as wf:
                                wf.setnchannels(1)
                                wf.setsampwidth(2)
                                wf.setframerate(44100)
                                wf.writeframes(b"".join(audio_chunks))
                            print(f"🔊 Сохранено аудио в {OUTPUT_FILE}")
                        break

            except websockets.ConnectionClosed:
                print("🔌 Connection closed by server")


    if __name__ == "__main__":
        asyncio.run(main())
    ```

    Где:

    * `API_KEY` — [API-ключ](../../../iam/concepts/authorization/api-key.md) [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md), от имени которого вызываются методы {{ realtime-api }}. 
    * `FOLDER` — идентификатор [каталога](../../../resource-manager/concepts/resources-hierarchy.md#folder), в котором вызывается {{ realtime-api }}.
    * `INPUT_FILE` — путь к файлу, содержащему входное аудиосообщение. В этом примере в качестве входного аудиосообщения вы можете использовать любой аудиофайл, в том числе [сгенерированный](../../../speechkit/operations/tts-playground.md) с помощью {{ speechkit-name }} Playground.
    * `CONVERTED_FILE` — путь к временному файлу, в который будет сохранено входное аудиосообщение при конвертации его в нужный формат (mono, 24 kHz, 16-bit PCM).
    * `OUTPUT_FILE` — путь к сгенерированному аудиофайлу с ответом модели.
1. Выполните созданный файл:

    ```bash
    python3 voice-agent.py
    ```

    В результате выполнения кода ответ модели в текстовом формате будет выведен на экран, а в аудиоформате — сохранен в файл, путь к которому задан в переменной `OUTPUT_FILE`.

#### См. также {#see-also}

* [{#T}](../../concepts/agents/realtime.md)