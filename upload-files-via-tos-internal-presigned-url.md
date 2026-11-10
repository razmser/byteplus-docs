# Upload files by using an internal TOS presigned URL

Store files in a private Torch Object Storage (TOS) bucket in the same region as the ModelArk service you access. Then, generate `GET` presigned URLs with the internal TOS endpoint and use those URLs to pass files to ModelArk over the private network.

A generated presigned URL looks like this:

```text
https://<YOUR_PRIVATE_BUCKET>.tos-ap-southeast-1.ibytepluses.com/path/to/object?...signature...
```


This method provides the following benefits:


* The source file is not directly accessible from the public network. Access is authorized only through the presigned URL, which improves data security.

* When the URL uses the internal endpoint supported by your environment, file transfer can avoid public network traffic, reducing network costs and improving transfer efficiency.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">The TOS bucket and the ModelArk service must be in the same region.</div>


<span id="generate-internal-tos-url"></span>
## Step 1: Generate an internal TOS presigned URL

For an object that has already been stored in TOS, generate a GET presigned URL that is used only for reading the object.

Configure the following environment variables:

```bash
# TOS access credentials (Access Key / Secret Key)
export TOS_ACCESS_KEY="YOUR_TOS_ACCESS_KEY"
export TOS_SECRET_KEY="YOUR_TOS_SECRET_KEY"

# ModelArk API Key
export ARK_API_KEY="YOUR_ARK_API_KEY"
```


The following example uses a video file:

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.getenv("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
# Replace with the presigned URL generated in step 1.
video_url = "https://your-private-bucket.tos-ap-southeast-1.volces.com/path/to/object.mp4?...signature..."
resp = client.chat.completions.create(
    # Replace with Model ID
    model="seed-2-0-lite-260228",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "Please analyze this video and output: 1. The main content of the video 2. Key people/objects 3. Key actions 4. Scene changes 5. A brief summary."
                },
                {
                    "type": "video_url",
                    "video_url": {
                        "url": video_url
                    }
                }
            ],
        }
    ],
)

print(resp.choices[0].message.content)
```


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Presigned URLs are valid only for a limited period. You can set the validity period by using the <code>expires</code> parameter. For details, see <a href="https://docs.byteplus.com/en/docs/tos/Normal-pre-signature-python-sdk">General presigned (Python SDK)</a>.</div>


Sample output:

```text
https://<YOUR_PRIVATE_BUCKET>.tos-ap-southeast-1.ibytepluses.com/path/to/object?...signature...
```


> The URL must use the internal TOS endpoint that matches the region where you access ModelArk.


<span id="call-modelark-presigned-url"></span>
## Step 2: Call ModelArk with the presigned URL

Pass the presigned URL generated in Step 1 as `video_url` to the model for video understanding.

Sample code:

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.getenv("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
# Replace this value with the presigned URL generated in Step 1
video_url = "https://<YOUR_PRIVATE_BUCKET>.tos-ap-southeast-1.ibytepluses.com/path/to/object.mp4?...signature..."
resp = client.chat.completions.create(
    # Replace with Model ID
    model="seed-2-0-lite-260228",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "Please understand this video and output: 1. Main content 2. Key people or objects 3. Key actions 4. Scene changes 5. A brief summary"
                },
                {
                    "type": "video_url",
                    "video_url": {
                        "url": video_url
                    }
                }
            ],
        }
    ],
)

print(resp.choices[0].message.content)
```



* Replace the Model ID as needed. For details about Model IDs, see [Model list](model-list.md).


<span id="faq"></span>
## FAQ

<span id="modelark-cannot-read-file"></span>
### ModelArk cannot read the input file

Check the following items first:


1. Whether the TOS bucket and ModelArk service are in the same region.

2. Whether the correct internal TOS endpoint is used for that region.

3. Whether the presigned URL has expired.

4. Whether the object exists, and whether the object key is exactly the same as the value used when the URL was signed.

5. Whether the URL was truncated, repeatedly encoded, or lost query parameters during transmission.

6. Whether the selected model supports the current input type.
