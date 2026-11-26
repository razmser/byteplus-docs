# Model fine-tuning dataset format description

# **Requirements**

Currently, model fine\-tuning supports uploading or importing JSON format dataset files. **All samples must ensure one sample per line.**

Refer to the specific format examples below; a sample file is provided after each example.

# Supervised fine\-tuning (SFT)

## Text generation model

### **Format example**

Ensure one sample per line. You can also [download the sample file](https://ark-cdn.tos-cn-beijing.volces.com/samples/SFT_Text_Sample.jsonl) to read; the following is the expanded effect.

```JSON
{
    "messages": [
        {
            "role": "user",
            "content": "To be, or not to be: what follows as the next line in this venerable and renowned work?"
        },
        {
            "role": "assistant",
            "content": "That is the question..."
        }
    ]
}
```

```JSON
{
    "messages": [
        {
            "role": "system",
            "content": "Please reply only with the author's name based on the content of the work."
        },
        {
            "role": "user",
            "content": "To be or not to be, that is the question."
        },
        {
            "role": "assistant",
            "reasoning_content": "The phrase 'To be or not to be, that is the question.' is one of the most famous lines in world literature. It comes from William Shakespeare's tragedy 'Hamlet', specifically from Act 3, Scene 1, where Hamlet delivers his soliloquy. Therefore, the author of this work is William Shakespeare.",
            "content": "William Shakespeare",
            "loss_weight": 1
        }
    ]
}
```

### **Format**

One JSON format data per line:

* **messages**  *(list, required)* : Description of a dialogue.

   * **role**  *(str, required)* : Role. Valid values include system, user, and assistant.

      > **Tip**

      > **Note**: To improve the model's Function Calling capability, you need to select a model that supports Function Calling training and provide a training set containing Function Calling formatted data for fine\-tuning; for specific models and formats, please refer to [Notes for multi\-turn output](function-calling.md#ff6e0fcb)

* **content**  *(str, required)* : Dialogue content text.

* **loss_weight**  *(float, optional)* : Training weight for content loss. When role is set to system or user, the default value of loss_weight is 0.0 and cannot be modified. When role is set to assistant, the default value of loss_weight is 1.0. You can modify the default value of loss_weight in the training data. The value range is [0.0, 1.0].

* **reasoning_content**  *(str, optional)* : model's in\-depth thinking content, only allowed to be carried by the last message where the role is assistant.

   * Because during the reasoning process of multi\-turn conversations in the seed\-1.6 model, the reasoning_content from previous rounds of conversations will not participate in reasoning. Therefore, to ensure the reasoning effect after fine\-tuning, only carry reasoning_content in the last round of conversation.

      * If there is a fine\-tuning requirement to optimize the in\-depth thinking ability in multi\-turn conversation scenarios, split a sample carrying multi\-turn reasoning_content into multiple samples where only the last round of conversation carries reasoning_content. For more content, refer to [Appendix 2: Sample File Splitting for Multi-turn reasoning_content](fine-tuning-dataset-format.md#5d730edd).

      * If only the last turn of the conversation carries reasoning_content during training in multi\-turn conversation scenarios, the model will learn not to perform thinking in intermediate conversation turns, affecting the model's thinking performance.

         ```Plaintext
         For example, suppose the user samples are up1 assistant1 (with cot), up2 assistant2 (with cot), up3 assistant3 (with cot), they should be split into the following three samples for training respectively:
         
         up1 assistant1 (with cot)
         up1 assistant1 (without cot & loss_mask = 0) up2 assistant2 (with cot)
         up1 assistant1 (without cot & loss_mask = 0) up2 assistant2 (without cot & loss_mask = 0) up3 assistant3 (with cot)
         ```

* **thinking** (str, required for models that support this parameter, prohibited for models that do not): Some models with deep thinking capabilities support controlling whether to enable deep thinking capabilities through the **thinking** field.

   ```JSON
   {
       "messages": [
           {
               "role": "user",
               "content": "What is SFT?"
           },
           {
               "role": "assistant",
               "content": "In the field of Natural Language Processing (NLP), Supervised Finetuning (SFT) is a crucial technical method used to enhance the performance of large models in a specific domain. Through careful planning and implementation, SFT can guide the model's learning process and ensure that its learning outcomes are highly consistent with the set goals."
           }
       ],
       "thinking": "disabled"
   }
   ```

   * Different models have different configurable enumeration values; for more models, refer to [Text and code generation](model-list.md#text-and-code).

      |Model Version |enabled |disabled |auto |
      |---|---|---|---|
      |seed\-1\-6\-250615 |✅ |✅ |✅ |
      |seed\-1\-6\-flash\-250615 |✅ |✅ |❌ |

      * `enabled` indicates forcing the model to think; in this case, the sample needs to carry the reasoning_content field, otherwise the model performance will be affected.

      * `disabled` indicates forcing the model not to think; in this case, the sample must not carry the reasoning_content field, otherwise the model performance will be affected.

      * `auto` indicates allowing the model to independently determine whether to think based on the question: think about complex questions and answer simple questions directly. In this mode, users independently decide whether they want the model to think. If they want the model to think, carry the reasoning_content field; otherwise, do not carry it.

      * For the above format requirements, existing datasets can refer to [Appendix 1: Dataset Thinking Field Processing Tool](fine-tuning-dataset-format.md#089b8570) for format conversion.

## Text vectorization model

### **Format example**

Ensure one sample per line. You can [download the sample file](https://ark-cdn.tos-cn-beijing.volces.com/samples/SFT_TextEmbedding_Sample.jsonl) to read; the following is the expanded effect.

```JSON
{
    "query":"Clothing store",
    "docs":[
        {
            "text":"Title: You must realize your dreams. In this life.",
            "label":0
        },
        {
             "text":"Title: It seems that the price is a bit higher..CCC#Clothing people#TikTok assistant#Be a happy girl#Physical store#Outfit#Physical Wangfu Hotel",
             "label":1
         },
        {
              "text":"Title: I like this sentence recently: Take good care of your health and emotions, and you will win more than half of this life. The rest is up to life",
              "label":0
         }
    ]
}
```

### **Format description**

Each line contains one JSON\-formatted data entry:

* **query** (str, required): retrieve the question text.

* **docs** (list, required): A list of positive and negative example text corresponding to the question retrieval text.

   * **text** (str, required): Content of positive and negative example texts.

   * **label** (bool, required): A label value of 1 indicates the sample is a positive example, and one data entry must contain 1 positive example; a label value of 0 indicates the sample is a negative example, and one data entry can contain 0\-5 negative examples.

## Vision understanding model

The dataset format of the vision understanding model is aligned with that of the text generation model. For `seed-2-0-mini-260428` and `seed-2-0-lite-260428`, SFT supports mixing text, images, audio, and video in the `content` array for the `system` and `user` roles.

### Multimodal format examples

The dataset must be a JSONL file, with each line containing one independent, valid JSON object.

**Audio input through TOS**

```JSON
{"messages":[{"role":"user","content":[{"type":"text","text":"Transcribe this customer service call and summarize the customer's request."},{"type":"audio_url","audio_url":{"url":"tos://bucketname/audio/customer_call.wav"}}]},{"role":"assistant","content":"Transcript: The customer asked about a delayed order and provided the order number. Summary: The customer wants to know the reason for the delay and the estimated delivery time.","loss_weight":1.0}]}
```

**Video input through TOS**

```JSON
{"messages":[{"role":"user","content":[{"type":"video_url","video_url":{"url":"tos://bucketname/video/product_intro.mp4"}},{"type":"text","text":"Based on the visuals and audio, determine whether this clip is suitable for a product introduction."}]},{"role":"assistant","content":"The video is clear and the narration is complete, so it is suitable for a product introduction.","loss_weight":1.0}]}
```

### Multimodal format description

The following expanded sample shows how text, image, audio, and video content can be mixed in one conversation:

```JSON
{
  "messages": [
    {
      "role": "system",
      "content": [
        {
          "type": "text",
          "text": "You are a multimodal quality inspection assistant."
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Review the ticket screenshot, customer call, and operation recording, then summarize how the issue was handled."
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "tos://bucketname/image/ticket.png"
          }
        },
        {
          "type": "audio_url",
          "audio_url": {
            "url": "tos://bucketname/audio/customer_call.wav"
          }
        },
        {
          "type": "video_url",
          "video_url": {
            "url": "tos://bucketname/video/operation_record.mp4"
          }
        }
      ]
    },
    {
      "role": "assistant",
      "reasoning_content": "The customer reported a delayed order. The recording shows that the support agent verified the order and completed the compensation process.",
      "content": "The support agent explained the delay, verified the order, and completed the compensation process.",
      "loss_weight": 1.0
    }
  ],
  "thinking": {
    "type": "enabled"
  }
}
```

Each line contains one JSON\-formatted sample:

* **messages**  *(list, required)* : Conversation messages.

   * **role**  *(str, required)* : One of `system`, `user`, `assistant`, or `tool`. The `system` and `user` roles support multimodal arrays. The `assistant` role supports text output only.

   * **content**  *(str or list, required)* : Message content. For `system` and `user`, a list can contain the following content types:

      * **text**  *(object)* : Text content. The `text` field cannot be empty.

      * **image_url**  *(object)* : Image input. The `image_url.url` field supports a TOS URL, Base64 data, or a relative path. For details, see [Image format requirements](fine-tuning-dataset-format.md#65245bbb).

      * **audio_url**  *(object)* : Audio input. The `audio_url.url` field must contain a TOS object address.

      * **video_url**  *(object)* : Video input. The `video_url.url` field supports a TOS object address or Base64 data. Videos can contain an embedded audio track.

   * **loss_weight**  *(float, optional)* : Training weight. For `system` and `user`, the value defaults to `0.0` and cannot be changed. For `assistant`, the value defaults to `1.0` and can range from `0.0` to `1.0`.

   * **reasoning_content**  *(str, optional)* : Deep reasoning content. Only the final `assistant` message can contain this field.

* **thinking**  *(object, optional)* : Controls deep reasoning for models that support this field. Supported values vary by model.

For audio and video input requirements, see [Audio and video file requirements](fine-tuning-dataset-format.md#c2d8a641).

### Image format requirements

* **Dataset file size**: A single dataset file size must be controlled below 2GB.

* **Picture specifications**: A single picture size is limited to within 10M, and the aspect ratio should be less than 200. A single picture supports a maximum of 5120 tokens; if exceeding this value, it will be sampled to the maximum (picture token = length pixels \* width pixels / 784).

* **Sample length**: If the length of a single sample exceeds the model training window, it will be truncated, and pictures are truncated as a whole per picture. The window sizes of different models are as follows:

   * seed\-1\-6\-250615: 128k

   * seed\-1\-6\-flash\-250615: 128k

* **Supported picture formats**: See the table below.

   |**Image Format** |**File Extension** |Content Type |
   |---|---|---|
   |JPEG |.jpg or .jpeg |image/jpeg |
   |PNG |.apng or .png |image/png |
   |GIF |.gif |image/gif |
   |WEBP |.webp |image/webp |
   |BMP |.bmp |image/bmp |
   |TIFF |.tiff or .tif |image/tiff |
   |ICO |.ico |image/x\-icon |
   |DIB |.dib |image/bmp |
   |ICNS |.icns |image/icns |
   |SGI |.sgi |image/sgi |
   |JPEG2000 |.j2c, .j2k, .jp2, .jpc, .jpf, or .jpx |image/jp2 |

### Image inputting

In the data input of the visual understanding model, there are multiple Image inputting methods to meet the needs of different scenarios, and the following is a detailed introduction.

#### **1. (Recommended) use TOS to store pictures**

Fill in the TOS object address in the URL in the form of "`tos://${bucket}/${object}`". **Note that the picture referenced by the TOS URL must be in the same bucket as the JSONL file.**  This format can speed up data processing, reduce the time spent on multi\-role transmission, and thereby improve training efficiency.

```JSON
{
  "messages": [
    {
      "role": "user",
      "content": [
         {
          "type": "image_url",
          "image_url": {
            "url": "tos://bucketname/xxx.png"
          }
        },
        {
          "type": "text",
          "text": "What’s in this image?"
        }
      ]
    },
    {
      "role": "assistant",
      "content": "It is the logo of Skylark LLM model."
    }
  ]
}
```

#### **2.**  Use Base64 encoding

##### **Format example**

After Base64\-encoding the picture, fill it into the jsonl file; for details, refer to [download the sample file.](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/training/vlm_data.jsonl)

Ensure that each sample is on a single line in the file; the following is an example of the expanded effect.

```JSON
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "image_url",
          "image_url": {
            "url": "data:image/{image_extension};base64,{image_base64}"
          }
        },
        {
          "type": "text",
          "text": "What’s in this image?"
        }
      ]
    },
    {
      "role": "assistant",
      "content": "It is a cat."
    }
  ]
}
```

##### **Format description**

* Supports mixing pictures and text within the user role; the order of pictures and text is not restricted, and training proceeds in the order of the samples.

* Supports multi\-turn conversations and multiple pictures in single\-turn conversations.

* The text field cannot be an empty string.

* Supports uploading or selecting multiple files or datasets when creating a fine\-tuning task. **The size of a single data file is limited to 2G or less; large files can be split into multiple small files.**

##### **Appendix: Picture encoding method**

The following is an example of Python code for Base64 encoding of picture.

```Python
import base64

# Fill the result into the URL field
def encode_image(image_path):
    extension = image_path.split(".")[-1]
    with open(image_path, "rb") as image_file:
        base64_image = base64.b64encode(image_file.read()).decode('utf-8')
        return f"data:image/{extension};base64,{base64_image}"
```

#### 3. Use relative path for picture

##### **Format example**

The following is the format example of using picture relative path in jsonl file.

1. Directory structure is as follows:

   ```Bash
   vlm
   ├── data.jsonl
   └── image
       ├── Skylark_app_logo.jpeg
       └── Skylarkk_logo.jpeg
   ```

2. The corresponding data example is as follows:

In the Jsonl file, ensure one sample per line; the following is an example of the expanded effect.

```JSON
{
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "file:./image/Skylark_logo.jpeg"
                    }
                },
                {
                    "type": "text",
                    "text": "What’s in this image?"
                }
            ]
        },
        {
            "role": "assistant",
            "content": "It is the logo of Skylark LLM model."
        }
    ]
}
```

##### **Format description**

**Note: This format only supports folders with less than 1000 pictures; for larger scales, base64 format is still recommended.**

* Supports storing picture and jsonl files in the same folder respectively, pointing to the picture in the jsonl file via relative path (absolute path tos URL is not supported temporarily). The folder can contain multiple jsonl files, but requires the jsonl files to be located in the folder root path.

* During operation, **if you need to upload a folder on the console page, drag the folder into the upload box**; clicking the upload box to select a folder is not currently supported. For the tos dataset folder path, you can directly click to select it in the console.

### **Video frame extraction picture sequence**

Visual understanding model fine\-tuning allows users to extract frames from videos into pictures, then arrange timestamps and pictures in order into the training set. You can refer to [Appendix 3: Frame extraction processing for video content](fine-tuning-dataset-format.md#5af16679).

**Format example**

```JSON
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Do you think this is scary?"
        },
        {
          "type": "text",
          "text": "[0.0 second]"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "base64/relative path"
          }
        },
        {
          "type": "text",
          "text": "[1.0 second]"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "base64/relative path"
          }
        },
        {
          "type": "text",
          "text": "[2.0 second]"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "base64/relative path"
          }
        }
      ]
    }
  ]
}
```

### Audio and video file requirements

Upload audio files to TOS and specify their TOS object addresses in `audio_url`. Pass video files through `video_url` in the multimodal `messages[].content[]` structure. When you use TOS object addresses, the audio or video files and the JSONL file must be in the same bucket.

> **tip**

> Use `audio_url` and `video_url` only with model versions and training methods that support the corresponding input modality. Full SFT and LoRA SFT for `seed-2-0-lite-260428` and `seed-2-0-mini-260428` support audio and video input. For the model scope, see [Select a model](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/fine-tuning-overview#c781196f).

**Audio file limits**

* Common audio formats such as WAV, MP3, M4A, AAC, and OGG are supported. The validation result displayed when you create the fine\-tuning task takes precedence.

* The maximum size of a single audio file is 512 MB.

* Pure audio training does not impose an audio duration limit.

* Ensure that each audio file can be decoded and does not contain an empty track, corrupted data, or an unsupported codec.

**Video input**

```JSON
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "video_url",
          "video_url": {
            "url": "tos://bucketname/video/demo.mp4"
          }
        },
        {
          "type": "text",
          "text": "Describe the sequence of actions performed by the person in the video."
        }
      ]
    },
    {
      "role": "assistant",
      "content": "The person walks into the room, picks up a package, and waves before leaving."
    }
  ]
}
```

The `video_url.url` field supports:

* TOS object addresses, such as `tos://bucketname/video/demo.mp4`.

* Base64 data, such as `data:video/mp4;base64,<BASE64_ENCODING>`.

Uploading a video through the File API and then passing a `file_id` or file reference is not supported.

**Video file limits**

* MP4: `video/mp4`.

* AVI: `video/x-msvideo`.

* MOV:

   * For a video URL, set the object storage Content\-Type to `video/quicktime`.

   * For Base64 data, use `video/mov`, for example `data:video/mov;base64,<BASE64_ENCODING>`.

* The maximum size of a single video file is 512 MB.

* After preprocessing, an embedded audio track uses the same token metering rules as a standalone audio file.

* If the video is used for audiovisual training, ensure that its audio track can be decoded.

To prepare TOS input:

1. Create a TOS bucket and upload the JSONL, audio, and video files to that bucket. For instructions, see [TOS quick start](https://docs.byteplus.com/en/docs/tos/docs-quick-start_1).

2. In the JSONL file, specify object addresses in the `tos://${bucket}/${object}` format, such as `tos://bucketname/audio/example.wav` or `tos://bucketname/video/example.mp4`.

3. When configuring the dataset for the fine\-tuning task, select the option to import from TOS and upload only the JSONL file.

## Video generation model

### Sample format

The sample format is a JSONL file (JSON Lines), a lightweight text file format. The core rule is that each line corresponds to an independent and valid JSON object. Ensure each conversation sample occupies a single line, as shown in the example below:

```Bash
{"output": {"content": {"video_url": "tos://${bucket}/${object}"}}}
```

### Format description

To illustrate the relationship between fields clearly, one piece of data in the JSONL file is expanded as follows:

```Bash
{
  "output": {
    "content": {
      "video_url": "tos://${bucket}/${object}"
    }
  }
}
```

**Field explanation**

Each line contains one piece of sample data in JSON format:

* output (object, required): The output structure of a single sample

* content (object, required): The output content structure

* video_url (str, required): TOS URL of the video file, formatted as: "tos://${bucket}/${object}"

   **Specific operations**

1. Create a storage bucket (Bucket) for storing files (Objects). For detailed steps to create a bucket, refer to [the Object Storage Console Quick Start](https://docs.byteplus.com/en/docs/tos/docs-quick-start_1). An example directory structure is as follows:

   ```Bash
   bucketname    # Bucket name
   ├── sample  # Folder name in the bucket
   ├── data.jsonl
   └── video   # Folder name in the bucket
   ├── xxx.mp4
   └── xxxx.mp4
   ```

2. Fill in the TOS object address in the JSONL file using the format "tos://${bucket}/${object}". For the structure above, the URL would be "tos://bucketname/video/xxx.mp4".**Note that the video files referenced by the TOS URL must be in the same bucket as the JSONL file.**  This format accelerates data processing, reduces time consumed by multi\-role transmission, and thereby improves training efficiency.After completing the above steps, **select the "Import from TOS" method when configuring data for the fine\-tuning task, and only upload the JSONL file.**

   **Video file restrictions**

* The shortest side of the resolution must be 480p or higher (both width and height ≥ 480 pixels)

* Maximum aspect ratio < 21:9

* Duration: 1–12 seconds

* Frame rate ≥ 24 fps

# Direct Preference Optimization (DPO)

## Text generation model

### Standard format

#### **Format example**

Supports preference contrastive learning between positive and negative examples of two answers. Ensure one sample per line. You can [download the sample file](https://ark-cdn.tos-cn-beijing.volces.com/samples/DPO_Text_Sample.jsonl) to read.

The following is the expanded effect.

```JSON
{
    "messages": [
        {
            "role": "system",
            "content": "This is a system"
        },
        {
            "role": "user",
            "content": "What is your name?"
        },
        {
            "role": "assistant",
            "content": "My name is Skylark."
        },
        {
            "role": "user",
            "content": "How to learn Python?"
        },
        {
            "role": "assistant",
            "chosen": "It's so easy. First, you need to learn Python syntax...",
            "rejected": "Check python doc yourself"
        }
    ]
}
```

#### **Format description**

One JSON\-formatted data per line:

* **messages**  *(list, required)* : Description of a dialogue.

   * **role**  *(str, required)* : Role, must be one of system, user, or assistant. The role of the last message must be assistant.

   * **content**  *(str, required)* : Conversation content text, not included in the last message

      * **chosen**  *(str, required)* : Preferred positive example content, must be included in the last message

   * **rejected**  *(str, required)* : Non\-preferred negative example content, must be included in the last message

### **Advanced format**

Advanced format supports providing a list of multiple model responses, and specifies preferences for responses through scores. The training process will automatically generate pairwise comparison partial order pairs for training based on the score values. Note: Two responses with the same score will not generate corresponding partial order pairs.

#### Format example

Ensure one sample per line. You can also [download sample file](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/training/dpo.yaml) to read.

The following is the expanded effect.

```JSON
{
    "messages": [
        {
            "role": "system",
            "content": [
                {
                    "text": "This is a system"
                }
            ]
        },
        {
            "role": "user",
            "content": [
                {
                    "text": "What is your name?"
                }
            ]
        },
        {
            "role": "assistant",
            "content": [
                {
                    "text": "My name is Skylark."
                }
            ]
        },
        {
            "role": "user",
            "content": [
                {
                    "text": "How to learn Python?"
                }
            ]
        },
        {
            "role": "assistant",
            "content": [
                {
                    "text": "I don't know!",
                    "score": 0.5
                },
                {
                    "text": "Check python doc yourself",
                    "score": 0.1
                },
                {
                    "text": "It's so easy. First, you need to learn Python syntax...",
                    "score": 1,
                    "lm_loss_mask": 1
                }
            ],
            "loss_weight": 1
        }
    ]
}
```

#### **Format description**

Each line contains one JSON\-formatted data entry:

* **messages**  *(list, required)* : Description of a dialogue.

   * **role**  *(str, required)* : Role. Valid values include system, user, and assistant. The role of the last message must be assistant, and only the last message can carry multiple reply partial order pairs

   * **content**  *(list, required)* : Dialogue content text or a list object **The content of the last message must be of list type, and the list length must be between 2 and** **5** The element of list is dict and contains the following parameters:

      * **text**  *(str, required)* : Text content.

      * **score**  *(float, required)* : Preference value, a float ranging from 0 to 1, with a higher value indicating stronger preference

      * **lm_loss_mask**  *(float, required)* : Whether to calculate sft loss for this message, default is 0, meaning not to calculate sft loss

   * **loss_weight**  *(float, optional)* : The weighting coefficient for this message loss, default is 1

## Vision understanding model

Support providing multiple model reply lists, and specify preferences for replies through score The training process will automatically generate pairwise comparison partial order pairs for training based on the score value. Note: Two responses with the same score will not generate corresponding partial order pairs.

Support mixing and arranging images and text through arrays in the content field of conversations where the role is user. Image format refer to[Image format requirements](fine-tuning-dataset-format.md#65245bbb).

### Format example

Ensure each sample is one line in the file; the following is an example of the expanded effect.

```Python
{
  "messages": [
    {
      "role": "system",
      "content": [{"type": "text",
                   "text": "This is a system"},
                  {"type": "image_url",
                   "image_url": {"url": "tos://bucket/1.jpg"}}]
    },
    {
      "role": "user",
      "content": [{"type": "text",
                   "text": "What's your name?"},
                  {"type": "image_url",
                   "image_url": {"url": "tos://bucket/1.jpg"}}]
    },
    {
      "role": "assistant",
      "content": [{"text": "My name is Skylark."}],  # The length can only be 1
      "loss_weight": 1.0              # The loss_weight of assistant defaults to 1.0
    },
    {
      "role": "user",
      "content": [{"type": "text", "text": "How to learn Python?"}]
    },
    {
      "role": "assistant",            # The role of the last message must be assistant
      "choices": [                    # There can be multiple responses
        {"content": "I don't know!",
         "score": 0.5,               # The score of this response
         "reasoning_content":"xxx"
        },
        {
          "content": [{"type": "text", "text": "Check python doc yourself"}],
          "score": 0.1
        },
        {
          "content": [{"type": "text", "text": "It's so easy. First, you need to learn Python syntax..."}],
          "score": 1.0,
          "lm_loss_mask": 0           # Whether this response trains the lm_head, defaults to 0
        },
        {
          "content": "It's so easy. First, you need to learn Python syntax...",
          "score": 1.0,
          "lm_loss_mask": 0           # Whether this response trains the lm_head, defaults to 0
        }
      ]

    }
  ],
  "thinking": "auto"
}
```

### Format description

Each line contains one JSON format data:

* **messages**  *(list, required)* : Description of a dialogue.

   * **role**  *(str, required)* : role, one of system, user, assistant, or tool. **The role of the last message must be assistant, and only the last conversation round can carry multiple response partial order pairs.**

   * **content** (list|str, required): Conversation content of non\-last rounds. *Among them, the user's message can mix pictures and texts through an array.*

   * **choices**  *(list, required)* : Text of conversation content or a list object, which can only be filled in the last round of conversation, and the list length is between 2 and 5. The element of list is dict and contains the following parameters:

      * **content**  *(list|str, required)* : Text content

      * **reasoning_content**  *(str, optional)* : In\-depth thinking content, which is only allowed to be carried by the message with the last role as assistant.

      * **score**  *(float, required)* : Preference value, a floating\-point number ranging from 0 to 1; the higher the value, the higher the preference.

      * **lm_loss_mask**  *(optional, required)* : Whether to calculate sft loss for this message; the default is 0, i.e., not to calculate sft loss.

      * **reasoning_content**  *(str, optional)* : Model's in\-depth thinking content

         * If lm_loss_mask is enabled, note that the loss_weight of the assistant's conversation in non\-last rounds should be set to 0; otherwise, it will affect the model's in\-depth thinking ability.

   * **loss_weight**  *(float, optional)* : The weight coefficient for this message loss, default is 1

* **thinking** (str, required for models that support this parameter, prohibited for those that do not): Some models with deep thinking capabilities support controlling whether to enable deep thinking through the **thinking** field.

   * Different models have different configurable enumeration values; for more models, please refer to [Text and code generation](model-list.md#text-and-code).

   |Model Version |enabled |disabled |auto |
   |---|---|---|---|
   |seed\-1\-6\-250615 |✅ |✅ |✅ |
   |seed\-1\-6\-flash\-250615 |✅ |✅ |❌ |

   * `enabled` indicates forcing the model to think; in this case, the assistant in choices must return with the reasoning_content field, otherwise the model performance will be affected

   * `disabled` indicates forcing the model not to think; in this case, the assistant in choices must not return with the reasoning_content field, otherwise the model performance will be affected

   * `auto` indicates allowing the model to independently judge whether thinking is needed based on the question and directly answer simple questions. In this mode, users independently decide whether they want the model to think. If the model is expected to think, then the content in choices should carry the reasoning_content field at this time; otherwise, it should not.

   * For the above format requirements, existing datasets can refer to [Appendix 1: Dataset Thinking Field Processing Tool](fine-tuning-dataset-format.md#089b8570) for format conversion.

# Appendix 1: Dataset thinking field processing tool

You can use a script to convert datasets without `thinking` into datasets with `thinking`:

* For samples in the dataset that do not contain `reasoning_content`, assign `thinking` to `disabled`.

* For samples containing `reasoning_content`, assign it to `enabled`.

You can download [this script](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/training/dataset_converter.zip) to convert the dataset, ensuring your dataset complies with the fine\-tuning model data format specifications.

# Appendix 2: Sample file splitting for multi\-turn reasoning_content

You can refer to the following script to split samples:

* Code implementation: Enter a JSONL sample file and output the processed training data to the specified folder.

* Usage example: python main.py \-\-input test.jsonl \-\-output res\-folder

   ```Python
   import json
   import os
   import argparse
   from process import process_sample
   from typing import List, Dict, Any
   
   def process_sample(sample: Dict[str, Any]) -> List[Dict[str, Any]]:
       """
       Processes a single sample data and generates a set of processed samples according to the rules.
   
       Parameters:
           sample: a dictionary containing messages, formatted as shown in the example.
   
       Returns:
           a list of processed samples.
       """
       messages = sample["messages"]
   
       # Used to store processed messages
       message_result_list = []
       processed_messages = []
       not_set_loss_weight_index = []
   
       for i in range(0, len(messages)):
           current = messages[i]
   
           # The last message does not require additional processing
           if i == len(messages) - 1:
               processed_messages.append(current)
               message_result_list.append(processed_messages.copy())
               break
   
           # Only process messages with role as assistant
           if current.get("role") == "assistant":
               # Check if loss_weight: 0 exists
               has_loss_weight_zero = "loss_weight" in current and current["loss_weight"] == 0
               # Check if reasoning_content exists
               has_reasoning_content = "reasoning_content" in current
   
               # Case 1: loss_weight:0 does not exist, and reasoning_content exists
               if not has_loss_weight_zero and has_reasoning_content:
                   # Split out the current message and add it to the message list
                   previous_messages = [msg.copy() for msg in processed_messages]
                   previous_messages.append(current.copy())
                   message_result_list.append(previous_messages.copy())
   
                   # Process the current message, do not retain reasoning_content, and record loss_weight:0
                   new_assistant_msg = current.copy()
                   del new_assistant_msg["reasoning_content"]
                   new_assistant_msg["loss_weight"] = 0
   
                   # Save the processed message segment for subsequent use
                   processed_messages.append(new_assistant_msg)
                   current = []
   
                   # Process messages with unset loss_weight from before, ensuring each message is used for training only once
                   for idx in not_set_loss_weight_index:
                       processed_messages[idx]["loss_weight"] = 0
                   not_set_loss_weight_index = []
   
               # Case 2: loss_weight:0 exists
               elif has_loss_weight_zero:
                   # Do not split and do not retain reasoning_content
                   new_assistant_msg = current.copy()
                   if "reasoning_content" in new_assistant_msg:
                       del new_assistant_msg["reasoning_content"]
   
                   processed_messages.append(new_assistant_msg)
               # Case 3: reasoning_content does not exist
               else:
                   processed_messages.append(current)
                   # Record the current messages with unset loss_weight
                   not_set_loss_weight_index.append(i)
           else:
               # Messages from non-assistant roles are directly added
               processed_messages.append(current)
   
       # Generate the final sample list
       processed_samples = []
       for messages in message_result_list:
           processed_sample = sample.copy()
           processed_sample["messages"] = messages
           processed_samples.append(processed_sample)
   
       return processed_samples
   
   def main():
       parser = argparse.ArgumentParser(description='Process JSONL sample data and output to a folder')
       parser.add_argument('--input', required=True, help='Path to the input JSONL file')
       parser.add_argument('--output', required=True, help='Path to the output folder')
   
       args = parser.parse_args()
   
       # Read JSONL file
       samples = []
       try:
           with open(args.input, 'r', encoding='utf-8') as f:
               for line in f:
                   if line.strip():  # Skip empty lines
                       samples.append(json.loads(line))
       except FileNotFoundError:
           print(f"Error: Input file {args.input} not found")
           return
   
       # Process all samples
       all_processed_samples = []
       for sample in samples:
           processed_samples = process_sample(sample)
           all_processed_samples.extend(processed_samples)
   
       # Write JSONL file to folder
       os.makedirs(args.output, exist_ok=True)
   
       try:
           # Ensure output directory exists
           os.makedirs(args.output, exist_ok=True)
   
           # Construct complete output path
           output_path = os.path.join(args.output, f"result.jsonl")
   
           # Write all samples to JSONL file
           with open(output_path, 'w', encoding='utf-8') as f:
               for sample in all_processed_samples:
                   f.write(json.dumps(sample, ensure_ascii=False) + '\n')
   
           print(f"Processing completed. JSONL file generated, containing {len(all_processed_samples)} samples, saved at {output_path}")
       except Exception as e:
           print(f"Error: Failed to write file - {e}")
   
   if __name__ == "__main__":
       main()
   ```

# Appendix 3: Frame extraction processing for video content

When you need to understand video content, you can extract frames from the video, generate several pictures, then combine the pictures with text as prompt content to request the video understanding model service. The following is a solution implemented using Python scripts for your reference.

## Code flow

1. Video file reading: Read the video file from the local path.

2. Frame extraction from the video supports 2 strategies:

   1. `CONSTANT_INTERVAL`: Frame extraction at fixed time intervals, such as one frame per 1s, and the time interval can be adjusted.

   2. `EVEN_INTERVAL`: Fixed total number of frames for uniform frame extraction. For example, regardless of video duration, extract 20 frames in total, and the total number of extracted frames can be adjusted.

3. Compressed frame image: To reduce network transmission delay and token usage, compress the image proportionally to within 640\*480.

4. Request visual understanding model: Assemble the request body, including the prompt (Prompt) and Base64 of all frame images, and call the model service using the ModelArk `ChatCompletion` interface.

## Sample code

### Environment configuration

Before running the script, you need to configure the API Key and obtain the Model ID. For specific methods, please refer to [Quick start](quick-start.md).

> When creating an inference endpoint, select a large model that supports visual understanding. For details, please refer to [Capabilities](model-list.md#text-code-capabilities).

Before running the script, install the dependency packages in the terminal first.

```Bash
pip install opencv-contrib-python
pip install --upgrade arkruntime
```

### Implement script and configuration

Configure your variables, including:

* `<VIDEO_FILE_PATH>`Replace the path of your video file.

* `<MODEL>`Replace with your model ID.

* `prompt = "describe video content"`Configure your text prompt; this is for example use only.

* Select your frame extraction strategy.

   ```Python
   ...
   # 1. CONSTANT_INTERVAL: Fixed time interval frame extraction, time interval can be adjusted
   # 2. EVEN_INTERVAL: Uniform frame extraction with fixed total number of frames, total number of frames can be adjusted
           extraction_strategy=Strategy.CONSTANT_INTERVAL,
           # extraction_strategy=Strategy.EVEN_INTERVAL,
   ...
   ```

### Complete Python script

```Python
import json
import os
import argparse
from typing import Optional
# Import the OpenCV library for video processing
import cv2
# Import the time library for timing
import time
# Import the file operation library for deleting directories
import shutil
# Import the enum library for defining frame extraction strategies
from enum import Enum

# Import the Base64 encoding library to convert pictures to Base64 encoding
import base64
# Import the operating system library for file path actions
import os

# Install the ModelArk SDK via python -m pip install --upgrade arkruntime
from arkruntime._exceptions import ArkAPIError
# Import the ModelArk SDK to call ModelArk APIs
from arkruntime import Ark

# Define the frame extraction strategy enumeration class
class Strategy(Enum):
    # Fixed interval frame extraction strategy, e.g., extract one frame every 1 second
    CONSTANT_INTERVAL = "constant_interval"
    # Uniform interval frame extraction strategy, uniformly extract from the entire length of the video according to the set maximum number of frames
    EVEN_INTERVAL = "even_interval"

def preprocess_video(
        video_file_path: str,
        output_dir: str,
        extraction_strategy: Optional[Strategy] = Strategy.EVEN_INTERVAL,
        interval_in_seconds: Optional[float] = 1,
        max_frames: Optional[int] = 10,
        keyframe_naming_template: str = "frame_{:04d}.jpg",
) -> list[str]:
    """Extract frames from video according to the specified strategy
    Parameters:
        video_file_path (str): Path to the video file
        output_dir (str): Output directory
        extraction_strategy (Optional[Strategy], optional): Frame extraction strategy.
             Fixed interval (e.g., extract one frame every 1 second) or
             Uniform interval (uniformly extract frames from the entire video length according to the set maximum number of frames)
             Default: fixed interval, extract one frame every 1 second
        interval_in_seconds (Optional[float], optional): Interval time for fixed interval frame extraction. Default: 1 second per frame
        max_frames (Optional[int], optional): Maximum number of frames to extract. Default: 10 frames
        keyframe_naming_template (_type_, optional): Naming template for extracted frame images
    Returns:
        list[str]: List of paths to extracted frame images
    """
    # Check if the output directory exists; if not, create it
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
    # Use OpenCV to open the video file
    cap = cv2.VideoCapture(video_file_path)
    # Get the frame rate of the video
    fps = cap.get(cv2.CAP_PROP_FPS)
    # Get the total number of frames of the video
    length = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))

    # Select the frame extraction interval according to the strategy
    if extraction_strategy == Strategy.CONSTANT_INTERVAL:
        # Calculate the frame interval for fixed interval frame extraction
        frame_interval = int(fps * interval_in_seconds)
    elif extraction_strategy == Strategy.EVEN_INTERVAL:
        # Calculate the frame interval for uniform interval frame extraction
        frame_interval = int(length / max_frames)
    else:
        # If the strategy is invalid, throw an exception
        raise ValueError("Invalid extraction strategy")
    # Initialize the frame counter
    frame_count = 0
    # Initialize keyframe list
    keyframes = []
    # Loop to read video frames
    while True:
        # Read a frame
        ret, frame = cap.read()
        # If reading fails, break out of the loop
        if not ret:
            break
        # If the current frame is a keyframe
        if frame_count % frame_interval == 0:
            # Generate the filename of the keyframe
            image_path = os.path.join(
                output_dir, keyframe_naming_template.format(len(keyframes))
            )
            # Save the keyframe as a picture
            cv2.imwrite(
                image_path,
                frame,
            )
            # Add the keyframe path to the list
            keyframes.append(image_path)
        # Increment the frame counter
        frame_count += 1
        # If the number of keyframes reaches the maximum value, break out of the loop
        if len(keyframes) >= max_frames:
            break

    print("[Frame extraction completed]")
    print("[Number of extracted frames]", len(keyframes))
    # Return keyframe path list
    return keyframes

def resize(image):
    """
    Resize the image to fit the specified dimensions.
    Parameters:
        image (numpy.ndarray): Input image in numpy array format.
    Returns:
        numpy.ndarray: Resized image.
    """
    # Get the original height and width of the picture
    height, width = image.shape[:2]
    # Determine the target size based on the picture's aspect ratio
    if height < width:
        target_height, target_width = 480, 640
    else:
        target_height, target_width = 640, 480
    # If the picture size is already less than or equal to the target size, directly return the original picture
    if height <= target_height and width <= target_width:
        return image
    # Calculate the new height and width, maintaining the picture's aspect ratio
    if height / target_height < width / target_width:
        new_width = target_width
        new_height = int(height * (new_width / width))
    else:
        new_height = target_height
        new_width = int(width * (new_height / height))
    # Resize the picture
    return cv2.resize(image, (new_width, new_height))

# Define a method to resize the picture in the specified path to an appropriate size and convert it to Base64 encoding
def encode_image(image_path: str) -> str:
    """
    Encode the image at the specified path
    Parameters:
        image_path (str): Path to the image file
    Returns:
        str: Encoded image string
    """
    # Read the picture
    image = cv2.imread(image_path)
    # Resize the picture
    image_resized = resize(image)
    # Encode the picture into JPEG format
    _, encoded_image = cv2.imencode(".jpg", image_resized)
    # Convert the encoded picture to a Base64 string
    return base64.b64encode(encoded_image).decode("utf-8")

def construct_messages(image_paths: list[str], prompt: str) -> list[dict]:
    """
    Construct a message list containing text and images
    Parameters:
        image_paths (list[str]): List of image file paths
        prompt (str): Text prompt
    Returns:
        list[dict]: Message list containing text and images
    """
    print("[Assembling request parameters]")
    # Initialize the message content list, containing a text message
    content = [
        {
            "type": "text",
            "text": prompt,
        },
    ]
    # Iterate through the image path list
    for image_path in image_paths:
        # Construct an image URL message for each image path
        content.append(
            {
                "type": "image_url",
                "image_url": {
                    # Use Base64 encoding to convert the image to a data URL
                    "url": f"data:image/jpeg;base64,{encode_image(image_path)}",
                    # Specify the image detail level as low
                    "detail":"low"
                },
            }
        )
    # Return the message list containing text and images
    return [
        {
            "role": "user",
            "content": content,
        }
    ]

if __name__ == "__main__":
    # Replace with your video file path
    video_path = "<VIDEO_FILE_PATH>"
    # API Key read from the environment variable; configure your API Key in advance
    ark_api_key = os.environ.get("ARK_API_KEY")
    # Replace <MODEL> with your model ID.
    model_id = "<MODEL>"
    # Replace with your text prompt; this is only for example use
    prompt = "Describe the video content"
    since = time.time()
    # Delete historical frames
    if os.path.exists("videoFrames"):
        shutil.rmtree("videoFrames")

    selected_images = preprocess_video(
        video_file_path=video_path,
        output_dir="videoFrames",

        # Two frame extraction strategies
        # 1. CONSTANT_INTERVAL: Frame extraction at fixed time intervals; time interval can be adjusted
        # 2. EVEN_INTERVAL: Uniform frame extraction with fixed total number of frames; total number of frames can be adjusted
        extraction_strategy=Strategy.CONSTANT_INTERVAL,
        # extraction_strategy=Strategy.EVEN_INTERVAL,

        # Corresponding frame extraction interval for fixed frame extraction strategy
        interval_in_seconds=1,

        # Maximum number of frames extracted from video
        max_frames=20
    )

    print("[Frame image compression completed]")
    preprocess_time_cost = time.time() - since

    client = Ark(
        api_key=ark_api_key,
        base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )
    since = time.time()
    try:
        response = client.chat.completions.create(
            model=model_id,
            messages=construct_messages(
                selected_images,
                prompt,
            )
        )

        print("[Video understanding result]\n", response.choices[0].message.content)
        print("\n[Total tokens consumed]", response.usage.total_tokens)
        print("- Input tokens:", response.usage.prompt_tokens)
        print("- Output tokens:", response.usage.completion_tokens)

    except ArkAPIError as e:
        print(e)

    api_time_cost = time.time() - since
    print("\n[Total end-to-end time]", round(preprocess_time_cost + api_time_cost, 2))
    print("- Video preprocessing time:", round(preprocess_time_cost, 2))
    print("- Result generation time:", round(api_time_cost, 2))
```
