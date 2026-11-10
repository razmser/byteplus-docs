# Model Playground overview

You can quickly try out the capabilities of different models in the Playground, evaluate model performance, and identify the model that best fits your business scenario. Playground provides a user\-friendly interactive interface that supports model, version, and invocation method selection, as well as parameter configuration.

Model Playground currently supports the following experiences:


* Language models: Perform text understanding and visual understanding tasks.

* Vision models: Perform image and video generation tasks.

* Speech models: Try capabilities such as audio generation and video voice\-over.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">You need available free trial credits.</div>



* <div data-tips="true" data-tips-type="warning">Before your credits are exhausted, you can try models directly in the Playground.</div>


* <div data-tips="true" data-tips-type="warning">After your free credits are exhausted, you need to activate the model before trying it in the Playground, and trial usage will incur charges.</div>



<span id="usage-example"></span>
## Usage example

The following sections use a language model as an example to show how to access Model Playground, configure model parameters, interact with the model, and quickly access the API.

<span id="access-playground"></span>
### Access Model Playground

You can access Model Playground from the following locations:


* In the left navigation bar of the console, click **Playground**.

* On the **Model Square** page, open a model details page and click **Try now**.


<span id="select-and-configure-model"></span>
### Select a model and configure parameters


1. Expand the model list in the upper\-left corner of Model Playground. On the **Language** tab, select a model, and then select a model version on the model details page. You can also use an existing inference endpoint or create a new one.

2. Click **Configure** on the right side of the model list to configure model parameters. Select an **API Pattern** to use. For details, see [Responses API](responses-api-text-generation.md) or [Chat API](text-generation.md).

   Adjust parameters based on the effects described in the following table.

   
   |**Parameter name** |**Description** |
   |---|---|
   |max_output_tokens |The maximum token limit for output text. Set this parameter to restrict the length of model output.<br><br>One Chinese character or English word is typically counted as one token. Use the [Token calculator](https://ai.byteplus.com/ark/region:ap-southeast-1/tokenCalculator?projectName=default) to calculate token consumption.<br><br>**Note**: The total number of input and output tokens is also limited by the model's context length. |
   |System Prompt |Sets the model's behavioral preferences, specifies the role it should play, or provides background information. The System Prompt has higher priority than instructions entered in the conversation.<br><br>For example, to make model output more concise, enter the following System Prompt: "Limit all replies to three sentences. Provide only the key conclusion without further explanation."<br><br>**Note**: Click the **pencil** icon on the right to open the prompt optimization interface and optimize the system instructions. |
   


<span id="interact-with-model"></span>
### Interact with the model

Enter instructions or questions in the dialog box, and then click the **Send** icon in the lower\-right corner to start interacting with the model.

Depending on the selected model, you can use the following features:


* Upload attachments: Upload images, videos, or other files.

* Internet access: Allow the model to use internet search tools. If disabled, the model generates content using only its own knowledge.

* Reasoning: Control the number of reasoning steps and generation time. A higher level typically improves output quality but takes longer.


After completing a conversation, you can:


* Continue sending content in the dialog box. Each message automatically includes the previous context.

* Regenerate, copy, or rate generated content.

* Create a new conversation or clear the context of the current conversation.


You can also use the **Chat history** list on the left to switch to, rename, or delete previous conversations.

<span id="access-api"></span>
### Quickly access the API

Click **API** in the upper\-right corner to copy the API key and sample code, quickly test API requests, and create an application.
