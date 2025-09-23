---
title: About {{ foundation-models-full-name }}
description: '{{ foundation-models-full-name }} is a service that provides access to the APIs of large neural networks that can generate high-quality texts and images. {{ gpt-lite }} and {{ gpt-pro }} can generate product descriptions, articles, news stories, newsletters, blog posts, and many other things. {{ yandexart-name }} can create an image by description. The quality of the neural network''s response depends directly on the accuracy of the instructions you provide. With a more specific prompt, you are more likely to get the result you expect.'
---

# About {{ foundation-models-full-name }}

{{ foundation-models-full-name }} comprises several large generative models plus an efficient toolset you can use to leverage their capabilities to advance your business.

With [{{ gpt-lite }} and {{ gpt-pro }}](generation/index.md), you can quickly generate text content, e.g., product descriptions, articles, news stories, newsletters, blog posts, and many other things. The quality of the neural network's response depends directly on the accuracy of the instructions you provide. With a more specific prompt, you are more likely to get the result you expect. For the full list of generative text models, see [{#T}](generation/models.md).

{{ foundation-models-name }} also provides the [API](../embeddings/api-ref/index.md) to work with _embeddings_, i.e., vector representations of text. It can be used to classify information, compare and match texts, or search through a knowledge base of your own. For more information on embeddings and the Embeddings API, see [{#T}](./embeddings.md).

With {{ yagpt-name }} classifiers, you can classify various texts. Special models are better at it than the {{ gpt-lite }}and {{ gpt-pro }} models, their API being tailored for classification tasks. For more information about the supported classification types, see [{#T}](classifier/index.md). 

To create images in {{ foundation-models-name }} use the {{ yandexart-name }} neural network that will help you create detailed and realistic images based on a text prompt.

In addition to models working with a single type of data, {{ foundation-models-name }} provides multimodal models. 

For information on the {{ foundation-models-name }} restrictions, refer to [{#T}](limits.md).

## {{ foundation-models-name }} working modes {#working-mode}

In {{ foundation-models-name }}, you can use models in three modes: _synchronous_, _asynchronous_, or _batch_ mode. The modes differ in response time and operation logic.

In synchronous mode, the model gets your request and returns the result immediately after processing. The response delay in synchronous mode is minimal but not instant: the model takes time to do the work. With the `stream` option enabled, the model sends intermediate generation results during the process. You may opt for synchronous mode if you need to maintain a chatbot dialog. 

In asynchronous mode, the model responds to a request by sending an [Operation object](../../api-design-guide/concepts/operation.md) containing the ID of the operation it is performing. You can use the ID to learn the status of the request and later get the result of it by submitting a request to a special output endpoint (its value depends on the model). Intermediate generation results are not available in asynchronous mode. In asynchronous mode, generation usually takes longer (from a couple of minutes to several hours) than in synchronous mode but is cheaper. Use asynchronous mode if you do not need an urgent response.

Batch processing mode allows you to process a large data array in a single request to the model. Input data is provided as a [dataset](./resources/dataset.md) whose type depends on the model. For each request, {{ foundation-models-name }} runs an individual instance of the model to process the dataset and then stops it. The result is saved as another dataset, which you can download in [Parquet](https://parquet.apache.org/) format or use immediately, e.g., to tune another model. It may take several hours to generate the result.

Different models support different operating modes.

## Prompt {#prompt}

Generative models are managed using _prompts_. A good prompt should contain the context of your request to the model (instruction) and the actual task the model should complete based on the provided context. The more specific your prompt, the more accurate will be the results returned by the model.

Apart from the prompt, other request parameters will impact the model's output too. Use {{ foundation-models-name }} Playground available from the [management console]({{ link-console-main }}) to test your requests.
