# Install and upgrade SDK

ModelArk provides SDKs for Python, Go, and Java, making it easy to quickly call ModelArk model services in the corresponding programming languages.

For new projects, we recommend using the latest stable version of the official ModelArk SDK. For APIs that are not yet supported by the new SDK, you can continue to install and use the legacy SDK; the two SDKs use different package names and import paths, so they can coexist during migration. Initialize only the required client in the corresponding code path to avoid mixing request or response types.

# Python SDK

## Prerequisites

Python is installed locally, and the version is 3.8 or later.

> You can check the Python version in the terminal by running a command.

```Bash
python -V
```

> Python can be installed with [UV](https://docs.astral.sh/uv/), which can also be used to manage virtual environments. UV is an extremely fast Python package and project manager written in Rust. It makes environment isolation easier and avoids affecting the existing Python configuration on your system.

## Install Python SDK

We recommend installing the latest version of the official ModelArk SDK:

```Bash
python -m pip install --upgrade arkruntime
```

To use it in your code:

```Python
from arkruntime import Ark
```

To call APIs that are not yet supported by the new SDK, you can continue to install the legacy SDK:

```Bash
pip install 'byteplus-python-sdk-v2[ark]'
```

> **Tip**

> If a local installation error occurs, try the following methods:

* > Windows CMD does not support single quotation marks as quote characters. If you run the command in Windows CMD, use double quotation marks instead:

   > ```Bash
>    pip install "byteplus-python-sdk-v2[ark]"
>    ```
>       </div>
>
>
> * > You can also try installing with [uv](https://docs.astral.sh/uv/), which is faster:
>
>
>    <div data-tips="true" data-tips-type="tip">   ```Bash
>    uv pip install 'byteplus-python-sdk-v2[ark]'
>    ```

* > If you need to install from source, download and decompress the SDK package for the corresponding version, go to the directory, and run:

   <div data-tips="true" data-tips-type="tip">   ```Bash
   pip install .
   ```
      </div>
   


<span id="d6b883b8"></span>
## Upgrade Python SDK

Upgrade the official ModelArk SDK:

```Bash
python -m pip install --upgrade arkruntime
```

Upgrade the legacy SDK:

```Bash
pip install -U 'byteplus-python-sdk-v2[ark]'
```

# Go SDK

## Prerequisites

Check the Go version. Version 1.20 or later is required.

```Bash
go version
```

If Go is not installed or the version does not meet the requirement, visit the [official Go website](https://golang.google.cn/dl/) to download and install version 1.20 or later.

## Install Go SDK

1. The Go SDK uses go mod for management. You can run the following command to initialize go mod. Replace `<YOUR_PROJECT_NAME>` with the project name.

   ```Bash
   # If you open a terminal window in the ark-demo folder, run the command go mod init ark-demo.
   go mod init <YOUR_PROJECT_NAME>
   ```

2. After initializing go mod locally, run the following command to install the latest official ModelArk SDK.

   ```Bash
   go get github.com/volcengine/ark-runtime-go@latest
   ```

3. Import the official ModelArk SDK in your code.

   ```Go
   import "github.com/volcengine/ark-runtime-go/arkruntime"
   ```

   > **Tip**

   > To use a specific verified version, replace `latest` with a specific version number. For version information, see [ark\-runtime\-go Releases](https://github.com/volcengine/ark-runtime-go/releases).

To call APIs that are not yet supported by the new SDK, you can continue to install and import the legacy SDK:

```Bash
go get -u github.com/volcengine/volcengine-go-sdk
```

```Go
import "github.com/volcengine/volcengine-go-sdk/service/arkruntime"
```

4. After updating dependencies, run the command to tidy dependencies.

   ```Bash
   go mod tidy
   ```

## Upgrade Go SDK

* Upgrade the official ModelArk SDK to the latest version:

   ```Bash
   go get github.com/volcengine/ark-runtime-go@latest
   ```

* Upgrade the official ModelArk SDK to a specified version:

   ```Bash
   go get github.com/volcengine/ark-runtime-go@<VERSION>
   ```

* Upgrade the legacy SDK:

   ```Bash
   go get -u github.com/volcengine/volcengine-go-sdk@<VERSION>
   ```

# Java SDK

## Scope

This SDK applies only to Java server\-side development and does not support the Android platform. To use related features on the Android platform, customers must develop their own adaptation solution.

## Prerequisites

1. Check and install the Java version. Java 1.8 or later is required.

   ```Bash
   java -version
   ```

If Java is not installed or the version does not meet the requirement, visit the [official Oracle website](https://www.java.com/en/download/help/index_installing.html) to download and install a Java version suitable for your operating system. Make sure to select version 1.8 or later.

## Install Java SDK

The ModelArk Java SDK supports installation through Maven and Gradle.

### Install the official ModelArk SDK (recommended)

We recommend using the official ModelArk SDK for new projects. Get the latest stable version from [Maven Central](https://central.sonatype.com/artifact/com.volcengine/ark-runtime), and replace `<VERSION>` with that version number.

```XML
<dependency>
  <groupId>com.volcengine</groupId>
  <artifactId>ark-runtime</artifactId>
  <version><VERSION></version>
</dependency>
```

```Groovy
implementation "com.volcengine:ark-runtime:<VERSION>"
```

### Install the legacy SDK

To call APIs that are not yet supported by the new SDK, you can continue to use the following legacy SDK. Replace `<VERSION>` with the latest version number of the ModelArk Java SDK. SDK versions are available at: https://central.sonatype.com/artifact/com.volcengine/volcengine\-java\-sdk\-ark\-runtime. To install a specific SDK version, you can also replace it with the specific version number.

#### Install through Maven

Configure the `pom.xml` file as follows. For the complete configuration, see [Maven Central](https://central.sonatype.com/artifact/com.byteplus/byteplus-java-sdk-v2-ark-runtime):

```XML
...
<dependency>
  <groupId>com.byteplus</groupId>
  <artifactId>byteplus-java-sdk-v2-ark-runtime</artifactId>
  <version><VERSION></version>
</dependency>
...
```

#### Install through Gradle

Configure the `build.gradle` file as follows, and add the dependency in `dependencies`.

```plaintext
implementation 'com.byteplus:byteplus-java-sdk-v2-ark-runtime:<VERSION>'
```

## Upgrade Java SDK

### Upgrade the official ModelArk SDK

Replace `<VERSION>` with the latest stable version on Maven Central. After verification, lock this version in the build file.

### Upgrade the legacy SDK

For the legacy SDK, query and upgrade it as follows.

> **Tip**

> Obtain SDK version information and replace `<VERSION>` with the specified/latest version number. SDK versions are available at: https://github.com/byteplus\-sdk/byteplus\-java\-sdk\-v2/releases

Same as installing the Java SDK; just specify the version number to upgrade to.

# Third\-party SDK

The ModelArk model calling API is compatible with the OpenAI API protocol. You can use community SDKs of different programming languages that are compatible with the OpenAI API protocol to call ModelArk large models or applications. You can easily migrate model services to the ModelArk platform and the Dola models. For detailed instructions, see [Compatible with OpenAI API](compatible-with-openai-sdk.md).

# Related documents

[Common SDK usage examples](sdk-common-examples.md): Contains common SDK usage examples.
