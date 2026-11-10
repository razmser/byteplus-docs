# RollbackEndpointRolling - Roll back inference endpoint rolling upgrade

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=RollbackEndpointRolling&Version=2024-01-01`

Rolls back a specified rolling update task and restores the inference endpoint to its state before the rolling update started.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Rolling update task ID

The ID of the rolling update task to roll back.

**Message** `string`  |  Rollback reason

The reason for the rollback.

## Response parameters

**Id** `string`  |  Rolling update task ID

The ID of the rolled\-back rolling update task.
