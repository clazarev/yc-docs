#|
|| **Model** | **Price per 1,000 tokens, synchronous mode,</br>without VAT** | **Price per 1,000 tokens, asynchronous mode,</br>without VAT** ||
|| {{ gpt-lite }} | {{ sku|USD|foundation_models.text_generation.v1|string }} | {% calc [currency=USD] 0,5 × {{ sku|USD|foundation_models.text_generation.v1|number }} %} ||
|| {{ gpt-pro }} | {% calc [currency=USD] 6 × {{ sku|USD|foundation_models.text_generation.v1|number }} %} | {% calc [currency=USD] round((3 × {{ sku|USD|foundation_models.text_generation.v1|number }}) × 100) / 100 %} ||
|| {{ gpt-pro }} 5.1  | {% calc [currency=USD] 2 × {{ sku|USD|foundation_models.text_generation.v1|number }} %} ^1^ | {% calc [currency=USD] {{ sku|USD|foundation_models.text_generation.v1|number }} %} ^1^ ||
|| {{ llama }} 8B | {{ sku|USD|foundation_models.text_generation_alt.v1|string }} | {% calc [currency=USD] 0,5 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} ||
|| {{ llama }} 70B | {% calc [currency=USD] 6 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} | {% calc [currency=USD] 3 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} ||
|| Qwen3 235B | {% calc [currency=USD] 2,5 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} ^1^ | —  || 
|| gpt-oss-120b | {% calc [currency=USD] 1,5 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} | — || 
|| gpt-oss-20b | {% calc [currency=USD] 0,5 × {{ sku|USD|foundation_models.text_generation_alt.v1|number }} %} | — || 
|#

^1^ The price is based on the current 50% discount.