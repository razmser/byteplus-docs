# Digital character library

ModelArk Model Playground provides a digital character library for Dreamina Seedance 2.0 and Dreamina Seedance 2.5 video generation models. You can browse and search for digital characters, use selected character assets for video generation, and combine them with templates and your own assets to quickly validate the results.

The Model Playground also provides a wide range of video generation templates. You can view the assets used by a template and obtain its sample code with one click.

> Tip: You can use digital character assets with the [Create video generation task API](create-video-generation-task-api.md). For details, see [Use preset digital characters](seedance-portrait-asset-guide.md#preset-avatar).


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>



* <div data-tips="true" data-tips-type="warning">ModelArk strives to present users with more diverse content. If you have any comments or suggestions about this feature, please <a href="https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514">create a ticket</a> or contact Support for feedback. We will carefully read and evaluate your feedback and handle it as soon as possible.</div>


* <div data-tips="true" data-tips-type="warning">To ensure compliant use of this feature, please read the <a href="https://docs.byteplus.com/en/docs/legal/tos_modelark_playground">ModelArk AI Playground User Terms</a> before you begin.</div>



<span id="901571f6"></span>
## About the digital character library

The digital character library offers a rich collection of digital character assets. Each digital character contains one or more image assets. You can send a selected digital character asset to the model as a reference and combine it with a prompt to generate a video containing that character.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/6ef40cec0c894427b1c908c7d1f56813~tplv-goo7wpa0wc-image.image) </span>

Each digital character asset corresponds to an asset ID. When using the video generation API, you can use a URI containing this ID to pass the corresponding asset to the model. The platform also provides a rich library of non\-portrait assets. You can view their asset IDs and URIs in the asset library.

You can search for digital characters in the Model Playground, view the asset ID of each character asset, and quickly apply the selected asset to a video generation task.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>



* <div data-tips="true" data-tips-type="tip">When using the digital character library for the first time, you must agree to the relevant agreement before you can browse and use the character assets normally.</div>



&nbsp;

<span id="b2049b21"></span>
## Browse and search for digital characters


1. Open ModelArk [Model Playground](https://console.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-5-260628) in your browser, and click the **Digital Character Library** tab below the input box.

2. Search for the character you want to use. Natural language search and conditional filtering are supported.

   1. Examples of natural language search: "Energetic and sunny young male college student"

   2. Use the filter box on the right to filter by character gender, age, and nationality.

3. Click a digital character to view its details. You can switch between the character's assets on the right.

4. After selecting the desired character asset, click the **Use** icon to add it to the input box. In the input box, use the "@+asset name" format to reference the asset, for example:

> ... The CEO from  **@Image1** accidentally bumps into the female lead wearing the outfit from  **@Image2** (character from  **@Image3**)  *...* 


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true" data-wrapper-indent="1">Tip</div>


<div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">By default, each request generates <strong>four video clips</strong>. To reduce costs, we recommend generating <strong>one clip per request</strong>.</div>


5. Click the send icon at the lower right corner of the input box to start video generation.


<span id="58715d25"></span>
## Quickly obtain sample code from the template library

The Model Playground for Seedance 2.0 and Seedance 2.5 provides a video generation template library, with each template containing complete instructions and assets.

You can generate with one click using a template, or generate videos based on a template combined with digital characters and custom content.

The template library also supports viewing REST API call samples for templates. You can directly copy sample commands for testing.

Refer to the following steps to quickly view sample code:


1. In the Seedance 2.0 and Seedance 2.5 Playground, click the **Templates** tab at the bottom.

2. Hover the mouse over a template and click the **Code** icon to view and copy the command.


**Caution**: Before testing, you need to set the `ARK_API_KEY` environment variable or replace `$ARK_API_KEY` with your API Key.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ccda6a851bd049aebb58d76a014b130b~tplv-goo7wpa0wc-image.image) </span>
