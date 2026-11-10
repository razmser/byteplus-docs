# FAQs

<span id="aa45e6c0"></span>
## Online inference

<span id="c0402d7a"></span>
### How to handle ArkAPIConnectionError (domain connection timeout)?

Check if the ModelArk public network domain is accessible from your local environment:

```Bash
ping ark.ap-southeast.bytepluses.com
```


If the domain is not accessible, consider disabling `HTTP_PROXY` using the following method:

```Python
# python sdk
import httpx

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    http_client=httpx.Client(trust_env=False),
)
```


If the ping command is successful, but the following error still occurs during the call:

```text
Failed to resolve 'ark.ap-southeast.bytepluses.com' ([Errno -3] Temporary failure in name resolution)
```


Try modifying the `/etc/hosts` file as follows and request again.

```Bash
# /etc/hosts file
${ip} ark.ap-southeast.bytepluses.com  # ${ip} is obtained from the output of ping
```


Where `${ip}` is obtained from the ping output.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f4a02223ecdd4d1fbc5dda7d84e0b0dd~tplv-goo7wpa0wc-image.image) </span>

If the above method does not work, try setting the environment variable.

```Bash
export no_proxy=
```


<span id="b74e8ad6"></span>
### Windows SDK installation failure

Due to the maximum path length limitation in Windows, installation may fail. Please follow the steps below:


1. Press `Win + R` and type regedit to open the Registry Editor.

2. Set the variable `LongPathsEnabled` to `1` under the path `\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem`.


<span id="8544b685"></span>
### Does the `messages.role` property in the `ChatCompletions API` require "user" in odd positions and "assistant" in even positions?

No, there is no such requirement.

Example:

Request

```Python
...
messages = [
        {"role": "system", "content": "You are Skylark, an AI assistant developed by BytePlus."},
        # Repeat 2 users
        {"role": "user", "content": "What are the common cruciferous plants?"},
        {"role": "user", "content": "The following are some common cruciferous plants: cabbage, kale"},
        {"role": "assistant", "content": "radish, cauliflower, mustard"},
        {"role": "user", "content": "Anything else?"},
    ],
...
```


The LLM responds normally:

```text
There are also rapeseed, shepherd's purse, apetalum, and zhugecai (February orchid).

1. **Rapeseed**
    - Rapeseed is an important oil crop. Its stem is upright, branched, and its leaves are alternate. The flowers are yellow and arranged in a cross shape. Rapeseed can be used to extract oil, and rapeseed oil is widely used in daily life.
2. **Shepherd's purse**
    - Shepherd's purse is a common wild vegetable. The basal leaves are clustered in a rosette shape, and the leaves are pinnate. It has strong adaptability and often grows in fields, roadsides, etc. Shepherd's purse can be eaten and contains rich nutrients such as protein and vitamins. It can be used to make dumplings and cook soup.
3. **Apetalum**
    - It is an annual or biennial herb. The stem is upright or obliquely ascending and has many branches. The leaves are narrowly spoon-shaped or obovate. The seeds of apetalum can be used as medicine, which has the effects of relieving cough and reducing phlegm.
4. **Zhugecai (February Orchid)**
    - It is named after the blue-purple flowers that begin to bloom around the second month of the lunar calendar. It has strong adaptability to the environment, is cold-resistant and drought-resistant, and grows mostly in plains, mountains, roadsides, etc. The flowers are small and numerous, and have high ornamental value during the blooming period
```


<span id="c850ca01"></span>
### Does the `messages` property in the `ChatCompletions` API support only passing a message with `role` being `system`?

Yes, this is supported.

Example:

Request

```Python
...
messages = [
        {"role": "system", "content": "You are Skylark, an AI assistant developed by BytePlus. What are some common cruciferous plants?"},
        ...
]
```


The LLM responds normally:

```text
The following are some common cruciferous plants:

### 1. Vegetables
1. **Cabbage**
- Including Chinese cabbage, Chinese cabbage, etc. Chinese cabbage has broad leaves and is a common vegetable in northern my country in winter. There are different varieties such as green and white. It is rich in nutrients and rich in vitamin C, cellulose, etc. The Chinese cabbage plant is relatively small, with tender green leaves, a short growth cycle, and a fresh taste.
2. **Cabbage**
- **Cabbage (cabbage)**: The leaves are wrapped in layers into a ball shape, and there are green cabbage and purple cabbage. It has strong adaptability, is resistant to storage and transportation, and can be used to make salads, stir-fry, pickles, etc.
- **Cauliflower (cauliflower)**: The flower ball is formed by the top of the fat and tender flower branches, and the color is white or light yellow. Broccoli is a variant of cauliflower, which is high in nutrients, especially vitamin C, carotene and folic acid.
3. **Radish**
- There are many types such as white radish, red radish (carrots do not belong to the cruciferous family, but red radish does), green radish, etc. The fleshy root of radish is edible. It can be eaten raw, such as cold shredded radish, or cooked, such as stewed radish with pork ribs. Radish contains amylase, mustard oil and other substances, which have the effects of aiding digestion and increasing appetite.
4. **Mustard**
- **Leaf mustard**: such as Chinese mustard, the leaves are deeply cracked, the edges are wrinkled, and it has a special spicy flavor. It is an important raw material for making pickles. Pickled Chinese mustard can be used for stir-fried minced meat and other dishes.
- **Stem mustard (pickled mustard)**: The stem is enlarged, forming a tumor-like protrusion. The fleshy stem is the main edible part. It is often used to make pickled mustard, which is a common side dish.

### 2. Ornamental plants
1. **Violet**
- Violet flowers are lush and colorful, including purple, white, pink, etc. It has a light fragrance and a long flowering period. It is often used to decorate flower beds and flower borders, and can also be planted in pots for viewing.
2. **Zhugecai (February Orchid)**
-February Orchid is a common wild flower with lavender or white flowers. It has strong adaptability and can reproduce by self-seeding. It often grows on hillsides, grasslands, roadsides, etc. In gardens, it can be used to create wild landscapes, or it can be planted in large areas to form a sea of flowers.
```


<span id="b556b65f"></span>
### How do I check if V3 APIs are used?


* If you use an HTTP API, the call path contains `/api/v3`.

* If you use the BytePlus SDK, check the `import` path:

   * The word `ark` indicates `V3` APIs.

   * The word `maas` indicates `V1`/`V2` APIs.


<span id="effccb14"></span>
### **InvalidParameter error when using the visual understanding model?** 


* **Image download timeout**: To prevent unnecessary bandwidth waste, the default image download timeout is 5s. This may be because the image access speed is slow, such as on foreign websites, or because the image is large. In these cases, we recommend moving the image to TOS or compressing it to below 100 kB.

* **Link access denied**: The status code is `403`. Some image servers may have special security/ACL policies that block access from BytePlus sources. We recommend checking the security policies of the image server used by the customer, such as Alibaba Cloud Object Storage, Tencent Cloud Object Storage, and so on.

* **Unsupported image format, or the image format does not match the metadata**:

   * The following image formats are automatically verified based on the first 512 bytes of the image content:

      
      |Image format |Content\-Type |
      |---|---|
      |jpg/jpeg |image/jpeg |
      |png |image/png |
      |gif |image/gif |
      |webp |image/webp |
      |bmp |image/bmp |
      |dib |image/bmp |
      |ico |image/x\-icon |
      

   * The following image formats are verified based on the metadata (Content\-Type) of the image URL, and `Content-Type` must be set correctly.

      * To set file metadata for TOS (BytePlus object storage), see: https://www.byteplus.com/docs/6349/145523.

      * To set file metadata for OSS (Alibaba Cloud Object Storage), see: https://www.alibabacloud.com/help/en/oss/user\-guide/configure\-the\-content\-type\-header.

      
      |Image format |Content\-Type |
      |---|---|
      |TIFF |`image/tiff` |
      |SGI |`image/sgi` |
      |ICNS |`image/icns` |
      |JPEG2000 |`image/jp2` |
      


<span id="85251eec"></span>
### Are TS video files supported?

We recommend converting TS files to MP4 format. TS and MP4 use different audio and video container formats. Both are used to carry encoded audio and video streams, so re\-encoding is not required during format conversion. You can use the [open-source tool FFmpeg](https://ffmpeg.org/download.html) to directly copy the original audio and video streams for fast conversion while keeping the original file encoding format. The following is an example conversion command:

```Bash
ffmpeg -i input.ts -c copy output.mp4
```


<span id="d3743e3b"></span>
## Batch inference

<span id="48fce2af"></span>
### When the task status is Running, Stopping, Stopped, or Failed, will there be output of partially successful results?

Yes, the results of the successfully completed portions will be written to the output, and those parts will be billed normally. However, the order of execution is not guaranteed.

<span id="c7e33a93"></span>
### Based on what criteria is the "Failed" status determined? Is it based on the success/error ratio, or does any error trigger a failure?

The task is determined as failed under the following conditions:


* The input file format does not meet the requirements, such as a data entry not being in JSON format. This type of error will be checked before the task starts, and any error will result in a failure determination.

* Unexpected errors, such as a task crash.

* Task execution timeout.


If a request fails when run, for example, due to a security audit failure, the result of this request will be written to the error folder, and it will not cause the entire task to fail.

<span id="544b65f6"></span>
### For a large number of sub\-tasks where the processing time cannot be estimated, is there an upper limit for the maximum waiting time?

The maximum waiting time (CompletionWindow) can be set up to 28 days. If not specified, the default is 28 days.

<span id="26324f4b"></span>
### If the batch inference task is Stopped due to timeout, are the processing results before the stoppage retained?

Yes, the results are written to the output.

<span id="71cf8a1b"></span>
### Is the output length 4K, 6K, or something else?

The limit is the same as online inference, and the output will be truncated if it exceeds the limit.

<span id="b4b42090"></span>
### Batch inference is billed by token usage. Is it based on input tokens, output tokens, or the sum of both?

Similar to online inference, billing is based on the amount of content processed by the model, including both input and output.

<span id="1e98eaca"></span>
### How are the failed results, that is, the results in errors.jsonl, billed?

Failed results are not billed.

<span id="d35d7806"></span>
## Visual understanding

<span id="2b362525"></span>
### Error invalid parameter when using visual understanding model?


* Download image timeout: To prevent unnecessary bandwidth waste, the default image download timeout is 5 seconds. It may be due to slow image access speed, such as on foreign websites, or larger images. For the above situations, it is recommended to switch to TOS or compress the image to below 100kB.

* Link access prohibited: Status code 403, some image servers may have special security/ACL policies that prohibit access from Volcengine sources. It is recommended to check the security policies of the image servers used by the customer (Alibaba Cloud Object Storage/Tencent Cloud Object Storage/...).

* Unsupported image format, or image format does not match metadata:

   * The following image formats are automatically verified based on the first 512 bytes of the image content:

      
      |Picture Format |Content\-Type |
      |---|---|
      |jpg/jpeg |image/jpeg |
      |png |image/png |
      |gif |image/gif |
      |webp |image/webp |
      |bmp |image/bmp |
      |dib |image/bmp |
      |ico |image/x\-icon |
      


<span id="0a1e4d96"></span>
## Model unit

<span id="41a273d9"></span>
### How long does it take for the model unit to be "shipped" after placing an order?

After placing an order, the system will schedule resources, which usually takes about 10 minutes to complete. No fees will be charged before the scheduling is finished.

<span id="78dd71a8"></span>
### How to ensure the purchase of **postpaid (hourly)**  model units?

Guaranteed purchase cannot be fully ensured. Postpaid resources are limited, and there may be cases where different customers compete for resources during the same business peak period—units are allocated on a first\-come, first\-served basis. If the purchase fails, a full refund will be issued. Ark will plan to add an alert function for purchase failures in the future.

<span id="b1dd3cb4"></span>
### Can model units be used together with the structured output function?

Currently, this is not supported. After purchasing a model unit, it cannot be used in combination with the [structured output](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1568221) function. Whether to support this combined usage method will be considered based on actual circumstances in the future.

<span id="112a7e97"></span>
### What are the differences between model units of different machine types? How to choose a machine type?

Different machine types have different computing power, which leads to differences in processing speed, computing capacity, and the maximum load that can be supported among model units of different machine types.

Different models are suitable for different machine types. The platform has preset optimal deployment scheme combinations covering different scenarios for different models. Users do not need to manually select a machine type; they only need to choose the corresponding template according to their actual needs to complete the deployment.
