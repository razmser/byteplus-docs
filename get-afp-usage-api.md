# Get package AFP quota

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetAFPUsage&Version=2024-01-01`

Query AFP (Agent Frame Point) quota usage for the personal packages of `AgentPlan`, including quotas and used amounts for four rolling windows: 5 hours, the past day, the past week, and the past month. For team seat usage, use the *GetSeatAFPUsage* API instead.

> **Note**

> Only applies to personal packages of Agent Plan; for team seat usage, use GetSeatAFPUsage instead. The returned Quota / Used values are in AFP, and timestamps are epoch milliseconds.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Response parameters

**AFPDaily** `object`  |  1\-day usage

AFP usage in the rolling 1\-day window.

**Quota** `string`  |  Total window quota

`AFPDaily.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`AFPDaily.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`AFPDaily.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`AFPDaily.Used`

AFP quota consumed in this window.

**AFPFiveHour** `object`  |  5\-hour usage

AFP usage in the rolling 5\-hour window.

**Quota** `string`  |  Total window quota

`AFPFiveHour.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`AFPFiveHour.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`AFPFiveHour.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`AFPFiveHour.Used`

AFP quota consumed in this window.

**AFPMonthly** `object`  |  1\-month usage

AFP usage in the rolling 1\-month window.

**Quota** `string`  |  Total window quota

`AFPMonthly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`AFPMonthly.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`AFPMonthly.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`AFPMonthly.Used`

AFP quota consumed in this window.

**AFPWeekly** `object`  |  1\-week usage

AFP usage in the rolling 1\-week window.

**Quota** `string`  |  Total window quota

`AFPWeekly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`AFPWeekly.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`AFPWeekly.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`AFPWeekly.Used`

AFP quota consumed in this window.

**PlanType** `string`  |  Package tier

Package tier. Value:

* `Small`: small package;

* `Medium`: medium package;

* `Large`: large package;

* `Max`: maximum package.
