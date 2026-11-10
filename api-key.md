# Obtain and configure an API key

Before calling models on ModelArk, you need an API key for authentication. Because the API key is sensitive, a leaked API key may allow others to consume your model quota and result in your financial losses. Therefore, we provide a method for configuring the API key in environment variables so that you can use the API key properly and securely.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">API key plaintext format change</div>


<div data-tips="true" data-tips-type="warning">ModelArk API keys created after September 17, 2026, 12:00 (UTC+8) will use the new plaintext format. This adjustment only affects the API key format and does not affect the API authentication method. API keys created before this time remain valid and do not need to be regenerated.</div>


<span id="20bdcc68"></span>
## Get an API key


1. Sign in to the console.

2. (Optional) Switch the project space in the lower\-left corner of the console, and then select [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey).

3. Click **Create API key**.

4. In the **Name** text box in the dialog box, confirm or change the API key name, and then click Create.

   You can view the newly created API key in the **API keys** list on the [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page.

    &nbsp;


<span id="configure-the-api-key"></span>
## Configure the API key

We recommend configuring the API key in environment variables instead of hard\-coding it in your code. This prevents the API key from being leaked with your code, which could allow others to use your quota and incur additional costs.

For the configuration method, see: [Environment variable configuration guide](environment-variable-configuration-guide.md).

<span id="64cf3cd0"></span>
## Instructions


* API key quota: A primary account can create up to 50 API keys. To request a higher quota, submit a [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514) to request.

* API key permission control: An API key is created in your current project and is used to access resources in the current project (that is, models and applications). You can additionally restrict the model IDs / custom inference endpoints that the API key can access, or the IP addresses that can call this API key.

   * Tips: You can switch the project space to create an API key, which can restrict the API key to be used only as an authentication credential for model services in the specified project space. When your account is a shared account, you can use this method to isolate permissions.

   * Note: An API key can only access endpoints in the specified project and does not support cross\-project access. If you migrate an endpoint across projects, authentication with the original API key will become invalid.
