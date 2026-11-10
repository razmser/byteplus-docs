# GetFoundationModelVersionConfiguration

Obtains the version configuration information about the base model. This operation is not recommended. We recommend that you use [GetFoundationModelVersion](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/GetFoundationModelVersion).

<span id=".6LCD6K-V"></span>
## Debugging

<span id=".6K-35rGC5Y-C5pWw"></span>
## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).



**Action** string `Required` `Example value: GetFoundationModelVersionConfiguration`

The operation to be performed. Value: GetFoundationModelVersionConfiguration.



**Version** string `Required` `Example value: 2024-01-01`

The version of the API. Value: 2024\-01\-01.



**Id** string `Required` `Example value: fmvc-20240914******-*****`

The ID of the foundation model version configuration, which needs to be obtained from the `ActiveConfigurationId` of the ListFoundationModelVersions interface.



**FoundationModelName** string `Required` `Example value: skylark-pro`

The name of the foundation model



&nbsp;

<span id=".6L-U5Zue5Y-C5pWw"></span>
## Response parameters

The following table describes only the response parameters specific to this API. For more information, see [Return Structure](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/GetFoundationModelVersionConfiguration).



**Id** string `Example value: fmvc-20240914******-*****`

ID of the foundation model version configuration



**FoundationModelName** string `Example value: skylark-pro`

Name of the foundation model



**FoundationModelVersion** string `Example value: 240828`

Name of the foundation model version



**Configuration** object `Example value: \-`

Detailed information of the foundation model configuration


**AppSettings** object

Configuration of the experience application


**Greeting** string

Greeting



**Parameters** object[]

Configuration of the foundation model parameters


**Type** string `Example value: String`

Parameter type, Int / Bool / Float / String



**Name** string

Parameter name



**DisplayName** string

Display name of the parameter



**Min** string

Minimum value of the parameter



**Max** string

Maximum value of the parameter



**Options** string[]

Optional items of the parameter



**DefaultValue** string

Default value of the parameter



**Description** string

Description of the parameter




**Prompts** object[]

Configuration of the preset prompts of the foundation model


**Scenario** string

Scenario of the prompt



**Message** string

Content of the prompt




**MaxInputTokenLength** integer

Maximum input token length



**SystemPrompt** object

Configuration of the system prompt


**Message** string

Content of the system prompt



**ModificationAllowed** boolean `Example value: True`

Whether the system prompt can be modified during the experience



**MaxInputTokenLength** long

Maximum token input length of the system prompt





**CustomizationJobSettings** object

Configuration of the customization task


**SftSettings** object

Configuration of the SFT training


**Enabled** boolean

Whether it is enabled



**SupportPresetDatasets** object[]

Configuration of the preset datasets mixing


**Name** string

Name of the mixed preset dataset



**Description** string

Description of the mixed preset dataset



**SampleCount** integer

Sample count



**Labelled** boolean

Whether the mixed preset dataset is labelled




**Parameters** object[]

Configuration of the customization task parameters


**Type** string `Example value: String`

Parameter type, Bool / Int / Float / String



**Name** string

Parameter name



**DisplayName** string

Display name of the parameter



**Min** string

Minimum value of the parameter



**Max** string

Maximum value of the parameter



**Options** string[]

Optional items of the parameter



**DefaultValue** string

Default value of the parameter



**Description** string

Description of the parameter



**IncrementalLearningLocked** boolean

Whether the parameter value can be modified in incremental learning





**LoraSettings** object

Configuration of the LoRA training


**Enabled** boolean

Whether it is enabled



**SupportPresetDatasets** object[]

Configuration of the preset datasets mixing


**Name** string

Name of the mixed preset dataset



**Description** string

Description of the mixed preset dataset



**SampleCount** integer

Sample count



**Labelled** boolean

Whether the mixed preset dataset is labelled




**Parameters** object[]

Configuration of the customization task parameters


**Type** string `Example value: String`

Parameter type, Bool / Int / Float / String



**Name** string

Parameter name



**DisplayName** string

Display name of the parameter



**Min** string

Minimum value of the parameter



**Max** string

Maximum value of the parameter



**Options** string[]

Optional items of the parameter



**DefaultValue** string

Default value of the parameter



**Description** string

Description of the parameter



**IncrementalLearningLocked** boolean

Whether the parameter value can be modified in incremental learning





**ContinuePretrainSettings** object

Configuration of the Continue Pretrain training


**Enabled** boolean

Whether it is enabled



**SupportPresetDatasets** object[]

Configuration of the preset datasets mixing


**Name** string

Name of the mixed preset dataset



**Description** string

Description of the mixed preset dataset



**SampleCount** integer

Sample count



**Labelled** boolean

Whether the mixed preset dataset is labelled




**Parameters** object[]

Configuration of the customization task parameters


**Type** string `Example value: String`

Parameter type, Bool / Int / Float / String



**Name** string

Parameter name



**DisplayName** string

Display name of the parameter



**Min** string

Minimum value of the parameter



**Max** string

Maximum value of the parameter



**Options** string[]

Optional items of the parameter



**DefaultValue** string

Default value of the parameter



**Description** string

Description of the parameter



**IncrementalLearningLocked** boolean

Whether the parameter value can be modified in incremental learning







**Status** string `Example value: Tested`

Foundation model version configuration status:


* Untested Not tested

* Testing Testing

* Tested Tested

* Failed Failed



**CreateTime** string `Example value: 2006-01-02T15:04:05Z07:00`

Creation time of the foundation model version configuration, in RFC3339 format



**UpdateTime** string `Example value: 2006-01-02T15:04:05Z07:00`

Update time of the foundation model version configuration, in RFC3339 format



&nbsp;

<span id=".6K-35rGC56S65L6L"></span>
## Sample requests

```text
POST /?Action=GetFoundationModelVersionConfiguration&Version=2024-01-01 HTTP/1.1
Host: ark.ap-southeast-1.byteplusapi.com
Content-Type: application/json; charset=UTF-8
X-Date: 20241108T062937Z
X-Content-Sha256: 287e874e******d653b44d21e
Authorization: HMAC-SHA256 Credential=Adfks******wekfwe/20241108/**-******/ark/request, SignedHeaders=host;x-content-sha256;x-date, Signature=47a7d934ff7b37c03938******cd7b8278a40a1057690c401e92246a0e41085f
{
  "Id": "fmvc-20240914******-*****",
  "FoundationModelName": "skylark-pro"
}
```


<span id=".6L-U5Zue56S65L6L"></span>
## Sample responses

```json
{
  "ResponseMetadata": {
    "RequestId": "20241108152417129217208184B83623",
    "Action": "GetFoundationModelVersionConfiguration",
    "Version": "2024-01-01",
    "Service": "ark",
    "Region": "ap-southeast"
  },
  "Result": {
    "Id": "fmvc-20240914******-*****",
    "FoundationModelName": "skylark-pro",
    "FoundationModelVersion": "240828",
    "Configuration": {
      "AppSettings": {
        "Greeting": "PD8tc",
        "Parameters": [
          {
            "Type": "String",
            "Name": "VmyVdsskE",
            "DisplayName": "HJBjfUC",
            "Min": "g5AQZcjfzS",
            "Max": "KVb4XpKRa",
            "Options": [
              "h"
            ],
            "DefaultValue": "7Bj6ypR1",
            "Description": "8s5"
          }
        ],
        "Prompts": [
          {
            "Scenario": "06hu6AVON92",
            "Message": "TnNySWLzk"
          }
        ],
        "MaxInputTokenLength": 472,
        "SystemPrompt": {
          "Message": "YIs",
          "ModificationAllowed": true,
          "MaxInputTokenLength": 525
        }
      },
      "CustomizationJobSettings": {
        "SftSettings": {
          "Enabled": false,
          "SupportPresetDatasets": [
            {
              "Name": "j4",
              "Description": "U",
              "SampleCount": 49,
              "Labelled": false
            }
          ],
          "Parameters": [
            {
              "Type": "String",
              "Name": "jYndBB",
              "DisplayName": "n",
              "Min": "266KPg1Twd",
              "Max": "ApLxU3",
              "Options": [
                "4"
              ],
              "DefaultValue": "7mzh7VQhze",
              "Description": "x1JwQHNDsKF",
              "IncrementalLearningLocked": false
            }
          ]
        },
        "LoraSettings": {
          "Enabled": true,
          "SupportPresetDatasets": [
            {
              "Name": "fT3GVuNzHW",
              "Description": "AhiLwIdfe6",
              "SampleCount": 521,
              "Labelled": true
            }
          ],
          "Parameters": [
            {
              "Type": "String",
              "Name": "Y2nq7r",
              "DisplayName": "wLUKKddH1NN",
              "Min": "0kstzmQoY",
              "Max": "0VOtfU0Ki",
              "Options": [
                "EfnCdO"
              ],
              "DefaultValue": "R7QWG",
              "Description": "THT",
              "IncrementalLearningLocked": true
            }
          ]
        },
        "ContinuePretrainSettings": {
          "Enabled": false,
          "SupportPresetDatasets": [
            {
              "Name": "vQQpQ5NLDDW",
              "Description": "TGezbHr",
              "SampleCount": 734,
              "Labelled": true
            }
          ],
          "Parameters": [
            {
              "Type": "String",
              "Name": "duxuPkFd",
              "DisplayName": "Gq1MbSizUk",
              "Min": "Bn0VW8ksTt",
              "Max": "DfqFVfmwG",
              "Options": [
                "MoqPrr"
              ],
              "DefaultValue": "6",
              "Description": "KIFatHpb5",
              "IncrementalLearningLocked": true
            }
          ]
        }
      }
    },
    "Status": "Tested",
    "CreateTime": "2006-01-02T15:04:05Z07:00",
    "UpdateTime": "2006-01-02T15:04:05Z07:00"
  }
}
```


<span id=".6ZSZ6K-v56CB"></span>
## Error codes

For a list of error codes, see [Common Error Codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).
