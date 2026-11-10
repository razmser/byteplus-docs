# Compatible with OpenAI API

ModelArk model APIs are largely compatible with the OpenAI SDK. With minimal code changes, you can integrate ModelArk model services into your existing code.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Community third\-party SDKs are not maintained by the BytePlus team; this document is for reference only.</div>


<span id="509924d1"></span>
# Prerequisites

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If you're new to ModelArk, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399008">Quick start</a> to get up and running quickly.</div>


<span id="fe0e81ca"></span>
# OpenAI SDK


* Python version: 3.7 or later.

* OpenAI SDK: version 1.0 or later. Install with the following command:

   ```bash
   pip install --upgrade "openai>=1.0"
   ```
   


<span id="0569f4fe"></span>
## Quickstart example

```Python
from openai import OpenAI
import os

client = OpenAI(
    #The base URL for model invocation.
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Configure your API key in environment variables
    api_key=os.environ.get("ARK_API_KEY"),
)

completion = client.chat.completions.create(
    #Replace with Model ID.
    model="seed-2-0-lite-260228",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
)
print(completion.choices[0].message.content)
```


<span id="922db236"></span>
## Extra field settings

Use the **extra_body** dictionary to pass fields not supported by the OpenAI SDK, such as the **thinking** field to toggle deep reasoning.

```Python
from openai import OpenAI
import os

client = OpenAI(
    #The base URL for model invocation.
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Configure your API key in environment variables
    api_key=os.environ.get("ARK_API_KEY"),
)

completion = client.chat.completions.create(
    #Replace with Model ID.
    model="seed-2-0-lite-260228",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    extra_body={
         "thinking": {
             "type": "disabled", # Do not use deep thinking capability
             # "type": "enabled", # enable deep reasoning
         }
     }
)
print(completion.choices[0].message.content)
```


<span id="1cd60a34"></span>
## Custom header settings

Use it to carry additional information, such as a configuration ID for log correlation, and to enable data encryption.

```Python
from openai import OpenAI
import os

client = OpenAI(
    #The base URL for model invocation.
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Configure your API Key in environment variables
    api_key=os.environ.get("ARK_API_KEY"),
)

completion = client.chat.completions.create(
    #Replace with Model ID.
    model="seed-2-0-lite-260228",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    # Custom request ID
    extra_headers={"X-Client-Request-Id": "202406251728190000B7EA7A9648AC08D9"}
)
print(completion.choices[0].message.content)
```


<span id="697a06ce"></span>
# LangChain OpenAI SDK

Install the LangChain OpenAI SDK:

```bash
pip install langchain-openai
```


<span id="2bcdd714"></span>
## Sample code

```Python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate
import os

llm = ChatOpenAI(
    # Configure your API key in environment variables
    openai_api_key=os.environ.get("ARK_API_KEY"),
    #The base URL for model invocation
    openai_api_base="https://ark.ap-southeast.bytepluses.com/api/v3",
    #Replace with Model ID
    model="seed-2-0-lite-260228",
)

template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)

question = "What NFL team won the Super Bowl in the year Justin Bieber was born?"

llm_chain = prompt | llm

print(llm_chain.invoke(question))
```
