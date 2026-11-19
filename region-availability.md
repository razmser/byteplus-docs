# Region availability

A region is a geographic area where compute resources for model inference are primarily provisioned and scheduled.

ModelArk currently supports the following regions:

* Johor, Asia Pacific (AP):

   * Region ID: `ap-southeast-1`

   * Base URL: `https://ark.ap-southeast.bytepluses.com/api/v3`

* Dublin, Europe (EU):

   * Region ID: `eu-west-1`

   * Base URL: `https://ark.eu-west.bytepluses.com/api/v3`

# Key concepts

## Regional isolation

You access model services through region\-scoped customized online inference endpoints:

* An inference endpoint created in a region must be invoked using the region\-specific base URL. Inference endpoints in one region cannot be called through another region's URL.

* Platform\-level resources, such as API keys and model activation status, are isolated by region.

For example, when making the following request, ensure that the **Base URL** and **API key** are for the region in which the inference endpoint was created:

**AP region demo**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "<AP_ENDPOINT_ID>",
    "messages": [
      {"role": "user", "content": "hello"}
    ]
  }'
```

**EU region demo**

```Bash
curl https://ark.eu-west.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "<EU_ENDPOINT_ID>",
    "messages": [
      {"role": "user", "content": "hello"}
    ]
  }'
```

## How inference requests are routed

Inference requests sent to an inference endpoint are primarily routed to the region where the endpoint is created. However, based on overall resource scheduling, some requests may be routed to inference resources in other regions.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/661b9c35dca042f4bbd488ecadde0704~tplv-goo7wpa0wc-image.image)

|Case |Region |Request origin |Observed or expected routing behavior |
|---|---|---|---|
|Case 1 |AP |Singapore |Inference prefers AP, but may spill over to EU if needed |
|Case 2 |EU |Austria |Inference prefers EU, but may spill over to AP if needed |

# Switch between regions

You can switch regions from the upper\-left corner of the [console](https://ai.byteplus.com/ark/region:ap-southeast-1).

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ffa80563dd524eecbeab85da6deed2e4~tplv-goo7wpa0wc-image.image)

After switching the region, the console displays resources available in and information about the current region.

# Capability availability by region

This section lists models and APIs supported in the EU region. The availability of platform capabilities and services is also listed for reference.

## Models supported in the EU region

The EU region currently supports the following models:

* `seed-2-0-lite`

For the full supported model list, see [Model list](model-list.md).

## APIs supported in the EU region

* [Chat API](chat-api.md)

* [Image generation API](image-generation-api.md)

## Platform capabilities and services

|Capability or service |AP |EU |
|---|---|---|
|Foundation models (page) |✅ |✅ |
|Model profile page |✅ | |
|Starter apps |✅ | |
|Playground |✅ | |
|PromptPilot |✅ | |
|Online inference |✅ |✅ |
|Batch inference |✅ | |
|Tuning and Dataset |✅ | |
|Model activation management |✅ |✅ |
|API keys |✅ |✅ |
|Usage management |✅ |✅ |
|Network configuration |✅ | |
|Tokenizer tool |✅ | |
|Project configuration |✅ |✅ |

# Related documents

* [Quick start](quick-start.md)

* [BytePlus ModelArk Data Processing](BytePlus_ModelArk_Data_Processing.md)

* [International Availability for BytePlus Model Service](availability.md)
