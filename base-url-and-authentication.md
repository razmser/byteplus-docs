# Base URL and authentication

ModelArk APIs are divided into data plane APIs (APIs for model calls) and control plane APIs related to management, such as managing inference endpoints. The authentication methods they support are different. The following describes the authentication methods for ModelArk APIs.

# Concepts

* **Data plane API**: An interface that directly handles **business data transfer, real\-time interaction, and user request processing**. It focuses on the "flow and processing of actual business data" and carries the core service capabilities that the system provides externally.The Chat API and Responses API used to request large model services are both data plane APIs.

* **Control plane API**: An interface used for **system resource management, configuration control, and status monitoring**. It focuses on managing and scheduling the data plane and system resources, and serves as the "control center" that ensures stable system operation. For example, in ModelArk, APIs used to manage API keys, base models, and other resources are all control plane APIs.

* **Base URL**: The "base template" used to build a complete API request URL. It includes the **protocol (such as http/https), host (host domain name or IP), port (optional), and base path (optional)** , and is the "common prefix" for all specific API paths. You can concatenate the base URL with parameters such as the API path/version to form a complete API URL. Typical structure: `[protocol]://[host]/[base path (optional)]`

# Base URL

> **Note**

> The base URL for the data plane API provided below is different from the base URL supported by Coding Plan. Coding Plan users should use the correct base URL to avoid extra fees caused by an incorrect address. For details, see [Base URL](coding-plan-personal-get-started.md#e17276e8).

Base URLs for each API type:

* Data plane API: https://ark.ap\-southeast.bytepluses.com/api/v3

# Data plane API authentication

Two authentication methods are supported: API key authentication (simple and convenient) and Access key authentication (traditional permission control for cloud resources, allowing management by dimensions such as resource groups and cloud products, for fine\-grained enterprise management).

## API key signature authentication

### Prerequisites

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

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
    "model": "dola-seed-2-1-turbo-260628",
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

* Replace the model ID as needed. To look up the model ID, see [Model list](model-list.md).

## Access Key signature authentication

### Prerequisites

You have obtained an access key. To create/view an access key, see [API access key management](https://console.byteplus.com/iam/keymanage).

> Because the access key of a root account has extensive permissions, we recommend that you create an IAM user and grant permissions such as ModelArk permissions, and then use the IAM user's access key to perform operations. For details, see [Access control using IAM](use-iam-to-manage-permissions.md).

> When using access key authentication, the `model` parameter must be configured as the endpoint ID.

# Control plane API authentication

Control plane APIs include APIs such as managing API keys and managing inference endpoints.

## Access Key signature authentication

You have obtained an access key. To create/view an access key, see [API access key management](https://console.byteplus.com/iam/keymanage).

### Method: Usage examples/instructions (simple, recommended)

See [OpenAPI Center](https://api.byteplus.com/api-explorer?action=GetApiKey&groupName=Manage%20API%20Key&serviceCode=ark&tab=3&tab_sdk=GO&version=2024-01-01).
