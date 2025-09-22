{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Убедитесь, что выполняется подписание артефактов при сборке приложения.

{% endlist %}

**Инструкции и решения по выполнению:**

Артефакты в рамках пайплайна можно подписывать с помощью стороннего ПО [Cosign](https://github.com/sigstore/cosign) для подписи [артефактов](https://docs.sigstore.dev/signing/quickstart/), образов и [in-to-to аттестаций](https://github.com/in-toto/attestation/tree/main/spec/predicates), чтобы в дальнейшем загрузить их в {{ container-registry-full-name }}.

С помощью специальной сборки утилиты Cosign сохраняйте созданную [ключевую пару электронной подписи](../../../kms/concepts/asymmetric-signature-key.md) в сервисе [{{ kms-full-name }}](../../../kms/quickstart/index.md), подписывайте файлы и артефакты закрытым ключом этой ключевой пары и проверяйте электронную подпись с помощью ее открытого ключа.

Подробнее см. в разделе [{#T}](../../../container-registry/tutorials/sign-cr-with-cosign.md).