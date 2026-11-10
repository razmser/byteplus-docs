# Files API tutorial

You can upload, retrieve, list, and delete files using the Files API.

In multimodal understanding scenarios, the Files API is used together with the Responses API and Chat API to provide the following benefits:

* Large file support: When files are stored in the default hosted storage space of ModelArk, files up to 512 MB can be uploaded. When files are stored in the TOS buckets you specified, video files up to 2 GB can be uploaded to meet the needs for large files.

* Reuse: Supports reusing files across multiple requests through the File ID, avoiding repeated uploads and reducing public network download latency.

* Shorter inference time: Decouples data preprocessing from model inference, avoids uploading content repeatedly for each request, and reduces latency caused by preprocessing.

# Prerequisites

[Obtain an API key](https://console.byteplus.com/ark/apiKey)

# API documentation

[Files API reference](upload-files-api.md)

# Files API usage examples

## Upload a file

You can use the Files API to upload files such as images, videos, audio, and documents. After the upload succeeds, a file ID is returned. The file ID can be reused across multiple requests without uploading the content again, reducing public network download latency. If the file is larger than 50 MB, or if you want to reuse the file across multiple requests, you can upload the file using the Files API and then use the file ID to initiate requests.

The file storage location is determined by the `tos` parameter in the request. For details, see the parameter descriptions in [Upload file](upload-files-api.md):

* If the `tos` parameter is not passed: The file is stored in the default managed storage space of ModelArk.

* If the `tos` parameter is passed: The file is stored in the BytePlus TOS bucket specified by the user.

> The target BytePlus TOS bucket can be used only after authorization is completed in the console. For details, see [User TOS authorization](project-configuration.md#4eb1b277).

### File upload methods

The Files API supports uploading files in two ways: binary files and file URLs.

* `file` (binary file): The local file to upload.Files can be stored in the default managed storage space of ModelArk or a BytePlus TOS bucket specified by the user.

* `url` (file URL): Supports HTTP/HTTPS URLs and TOS URIs.

   * HTTP/HTTPS URL: A file URL that can be directly accessed over the public internet. Make sure the file URL is accessible.Files can be stored in the default managed storage space of ModelArk or a BytePlus TOS bucket specified by the user.

   * TOS URI: A resource location identifier specific to TOS, in the format `tos://<bucket>/<prefix>/<file_name>`.When this method is used, files can only be stored in a BytePlus TOS bucket.

#### Upload files

* Store files in the default managed storage space of ModelArk

   **Curl**

```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
       -H "Authorization: Bearer $ARK_API_KEY" \
       -F 'purpose=user_data' \
       -F 'file=@/Users/doc/demo.mp4' \
       -F 'preprocess_configs[video][fps]=0.3'
   ```

   The response parameters are as follows:

   ```Bash
   {
       "object": "file",
       "id": "file-20251018114827-6zgrb",
       "purpose": "user_data",
       "filename": "demo.mp4",
       "bytes": 695110,
       "mime_type": "video/mp4",
       "created_at": 1760759307,
       "expire_at": 1761364107,
       "status": "processing",
       "preprocess_configs": {
           "video": {
               "fps": 0.3
           }
       }
   }
   ```

   **Python SDK**

```Python
   import os
   from arkruntime import Ark
   
   client = Ark(
       base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
       api_key=os.getenv('ARK_API_KEY')
   )
   
   file = client.files.create(
       # replace with your local video path
       file=open("/Users/doc/demo.mp4", "rb"),
       purpose="user_data",
       preprocess_configs={
           "video": {
               "fps": 0.3,  # define the sampling fps of the video, default is 1.0
           }
       }
   )
   print(file)
   ```

   **Go SDK**

```Go
   package main
   
   import (
       "context"
       "fmt"
       "os"
   
       "github.com/volcengine/ark-runtime-go/arkruntime"
       "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
   )
   
   func main() {
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx := context.Background()
   
       data, err := os.Open("/Users/doc/demo.mp4")
       if err != nil {
           fmt.Printf("read file error: %v\n", err)
           return
       }
       fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
   Purpose: file.PurposeUserData,
           PreprocessConfigs: file.NewOptPreprocessConfigs(file.PreprocessConfigs{
               Video: file.NewOptVideo(file.Video{
                   Fps: file.NewOptFloat64(0.3),
               }),
           }),
       }, data)
   
       if err != nil {
           fmt.Printf("upload file error: %v", err)
           return
       }
       fmt.Printf("file info: %v\n", fileInfo)
   
   }
   ```

   **Java SDK**

```Java
   package com.ark.sample;
   
   import com.volcengine.ark.runtime.models.file.*;
   import com.volcengine.ark.runtime.service.ArkService;
   import java.io.File;
   
   public class demo {
   
       public static void main(String[] args) {
           String apiKey = System.getenv("ARK_API_KEY");
           ArkService service = ArkService.builder().apiKey(apiKey)
                   .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                   .build();
   
           System.out.println("===== Upload File Example=====");
           FileObject fileMeta;
           fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your image file path
                           .purpose(Purpose.USER_DATA)
                           .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().fps(0.3).build()).build())
                           .build(), new File("/Users/doc/demo.mp4"));
           System.out.println("Uploaded file Meta: " + fileMeta);
   
           service.shutdownExecutor();
       }
   }
   ```

   **OpenAI SDK**

```Python
   import os
   from openai import OpenAI
   
   client = OpenAI(
       base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
       api_key=os.getenv('ARK_API_KEY')
   )
   
   file = client.files.create(
       # replace with your local video path
       file=open("/Users/doc/demo.mp4", "rb"),
       purpose="user_data",
       extra_body={
           "preprocess_configs":{
               "video": {
                   "fps": 0.3
               }
           }
       }
   )
   print(file)
   ```

* Store the file in a BytePlus TOS bucket:

   **Curl**

```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
       -H "Authorization: Bearer $ARK_API_KEY" \
       -F 'purpose=user_data' \
       -F 'file=@/Users/doc/demo.mp4' \
       -F 'preprocess_configs[video][fps]=0.3' \
       -F "tos[bucket]=my-bucket" \
       -F "tos[prefix]=ark-files/"
   ```

   **Python SDK**

```Python
   import os
   from arkruntime import Ark
   
   client = Ark(
       base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
       api_key=os.getenv('ARK_API_KEY')
   )
   
   file = client.files.create(
       file=open("/Users/doc/demo.mp4", "rb"),
       purpose="user_data",
       preprocess_configs={
           "video": {
               "fps": 0.3
           }
       },
       tos={
           "bucket": "my-bucket",
           "prefix": "ark-files/"
       }
   )
   print(file)
   ```

   **Go SDK**

```Go
   package main
   
   import (
       "context"
       "fmt"
       "os"
   
       "github.com/volcengine/ark-runtime-go/arkruntime"
       "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
   )
   
   func main() {
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx := context.Background()
   
       data, err := os.Open("/Users/doc/demo.mp4")
       if err != nil {
           fmt.Printf("read file error: %v\n", err)
           return
       }
   
       fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
   Purpose: file.PurposeUserData,
           Tos: file.NewOptTosStorage(file.TosStorage{
               Bucket: file.NewOptString("my-bucket"),
               Prefix: file.NewOptString("ark-files/"),
           }),
           PreprocessConfigs: file.NewOptPreprocessConfigs(file.PreprocessConfigs{
               Video: file.NewOptVideo(file.Video{
                   Fps: file.NewOptFloat64(0.3),
               }),
           }),
       }, data)
       if err != nil {
           fmt.Printf("upload file error: %v", err)
           return
       }
       fmt.Printf("file info: %v\n", fileInfo)
   }
   ```

   **Java SDK**

```Java
   package com.ark.sample;
   import com.volcengine.ark.runtime.models.file.*;
   import com.volcengine.ark.runtime.service.ArkService;
   import java.io.File;
   
   public class demo {
   
       public static void main(String[] args) {
           String apiKey = System.getenv("ARK_API_KEY");
           ArkService service = ArkService.builder()
                   .apiKey(apiKey)
                   .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                   .build();
   
           System.out.println("===== Upload File Example =====");
           FileObject fileMeta;
           fileMeta = service.uploadFile(FileCreateRequest.builder()
                           .purpose(Purpose.USER_DATA)
                           .tos(TosStorage.builder()
                                   .bucket("my-bucket")
                                   .prefix("ark-files/")
                                   .build())
                           .preprocessConfigs(PreprocessConfigs.builder()
                                   .video(Video.builder().fps(0.3).build())
                                   .build())
                           .build(), new File("/Users/doc/demo.mp4"));
           System.out.println("Uploaded file Meta: " + fileMeta);
   
           service.shutdownExecutor();
       }
   }
   ```

#### Upload via HTTP/HTTPS URL

* Store files in the default managed storage space of ModelArk

   **Curl**

```Bash
   curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
         -H "Authorization: Bearer $ARK_API_KEY" \
         -F "purpose=user_data" \
         -F "url=https://example.com/docs/demo_img.png"
   ```

   **Python SDK**

```Python
   import os
   from arkruntime import Ark
   
   client = Ark(
       base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
       api_key=os.getenv('ARK_API_KEY')
   )
   
   file = client.files.create(
       purpose="user_data",
       url="https://example.com/docs/demo_img.png"
   )
   print(file)
   ```

   **Go SDK**

```Go
   package main
   
   import (
       "context"
       "fmt"
       "os"
   
       "github.com/volcengine/ark-runtime-go/arkruntime"
       "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
   )
   
   func main() {
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx := context.Background()
   
       fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
           Purpose: file.PurposeUserData,
           URL:     file.NewOptString("https://example.com/docs/demo_img.png"),
       }, nil)
   
       if err != nil {
           fmt.Printf("upload file error: %v", err)
           return
       }
       fmt.Printf("file info: %v\n", fileInfo)
   }
   ```

   **Java SDK**

```Java
   package com.ark.sample;
   
   import com.byteplus.ark.runtime.model.files.*;
   import com.byteplus.ark.runtime.service.ArkService;
   
   public class demo {
       public static void main(String[] args) {
           String apiKey = System.getenv("ARK_API_KEY");
           ArkService service = ArkService.builder()
                   .apiKey(apiKey)
                   .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                   .build();
   
           System.out.println("===== Upload File Example =====");
           FileMeta fileMeta;
           try {
               fileMeta = service.uploadFile(
                       UploadFileRequest.builder()
                               .url("https://example.com/docs/demo_img.png")
                               .purpose("user_data")
                               .build());
               System.out.println("Uploaded file Meta: " + fileMeta);
           } catch (Exception e) {
               e.printStackTrace();
           }
   
           service.shutdownExecutor();
       }
   }
   ```

* Store files in a BytePlus TOS bucket

   **Curl**

```Bash
   curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
       -H "Authorization: Bearer $ARK_API_KEY" \
       -F "purpose=user_data" \
       -F "url=https://example.com/docs/demo_img.png" \
       -F "tos[bucket]=my-bucket" \
       -F "tos[prefix]=ark-files/"
   ```

   **Python SDK**

```Python
   import os
   from arkruntime import Ark
   
   client = Ark(
       base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
       api_key=os.getenv('ARK_API_KEY')
   )
   
   file = client.files.create(
       url="https://example.com/docs/demo_img.png",
       purpose="user_data",
       tos={
           "bucket": "my-bucket",
           "prefix": "ark-files/"
       }
   )
   print(file)
   ```

   **Go SDK**

```Go
   package main
   
   import (
       "context"
       "fmt"
       "os"
   
       "github.com/volcengine/ark-runtime-go/arkruntime"
       "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
   )
   
   func main() {
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx := context.Background()
   
       fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
           Purpose: file.PurposeUserData,
           URL:     file.NewOptString("https://example.com/docs/demo_img.png"),
           Tos: file.NewOptTosStorage(file.TosStorage{
               Bucket: file.NewOptString("my-bucket"),
               Prefix: file.NewOptString("ark-files/"),
           }),
       }, nil)
       if err != nil {
           fmt.Printf("upload file error: %v", err)
           return
       }
       fmt.Printf("file info: %v\n", fileInfo)
   }
   ```

   **Java SDK**

```Java
   package com.ark.sample;
   
   import com.byteplus.ark.runtime.model.files.*;
   import com.byteplus.ark.runtime.service.ArkService;
   
   public class demo {
   
       public static void main(String[] args) {
           String apiKey = System.getenv("ARK_API_KEY");
           ArkService service = ArkService.builder()
                   .apiKey(apiKey)
                   .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                   .build();
   
           System.out.println("===== Upload File Example =====");
           FileMeta fileMeta;
           try {
               fileMeta = service.uploadFile(
                       UploadFileRequest.builder()
                               .url("https://example.com/docs/demo_img.png")
                               .purpose("user_data")
                               .tos(TosStorage.builder()
                                       .bucket("my-bucket")
                                       .prefix("ark-files/")
                                       .build())
                               .build());
               System.out.println("Uploaded file Meta: " + fileMeta);
           } catch (Exception e) {
               e.printStackTrace();
           }
   
           service.shutdownExecutor();
       }
   }
   ```

#### Upload via TOS URI

When you pass a TOS URI, the file must be stored in a BytePlus TOS bucket. Note the following:

* `url` specifies the location of the source file to read, that is, an existing file in a BytePlus TOS bucket.

* `tos[bucket]` and `tos[prefix]` specify the bucket and storage path for uploading the file to a BytePlus TOS bucket.

**Curl**

```Bash
curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -F "purpose=user_data" \
  -F "url=tos://my-bucket/source/raw_video.mp4" \
  -F "tos[bucket]=my-bucket" \
  -F "tos[prefix]=ark-files/"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

file = client.files.create(
    url="tos://my-bucket/source/raw_video.mp4",
    purpose="user_data",
    tos={
        "bucket": "my-bucket",
        "prefix": "ark-files/"
    }
)
print(file)
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
        Purpose: file.PurposeUserData,
        URL:     file.NewOptString("tos://my-bucket/source/raw_video.mp4"),
        Tos: file.NewOptTosStorage(file.TosStorage{
            Bucket: file.NewOptString("my-bucket"),
            Prefix: file.NewOptString("ark-files/"),
        }),
    }, nil)
    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }
    fmt.Printf("file info: %v\n", fileInfo)
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.byteplus.ark.runtime.model.files.*;
import com.byteplus.ark.runtime.service.ArkService;

public class demo {

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example =====");
        FileMeta fileMeta;
        try {
            fileMeta = service.uploadFile(
                    UploadFileRequest.builder()
                            .url("tos://my-bucket/source/raw_video.mp4")
                            .purpose("user_data")
                            .tos(TosStorage.builder()
                                    .bucket("my-bucket")
                                    .prefix("ark-files/")
                                    .build())
                            .build());
            System.out.println("Uploaded file Meta: " + fileMeta);
        } catch (Exception e) {
            e.printStackTrace();
        }

        service.shutdownExecutor();
    }
}
```

In video understanding scenarios involving large videos, when you upload through the Files API by using `url` and pass the `tos` parameter, a single video file can be up to 2 GB (applies only to video files; other file types are still up to 512 MB). After the file is uploaded, it enters an asynchronous processing flow. You can use it only after the file status changes to active.

**Curl**

```Bash
curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -F "purpose=user_data" \
  -F "url=tos://my-bucket/videos/long-video.mp4" \
  -F "tos[bucket]=my-bucket" \
  -F "tos[prefix]=ark-files/" \
  -F "preprocess_configs[video][model]=dola-seed-2-1-turbo-260628" \
  -F "preprocess_configs[video][max_video_tokens]=200000" \
  -F "preprocess_configs[video][min_frames]=16"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

file = client.files.create(
    url="tos://my-bucket/videos/long-video.mp4",
    purpose="user_data",
    tos={
        "bucket": "my-bucket",
        "prefix": "ark-files/"
    },
    preprocess_configs={
        "video": {
            "model": "dola-seed-2-1-turbo-260628",
            "max_video_tokens": 200000,
            "min_frames": 16
        }
    }
)
print(file)
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/volcengine-go-sdk/service/arkruntime"
    "github.com/volcengine/volcengine-go-sdk/service/arkruntime/model/file"
    "github.com/volcengine/volcengine-go-sdk/volcengine"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.UploadFile(ctx, &file.UploadFileRequest{
        Purpose: file.PurposeUserData,
        URL:     volcengine.String("tos://my-bucket/videos/long-video.mp4"),
        Tos: &file.TosStorage{
            Bucket: volcengine.String("my-bucket"),
            Prefix: volcengine.String("ark-files/"),
        },
        PreprocessConfigs: &file.PreprocessConfigs{
            Video: &file.Video{
                Model:          volcengine.String("dola-seed-2-1-turbo-260628"),
                MaxVideoTokens: volcengine.Int64(200000),
                MinFrames:      volcengine.Int64(16),
            },
        },
    })
    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }
    fmt.Printf("file info: %v\n", fileInfo)
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.model.files.*;
import com.volcengine.ark.runtime.service.ArkService;

public class demo {

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example =====");
        FileMeta fileMeta;
        try {
            fileMeta = service.uploadFile(
                    UploadFileRequest.builder()
                            .url("tos://my-bucket/videos/long-video.mp4")
                            .purpose("user_data")
                            .tos(TosStorage.builder()
                                    .bucket("my-bucket")
                                    .prefix("ark-files/")
                                    .build())
                            .preprocessConfigs(PreprocessConfigs.builder()
                                    .video(Video.builder()
                                            .model("dola-seed-2-1-turbo-260628")
                                            .maxVideoTokens(200000L)
                                            .minFrames(16L)
                                            .build())
                                    .build())
                            .build());
            System.out.println("Uploaded file Meta: " + fileMeta);
        } catch (Exception e) {
            e.printStackTrace();
        }

        service.shutdownExecutor();
    }
}
```

### File storage limits

|Comparison item |Default storage space managed by ModelArk |BytePlus TOS bucket |
|---|---|---|
|Authorization requirements |No additional authorization is required. |The target storage bucket can be used only after authorization is completed in the console. See [User TOS authorization](project-configuration.md#4eb1b277). |
|Single file size |512 MB |2 GB for video files, and 512 MB for other file types. |
|Total storage capacity |20 GB |No limit |
|Storage duration |Stored for 7 days by default. You can customize the storage validity period by using the **expire_at** parameter. The value range is 1 to 30 days.<br><br>> If you have frequent storage needs, we recommend that you actively manage storage space by shortening the file storage duration and proactively calling the deletion API to clean up inactive files. |Stored for 7 days by default. You can customize the storage validity period by using the **expire_at** parameter. The value range is 1 to 30 days. |
|Object operation limits |Call the Files API to delete the file. See [Delete file](delete-files-api.md). |After an object is managed, it can only be read. You cannot delete, overwrite, or modify the object through the TOS console or TOS API. Asynchronous write, delete, and modify operations that affect the object, such as lifecycle deletion and overwrite copy, are also subject to managed protection restrictions. Call the Files API to delete the file. See [Delete file](delete-files-api.md). |

### File preprocessing

When you upload files by using the Files API, the API preprocesses the files based on the uploaded file type.

* Video files: By default, frames are extracted at a rate of 1 frame per second. You can set a custom frame rate with the **preprocess_configs.video.fps** parameter. For long videos with little visual change, you can set a lower FPS value. If you need to capture visual changes in detail, you can set a higher FPS value. You can also use `min_frame_tokens`, `max_frame_tokens`, and `max_video_tokens` to control the compression strategy for individual frames and the overall video information retention strategy. After file preprocessing, using the File ID in the Responses API can reduce inference latency.

* PDF files: PDF files are processed page by page into multiple images. During preprocessing, the split images are not scaled in resolution, so that the original information in the PDF file can be preserved completely and clearly.

> **Tip**

> When the `tos` parameter is passed, file preprocessing outputs are saved to the TOS path specified by the parameter. For example, if `tos[bucket]=my-bucket` and `tos[prefix]=ark-files/` are configured in the request, the preprocessing outputs are stored in the `my-bucket/ark-files/ark_processed/{file_id}/` directory, where `{file_id}` is the ID of the file uploaded this time.

### Preprocessing timeout limit

The timeout limit for file preprocessing by using the Files API is 5 minutes. Timeouts are usually affected by factors such as video duration, number of PDF pages, pixels per page, pixels per frame, and audio duration.

**Timeout solutions**:

First, check whether the file resolution is too high. For example, frame extraction from 1080p videos can easily cause timeouts, so we recommend compressing videos to 720p or lower.

> The file resolution will be compressed in the model inference stage anyway, so using files of ultra high resolution does not help.

**Video compression tools and commands**

The following is an example command for compressing a video file to 720p. To download the FFmpeg tool, see [Download FFmpeg](https://ffmpeg.org/download.html).

```Bash
ffmpeg -i input.mp4 \
  -vf "scale=1280:720" \
  -c:v libx264 -crf 23 \
  -c:a aac -b:a 128k \
  output_720p.mp4
```

### File types

The Files API supports multiple file types, as described below.

|File types |File format |MIME type |
|---|---|---|
|Image |.jpg,.jpeg,.png,.gif,.webp,.bmp,.tiff,.ico,.icns,.sgi,.jp2,.heic,.heif |`image/jpeg`, `image/png`, `image/gif`, `image/webp`, `image/bmp`, `image/tiff`, `image/x-icon`, `image/x-icns`, `image/sgi`, `image/jp2`, `image/heic`, `image/heic` |
|Video |.mp4,.avi,.mov |`video/mp4`, `video/x-msvideo`, `video/quicktime` |
|Document |.pdf |`application/pdf` |
|Audio |.mp3,.wav,.aac,.m4a |`audio/mpeg`, `audio/wav`, `audio/aac`, `audio/x-m4a` |

## Manage files

### Retrieve a file

You can retrieve file information by file ID, such as file size, expiration time, MIME type, and file processing status. For details, see [Retrieve file](search-files-api.md).

> After a file is uploaded successfully, the preprocessing is automatically triggered. After the API returns `file_id`, you can call the retrieve file API to query the file processing status. Only when `status` changes to `active` can you use `file_id` in the Responses API and Chat API for multimodal understanding.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/files/file-20251119**** \
-H "Authorization: Bearer $ARK_API_KEY"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Retrieve file
response = client.files.retrieve(
    file_id="file-20251119****"
)

print(response)
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.RetrieveFile(ctx, "file-20251119****")
    if err != nil {
        fmt.Printf("get file status error: %v", err)
        return
    }
    fmt.Printf("file info: %v", fileInfo)

}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        // Retrieve the file metadata.
        FileObject fileMeta = service.retrieveFile("file-20251119****");
        System.out.println("Retrieve File:" + fileMeta);

        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.files.retrieve(
    file_id="file-20251119****"
)

print(response)
```

### List files

Use the Files API to query the list of uploaded files. For details, see [List files](list-files-api.md).

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
-H "Authorization: Bearer $ARK_API_KEY"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.files.list()

print(response)
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.ListFiles(ctx, &file.FilesListParams{})
    if err != nil {
        fmt.Printf("get file List error: %v", err)
        return
    }
    fmt.Printf("file List: %v", fileInfo)
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        FileListRequest request = new FileListRequest();
        FileListResponse listFiles = service.listFiles(request);
        System.out.println("List Files:" + listFiles);

        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.files.list()

print(response)
```

### Delete files

The Delete file API deletes a file by file ID and removes it from the storage space. For more information, see [Delete file](delete-files-api.md).

If you have frequent storage needs, you can manage storage space as follows. For details of storage limits, see [File storage limits](file-api.md#d75377d6).

* Shorten file storage duration: Successfully uploaded files are stored for 7 days by default. You can customize the storage validity period by using the **expire_at** parameter. The value range is 1 to 30 days. Files are automatically deleted after the storage validity period expires. For parameter settings, see [Upload file](upload-files-api.md).

* Manually call the delete API to clean up inactive files: Delete uploaded files through the Files API. The following is an example.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/files/file-20251014**** \
-X DELETE \
-H "Authorization: Bearer $ARK_API_KEY"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

if __name__ == "__main__":
    try:
        response = client.files.delete(
            file_id="file-20251014****"
        )
        print(response)
    except Exception as e:
        print(f"failed to delete response: {e}")
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.DeleteFile(ctx, "file-20251014****")
    if err != nil {
        fmt.Printf("delete file error: %v", err)
        return
    }
    fmt.Printf(" delete file: %v", fileInfo)
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        // Delete the file.
        FileDeleted deleteFile = service.deleteFile("file-20251014****");
        System.out.println("Delete File:" + deleteFile);

        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

if __name__ == "__main__":
    try:
        response = client.files.delete(
            file_id="file-20251014****"
        )
        print(response)
    except Exception as e:
        print(f"failed to delete response: {e}")
```

# Use File ID for multimodal understanding

When files are large or need to be reused across multiple requests, we recommend uploading files through the Files API, and then using file IDs in the Responses API or Chat API for multimodal understanding. For specific examples, see [Video understanding](multimodal-understanding.md#098ef3d4), [Image understanding](multimodal-understanding.md#70e09284), [Document understanding](multimodal-understanding.md#18a762a5), and [Audio understanding](audio-understanding.md).

After uploading a file, you must wait until file processing is complete (that is, when **status** is active) before you can use the corresponding file ID for analysis in the Responses API or Chat API. The following is a code sample for video understanding.

**cURL**

1. Upload the video file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
       -H "Authorization: Bearer $ARK_API_KEY" \
       -F 'purpose=user_data' \
       -F 'file=@/Users/doc/demo.mp4' \
       -F 'preprocess_configs[video][fps]=0.3'
   ```

2. Reference the File ID in the Responses API.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H 'Content-Type: application/json' \
     -d '{
         "model": "dola-seed-2-1-turbo-260628",
         "input": [
             {
                 "role": "user",
                 "content": [
                     {
                         "type": "input_file",
                         "file_id": "file-20251018****"
                     },
                     {
                         "type": "input_text",
                        "text": "Describe the sequence of actions performed by the person in the video and output the results in JSON format. Include start_time, end_time, event, and danger (boolean), and express timestamps in HH:mm:ss format."
                     }
                 ]
             }
         ]
     }'
   ```

**Python SDK**

```Python
import asyncio
import os
from arkruntime import AsyncArk
from arkruntime.types.responses.response_completed_event import ResponseCompletedEvent
from arkruntime.types.responses.response_reasoning_summary_text_delta_event import ResponseReasoningSummaryTextDeltaEvent
from arkruntime.types.responses.response_output_item_added_event import ResponseOutputItemAddedEvent
from arkruntime.types.responses.response_text_delta_event import ResponseTextDeltaEvent
from arkruntime.types.responses.response_text_done_event import ResponseTextDoneEvent

client = AsyncArk(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

async def main():
    # upload video file
    print("Upload video file")
    file = await client.files.create(
        # replace with your local video path
        file=open("/Users/doc/demo.mp4", "rb"),
        purpose="user_data",
        preprocess_configs={
            "video": {
                "model": "dola-seed-2-1-turbo-260628",
                "fps": 0.3,  # define the sampling fps of the video, default is 1.0
            }
        }
    )
    print(f"File uploaded: {file.id}")

    # Wait for the file to finish processing
    await client.files.wait_for_processing(file.id)
    print(f"File processed: {file.id}")

    stream = await client.responses.create(
        model="dola-seed-2-1-turbo-260628",
        input=[
            {"role": "user", "content": [
                {
                    "type": "input_video",
                    "file_id": file.id  # ref video file id
                },
                {
                    "type": "input_text",
                    "text": "Describe the sequence of actions performed by the person in the video and output the results in JSON format. Include start_time, end_time, event, and danger (boolean), and express timestamps in HH:mm:ss format."      
                }
            ]},
        ],
        caching={
            "type": "enabled",
        },
        store=True,
        stream=True
    )

    async for event in stream:
        if isinstance(event, ResponseReasoningSummaryTextDeltaEvent):
            print(event.delta, end="")
        if isinstance(event, ResponseOutputItemAddedEvent):
            print("\noutPutItem " + event.type + " start:")
        if isinstance(event, ResponseTextDeltaEvent):
            print(event.delta,end="")
        if isinstance(event, ResponseTextDoneEvent):
            print("\noutPutTextDone.")
        if isinstance(event, ResponseCompletedEvent):
            print("Response Completed. Usage = " + event.response.usage.model_dump_json())

if __name__ == "__main__":
    asyncio.run(main())
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "io"
    "os"
    "time"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fmt.Println("----- upload video data -----")
    data, err := os.Open("/Users/doc/demo.mp4")
    if err != nil {
        fmt.Printf("read file error: %v\n", err)
        return
    }
    fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
        Purpose: file.PurposeUserData,
        PreprocessConfigs: file.NewOptPreprocessConfigs(file.PreprocessConfigs{
            Video: file.NewOptVideo(file.Video{
                Model: file.NewOptString("dola-seed-2-1-turbo-260628"),
                Fps:   file.NewOptFloat64(0.3),
            }),
        }),
    }, data)

    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }

    // Wait for the file to finish processing
    for fileInfo.Status == file.StatusProcessing {
        fmt.Println("Waiting for video to be processed...")
        time.Sleep(2 * time.Second)
        fileInfo, err = client.RetrieveFile(ctx, fileInfo.ID) // Retrieve file info
        if err != nil {
            fmt.Printf("get file status error: %v", err)
            return
        }
    }
    fmt.Printf("Video processing completed: %s, status: %s\n", fileInfo.ID, fileInfo.Status)
    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
                        responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: "", FileID: responses.NewOptString(fileInfo.ID)})},
                        responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Describe the sequence of actions performed by the person in the video and output the results in JSON format. Include start_time, end_time, event, and danger (boolean), and express timestamps in HH:mm:ss format."})},
                    },
                ),
            },
        ),
    }
    createResponsesReq := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
    }

    resp, err := client.CreateResponsesStream(ctx, createResponsesReq)
    if err != nil {
        fmt.Printf("stream error: %v\n", err)
        return
    }
    var responseId string
    for {
        event, err := resp.Recv()
        if err == io.EOF {
            break
        }
        if err != nil {
            fmt.Printf("stream error: %v\n", err)
            return
        }
        handleEvent(event)
        if event.OneOf.Type == responses.ResponseCreatedEventResponseStreamEventSum {
            responseId = event.OneOf.ResponseCreatedEvent.Response.ID
            fmt.Printf("Response ID: %s", responseId)
        }
    }
}

func handleEvent(event *responses.ResponseStreamEvent) {
    switch event.OneOf.Type {
    case responses.ResponseReasoningSummaryTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseReasoningSummaryTextDeltaEvent.Delta.Or(""))
    case responses.ResponseReasoningSummaryTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated reasoning text: %s\n", event.OneOf.ResponseReasoningSummaryTextDoneEvent.Text.Or(""))
    case responses.ResponseTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseTextDeltaEvent.Delta.Or(""))
    case responses.ResponseTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated output text: %s\n", event.OneOf.ResponseTextDoneEvent.Text.Or(""))
    }
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import java.io.File;
import java.util.concurrent.TimeUnit;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example=====");
        // upload a video for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your video file path
                        .purpose(Purpose.USER_DATA)
                        .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().model("dola-seed-2-1-turbo-260628").fps(0.3).build()).build())
                        .build(), new File("/Users/doc/demo.mp4"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status:" + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for video to be processed...");
                TimeUnit.SECONDS.sleep(2);
                fileMeta = service.retrieveFile(fileMeta.getId());
            }
        } catch (Exception e) {
            System.err.println("get file status error: " + e.getMessage());
        }
        System.out.println("Uploaded file Meta: " + fileMeta);

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .stream(true)
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemVideo.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Describe the sequence of actions performed by the person in the video and output the results in JSON format. Include start_time, end_time, event, and danger (boolean), and express timestamps in HH:mm:ss format.").build()))
                        ).build())))
                .build();

        service.streamResponse(request)
                .doOnError(Throwable::printStackTrace)
                .blockingForEach(event -> {
                    if (event instanceof ResponseReasoningSummaryTextDeltaEvent) {
                        System.out.print(((ResponseReasoningSummaryTextDeltaEvent) event).getDelta());
                    }
                    if (event instanceof ResponseOutputItemAddedEvent) {
                        System.out.println("\nOutputItem " + (((ResponseOutputItemAddedEvent) event).getItem().getType()) + " Start: ");
                    }
                    if (event instanceof ResponseTextDeltaEvent) {
                        System.out.print(((ResponseTextDeltaEvent) event).getDelta());
                    }
                    if (event instanceof ResponseTextDoneEvent) {
                        System.out.println("\nOutputText End.");
                    }
                    if (event instanceof ResponseOutputItemDoneEvent) {
                        System.out.println("\nOutputItem " + ((ResponseOutputItemDoneEvent) event).getItem().getType() + " End.");
                    }
                    if (event instanceof ResponseFunctionCallArgumentsDoneEvent) {
                        System.out.println("\nFunctionCall Arguments: " + ((ResponseFunctionCallArgumentsDoneEvent) event).getArguments());
                    }
                    if (event instanceof ResponseCompletedEvent) {
                        System.out.println("\nResponse Completed. Usage = " + ((ResponseCompletedEvent) event).getResponse().getUsage());
                    }
                });


        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
import time
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

file = client.files.create(
    file=open("/Users/doc/demo.mp4", "rb"),
    purpose="user_data",
    extra_body={
        "preprocess_configs": {
            "video": {
                "model": "dola-seed-2-1-turbo-260628",
                "fps": 0.3,
            }
        }
    },
)
# Wait for the file to finish processing
while (file.status == "processing"):
    time.sleep(2)
    file = client.files.retrieve(file.id)
print(f"File processed: {file}")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Describe the sequence of actions performed by the person in the video and output the results in JSON format. Include start_time, end_time, event, and danger (boolean), and express timestamps in HH:mm:ss format.",
                },
            ]
        }
    ],
    stream=True
)


for event in response:
    if event.type == "response.reasoning_summary_text.delta":
        print(event.delta, end="")
    if event.type == "response.output_item.added":
        print("\noutPutItem " + event.type + " start:")
    if event.type == "response.output_text.delta":
        print(event.delta,end="")
    if event.type == "response.output_item.done":
        print("\noutPutTextDone.")
    if event.type == "response.completed":
        print("\nResponse Completed. Usage = " + event.response.usage.model_dump_json())
```

# Billing

File upload, file management, and other capabilities provided by the Files API do not incur fees.

Billing information for uploaded files varies by storage location as follows:

* Files stored in the default storage space managed by ModelArk: Each account has 20 GB of free storage. If this limit is exceeded, you cannot upload files. Delete files to free up storage space before uploading more files.

* Files stored in a user\-specified BytePlus TOS bucket: Fees are incurred for storage, traffic, requests, data retrieval, and other items. For detailed billing rules, see [Object hosting](https://docs.byteplus.com/en/docs/tos/Object_hosting#Mv2UN2Bn).

# Usage limits and error codes

* The Files API QPS rate limits and bandwidth limits are as follows.

   * Upload files: 20 QPS, 100 Mbps bandwidth

   * Retrieve files: 20 QPS

   * List files: 20 QPS

   * Delete files: 20 QPS

* Error codes: Click [Error codes](error-codes.md) for more information.
