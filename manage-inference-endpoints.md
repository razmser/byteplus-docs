# Manage inference endpoints

This article walks you through how to view and manage online inference endpoints.

For details on the differences between preset inference endpoints and customized inference endpoints, see [About inference endpoints](deployment-overview.md).

# Manage endpoints

1. [Log in to the console](https://ai.byteplus.com/ark/region:ap-southeast-1).

2. Switch the project space in the lower\-left corner of the console.

3. Click **Online inference** in the left navigation bar to enter the list view.

4. On the online inference list, you can **edit** / **delete** / **disable** inference endpoints and view endpoint status.

Status of the online inference endpoint:

|Status |Description |
|---|---|
|Scheduling |Backend resources are being scheduled, which may be in a queue or may already be starting. |
|Healthy |The endpoint status is normal and can be called as expected.<br><br>> The Healthy status doesn't indicate the current load of the endpoint. If the rate limit is exceeded, the status may still be healthy, but the service will return an error. |
|Stopped |When the user triggers the action to stop the endpoint or the model is taken offline, the system will shut down the corresponding endpoint. |
|Abnormal |The accessed model is abnormal. You can submit a ticket, and the backend will process it for you. |

# View endpoint details and switch versions

Click the endpoint name to open the endpoint details page, where you can view information such as the API call tutorial, monitoring metrics, and security audit details.

Endpoints of some models support version switching.

## Overview

In the overview information, you can view the model information of the inference endpoint.

* Basic information: connected model, rate limits, creator, and other information.

* Billing information: billing type and input/output price.

## Version switching

Some models support version switching. You can replace the version in use with another one without creating a new endpoint.

> **Note**

* > Models may differ in pricing, rate limits, inference\-cache behavior, and API parameters. We recommend that you fully review and test before switching model versions.

* > During a version switch, the cache is temporarily unavailable. When you send requests via the Responses API, cache hits will not occur, but the requests will still be stored and will incur cache storage charges. After the switch completes, historical caches from previous turns can be hit normally.

* > Built\-in tool support may also differ between model versions. If the target model does not support the required tool calls, the request will fail. For compatibility details, see [Tool use](model-list.md#f44ceef7).

Version switching takes about 1 hour. Rollback is supported during the process \- you can gradually switch back as needed or roll back in one step.

## Monitoring

Using BytePlus' [Vital Managed Service for Prometheus](https://docs.byteplus.com/en/docs/vmp/what-is-vital-managed-service-for-prometheus) (VMP), real\-time monitoring functionality is provided for endpoints.

### Turn on monitoring

Users with `ArkStandardGlobalAccess` or `ArkFullAccess` permissions can click the One\-click enable monitoring button. The system will automatically enable the monitoring service and complete the relevant configuration.

Monitoring metrics collected by ModelArk are not charged to customers, and data is retained for approximately 15 days. For other VMP\-related charges, please refer to the relevant VMP documents.

### Monitor the endpoints

After turning on monitoring, you can view the monitoring metrics of the current inference endpoint on the monitoring page to understand model invocation status.

* Supports filtering by API interface, model name, and model version for the current endpoint.

* Supports filtering by query time range.

* Supports viewing monitoring metrics for token usage, performance, anomaly statistics, inference cache, etc.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/81d7a22b4b3f4831bc3ffeeab0c1edb0~tplv-goo7wpa0wc-image.image)

### Configure metric alerts

#### Prerequisites

If alert rules need to be configured for a subaccount, the VMPFullAccess permission of the account is required. If you only have VMPReadOnlyAccess, the following API permissions need to be added:

* VMP: CreateAlertingRuleGroup, which creates alert rule groups via an alert template.

* VMP: UpdateAlertingRuleGroup, which updates alert rule groups.

* VMP: UpgradeAlertingRuleGroups, which upgrades alert rule groups based on the latest alert template.

* VMP: DeleteAlertingRuleGroup, which deletes alert rule groups.

#### Configure monitoring metric alerts

You can configure monitoring metric alerts with one click on the monitoring page of the inference endpoint in the ModelArk console.

It is recommended to use the default configuration with pre\-filled parameters for the current endpoint.

To configure reasonably according to business needs, such as avoiding repeated alerts for the same endpoint and alert flooding, you can go to the managed Prometheus platform to view alert rules.

## Security audit

For details, see [Console - Inference Security Audit](console-inference-security-audit.md).
