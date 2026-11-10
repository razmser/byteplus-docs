# Model deprecations

*Last Updated: September 10, 2026*

> **Tip**

> Long\-term support (LTS) models provide lifecycle support for at least one year. For services that need to run stably over the long term and minimize version migrations, we recommend prioritizing LTS model versions as candidate versions and completing service validation before integration. For the list of LTS models and the support mechanism, see [LTS mechanism overview](lts-mechanism.md).

# Fourth batch of model deprecations (November 11, 2026)

## Deprecation timetable

|**First Notification Date** |**Deprecation Date** |**Deactivation Date** |**Impact Description** |
|---|---|---|---|
|September 8, 2026 |September 15, 2026 |November 11, 2026 |Model deprecation will proceed from the notification date; starting from the First Notification Date, quotas for deprecated models will be gradually reduced.<br><br>After the Deprecation Date, new inference endpoints or fine\-tuning tasks based on deprecated models cannot be created.<br><br>By the Deactivation Date, the model will be officially deactivated and cease service; switching to the recommended model is advised. |

## Deprecated model list

> **Note**

* > Capabilities, API parameters, pricing, and real\-world performance vary by model/version. Before migrating, please carefully review the model documentation and run necessary evaluations to compare performance before and after the switch.

* > Before migrating an embedding model, rebuild existing knowledge bases. Unlike other foundation models, embedding models have only an EOM phase and no EOS phase. At EOM, no new endpoints can be created for the deprecated embedding model, but existing services continue to operate normally.

* > If your business uses Bot API or Context API, migrate to Responses API as instructed in the documentation. Otherwise, calls will fail after the model is deactivated. Models that support Bot API or Context API are marked in the table. For instructions, see [Migrate to Responses API](responses-api-migration.md).

* > The seedance\-1\-5\-pro\-251215 model will be shut down after 17:00 on November 11, 2026 (UTC+8) and will not be automatically migrated to a new model. Switch models in advance to avoid service disruptions.

|**Model ID** |**Recommended Replacement** |**Automated Replacement** |
|---|---|---|
|`deepseek-v3-2-251201` (Bot API supported) |`deepseek-v4-flash-ga-260731` |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`gpt-oss-120b-250805` |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`kimi-k2-5-260127` |`dola-seed-2-1-turbo-260628` |`dola-seed-2-1-turbo-260628` |
|`glm-5-1-260408` |`dola-seed-2-1-turbo-260628` |`dola-seed-2-1-turbo-260628` |
|`glm-4-7-251222` |`dola-seed-2-1-turbo-260628` |`dola-seed-2-1-turbo-260628` |
|`seed-1-6-code-preview` |`dola-seed-2-1-turbo-260628` |`dola-seed-2-1-turbo-260628` |
|`seed-1-6-sc-preview` |`dola-seed-sc-260628` |`dola-seed-sc-260628` |
|`skylark-pro-sc-preview` |`dola-seed-sc-260628` |`dola-seed-sc-260628` |
|`seed-1-6-flash-250715` |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-1-6-flash-250615` |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-1-6-250615` |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-1-6-250915` |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-1-8-251228` |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-2-0-mini-260215` |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-mini-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seed-2-0-lite-260228` |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |`seed-2-0-lite-260428` [LTS](lts-mechanism.md#lts-model-list) |
|`seedance-1-5-pro-251215` |`dreamina-seedance-2-0-mini-260615` |Deactivated<br><br>> **tip**<br><br><br>> Switch to the corresponding replacement model in advance, and verify the impact of the new model on your business.<br> |
|`skylark-embedding-vision-250615` |`skylark-embedding-vision-251215` |Not deactivated |

> **Region\-specific deactivation**

> The following models are deactivated on September 10, 2026 for the eu\-west\-1 region. All endpoints calling these models are shutdown. Please use these models in ap\-southeast\-1.

* > `seed-2-0-mini-260215`

* > `seedream-5-0-lite-260128`

# **Background**

As ModelArk platform models undergo upgrades and releases, newer versions will significantly outperform older ones in performance, offer lower inference latency, and improve in other aspects. Therefore, we will gradually deactivate these older versions. When a model is confirmed to be deprecated, we will issue notifications via in\-site messages and other methods before its official deactivation. Please review these notifications promptly.

## Model deprecation and deactivation process

The complete model deprecation and deactivation process is as follows:

1. **Announce the deprecation of older model versions**, and specify the date when the model will be deactivated and cease service

2. Issue notifications via multiple methods such as in\-site messages and platform announcements

3. By the model's deactivation date, **the model will be officially deactivated and cease service**, and existing inference services can no longer be invoked.

Note: In general, each model version will be officially deprecated and deactivated a few months after launch. Before service termination, we will reserve sufficient time for you to complete performance tests of the new version and manually switch related endpoints to the newer version after testing.

## **Notification methods**

* We will issue notifications about model deprecation and deactivation through multiple channels such as **in\-site messages and platform announcements**.

* Notifications will be issued at least 30 calendar days before the model is officially deactivated.

* Please pay attention to relevant notifications about model deprecation.

## **Impact of Model Deprecation and Deactivation**

* **Model Deprecation**

   * Cannot create new inference endpoints: Deprecated models cannot be used to create new inference endpoints, but existing endpoints support switching to other versions; new fine\-tuning tasks based on deprecated models cannot be created.

* **Model Deactivation**

   * Fine\-tuned models: Services will shut down on the deactivation date and cannot be invoked.

   * Base models: If you do not manually switch to a newer version by the deactivation date, the system will either automatically switch the corresponding endpoints or terminate the service. For details, refer to the table below.

> **Tip**

* > If the Free Token Only mode is enabled, the endpoints in this mode will be shut down on the model's deactivation date.

* > If inference quotas are configured, the related endpoints will be shut down on the model's deactivation date.

## **Model switching instructions**

### **Switching between different versions of the same model or across models**

The platform supports **smooth switching of inference endpoints** for switching between **different versions of the same model, or across models and their versions**.

* Operation Guide

|**Procedure** |**Illustration** |
|---|---|
|1. Access the ModelArk Platform and select [Online Inference] |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/b73fd4541e9449aa8a695672ac19614d~tplv-goo7wpa0wc-image.image) |
|2. Select the inference endpoint to switch, and enter its details page |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/7d19271f1e7644f39419373848156da8~tplv-goo7wpa0wc-image.image) |
|3. Click [Switch Version], select the target model version on the [Select Model] page, then click [Confirm]. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/540642d09c1949d5bb74848672f6aa71~tplv-goo7wpa0wc-image.image) ![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/2a94108598ea4cebb4c51f0c01417198~tplv-goo7wpa0wc-image.image) |
|4. Check the model version on the **inference endpoint details page** to confirm the smooth switch.<br><br><br>**Note**:<br><br><br>* Switch time: The version migration process will not exceed 1 hour.<br><br>* Cancel switch: Click [Cancel Switch] to cancel the migration; traffic will gradually shift back to the original version.<br><br>* Both switch and cancel processes use proportional traffic switching and will not affect normal business. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/6d9dd2924a744826b8a94b51da3d462f~tplv-goo7wpa0wc-image.image) |

# Deprecated models

## Third batch of model deprecations (March 17, 2026)

### Deprecation timetable

|**First Notification Date** |**Deprecation Date** |**Deactivation Date** |**Impact Description** |
|---|---|---|---|
|March 17, 2026 |March 23, 2026 |May 13, 2026 |Model deprecation will proceed from the notification date; starting from the First Notification Date, quotas for deprecated models will be gradually reduced.<br><br>After the Deprecation Date, new inference endpoints or fine\-tuning tasks based on deprecated models cannot be created.<br><br>By the Deactivation Date, the model will be officially deactivated and cease service; switching to the recommended model is advised. |

### Deprecated model list

> **Note**

> Capabilities, API parameters, pricing, and real\-world performance vary by model/version. Before migrating, please carefully review the model documentation and run necessary evaluations to compare performance before and after the switch.

|**Model ID** |**Recommended Replacement** |**Automated Replacement** |
|---|---|---|
|deepseek\-v3\-1\-250821 |deepseek\-v3\-2\-251201 |seed\-2\-0\-lite\-260228 |
|deepseek\-r1\-250528 |deepseek\-v3\-2\-251201 |seed\-2\-0\-lite\-260228 |
|bytedance\-seedance\-1\-0\-lite\-i2v\-250428 |seedance\-1\-0\-pro\-fast\-251015 |dreamina\-seedance\-2\-0\-fast\-260128 |
|bytedance\-seedance\-1\-0\-lite\-t2v\-250428 |seedance\-1\-0\-pro\-fast\-251015 |dreamina\-seedance\-2\-0\-fast\-260128 |
|bytedance\-seedream\-3\-0\-t2i\-250415 |seedream\-5\-0\-lite\-260128 |seedream\-5\-0\-lite\-260128 |
|kimi\-k2\-250905 |seed\-2\-0\-lite\-260228 |seed\-2\-0\-lite\-260228 |
|kimi\-k2\-thinking\-251104 |seed\-2\-0\-lite\-260228 |seed\-2\-0\-lite\-260228 |
|doubao\-1\-5\-pro\-32k\-250115 |seed\-2\-0\-mini\-260215 |seed\-2\-0\-mini\-260215 |
|skylark\-pro\-sc\-250615 |bytedance\-seed\-sc\-260215 |bytedance\-seed\-sc\-260215 |

## **Second batch of model deprecations (November 17, 2025)**

### **Deprecation timetable**

|**First Notification Date** |**Deprecation Date** |**Deactivation Date** |**Impact Description** |
|---|---|---|---|
|November 17, 2025 |November 24, 2025 |January 22, 2026 |Model deprecation will proceed from the notification date.<br><br>After the Deprecation Date, new inference endpoints or fine\-tuning tasks based on deprecated models cannot be created.<br><br>By the Deactivation Date, the model will be officially deactivated and cease service; switching to the recommended model is advised.<br><br>**Note**: Allowed throughput rate (TPM/RPM/IPM) of all the preset inference endpoints will be set to **0** on the **Deactivation Date**. |

### Deprecated model list

> **Note**

> Capabilities, API parameters, pricing, and real\-world performance vary by model/version. Before migrating, please carefully review the model documentation and run necessary evaluations to compare performance before and after the switch.

|**Model ID** |**Recommended Replacement** |**Automated Replacement** |
|---|---|---|
|deepseek\-r1\-250120 |deepseek\-v3\-1\-250821 |deepseek\-v3\-2\-251201 |
|deepseek\-v3\-250324 |deepseek\-v3\-1\-250821 |deepseek\-v3\-2\-251201 |
|kimi\-k2\-250711 |kimi\-k2\-250905 |kimi\-k2\-250905 |
|skylark\-embedding\-vision\-250328 |skylark\-embedding\-vision\-250615 |Not deactivated |
|skylark\-pro\-sc\-250315 |skylark\-pro\-sc\-250615 |skylark\-pro\-sc\-250615 |
|skylark\-pro\-250415 |seed\-1\-6\-250915 |seed\-1\-8\-251228 |
|skylark\-vision\-250515 |seed\-1\-6\-250915 |seed\-1\-8\-251228 |
|seededit\-3\-0\-i2i\-250628 |seedream\-4\-0\-250828 |seedream\-4\-5\-251128 |
