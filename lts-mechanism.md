# LTS mechanism overview

ModelArk provides long\-term support (LTS) for specified model versions, and clearly defines the support period, maintenance scope, and retirement notification rules to help you plan production integration and migration.

## Scope

LTS applies to **specific model versions** that ModelArk explicitly announces as LTS. It does not automatically apply to other versions in the same model series. For businesses that require long\-term stable operation and fewer version migrations, we recommend that you prioritize LTS model versions as candidate versions and complete business validation before integration.

## LTS model list

|LTS model ID |Version date |Earliest retirement date |
|---|---|---|
|[seed-2-0-lite-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-lite) |2026\-04\-28 |2027\-04\-30 |
|[seed-2-0-mini-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-mini) |2026\-04\-28 |2027\-04\-30 |

The model IDs in the table identify the specific model versions covered by LTS guarantees. For calling methods and model capability information, see [Model list](model-list.md) and the corresponding model descriptions.

## Support period and coverage

LTS models provide **lifecycle coverage of at least one year**. After a model version is confirmed as LTS, ModelArk will publish the earliest retirement date for that version to help you plan business integration, evaluation, and migration.

|Coverage item |Note |
|---|---|
|Support period |Except for [exceptions](lts-mechanism.md#exceptions), a model version will not stop providing service before the published earliest retirement date. |
|Retirement notification |A notification will be issued at least three months before formal retirement, leaving time for business evaluation and migration. |
|Replacement model |A replacement model will be provided before retirement. In principle, it will be no worse than the original model in terms of performance, stability, API capabilities, and availability. Different models may perform differently in specific business scenarios. You still need to complete validation before migration. |
|Service maintenance |Model services are continuously maintained during the support period, and issues and defects that affect normal operation will be fixed or handled. |
|Model performance |The model performance will not be lower than the level when this version was first launched. LTS does not commit to continuously improving model performance and does not include new capabilities from later versions. |
|Existing quota |During the support period, calls within the activated quota can be used normally. |
|Additional quota |To add or increase quota, you must apply according to platform rules. Approval is subject to the actual review result. |

> **Tip**

* > The earliest retirement date only means that the model will not stop providing service before that date. It does not represent the actual retirement date. The actual retirement date is subject to the formal retirement notification, which will be issued at least three months before the actual retirement date.

* > The support period is calculated uniformly by model version, and is not recalculated based on when the model is activated or first called.

## Exceptions

In cases such as laws and regulations, regulatory requirements, or major security risks, ModelArk may adjust the support arrangements for related models in advance and issue notifications and migration plans.

## Model retirement and migration recommendations

The model retirement process proceeds in sequence: notification, end of marketing (EOM), and end of service (EOS). For the definitions of each phase, batch schedules, and handling rules for different integration methods, see [Model deprecations](model-deprecation-notice.md). For migration steps and API parameter adaptation instructions, see [Retiring model migration guide](model-deprecation-migration-guide.md).

## FAQs

### Will the original LTS version retire immediately after a new version in the same series is released?

No. The original LTS version continues to provide service according to the published support arrangement. The release of a new version does not change the support period of the original version.

### Will the model stop providing service immediately after the earliest retirement date is reached?

The actual retirement date may be later than the earliest retirement date. The specific date is subject to the retirement notification. Except for exceptions, the formal retirement date must meet both conditions: it must be no earlier than the published date, and a notification must be issued at least three months in advance.

### Does LTS mean that every call returns exactly the same result?

No. LTS provides version support periods and maintenance coverage, but does not guarantee that every generated result is exactly the same. Evaluate model performance based on your actual business needs.

### After using an LTS model, do I still need to pay attention to retirement notifications?

Yes. LTS provides a clear support period and migration window, but it does not mean permanent support. Related businesses should pay attention to formal retirement notifications and complete replacement model evaluation and migration before the specified time.
