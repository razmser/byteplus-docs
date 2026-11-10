# Product updates - 2025

Key feature updates of the ModelArk platform in 2025.

# Release note \- 202512

|Module |Description |Documentation/link |
|---|---|---|
|Console |* Support viewing the platform's main features without logging in. |[Console](https://ai.byteplus.com/ark/region:ap-southeast-1/overview) |
|Playground |* Support offline video generation in playground, with 50% off on costs |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience) |
|Model Inference |* Interaction optimization for the model unit creation page and details page<br><br>* The model unit supports viewing input and output token usage data and average cache hit rate.<br><br>* Safe Experience Mode supports image generation and video generation models<br><br>* Model usage limits support image generation and video generation models |[Model Inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint) |
|Usage |* Supports viewing the total cache hit count for inputs under the model and endpoint dimensions |[Usage](https://ai.byteplus.com/ark/region:ap-southeast-1/usageTracking) |
|API |* Launched the Models API, supporting the query of models and their features.<br><br>* Video generation models support offline inference: The Content Generation API has added the service tier and execution_expires_after parameters, which allow setting the inference mode and inference timeout for video generation models. |[Video Generation API](create-video-generation-task-api.md) |

# Release note \- 202511

|**Module** |**Description** |**Documentation** |
|---|---|---|
|Foundation Models |* Optimize the typesetting and layout of Foundation Models to display more comprehensive model fields. |[Foundation Models Introduction](foundation-models.md) |
|Playground |* Completed consistency optimization for the experience of LLMs and vision models, while focusing on upgrading features such as model selection switching and model information display, to improve the efficiency of understanding and experiencing models. |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/language-models) |
|Monitoring |* Supports viewing usage and call counts by model, endpoint, and API Key; also supports viewing overall usage and that of each sub\-module. |[Monitoring](1159199.md) |

# Release note \- 202509

|**Module** |**Description** |**Documentation** |
|---|---|---|
|Foundation Models |* Optimized Smart Assistant's response quality and performance; added shortcut key support for screenshot\-based queries<br><br>* Optimized Seedance 1.0 Lite model details page; added information on reference image model capabilities |[Foundation Models](foundation-models.md) |
|Playground |\* Experience SeedEdit 3.0L image\-to\-image model |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/language-models) |
|Monitoring |\* Split and display usage by billing items |[Monitoring](1159199.md) |
|Model Activation |\* Optimized One\-Click Model Experience Activation: Added Select All/Unselect All toggle, Worry\-Free Experience, Error Alerts, and other enhancements. |[Model Activation](1159200.md) |
|API |* Seedance 1.0 Lite model adds a reference image parameter, supporting video generation with multi\-image reference.<br><br>* Responses API SDK now supports Python SDK |[Chat API](chat-api.md) |

# Release note \- 202508

|**Module** |**Description** |**Documentation** |
|---|---|---|
|APIs |* The API return value for querying information about a video generation task in the video generation model has added new parameters: seed, resolution, duration, ratio, and framespersecond<br><br>* Release the Response API  **【Beta Testing】**  |[Retrieve a video generation task](get-video-generation-task-api.md) |
|Model Activation |* Support for self\-service cache shutdown<br><br>* Support for one\-click activation of all model services |[Model Activation](1159200.md) |
|AI App Lab |* Support for creating and duplicating high\-code applications<br><br>* Open\-source interactive bilingual video generation application |[AI App Lab](https://ai.byteplus.com/ark/region:ap-southeast-1/application) |

# Release note \- 202507

|**Module** |**Description** |**Documentation** |
|---|---|---|
|Foundation Models |* Optimize the Foundation Models and the model details page |[Foundation Models Introduction](foundation-models.md) |
|Playground |* Support the experience of the text\-to\-image model Bytedance\-Seedream\-3.0\-t2i.<br><br>* Support the experience of video generation models Bytedance\-Seedance\-1.0\-lite\-i2v, Bytedance\-Seedance\-1.0\-lite\-t2v, and ByteDance\-Seedance\-1.0\-pro |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/language-models) |
|Billing |\* Model Billing Upgrade |[Pricing](model-pricing.md) |
|Model Activation |\* Support self\-service De\-activate the model service |[Model Activation](1159200.md) |
|API |* The content of all roles in the chat API Message supports the list type.<br><br>* The video generation API has added a `seed` parameter to control the randomness of the generated content. For the same request, if the same random seed is used, similar video generation results will be produced.<br><br>* A new option 21: 9 has been added to the ratio parameter of the video generation API.<br><br>* The visual understanding model supports uploading video files. |[Retrieve a video generation task](get-video-generation-task-api.md) |

# Release note \- 202506

|**Module** |**Description** |**Documentation** |
|---|---|---|
|Console |\* Added overview page, offering quick start and user guidance | |
|Model capability |* Supported JSON object on certain models<br><br>* Supported Context Caching |* [Text generation](text-generation.md)<br><br>* [Context cache](context-cache.md) |

# Release note \- 202504

|**Module** |**Description** |**Documentation** |
|---|---|---|
|Starter apps |\* Released DeepSearch: A high\-efficiency tool powered by DeepSeek\-R1, enabling in\-depth multi\-faceted analysis with integrated online resources to deliver comprehensive solutions. |[Agent builder](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/agent-builder-agent-builder) |
|Playground |* Introduced a web\-browser plugin for LLMs.<br><br>* Enabled code mode switching within parameter settings.<br><br>* Released fast access functionality to simplify the user journey. |[Language models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/language-models) |
|Model Inference |* Added usage field for each chunk in streaming output of the chat/completions API<br><br>* Supported Function calling on DeepSeek\-V3 and DeepSeek\-R1<br><br>* Updated **Free Tokens Only Mode**, allowing users to consume free tokens only during API calls, ensuring zero billing |[Free Tokens Only mode](free-tokens-only-mode.md)<br><br>[Create Standard inference endpoint](online-inference-standard.md) |
|Monitoring |* Separated endpoints and playground usage | |

# Release note \- 202502

|Module |Description |Documentation |
|---|---|---|
|Foundation models |Launched Foundation Models, a centralized hub showcasing LLMs released by all model providers on the platform. |[Foundation models](foundation-models.md) |
|Playground |Released the experience center, which allows you to quickly experience each model and explore its advanced capabilities in language, image, and more. |[Language models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/language-models) |
|Model inference |* Enhanced the inference endpoint monitoring dashboard to display global endpoint performance, including metrics like TPM (Tokens per Minute), RPM (Requests per Minute), and success rate.<br><br>* Added associated agent information on the inference endpoint details page, with quick creation functionality for agents. |[Create Standard inference endpoint](online-inference-standard.md) |
