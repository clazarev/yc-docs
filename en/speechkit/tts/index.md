# Speech synthesis

_Speech synthesis_ in {{ speechkit-full-name }} allows you to convert any text to speech in multiple languages.

{{ speechkit-name }} voice models use deep neural network technology. When synthesizing speech, the model pays attention to many details in the original voice. The model evaluates the entire text, not individual sentences, before starting the synthesis. This enables the synthesized voice to sound clear and natural, without electronic distortion, and reproduce appropriate intonations of a real person's speech.

The service is available at `{{ api-host-sk-tts }}:443`.

{% include [try-speechkit](../../_includes/speechkit/try-speechkit.md) %}


## Synthesis options {#features}


{% include [api-concepts](../../_includes/speechkit/api-concepts.md) %}

{{ speechkit-name }} synthesis has two APIs: [API v1](request.md) (REST) and [API v3](../tts-v3/api-ref/grpc/) (gRPC). The [{{ speechkit-name }} Python SDK](../sdk/python/index.md) is also implemented based on the API v3.

|                                                    | API v1 | API v3                                      |
|----------------------------------------------------|---|---------------------------------------------|
| Specification                                       | REST | gRPC, REST                                       |
| Selecting voice                                       | `voice` parameter | `hints: voice` parameter                      |
| Selecting language                                        | Depends on the voice </br>`lang` parameter | Depends on the voice, not specified explicitly in the request |
| Specifying role                                     | Depends on the voice </br>`emotion` parameter | Depends on the voice </br>`hints: role` parameter |
| [Voice tone management](#pitch)               | No | `hints: pitch_shift` parameter |
| [Controlling pronunciation](#markup)                | SSML </br> TTS | TTS                                         |
| [Pronunciation speed](#speed)                    | `speed` parameter | `hints: speed` parameter                      |
| [Adjusting volume](#volume)                     | No | `loudness_normalization_type` parameter      |
| [Output audio format](#format)            | `format` parameter | `output_audio_spec` parameter                |
| Specifying [LPCM](../formats.md#lpcm) parameters      | `sampleRateHertz` parameter | `output_audio_spec: raw_audio` parameter|
| [Audio pattern-based synthesis](templates.md)            | No | `text_template` parameter                   |
| [Pricing method](../pricing.md#rules-tts)      | Total number of characters in the requests | By request   |
| Automatic splitting of long phrases              | Not required | `unsafe_mode` parameter  |

{% note info %}

Multiple responses with audio fragments can be returned to a single request to the {{ speechkit-name }} API v3. A complete response is a result of merging all the fragments received.

{% endnote %}

## Streaming synthesis {#streaming}

{{ speechkit-name }} allows you to [send texts](api/tts-streaming.md) for synthesis one by one within a single session using the API v3. This is convenient if you do not have access to the text in advance but need to generate the speech fast, e.g., when synthesizing real-time LLM responses.

Streaming synthesis does not suport [{{ brand-voice-lite-name }}](./brand-voice/index.md#lite) voices yet.

## Languages and voices {#langs}

You can select a voice to convert your text to speech. Each voice corresponds to a model trained on the speaker's speech pattern. Voices differ by tone, gender, and language. For a list of voices and their specifications, see [{#T}](voices.md).

If no voice suits your business, {{ speechkit-name }} can create a unique one specifically for you. For more information, see [{#T}](brand-voice/index.md).

{{ speechkit-name }} can synthesize speech in different languages. Each voice is designed to synthesize speech in a specific language. The voices can also read text in <q>another</q> language, but the quality of the synthesized speech will be worse in this case, as the <q>speaker</q> will pronounce the text with an accent, and there might be errors in word synthesis.

### Role {#role}

The synthesized speech will sound differently depending on the selected role. Role is a manner of pronunciation for the same speaker. Different sets of roles are available for different voices. Attempting to use a role the selected voice does not have will cause a service error.

### Voice tone {#pitch}

Each {{ speechkit-name }} voice has a certain pitch. In API v3, you can change the voice by specifying a shift from its base tone. The shift is specified in the `hints: pitch_shift` parameter in the range `[-1000;1000]` (in Hz). The default value is `0`. Positive `hints: pitch_shift` values make the voice higher, negative ones make it lower.

## Controlling pronunciation {#markup}

To have control over pronunciation in synthesized speech, mark up the source text explicitly. {{ speechkit-name }} can synthesize speech from text marked up using [Speech Synthesis Markup Language](https://en.wikipedia.org/wiki/Speech_Synthesis_Markup_Language) (SSML) or TTS markup. These markup methods enable you to set the length of pauses, the pronunciation of individual sounds, and more. SSML and TTS markup have different data transmission parameters:

* SSML is only supported in API v1 requests. To transmit text in SSML format, include the `ssml` parameter in the request body and use the `<speak>` tag as a wrapper for the text. For more information about SSML tags, see [{#T}](markup/ssml.md).
* TTS markup is supported in the API v1 and API v3. In API v1 requests, transmit the text marked up according to TTS rules in the `text` parameter in the request body. API v3 and the Python SDK require no special parameters and consider any transmitted text as marked up according to TTS rules. For more information about using TTS markup, see [{#T}](markup/tts-markup.md).

{% include [note-templates-markup](../../_includes/speechkit/note-templates-markup.md) %}

## Synthesis settings {#settings}

You can configure both pronunciation and technical characteristics of the synthesized speech.

### Synthesized speech speed {#speed}

The speed of synthesized speech affects perception of information. If the speech is too fast or too slow, it sounds unnatural. However, this can be useful in commercials where every second of air time counts.

By default, the speed of generated speech corresponds to the average speed of human speech.

### Volume normalization {#volume}

In [API v3](../tts-v3/api-ref/grpc/index.md) and Python SDK requests, you can set the type and level of volume normalization. This can be useful if you are using {{ speechkit-name }} synthesis along with other sound sources. For example, so that the volume of the voice assistant does not differ from the phone notifications.

{{ speechkit-name }} supports two normalization types:
* `MAX_PEAK` [peak normalization](https://en.wikipedia.org/wiki/Audio_normalization#Peak_normalization), where the audio level rises to the maximum possible distortion-free value attainable for digital audio.
* `LUFS` weighted normalization based on the [EBU R 128](https://en.wikipedia.org/wiki/EBU_R_128) standard, where volume is normalized relative to the full digital scale.

The normalization type is set in the `loudness_normalization_type` parameter. By default, {{ speechkit-name }} uses LUFS.

The normalization level is set in the `hints: volume` parameter. Possible values depend on the normalization type:
* For `MAX_PEAK`, the parameter can be set in the `(0;1]` range, the default value is `0.7`.
* For `LUFS`, the parameter varies in the `[-149;0)` range, the default value is `-19`.

If the normalization level falls outside the supported range, the {{ speechkit-name }} server will return the `InvalidArgument` error.

### Synthesized audio file format {#format}

You can select the audio file format that will be used by {{ speechkit-name }} to synthesize speech.

For a full list of available formats and their specifications, see [{#T}](../formats.md).

## Use cases {#examples}

* [{#T}](../tutorials/recognizer-bot.md)
* [{#T}](../tutorials/speechkit-integrarion-via-agi-gw.md)
* [{#T}](api/tts-examples-v3.md)
* [{#T}](api/tts-v3-rest.md)
* [{#T}](api/tts-ogg.md)
* [{#T}](api/tts-wav.md)
* [{#T}](api/tts-ssml.md)
* [{#T}](api/tts-streaming.md)

#### See also {#see-also}

* Try speech synthesis using our demo on the [service page](/services/speechkit#demo).
* Review the parameters of the [API v1](request.md) method and [API v3](../tts-v3/api-ref/grpc/) requests.

