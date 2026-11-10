# Console - inference endpoint security monitoring overview

The Endpoint Security Monitoring page provides a global view of security status and risk overview, helping users to quickly identify and locate potential security risks for all endpoints within an account. The page includes security protection capabilities, security risk events, and endpoints with risks.

On the Monitoring page, click the "Endpoint Security Monitoring" tab in the middle of the page to view the security audit data for all endpoints under this account.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/249d7b7c0b784d4eb310b227be2cbddf~tplv-goo7wpa0wc-image.image) </span>

<span id="788db0d2"></span>
## **Security Protection Capabilities Dashboard**

The "Security Protection Capabilities Dashboard" at the top displays the platform's security protection capabilities and their activation status. Sandbox, Network Layer Transport Encryption, and Network Isolation are enabled by default.


|Metric Name |Description |
|---|---|
|Sandbox |Tracks the number of risks intercepted, ensuring protection against security threats during task execution. |
|Network Layer Transport Encryption |Shows the status of network layer transport encryption. |
|Network Isolation |Implements task\-level network isolation policies to separate different tasks in the same network, blocking direct communication and enhancing network security. |


Hovering over the "i" icon next to the security capability names on the dashboard provides detailed explanations to help you better understand the corresponding security capabilities.

<span id="72b8ee50"></span>
## **Security Risk Events Dashboard**

The Security Risk Events module is divided into the "Security Risk Event Overview" and the "Security Risk Event Trend Chart."


|Metric Name |Description |
|---|---|
|Application layer session encryption |Shows the number and details of access points that have not enabled application layer session encryption in the past 7 days. |
|Sandbox Abnormal Connection |Counts the number of abnormal network connections from the sandbox in the last 180 days, reflecting whether the sandbox has actively sent data externally. |
|Sandbox Abnormal Login |Counts the number of login attempts to the sandbox remotely or locally in the last 180 days, helping to identify potential attempts to steal data within the sandbox. |
|Sandbox Container Escape |Counts the number of commands executed beyond sandbox permissions in the last 180 days, monitoring for unauthorized privileged operations. |


The "Security Risk Event Overview" in the middle displays security risk information for all endpoints within the specified time period, including the number of sandbox abnormal connections, sandbox abnormal logins, and sandbox container escapes. This helps you visually understand the security risk status of all endpoints. A value of 0 indicates that no such risks occurred within the specified time period.

The "Security Risk Event Trend Chart" on the right displays the number and trends of security risks for all endpoints within the selected time range. This includes all security risks, such as sandbox abnormal connections, sandbox abnormal logins, and sandbox container escapes. It displays all trends by default. You can hide or show specific risk trends by clicking the corresponding trend buttons.

<span id="cd29b183"></span>
## **Endpoints with Risks Dashboard**

The "Endpoints with Risks Dashboard" at the bottom displays the total number of endpoints with risks and details of these endpoints.

The "Total Endpoints with Risks" on the left displays the total number of endpoints with risks within the specified time period. A value of 0 indicates that no risks occurred for any endpoints within the specified time period.

The "Risk Endpoint Details" on the right shows details of endpoints with risks within the specified time period, including the endpoint name and the number of risks encountered. This helps you quickly locate endpoints that have experienced risks within the specified time period.

Clicking "View Details" takes you to the security audit page for the corresponding endpoint to view detailed information about the risks encountered.
