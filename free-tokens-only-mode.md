# Free Tokens Only mode

<span id="9cdfb8b4"></span>
# About Free Tokens Only mode

Free Tokens Only mode is an experience mode provided by the ModelArk platform that allows you to try model inference services at no cost.

In this mode, calls to the inference API consume only the 500k free tokens granted by the platform. When the free quota is nearly exhausted, the service will be automatically paused to prevent any additional charges.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">The free quota applies only to offset tokens consumed by model inference and cannot be used to cover fees incurred from using plugins.</div>


<span id="411c88f0"></span>
# How it works

Free Tokens Only mode is configured at the account level and applies to all models under the account that support this mode.


* When enabled: Online inference calls consume only the free quota. Once the free quota is exhausted, the service is automatically paused.

* When disabled: Online inference calls first consume the free quota. After the free quota is used up, usage is billed based on actual token consumption.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>



* <div data-tips="true" data-tips-type="warning">If you wish to continue making calls after the service has been paused, you must disable it.</div>


   <div data-tips="true" data-tips-type="warning"><strong>Note</strong>: Once disabled, Free Tokens Only mode cannot be re\-enabled.   </div>
   

* <div data-tips="true" data-tips-type="warning">By default, Free Tokens Only mode is disabled. If needed, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1465347#34d3c92e">Function entrance</a> for how to enable.</div>



<span id="41b0c2d6"></span>
# Scope


* **Accounts**: Accounts that have not activated model services and have never enabled or disabled Free Tokens Only mode (including both identity\-verified personal accounts and enterprise accounts) are eligible to enable this mode.

* **Models**: All models are supported except for Multimodal embedding (vectorization) models and Simultaneous interpretation models.

* **Billing behavior**:

   * Charges avoided: Online inference billed on a postpaid, token\-based basis.

   * Charges not avoided: Fees incurred from using plugins and other auxiliary services.


**Note**: If you need to purchase model units or enable inference caching, you must disable Free Tokens Only in advance.

<span id="34d3c92e"></span>
# Tutorial

<span id="64c1252b"></span>
## Enable Free Tokens Only mode

Option 1: Enter the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false), click the switch on the "Free Tokens Only Mode" banner above the model list.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/9094f8b400164d47b4369592f54eb5ba~tplv-goo7wpa0wc-image.image) </span>

Option 2: Check the Free Tokens Only mode when activating a model. Free Tokens Only mode will be enabled when the model is activated.

<span id="1647fda1"></span>
## Disable Free Tokens Only mode

Enter the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false), click the switch on the "Free Tokens Only Mode" banner to turn it off.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/739315044434431aa774be10c867b881~tplv-goo7wpa0wc-image.image) </span>

<span id="b0493e61"></span>
## View the status of Free Tokens Only mode


* On the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false), you can view whether the corresponding model is in Free Tokens Only mode.

   An icon will be displayed in the "Free Inference Quota" column if Free Tokens Only mode is on.

* After the free tokens are used up, the service pauses automatically. Disable Free Tokens Only mode to continue.

* You can also view the status of Free Tokens Only mode at the inference API entry points (such as Quick API Access, Online Inference, and the Experience Center).


<span id="af51ad2e"></span>
## API call error code

Service will be automatically suspended when the free quota is exceeded. The following error code and information will be returned:


|HTTP Error Code |Type |Code |Message |Description |
|---|---|---|---|---|
|429 |TooManyRequests |SetLimitExceeded |Your account [%s] has reached the set inference limit for the [%s] model, and the model service has been paused. To continue using this model, please visit the Model Activation page to adjust or close the "Free Tokens Only Mode".<br><br>Request ID: |The current account [%s] inference quota for [%s] model has been consumed. If you want to continue calling, please go to the ModelArk console to open the management page and turn off the "Free Tokens Only Mode" option. |


<span id="743d5c23"></span>
# FAQs

<span id="43702da4"></span>
### Q: When will the service be paused after Free Tokens Only mode is enabled?

A: When the platform\-granted 500k free tokens are fully consumed, the service will be automatically paused. You can view the details in the model list on the Service Management page.

<span id="4261e2b8"></span>
### Q: Can Free Tokens Only mode be re\-enabled after it is disabled?

A: No. Free Tokens Only mode is designed exclusively for new users to experience the free quota. Once it is disabled, it cannot be re\-enabled. Please proceed with caution.

<span id="80206653"></span>
### Q: How can I continue using the service after the free quota is exhausted?

A: Go to the model activation management page and disable Free Tokens Only mode. Any usage beyond the free quota will then be billed based on actual token consumption.

<span id="8ef5a018"></span>
### Q: Why does the activated model list page show remaining free inference quota, but the service is already paused?

A: If Free Tokens Only mode is enabled, the model service will be paused once the 500k free tokens granted by the platform are fully consumed.

The "Free Inference Quota" shown on the Activation Management page reflects the total free resource packages available under the account for that model. Additionally, data in the billing center may have an hour\-level delay compared to actual API usage.

Therefore, under Free Tokens Only mode, whether the free quota has been exhausted should be determined by the API response and the model status in the model list.

<span id="cd4d2d0b"></span>
### Q: Why could I see the Free Tokens Only mode option before, but cannot find it now?

A: Free Tokens Only mode is designed to help new users who have not yet activated any model services experience free inference more easily. Once you activate any model service without enabling Free Tokens Only mode, your account no longer meets the eligibility conditions, and the option will no longer be displayed.
