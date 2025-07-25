---
ms.custom: overview
ms.topic: include
ms.date: 05/16/2025
ms.author: johalexander
author: ms-johnalex
ms.service: azure
---
## Resources for Azure OpenAI Service

Azure OpenAI Service provides REST API access to the powerful language models available in OpenAI. Azure OpenAI helps you adapt these models to accomplish specific tasks, such as content generation, summarization, image understanding, semantic search, and natural language to code translation. Access Azure OpenAI by using the REST APIs, the Azure OpenAI SDK for .NET, or the web-based interface in [Azure OpenAI Studio](https://oai.azure.com/).

### SDKs and libraries

|Link|Description|
|---|---|
|[OpenAI SDK for Python](https://github.com/openai/openai-python/blob/main/README.md)|The GitHub source code version of the OpenAI Python library, which provides convenient access to the OpenAI API from applications written in the Python language.|
|[OpenAI Python Package](https://pypi.org/project/openai/)|The PyPi version of the OpenAI Python library.|
|[Switch from OpenAI to Azure OpenAI](/azure/ai-services/openai/how-to/switching-endpoints)|A guidance article on the small changes you need to make to your code, so you can swap back and forth between OpenAI and the Azure OpenAI Service.|
|[Streaming chat completions](https://github.com/openai/openai-cookbook/blob/main/examples/azure/chat.ipynb)|A notebook example that demonstrates how to get chat completions to work by using the Azure endpoints. The example focuses on chat completions, but also introduces other operations available with the API.|
|[Azure embeddings](https://github.com/openai/openai-cookbook/blob/main/examples/azure/embeddings.ipynb)|A notebook example that demonstrates how to use embeddings with Azure endpoints. The example focuses on embeddings, but also introduces other operations available with the API.|
|[Deploy model and generate text](/azure/ai-services/openai/quickstart?pivots=programming-language-python)|An article with minimal, straightforward detailed steps to deploy a model that can programmatically chat.|
|[OpenAI with Microsoft Entra ID role-based access control](/azure/ai-services/authentication?tabs=powershell#authenticate-with-azure-active-directory)|A look at authentication by using Microsoft Entra ID and [Azure role-based access control](/azure/role-based-access-control/overview).|
|[OpenAI with Azure AD-managed identities for Azure resources](/azure/ai-services/openai/how-to/managed-identity)|An article with more complex security scenarios that require Azure role-based access control. Explore how to authenticate to your OpenAI resource with Microsoft Entra ID.|
|[Azure OpenAI Service samples](https://github.com/Azure-Samples/openai/blob/main/README.md)|A compilation of useful Azure OpenAI Service resources and code samples to help you get started and accelerate your technology adoption journey.|

### Documentation

|Link|Description|
|---|---|
|[Azure OpenAI Service documentation](/azure/ai-services/openai/)|The hub page for Azure OpenAI Service documentation.|
|[Quickstart: Get started generating text with Azure OpenAI Service](/azure/ai-services/openai/quickstart?tabs=command-line%2Cpython-new&pivots=programming-language-python)|A quickstart that demonstrates how to set up the services you need and write code to prompt a model by using Python.|
|[Quickstart: Get started using GPT-35-Turbo and GPT-4 with Azure OpenAI Service](/azure/ai-services/openai/chatgpt-quickstart?tabs=command-line%2Cpython-new&pivots=programming-language-python)|A quickstart that demonstrates how to work with system, assistant, and user roles to tailor content in response to certain questions.|
|[Quickstart: Chat with Azure OpenAI models by using your own data](/azure/ai-services/openai/use-your-data-quickstart?tabs=command-line%2Cpython-new&pivots=programming-language-python)|A quickstart that helps you add your own data, such as a PDF or other document.|
|[Quickstart: Get started using Azure OpenAI Assistants (Preview)](/azure/ai-services/openai/assistants-quickstart?tabs=command-line%2Ctypescript&pivots=programming-language-python)|A quickstart that demonstrates how to instruct a model to use the built-in Python code interpreter to solve math problems step by step. This example provides a starting point to use your own AI assistants accessed through custom instructions.|
|[Quickstart: Use images in your AI chats](/azure/ai-services/openai/gpt-v-quickstart?tabs=image%2Ccommand-line&pivots=programming-language-python)|A quickstart that shows how to programmatically ask a model to describe the contents of an image.|
|[Quickstart: Generate images with Azure OpenAI Service](/azure/ai-services/openai/dall-e-quickstart?tabs=dalle3%2Ccommand-line&pivots=programming-language-python)|A quickstart that demonstrates how to programmatically generate images by using Dall-E based on a prompt.|

## Resources for other Azure AI services

In addition to Azure OpenAI Service, there are many other Azure AI services. Developers and organizations can rapidly create intelligent, market-ready, and responsible applications with out-of-the-box and prebuilt customizable APIs and models. Example applications include natural language processing for conversations, search, monitoring, translation, speech, vision, and decision-making.

### Samples

|Link|Description|
|---|---|
|[Integrate speech into your apps with Azure AI Speech SDK Samples](https://github.com/Azure-Samples/cognitive-services-speech-sdk)|Samples for the Azure Cognitive Services Speech SDK. Links to samples for speech recognition, translation, speech synthesis, and more.|
|[Azure AI Document Intelligence SDK](/azure/ai-services/document-intelligence/versioning/sdk-overview-v4-0)|Azure AI Document Intelligence (formerly Form Recognizer) is a cloud service that uses machine learning to analyze text and structured data from documents. The Document Intelligence software development kit (SDK) is a set of libraries and tools that enable you to easily integrate Document Intelligence models and capabilities into your applications.|
|[Extract structured data from forms, receipts, invoices, and cards using Form Recognizer in Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/README.md#samples-for-azure-form-recognizer-client-library-for-python)|Samples for the Azure.AI.FormRecognizer client library.|
|[Extract, classify, and understand text within documents using Text Analytics in Python](/python/api/overview/azure/ai-textanalytics-readme?view=azure-python&preserve-view=true)|The client Library for Text Analytics. These APIs are part of the [Azure AI Language](/azure/ai-services/language-service) service, which provides Natural Language Processing (NLP) features for understanding and analyzing text.|
|[Document Translation in Python](/azure/ai-services/translator/document-translation/quickstarts/client-library-sdks?tabs=dotnet&pivots=programming-language-python)|A quickstart article that uses Document Translation to translate a source document into a target language while preserving structure and text formatting.|
|[Question answering in Python](/azure/ai-services/language-service/question-answering/quickstart/sdk?tabs=windows&pivots=programming-language-python)|A quickstart article with steps to get an answer (and confidence score) from a body of text that you send along with your question.|
|[Conversational Language Understanding in Python](/python/api/overview/azure/ai-language-conversations-readme?view=azure-python&preserve-view=true)|The client library for Conversational Language Understanding (CLU). CLU is a cloud-based conversational AI service that can extract intents and entities in conversations. CLU acts like an orchestrator to select the best candidate to analyze conversations to get the best response from apps like QnA, Luis, and Conversation App.|
|[Analyze images](/azure/ai-services/computer-vision/sdk/overview-sdk)|Sample code and setup documents for the Microsoft Azure AI Image Analysis SDK.|
|[Azure AI Content Safety SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/contentsafety/azure-ai-contentsafety)|The SDK can help detect harmful user-generated and AI-generated content in applications and services. Content Safety includes text and image APIs that allow you to detect material that is harmful.|

### Documentation

|AI service|Description|API reference|Quickstart|
|---|---|---|---|
|[Content Safety](/azure/ai-services/content-safety/)|An AI service that detects unwanted content.|[Content Safety API reference](/python/api/overview/azure/ai-contentsafety-readme)|[Quickstart](/azure/ai-services/content-safety/quickstart-text?tabs=visual-studio%2Cwindows&pivots=programming-language-python)|
|[Document Intelligence](/azure/ai-services/document-intelligence/)|Turn documents into intelligent data-driven solutions.|[Document Intelligence API reference](/python/api/overview/azure/ai-formrecognizer-readme)|[Quickstart](/azure/ai-services/document-intelligence/quickstarts/get-started-sdks-rest-api?pivots=programming-language-python)|
|[Language](/azure/ai-services/language-service/)|Build apps with industry-leading natural language understanding capabilities.|[Text Analytics API reference](/python/api/overview/azure/ai-textanalytics-readme)|[Quickstart](/azure/ai-services/language-service/text-analytics-for-health/quickstart?tabs=windows&pivots=programming-language-python)|
|[Search](/azure/search/)|Bring AI-powered cloud search to your applications.|[Search API reference](/python/api/overview/azure/search)|[Quickstart](/azure/search/search-get-started-text?tabs=python)|
|[Speech](/azure/ai-services/speech-service/)|Speech to text, text to speech, translation, and speaker recognition.|[Speech API reference](/python/api/overview/azure/cognitiveservices/speech)|[Quickstart](/azure/ai-services/speech-service/get-started-speech-to-text?tabs=windows%2Cterminal&pivots=programming-language-python)|
|[Translator](/azure/ai-services/translator/)|Use AI-powered translation to translate more than 100 in-use, at-risk and endangered languages and dialects.|[Translation API reference](/python/api/overview/azure/ai-translation-document-readme)|[Quickstart](/azure/ai-services/translator/quickstart-text-sdk?pivots=programming-language-python)|
|[Vision](/azure/ai-services/computer-vision/)|Analyze content in images and videos.|[Image Analysis API reference](/python/api/overview/azure/ai-vision-imageanalysis-readme)|[Quickstart](/azure/ai-services/computer-vision/quickstarts-sdk/image-analysis-client-library?tabs=windows%2Cvisual-studio&pivots=programming-language-python)|

## Training

|Link|Description|
|---|---|
|[Generative AI for beginners workshop](https://github.com/microsoft/generative-ai-for-beginners/tree/main)|Learn the fundamentals of building Generative AI apps with our 18-lesson comprehensive course by Microsoft Cloud Advocates.|
|[Get started with Azure AI services](/training/paths/get-started-azure-ai/)|Azure AI services are building blocks of AI functionality you can integrate into your applications. Complete this learning path to explore how to provision, secure, monitor, and deploy Azure AI services resources and use them to build intelligent solutions.|
|[Microsoft Azure AI Fundamentals: Generative AI](/training/paths/introduction-generative-ai/)|Complete this learning path to understand how large language models form the foundation of generative AI. Explore how Azure OpenAI Service provides access to the latest generative AI technology. Learn how Azure OpenAI prompts and responses can be fine-tuned and how Microsoft's responsible AI principles drive ethical AI advancements.|
|[Develop Generative AI solutions with Azure OpenAI Service](/training/paths/develop-ai-solutions-azure-openai/)|Azure OpenAI Service provides access to OpenAI's powerful large language models such as ChatGPT, GPT, Codex, and Embeddings models. Complete this learning path for developers and explore how to generate code, images, and text by using the Azure OpenAI SDK and other Azure services.|
|[Build AI apps with Azure Database for PostgreSQL](/training/paths/build-ai-apps-azure-database-postgresql/)|Complete this learning path to explore Azure AI and Azure Machine Learning Services integrations provided by the Azure AI extension for Azure Database for PostgreSQL - Flexible Server. Learn how these services can enable you to build AI-powered apps.|

## AI application templates

AI application templates supply you with well-maintained, easy to deploy reference implementations that provide a high-quality starting point for your AI apps.

There are two categories of AI app templates, **building blocks** and **end-to-end solutions**. Building blocks are smaller-scale samples that focus on specific scenarios and tasks. End-to-end solutions are comprehensive reference samples that include documentation, source code, and deployment features. You can build on the solutions and extend them for your own purposes.

- To review a list of key templates available for each programming language, see [AI app templates](/azure/developer/ai/intelligent-app-templates?pivots=python).
- To browse all available templates, see the AI app templates on the [Azure Developer CLI gallery](https://azure.github.io/awesome-azd/?tags=aicollection).
