# OpenCode

OpenCode is an open\-source AI programming agent tool that provides multiple usage options, including terminal interface, desktop application, and IDE extension.

**Configure model name (via config file)**

# Key configuration

The key configurations for integrating tools are as follows:

## **Model selection**Specify the name of the model to be used in the configuration file of the tool.

The following models are supported:

* \`dola-seed-2.0-pro\`

* \`dola-seed-2.0-lite\`

* \`dola-seed-2.0-code\`

* \`glm-5.3-flash\`

* \`glm-5.2\`

* \`deepseek-v4.1-flash\`

* \`deepseek-v4-flash\`

* \`deepseek-v4-pro\`

> **Tip**

* > Due to regional regulatory requirements and other applicable considerations, the availability and features of the Model Service may vary by country or region. The final offering shall be subject to BytePlus’s actual provision.

* > The DeepSeek models are provided in compliance with applicable local laws and regulations.

**Configure ark-code-latest (in the console)**

1. Specify the model as \`ark-code-latest\` in the configuration file.

2. Select or switch the target model via the [management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe).

Auto mode is supported, and the model is automatically selected using an intelligent algorithm based on both "effect" and "speed" dimensions.

## **Base URL**

The Base URL differs by tool depending on the protocol it is compatible with:

* Tools compatible with the Anthropic protocol: https://ark.ap\-southeast.bytepluses.com/api/coding

* Tools compatible with the OpenAI protocol: https://ark.ap\-southeast.bytepluses.com/api/coding/v3

> **Note**

> Do not use the Base model URL (https://ark.ap\-southeast.bytepluses.com/api/v3). Requests sent to this Base URL do not consume your Coding Plan quota and will instead incur additional charges.

> **Tip**

> You can create an API key in the [console](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D).

# Install

In the command line interface, execute the following command to install OpenCode.

```Bash
npm install -g opencode-ai
```

After installation is complete, execute the following command to check the installation result. If the version number is displayed, the installation was successful.

```Bash
opencode --version
```

# Configure

## Method 1: Automated configuration (recommended)

ArkCLI Helper is a coding tool assistant that helps quickly configure the selected tool to connect to Coding Plan. Install and run it, then follow the on\-screen prompts to complete the tool configuration automatically, reducing manual setup time and the risk of errors.

> **Note**

* > ArkCLI Helper is an upgraded tool configuration service evolved from Ark Helper. Related capabilities will continue to iterate in ArkCLI Helper. We recommend using ArkCLI Helper for tool configuration.

* > ArkCLI Helper supports macOS, Linux, and Windows.

1. Install Ark CLI.

   ```Bash
   # 1. Install Ark CLI through npm
   npm install -g @byteplus/ark-cli
   
   # 2. Verify the installation
   arkcli --version
   ```

2. Sign in to Ark CLI.

   ```Bash
   arkcli auth login
   ```

   Follow the on\-screen prompts to select a sign\-in method. On first sign\-in, complete the following configuration as prompted:

   1. Select a project: Choose one that fits your needs, or use the default.

   2. Select `coding-plan`.

3. Run ArkCLI Helper and follow the on\-screen prompts to complete the OpenCode configuration.

   1. Enter the `arkcli helper` command in the terminal to start ArkCLI Helper.

   2. Select the Plan profile to configure: Choose `coding-plan_ap-southeast-1_personal (Coding Plan)`.

   3. Select the model to use.

   4. Select the AI agent to configure: `OpenCode`.

After you complete these steps, OpenCode is configured automatically. No manual installation is required.

## Method 2: Configure manually

1. Create and open OpenCode's configuration file. The file path varies by system.

   **macOS/Linux**

Configuration file path on macOS/Linux: `~/.config/opencode/opencode.json`.

      1. If the `.config/opencode` directory does not exist under your home directory, run the following command to create it.

         ```Bash
         mkdir -p ~/.config/opencode
         ```

      2. Create and open the configuration file.

         ```Bash
         nano ~/.config/opencode/opencode.json
         ```

   **Windows**

Configuration file path on Windows: `%USERPROFILE%\.config\opencode\opencode.json`. Using CMD as an example:

      1. If the `.config\opencode` directory does not exist under your user profile, run the following command to create it.

         ```Bash
         if not exist "%USERPROFILE%\.config\opencode" mkdir "%USERPROFILE%\.config\opencode"
         ```

      2. Create and open the configuration file.

         ```Bash
         notepad "%USERPROFILE%\.config\opencode\opencode.json"
         ```

2. Edit `opencode.json`. Key configuration:

   * `<ARK_API_KEY>`: Replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   * `model`: Update to the target model. See [Key configuration](coding-plan-personal-ai-opencode.md#8f1d9f20) for supported models.

   Coding Plan is compatible with the OpenAI interface protocol and supports both Responses API and Chat API. The Responses API configuration is recommended for better reasoning quality.

   The two API types differ only in the npm package used:

   * Responses API configuration: `"npm": "@ai-sdk/openai"`

   * Chat API configuration: `"npm": "@ai-sdk/openai-compatible"`

   Using `ark-code-latest` as an example:

> **warning**

* > `glm-5.2`, `glm-5.3-flash`, `deepseek-v4.1-flash`, `deepseek-v4-flash`, and `deepseek-v4-pro` support a 1M context window for long sessions involving large codebases. Specify the window size explicitly via the `limit.context` field.

* > Under `provider.byteplus-plan.models`, **two locations** (the object key and the `name` field) must be **replaced with the same Model Name**. Do not miss either one.

* > To enable image understanding for a model, add the following configuration under the model node:

> `"modalities": {"input": ["text", "image"], "output": ["text"]}`

**Responses API**

> **tip**

> The npm value must be `@ai-sdk/openai` so that the model uses the Responses API, which appends `/responses` to `baseURL` automatically.

```JSON
{
  "$schema": "https://opencode.ai/config.json",
  "model": "byteplus-plan/ark-code-latest",
  "provider": {
    "byteplus-plan": {
      "npm": "@ai-sdk/openai",
      "name": "BytePlus (Responses API)",
      "options": {
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKey": "<ARK_API_KEY>"
      },
      "models": {
        "ark-code-latest": {
          "name": "ark-code-latest",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "glm-5.2": {
          "name": "glm-5.2",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text"
            ],
            "output": [
              "text"
            ]
          }
        },
        "glm-5.3-flash": {
          "name": "glm-5.3-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "deepseek-v4-flash": {
          "name": "deepseek-v4-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          }
        },
        "deepseek-v4-pro": {
          "name": "deepseek-v4-pro",
          "limit": {
            "context": 1024000,
            "output": 65536
          }
        },
        "deepseek-v4.1-flash": {
          "name": "deepseek-v4.1-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-pro": {
          "name": "dola-seed-2.0-pro",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-lite": {
          "name": "dola-seed-2.0-lite",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-code": {
          "name": "dola-seed-2.0-code",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        }
      }
    }
  }
}
```

**Chat API**

> **tip**

> The npm value must be `@ai-sdk/openai-compatible` so that the model uses the Chat API, which appends `/chat/completions` to `baseURL` automatically.

```JSON
{
  "$schema": "https://opencode.ai/config.json",
  "model": "byteplus-plan/ark-code-latest",
  "provider": {
    "byteplus-plan": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "BytePlus (Chat API)",
      "options": {
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKey": "<ARK_API_KEY>"
      },
      "models": {
        "ark-code-latest": {
          "name": "ark-code-latest",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "glm-5.2": {
          "name": "glm-5.2",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text"
            ],
            "output": [
              "text"
            ]
          }
        },
        "glm-5.3-flash": {
          "name": "glm-5.3-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "deepseek-v4-flash": {
          "name": "deepseek-v4-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          }
        },
        "deepseek-v4-pro": {
          "name": "deepseek-v4-pro",
          "limit": {
            "context": 1024000,
            "output": 65536
          }
        },
        "deepseek-v4.1-flash": {
          "name": "deepseek-v4.1-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-pro": {
          "name": "dola-seed-2.0-pro",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-lite": {
          "name": "dola-seed-2.0-lite",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        },
        "dola-seed-2.0-code": {
          "name": "dola-seed-2.0-code",
          "limit": {
            "context": 256000,
            "output": 32000
          },
          "modalities": {
            "input": [
              "text",
              "image"
            ],
            "output": [
              "text"
            ]
          }
        }
      }
    }
  }
}
```

# Get started

1. Start OpenCode:

   ```Bash
   opencode
   ```

2. Enter `/models`, select the configured `<Model_Name>`, and use it in OpenCode.

# FAQs

## How do I enable Deep Thinking mode in OpenCode?

1. Edit the OpenCode configuration file: `~/.config/opencode/opencode.json`. In the `ark-code-latest` model configuration, add the following `options` field: `{ "thinking": { "type": "enabled" } }`.

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

2. After saving the configuration, restart OpenCode by running `opencode`. If you still do not see **Thinking** information during a conversation:

   1. Press **Ctrl + P** in the input box.

   2. Search for **think**.

   3. Select **Expand thinking** to view the model's reasoning output.

   ![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/beb557544bfe466bbc41b242c8ecf7cb~tplv-goo7wpa0wc-image.image)

# Error code

When using the Coding Plan package in programming tools, you may encounter errors. You can view the corresponding error scenarios based on the error information and locate the issue. For details, see [Error codes](error-codes.md).
