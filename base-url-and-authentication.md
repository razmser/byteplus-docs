# Base URL and authentication

ModelArk APIs are divided into data plane APIs (APIs for model calls) and control plane APIs related to management, such as managing inference endpoints. The authentication methods they support are different. The following describes the authentication methods for ModelArk APIs.

<span id="28e0db57"></span>
# Concepts


* **Data plane API**: An interface that directly handles **business data transfer, real\-time interaction, and user request processing**. It focuses on the "flow and processing of actual business data" and carries the core service capabilities that the system provides externally.The Chat API and Responses API used to request large model services are both data plane APIs.

* **Control plane API**: An interface used for **system resource management, configuration control, and status monitoring**. It focuses on managing and scheduling the data plane and system resources, and serves as the "control center" that ensures stable system operation. For example, in ModelArk, APIs used to manage API keys, base models, and other resources are all control plane APIs.

* **Base URL**: The "base template" used to build a complete API request URL. It includes the **protocol (such as http/https), host (host domain name or IP), port (optional), and base path (optional)** , and is the "common prefix" for all specific API paths. You can concatenate the base URL with parameters such as the API path/version to form a complete API URL. Typical structure: `[protocol]://[host]/[base path (optional)]`


<span id="b77a3928"></span>
# Base URL

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">The base URL for the data plane API provided below is different from the base URL supported by Coding Plan. Coding Plan users should use the correct base URL to avoid extra fees caused by an incorrect address. For details, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1928261#7fd1eee7">Base URL</a>.</div>


Base URLs for each API type:


* Data plane API: https://ark.ap\-southeast.bytepluses.com/api/v3

    &nbsp;


<span id="data-plane-api-authentication"></span>
# Data plane API authentication

Two authentication methods are supported: API key authentication (simple and convenient) and Access key authentication (traditional permission control for cloud resources, allowing management by dimensions such as resource groups and cloud products, for fine\-grained enterprise management).

<span id="60db1ed6"></span>
## API key signature authentication

<span id="6011c5a5"></span>
### Prerequisites

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If you're new to ModelArk, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399008">Quick start</a> to get up and running quickly.</div>


<span id="d44d13a6"></span>
### Signature construction

Add the `Authorization` header to the HTTP request header as follows:

```Bash
Authorization: Bearer $ARK_API_KEY
```


See the example below:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "messages": [
        {
            "role": "system",
            "content": "You are a helpful assistant."
        },
        {
            "role": "user",
            "content": "Hello!"
        }
    ]
  }'
```



* Replace the model ID as needed. To look up the model ID, see [Model list](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310).

    &nbsp;


<span id="access-key-signature-authentication"></span>
## Access Key signature authentication

<span id="3ad1c414"></span>
### Prerequisites

You have obtained an access key. To create/view an access key, see [API access key management](https://console.byteplus.com/iam/keymanage).

> Because the access key of a root account has extensive permissions, we recommend that you create an IAM user and grant permissions such as ModelArk permissions, and then use the IAM user's access key to perform operations. For details, see [Access control using IAM](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1263493).


<span id="d03b2bb1"></span>
### Usage examples

See[使用Access Key鉴权](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1544136).

> When using access key authentication, the `model` parameter must be configured as the endpoint ID.


<span id="bdd329d5"></span>
# Control plane API authentication

Control plane APIs include APIs such as managing API keys and managing inference endpoints.

<span id="50f355e8"></span>
## Access Key signature authentication

You have obtained an access key. To create/view an access key, see [API access key management](https://console.byteplus.com/iam/keymanage).

<span id="c04e9b57"></span>
### Method: Usage examples/instructions (simple, recommended)

See [OpenAPI Center](https://api.byteplus.com/api-explorer?action=GetApiKey&groupName=Manage%20API%20Key&serviceCode=ark&tab=3&tab_sdk=GO&version=2024-01-01).
