# Console - inference security audit

The Inference Security Audit page provides users with comprehensive security status monitoring and behavior auditing functions to ensure they are aware of potential risks during the inference process. The page displays data security status, the activation status of security protection measures, and audit logs.

Note:


* Only endpoints in a healthy state support security audits.

* The scope of models supporting security audits: Only applicable to BytePlus's Skylark series models (see [Model releases - 2025](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2172655)), not to models from other companies.


On the Inference Endpoint Details page, click the "Security Audit" tab to view the security audit page for the corresponding endpoint.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/840a1ae861c04f97a183a810ef40b7d9~tplv-goo7wpa0wc-image.image) </span>

<span id="81ec7df7"></span>
# Security Monitoring Dashboard

The Security Monitoring Dashboard allows you to view "Risk Monitoring" and "Security Protection Measures" for the current endpoint.


|Metric Name |Description |
|---|---|
|Sandbox Abnormal Connection |Counts the number of abnormal outward network connections from the sandbox in the last 180 days, indicating whether the sandbox has actively sent data externally. |
|Sandbox Abnormal Login |Counts the number of login attempts to the sandbox (remote or local) in the last 180 days, helping identify potential data theft attempts from within the sandbox. |
|Sandbox Container Escape |Counts the number of commands executed beyond the sandbox's permissions in the last 180 days, monitoring for unauthorized privilege escalations. |
|Intercepted Risks |Counts the number of illegal user commands intercepted in the last 180 days. |
|Application layer session encryption |Shows whether application layer session encryption is enabled in the last 7 days, and the specific percentage of inference traffic that is encrypted after it is enabled. |
|Network Layer Transport Encryption |Shows the status of network layer transport encryption. |


The "Risk monitoring in progress" dashboard at the top displays security risk information for the current endpoint, including the number of sandbox abnormal connections, sandbox abnormal logins, and sandbox container escapes, providing a visual overview of the security risk status. A value of 0 indicates that no such risks occurred within the last 180 days.

The "All\-round security protection" dashboard below displays the platform's security protection capabilities and their activation status.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/069f38079aec41f7a004b1eef28811d1~tplv-goo7wpa0wc-image.image) </span>

Hovering over the "i" icon next to a risk or security capability name on the dashboard provides a detailed explanation to help you better understand the corresponding risk or security capability.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/72461f3981b44918a4ec7cf7ed68231b~tplv-goo7wpa0wc-image.image) </span>

The data update time is displayed below the dashboard. If data cannot be retrieved, you can click the refresh button to retrieve the data again.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/6a6dc8c343ce47f09f895109a6a79583~tplv-goo7wpa0wc-image.image) </span>

By clicking the button next to the number of risks or "Intercepted Risks," you can view the corresponding audit log information on the right. Hovering over a log entry provides more detailed information.

To view more audit logs, click "View API Documentation" below to understand the interface call instructions for the audit log API.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/4e88d5a9473c47ddb1327116178695e1~tplv-goo7wpa0wc-image.image) </span>

<span id="f51e834b"></span>
# Inference Security Visualization

The Security Audit page supports two security visualizations: "Model Security" and "Session Data Security," which can be switched via the toggle in the top right corner of the page.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/b0f16a21c87c46fe84b8fc35080a380f~tplv-goo7wpa0wc-image.image) </span>

The "Model Security View" visually simulates the security technologies actually applied during the inference process to enhance user understanding. This includes network full\-link encryption, trusted security sandbox, and KMS encryption. You can also view the IPs that logged into the endpoint and their corresponding login counts (limited to the top three), as well as the time of the last KMS key request for the current inference endpoint. "No data" indicates no corresponding records within the last 180 days.

Clicking the "Details" button for endpoint logins allows you to view all IPs that logged into the current endpoint, their login counts, and the corresponding user accounts, helping you detect unauthorized login activity.

Clicking the "Details" button for KMS access allows you to view detailed KMS access logs, including the accessing username, key ring, key name, encryption/decryption object, and encryption/decryption action.

Key technologies involved in the visualization are as follows:


* **Network Full\-link Encryption**: Establishes an end\-to\-end encrypted communication channel between the user and the secure sandbox, preventing user data from being intercepted during transmission.

* **Trusted Security Sandbox**: A secure computing environment built with sandbox authentication mechanisms, computational environment isolation, and network isolation, ensuring isolation between inside and outside the sandbox to prevent user data theft.

* **KMS Encryption**: A FUSE\-based transparent encryption file system that ensures seamless encryption of data and models when written directly from secure sandbox memory to distributed storage. Keys are managed by the user through the Key Management System (KMS) and securely accessed within the secure sandbox.
