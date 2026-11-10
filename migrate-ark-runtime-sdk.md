# Migrate to the official Ark SDK

The official ModelArk SDK provides a unified model invocation interface for Python, Go, and Java. This guide describes how to migrate from the legacy channel SDK to the latest official ModelArk SDK.

We recommend first installing the latest stable version in an isolated test environment and validating requests, streaming output, error handling, and the resource lifecycle. After validation succeeds, lock the version actually used in your production project's dependency file, and then remove the legacy dependency.

You can keep both the new and legacy SDKs during the migration. APIs not yet covered by the new SDK can continue to be used from the legacy SDK or the corresponding REST API. The two SDKs use different package names and import paths. Initialize their clients separately, and do not mix request or response types.

<span id="sdk-migration-packages"></span>
## Install the new SDK


* **Python**: Run `python -m pip install --upgrade arkruntime`. Official sources: [GitHub](https://github.com/volcengine/ark-runtime-python) and [PyPI](https://pypi.org/project/arkruntime/).

* **Go**: Run `go get github.com/volcengine/ark-runtime-go@latest`. Official source: [GitHub](https://github.com/volcengine/ark-runtime-go).

* **Java**: Use the latest stable version from Maven Central in your build file. Official sources: [GitHub](https://github.com/volcengine/ark-runtime-java) and [Maven Central](https://central.sonatype.com/artifact/com.volcengine/ark-runtime).


<span id="python"></span>
### Python

```bash
python -m pip install --upgrade arkruntime
```


```python
import os

from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
```


<span id="go"></span>
### Go

```bash
go get github.com/volcengine/ark-runtime-go@latest
```


```go
import (
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
)

client := arkruntime.NewClientWithApiKey(
    os.Getenv("ARK_API_KEY"),
    arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
)
```


<span id="java"></span>
### Java

Maven and Gradle production dependencies must use a specific version. Check the latest stable version on [Maven Central](https://central.sonatype.com/artifact/com.volcengine/ark-runtime), set `ark-runtime.version` to that version, and commit the locked value after validation succeeds.

Do not use `LATEST` or open version ranges in production builds.

```xml
<dependency>
  <groupId>com.volcengine</groupId>
  <artifactId>ark-runtime</artifactId>
  <version>${ark-runtime.version}</version>
</dependency>
```


```java
import com.volcengine.ark.runtime.service.ArkService;

ArkService service = ArkService.builder()
    .apiKey(System.getenv("ARK_API_KEY"))
    .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
    .build();
```


<span id="sdk-migration-changes"></span>
## Migration key points


* **Python**: Replace `byteplus-python-sdk-v2[ark]` and `byteplussdkarkruntime` with `arkruntime`.

* **Go**: Replace `github.com/byteplus-sdk/byteplus-go-sdk-v2/service/arkruntime` with `github.com/volcengine/ark-runtime-go/arkruntime`.

* **Java**: Replace `com.byteplus:byteplus-java-sdk-v2-ark-runtime` and `com.byteplus.ark.runtime.model.*` with `com.volcengine:ark-runtime` and `com.volcengine.ark.runtime.models.*`.


After replacing the dependencies, adjust your code as follows:


1. Use the client initialization method shown above and explicitly pass in the base URL provided in the documentation.

2. Replace placeholders such as the model ID, endpoint ID, and file ID with actual resources under the current account.

3. Update the import paths for request and response types. The Java generated model package uses `models`; create union types in Go requests using the constructors provided by the current SDK.

4. Check the termination conditions and empty\-content handling in streaming code. A normal stream end signal should not be reported as a request failure.

5. Iterate through output items from the Responses API by type, and do not assume that the first output item is always the final text.

6. For calls that involve a resource lifecycle, such as files, content generation, and Responses, use the resource ID created by the current request, and do not reuse historical IDs from examples.

7. Update parameters based on the type definitions and API documentation of the current SDK. Do not directly reuse generated types from the legacy SDK.


<span id="sdk-migration-gaps"></span>
## Use the legacy SDK for compatibility

The APIs supported by the SDK will be updated as versions change. Before migration, check API coverage in the repository README files for [Python](https://github.com/volcengine/ark-runtime-python), [Go](https://github.com/volcengine/ark-runtime-go), and [Java](https://github.com/volcengine/ark-runtime-java). If the current version does not yet provide the target API or required parameters, continue using the legacy SDK or the corresponding REST API, and migrate after the new SDK supports them.

<span id="sdk-migration-checklist"></span>
## Migration checklist


1. Install the latest stable version in an isolated environment.

2. Replace dependencies, imports, and client initialization code.

3. Use the common client initialization method and explicitly configure the base URL for the current channel.

4. Update request types, response parsing, and streaming termination handling.

5. Replace example placeholders with actual resources accessible under the account, and verify the API key and required permissions.

6. Validate synchronous, asynchronous, streaming, and resource lifecycle calls.

7. Lock the validated version in the dependency file, and then remove the corresponding legacy SDK calls.

8. Continue using the legacy SDK or REST API for APIs not yet covered by the new SDK.
