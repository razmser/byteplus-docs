# FAQs

## AI coding tool integration

### How to enable the deep\-reasoning mode in Claude Code?

1. Edit the Claude Code configuration file `~/.claude/settings.json`, and add `"CLAUDE_CODE_EXTRA_BODY": "{\"thinking\":{\"type\":\"enabled\"}}"` under `env`.

2. After the configuration is complete, run the `claude` command to restart.

> **tip**

> To explicitly turn off deep reasoning, set `"type"` to `"disabled"`. Omitting the `thinking` field entirely is equivalent to `disabled`.

### How to enable deep\-reasoning in OpenCode?

1. Edit `~/.config/opencode/opencode.json`, add `options` in `<Model_Name>`. See the example below:

   ```JSON
   {
   "$schema": "https://opencode.ai/config.json",
   "provider": {
       "myprovider": {
           "npm": "@ai-sdk/openai-compatible",
           "name": "byteplus",
           "options": {
               "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
               "apiKey": "<ARK_API_KEY>"
           },
           "models": {
               "<Model_Name>": {
                   "name": "<Model_Name>",
                   "options": {
                       "thinking": {
                           "type": "enabled"
                       }
                   }
               }
           }
       }
   }
   }
   ```

2. Run `opencode` to restart. If the `Thinking` content still doesn't show up in the chat, you can press **CTRL** + **P** and search for `think`, then select `Show thinking`.

   ![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/beb557544bfe466bbc41b242c8ecf7cb~tplv-goo7wpa0wc-image.image)

### How to enable the deep reasoning mode in OpenClaw?

OpenClaw supports the following methods to turn on deep reasoning mode:

> **Tip**

> OpenClaw supports settings for deep reasoning level: off, minimal, low, medium, high, xhigh. For details, see [Thinking Levels](https://docs.openclaw.ai/tools/thinking).

1. Temporarily turn on deep reasoning mode for the current message

Add an inline command before the message: `/think:<level>` or `/t <level>`, as shown below:

```Bash
/think:high <YOUR_QUESTION>
# Or use shorthand mode
/t high <YOUR_QUESTION>
```

2. Turn on deep reasoning mode at the session level

Send a single command message: `/think:<level>` or `/t <level>`, as shown below:

```Bash
/think:high
# Or use shorthand mode
/t high
```

3. Turn on deep reasoning mode in global configuration

Use the config command to modify the global configuration: `openclaw config set agents.defaults.thinkingDefault <level>`, as shown below:

```Bash
openclaw config set agents.defaults.thinkingDefault high
# Restart gateway for changes to take effect
openclaw gateway restart
# View whether the configuration is effective
openclaw config get agents.defaults.thinkingDefault
```

### Error: The developer role is not supported when using OpenClaw

**Issue**

No response after sending a message in OpenClaw chat, with the following error message:

```Text
HTTP 400: The parameter messages.role specified in the request are not valid:
invalid value: developer, supported values are: system, assistant, user, tool.
```

**Cause**

API compatibility issue: The ModelArk API does not support the **developer** role (OpenAI's new API).

**Solution**

1. Add the **compat** compatibility configuration `"compat": { "supportsDeveloperRole": false }` inside the **model** object, as shown below:

> **Tip**

> The **compat** configuration must be placed at the model level and not at the provider level; otherwise, an `Unrecognized key: "compat"` error will occur.

```JSON
{
  "models": {
    "providers": {
      "byteplus": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-completions",
        "models": [
          {
            "id": "<Model_Name>",
            "name": "<Model_Name>",
            "reasoning": true,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 200000,
            "maxTokens": 8192,
            "compat": {
              "supportsDeveloperRole": false
            }
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "byteplus/<Model_Name>"
      }
    }
  }
}
```

2. After modifying the configuration, execute the following command to restart the gateway.

   ```Bash
   pkill -f openclaw
   openclaw gateway restart
   ```

### How to handle image understanding failure in OpenClaw?

1. Confirm that the model supports image input.

Check whether the `input` field of the model in the configuration file `~/.openclaw/openclaw.json` contains the `image` input type. Using the `deepseek-v4.1-flash` model as an example, the configuration is as follows:

```JSON
{
  "models": {
    "providers": {
        "byteplus-plan": {
            "models": [
                  {
                    "id": "deepseek-v4.1-flash",
                    "name": "deepseek-v4.1-flash",
                    "input": ["text", "image"]
                  }
            ]
        }
    }
  }
}
```

2. Configure the image understanding model and check the model alias configuration.

Explicitly set the model used for image understanding in `agents.defaults.imageModel`, and ensure that `agents.defaults.models` contains the correct model aliases.

```JSON
{
  "agents": {
    "defaults": {
      "imageModel": {
        "primary": "byteplus-plan/deepseek-v4.1-flash"
      },
      "models": {
        "byteplus-plan/deepseek-v4.1-flash": {
          "alias": "byteplus"
        }
      }
    }
  }
}
```

3. Restart the Gateway to apply the configuration, then images can be understood by OpenClaw.

```Bash
openclaw gateway restart
```

### How to handle the error 'gateway connect failed: Error: pairing required' when installing OpenClaw?

Execute the following command to delete the device and identity, then reinstall the Gateway and restart to resolve the issue.

```Bash
rm -rf ~/.openclaw/devices
rm -rf ~/.openclaw/identity
openclaw gateway install --force
openclaw gateway start
```

### How to handle the error 'run error: 404 The model or endpoint xxx does not exist or you do not have access to it' when configuring OpenClaw?

1. Please check whether the `baseurl` and other information in the following two configuration files are consistent.

   * `~/.openclaw/openclaw.json`: Global configuration

   * `~/.openclaw/agents/main/agent/models.json`: Local model configuration for a single Agent. This configuration has a higher priority and will override the global configuration.

2. If the two configuration files are inconsistent, you need to delete `~/.openclaw/agents/main/agent/models.json` first.

3. Configure `~/.openclaw/openclaw.json` according to the OpenClaw Documentation. After completing the configuration, save the file and execute the command to restart the service.

   ```Bash
   openclaw gateway restart
   ```

## Feishu plugin

### How to upgrade the Feishu plugin?

1. Execute the `openclaw -v` command to view the installed version of OpenClaw. The new plugin requires the following OpenClaw versions. If the version is lower than required, the plugin may run abnormally. You can execute the `npm install -g openclaw` command to upgrade.

   * **Linux**/**macOS: 2026.2.26** or above

   * **Windows: 2026.3.2** or above

2. Execute the following command to upgrade the official Feishu plugin to the latest version.

   ```Bash
   npx -y @larksuite/openclaw-lark-tools update
   ```

   If an error occurs when executing this command, add `sudo` before the command and execute it again.

### What actions should be taken when encountering issues while using the Feishu plugin?

1. Input the following content in the Feishu bot dialog box to troubleshoot and resolve issues.

   1. `/feishu start`: Confirm whether the installation was successful.

   2. `/feishu doctor`: Check if the configuration is normal.

   3. `/feishu auth`: Complete user authorization in batch.

   4. The plugin contains built\-in solutions for FAQs. You can directly input your question in the dialog box to get answers.

2. Execute the following command in the terminal to repair.

   ```Bash
   # View issues and fix them independently
   npx @larksuite/openclaw-lark-tools doctor
   # Automatic repair
   npx @larksuite/openclaw-lark-tools doctor --fix
   ```

3. Execute the following command in the terminal to obtain information and input it in the Feishu group to get answers.

   ```Bash
   # Execute the following command to view version information and assist troubleshooting
   npx @larksuite/openclaw-lark-tools info
   # Execute the following command to view detailed configuration information
   npx @larksuite/openclaw-lark-tools info --all
   ```

### How to configure the Feishu bot to enable streaming output, time display, and status presentation capabilities?

1. Execute the following command to configure the Feishu bot.

   * Turn on streaming output

   ```Bash
   openclaw config set channels.feishu.streaming true
   ```

   * Turn off streaming output

   ```Bash
   openclaw config set channels.feishu.streaming false
   ```

   * Turn on time display

   ```Bash
   openclaw config set channels.feishu.footer.elapsed true
   ```

   * Turn on status presentation

   ```Bash
   openclaw config set channels.feishu.footer.status true
   ```

2. After completing the configuration, you need to execute the following command to restart the service.

   ```Bash
   openclaw gateway stop
   openclaw gateway run
   ```

### How to modify the reply mode of the Feishu bot in the group?

Currently, the default mode of the plugin in the group is: only replies when the bot is mentioned with @.

* Mode 1: Only replies when the bot is mentioned with @.

   Execute the following command in the terminal to configure the Feishu bot. After completing the configuration, restart the service.

   ```Bash
   # Settings require @ to reply
   openclaw config set channels.feishu.requireMention true --json
   ```

   The complete configuration example is as follows:

   ```JSON
   {"channels": {"feishu": {"enabled": true,"appId": "<Your_AppID>","appSecret": "<Your_AppSecret>","requireMention": true,"groupPolicy": "open"}}}
   ```

* Mode 2: Replies to all messages, even without @bot.

   > **Tip**

   > This mode can easily flood the screen in large groups. Please use with caution!

   1. On the [Feishu Open Platform](https://open.larkoffice.com/app?lang=en-US), click the created application.

   2. In the left menu, select "Development Configuration \> Permissions & Scopes", click the "Add permissions scopes to app" button, add the permission "Read all messages in associated group chat (sensitive scope)"

   3. Republish the application.

   4. Run the following command in the terminal to configure the Feishu bot. After completing the configuration, restart the service.

   ```Bash
   # Settings so that replies do not require @
   openclaw config set channels.feishu.requireMention false --json
   ```

   The complete configuration example is as follows:

   ```JSON
   {"channels": {"feishu": {"enabled": true,"appId": "<Your_AppID>","appSecret": "<Your_AppSecret>","requireMention": false,"groupPolicy": "open"}}}
   ```

* Mode 3: Only replies when the specified group @mentions the bot, suitable for different rules in different groups.

   1. Obtain the group ID.

      * Obtain the group ID on the Feishu group settings page.

      * Have the bot reply with the group ID in the Feishu group.

   2. Configure the message reply rules for the specified group. After completing the configuration, restart the service.

   ```Bash
   # 1. Set all groups by default to not require @
   openclaw config set channels.feishu.requireMention false --json
   # 2. Set specific groups to require @ (The group ID here is just an example and needs to be replaced with the actual group ID)
   openclaw config set channels.feishu.groups.oc_xxxxxxxx.requireMention true --json
   ```

## Ark Helper

### How to upgrade Ark Helper?

Run the following command again to upgrade to the latest version of Ark Helper.

```Bash
curl -fsSL https://lf3-static.bytednsdoc.com/obj/eden-cn/ylwslo-yrh/ljhwZthlaukjlkulzlp/install.sh | sh
```

### How to uninstall Ark Helper?

Run the following command to uninstall Ark Helper.

```Bash
npm uninstall -g @byted-aml/ark-helper
```

## Use ModelArk Coding Plan

### What are the usage quota limits for Coding Plan?

Coding Plan offers two subscription plans to meet the needs of different development scenarios.

Resource consumption varies significantly with request context length and model choice, so the actual number of usable requests can fluctuate accordingly. **The estimated plan usage in the table below is based on the ** **`dola-seed-2.0-lite`** ** model in Claude Code**.

|Plan |Application scenarios |Usage limits |
|---|---|---|
|Lite |Moderate\-intensity development tasks, suitable for most developers. |* Every 5 hours: up to approximately 1,200 requests.<br><br>* Per week: up to approximately 9,000 requests.<br><br>* Per subscription month: up to approximately 18,000 requests. |
|Pro |Complex project development, suitable for developers with high\-intensity workloads. |> Five times the usage of the Lite plan.<br><br><br>* Every 5 hours: up to approximately 6,000 requests.<br><br>* Per week: up to approximately 45,000 requests.<br><br>* Per subscription month: up to approximately 90,000 requests. |

> **Note**

> Actual usage may vary depending on the complexity of development tasks, the size of the codebase, and how the coding tools are used.

### How to view the usage of the Coding Plan?

You can view the usage statistics and remaining quota of the plan on the [Coding Plan management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?&advancedActiveKey=subscribe) in the console.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/416dbb0255ad4bffb86920f0a173f907~tplv-goo7wpa0wc-image.image)

### Will other packages or account balances continue to be consumed after the package quota is exhausted?

No. After the package quota is exhausted within the time period, you need to wait for the next cycle for the quota to be automatically restored. Other packages or account balances will not be consumed.

* 5\-hour limit: Based on the actual request time, a sliding 5\-hour window is dynamically refreshed.

* Weekly limit: Refreshed every Monday at 00:00:00.

* Monthly limit: Refreshed at 00:00:00 on the first day of each subscription month.

### After purchasing the Coding Plan, do I need to separately enable a model and create an inference endpoint in the ModelArk console?

No. The Coding Plan is activated upon purchase. Once you successfully subscribe to the package, the corresponding capabilities are enabled, and there is no need for any additional model activation, endpoint creation, or other complex configuration. For how to get started with your preferred programming tools (such as Claude Code, Cursor, and others), please refer to [Quick start guide](coding-plan-personal-get-started.md).

### Which models are supported?

Please view the supported models on the [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe&tab=LLM) page.

### Which programming tools does Coding Plan support?

Coding Plan supports a variety of mainstream programming tools, such as Claude Code, Cursor, Cline (VSCode), Codex, Kilo Code, Roo Code, OpenCode, and more.

### How do I connect the Coding Plan with programming tools?

You need to use the dedicated Coding Plan API Base URL to connect. For connection steps, refer to [Quick start guide](coding-plan-personal-get-started.md).

* Base URL configuration (for example, Claude Code): https://ark.ap\-southeast.bytepluses.com/api/coding

* Compatible with OpenAI API tool Base URL (for example, Cline, Cursor, Codex): https://ark.ap\-southeast.bytepluses.com/api/coding/v3

Caution: The Coding Plan quota is only effective in supported coding tools and cannot be used for API calls. API calls are charged according to the existing ModelArk billing rules.

### **Can I use my plan** **in** **multiple tools at the same time?**

Yes, you can use the same package across all supported tools, but the quota is shared. Usage in all tools will consume the same plan quota.

### Does the package support team usage?

Currently, the ModelArk Coding Plan is primarily intended for individual developers. For team collaboration, please use the ModelArk model API and connect via pay\-as\-you\-go.

### Is the Coding Plan only available for use within the coding tool?

Yes. Separate API calls are billed independently and cannot use the quota of the Coding Plan. Using the Base URL and API Key provided by the ModelArk Coding Plan in non\-AI Coding Products may be considered abuse or a violation, which could result in subscription suspension or account ban.

## Subscribe to ModelArk Coding Plan

### **How do I upgrade my plan?**

You can upgrade your plan by visiting [the ModelArk Coding Plan event](https://www.byteplus.com/activity/codingplan) or [subscription management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe) to perform upgrade actions. The upgrade takes effect immediately.

### Can I get a refund after subscribing to a plan?

Once the subscription service is purchased, it is considered confirmed and refunds are not supported. Even if you do not use up the plan, the fee cannot be refunded. We recommend that you select a suitable subscription plan and period based on your usage needs.

### How do I renew my plan?

It is strongly recommended to turn on Automatic renewal when subscribing to avoid subsequent impact on usage. If you did not turn on Automatic renewal when subscribing, you can go to [subscription management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe) and click the **renew** button for the plan to renew your plan.

### How do I cancel Automatic renewal?

You can cancel Automatic renewal in [subscription management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe). Please make sure to cancel at least 7 days before the next billing date to avoid being charged for automatic renewal. After canceling Automatic renewal, the current period remains valid, but it will not be renewed upon expiration.

## Exclusive offer for new users

### Can a user only enjoy the Coding Plan discount once?

Yes, a user can only enjoy the discounted price once; subsequent purchases and renewals cannot enjoy the discount.

### Are there any restrictions on enjoying the discount when subscribing to the Coding Plan?

An authenticated entity can only enjoy the plan discount once. If there are multiple accounts under the authenticated entity, only the account that makes the first purchase can enjoy the discount.

### Why was there no discount when I made my purchase?

If you did not receive the first purchase discount, please confirm whether the verified owner of the current account has any other account that has already purchased a Coding Plan package.

## Limited\-time referral campaign event

### How can a referral relationship be successfully established?

When your friend (referee) first clicks your exclusive invitation link or completes login or registration using your invitation code, the invitation relationship will be permanently associated. This relationship is determined by the system's first record.

### Can a user be both a "Referrer" and a "Referee"?

Once you successfully create your invitation code, you become a referrer and are no longer eligible to enjoy the first\-order discount as a referee.

However, a referee may become a new referrer and refer others after completing their own subscription.

### Can vouchers be used to pay bills for "Automatic renewal"?

Yes. As long as the voucher is within its validity period, the system will deduct the voucher when processing automatic renewal payments.

### How can I view the friends I have successfully referred and the vouchers I have received?

You can view the friends you have successfully referred and the vouchers you have received on the [My Invitations](https://www.byteplus.com/en/activity/referral-aicodingplan) page. You can also view your vouchers in the [Billing Center - Coupons](https://console.byteplus.com/finance/coupon) page.

### How can vouchers be used? What is the validity period?

To encourage developers to explore the full potential of AI, referral reward vouchers can be used not only to subscribe to the Coding Plan, but also to offset postpaid API usage fees for Seed model services, including the latest Seed\-1.8 and Seedance\-1.5\-pro models.

The vouchers are valid for 90 days from the date of issuance and will expire automatically after the validity period.

### Why is the amount of the voucher reward less than 10% of the order amount after I refer a friend to place an order?

This is because your reward is calculated based on your friend's "actual payment amount", not the original order price. "Actual payment amount" refers to the actual cash portion paid by your friend. For example, if you refer a friend to subscribe to the ModelArk Coding Plan and your friend subscribes to the Lite monthly plan, your friend makes a payment of $10 × 90% = $9, and you will receive a voucher with a face value of $9× 10% = $0.9.
