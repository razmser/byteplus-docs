# Install and upgrade SDK

ModelArk provides SDKs for Python, Go, and Java, making it easy to quickly call ModelArk model services in the corresponding programming languages.

For new projects, we recommend using the latest stable version of the official ModelArk SDK. For APIs that are not yet supported by the new SDK, you can continue to install and use the legacy SDK; the two SDKs use different package names and import paths, so they can coexist during migration. Initialize only the required client in the corresponding code path to avoid mixing request or response types.

<span id="2708d57e"></span>
# Python SDK

<span id="f2baa8aa"></span>
## Prerequisites

Python is installed locally, and the version is 3.8 or later.

> You can check the Python version in the terminal by running a command.


```Bash
python -V
```


> Python can be installed with [UV](https://docs.astral.sh/uv/), which can also be used to manage virtual environments. UV is an extremely fast Python package and project manager written in Rust. It makes environment isolation easier and avoids affecting the existing Python configuration on your system.


<span id="bb014324"></span>
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


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If a local installation error occurs, try the following methods:</div>



* <div data-tips="true" data-tips-type="tip">Windows CMD does not support single quotation marks as quote characters. If you run the command in Windows CMD, use double quotation marks instead:</div>


   <div data-tips="true" data-tips-type="tip">   ```Bash
   pip install "byteplus-python-sdk-v2[ark]"
   ```
      </div>
   

* <div data-tips="true" data-tips-type="tip">You can also try installing with <a href="https://docs.astral.sh/uv/">uv</a>, which is faster:</div>


   <div data-tips="true" data-tips-type="tip">   ```Bash
   uv pip install 'byteplus-python-sdk-v2[ark]'
   ```
      </div>
   

* <div data-tips="true" data-tips-type="tip">If you need to install from source, download and decompress the SDK package for the corresponding version, go to the directory, and run:</div>


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


<span id="f116fb9f"></span>
# Go SDK

<span id="0fa8c2bc"></span>
## Prerequisites

Check the Go version. Version 1.20 or later is required.

```Bash
go version
```


If Go is not installed or the version does not meet the requirement, visit the [official Go website](https://golang.google.cn/dl/) to download and install version 1.20 or later.

<span id="ae8b42ab"></span>
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
   

   <div data-tips="true" data-tips-type="tip" data-tips-is-title="true" data-wrapper-indent="1">Tip   </div>
   

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">To use a specific verified version, replace <code>latest</code> with a specific version number. For version information, see <a href="https://github.com/volcengine/ark-runtime-go/releases">ark\-runtime\-go Releases</a>.   </div>
   

To call APIs that are not yet supported by the new SDK, you can continue to install and import the legacy SDK:

```Bash
go get -u github.com/byteplus-sdk/byteplus-go-sdk-v2
```


```Go
import "github.com/byteplus-sdk/byteplus-go-sdk-v2/service/arkruntime"
```



4. After updating dependencies, run the command to tidy dependencies.

   ```Bash
   go mod tidy
   ```
   


<span id="f0739bb0"></span>
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
   go get -u github.com/byteplus-sdk/byteplus-go-sdk-v2@<VERSION>
   ```
   


<span id="e7ae2925"></span>
# Java SDK

<span id="41f31f3c"></span>
## Scope

This SDK applies only to Java server\-side development and does not support the Android platform. To use related features on the Android platform, customers must develop their own adaptation solution.

<span id="e3518e9f"></span>
## Prerequisites


1. Check and install the Java version. Java 1.8 or later is required.

   ```Bash
   java -version
   ```
   


If Java is not installed or the version does not meet the requirement, visit the [official Oracle website](https://www.java.com/en/download/help/index_installing.html) to download and install a Java version suitable for your operating system. Make sure to select version 1.8 or later.

<span id="ae8db863"></span>
## Install Java SDK

The ModelArk Java SDK supports installation through Maven and Gradle.

<span id="install-the-official-modelark-sdk-recommended"></span>
### Install the official ModelArk SDK (recommended)

We recommend using the official ModelArk SDK for new projects. Get the latest stable version from [Maven Central](https://central.sonatype.com/artifact/com.volcengine/ark-runtime), and replace `<VERSION>` with that version number.

```XML
<dependency>
  <groupId>com.byteplus</groupId>
  <artifactId>ark-runtime</artifactId>
  <version><VERSION></version>
</dependency>
```


```Groovy
implementation "com.volcengine:ark-runtime:<VERSION>"
```


<span id="install-the-legacy-sdk"></span>
### Install the legacy SDK

To call APIs that are not yet supported by the new SDK, you can continue to use the following legacy SDK.

<span id="db12484d"></span>
#### Install through Maven

Configure the `pom.xml` file as follows. For the complete configuration, see [Maven Central](https://central.sonatype.com/artifact/com.byteplus/byteplus-java-sdk-v2-ark-runtime):

```XML
...
<dependency>
  <groupId>com.byteplus</groupId>
  <artifactId>byteplus-java-sdk-v2-ark-runtime</artifactId>
  <version>LATEST</version>
</dependency>
...
```


<span id="4858e8c3"></span>
#### Install through Gradle

Configure the `build.gradle` file as follows, and add the dependency in `dependencies`.

```Plaintext
implementation 'com.byteplus:byteplus-java-sdk-v2-ark-runtime:LATEST'
```


<span id="4ab4182d"></span>
## Upgrade Java SDK

<span id="upgrade-the-official-modelark-sdk"></span>
### Upgrade the official ModelArk SDK

Replace `<VERSION>` with the latest stable version on Maven Central. After verification, lock this version in the build file.

<span id="upgrade-the-legacy-sdk"></span>
### Upgrade the legacy SDK

For the legacy SDK, query and upgrade it as follows.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Obtain SDK version information and replace 'LATEST' with the specified/latest version number. SDK version numbers can be found at: https://github.com/byteplus\-sdk/byteplus\-java\-sdk\-v2/releases</div>


Same as installing the Java SDK; just specify the version number to upgrade to.

<span id="6f32c555"></span>
# Third\-party SDK

The ModelArk model calling API is compatible with the OpenAI API protocol. You can use community SDKs of different programming languages that are compatible with the OpenAI API protocol to call ModelArk large models or applications. You can easily migrate model services to the ModelArk platform and the Dola models. For detailed instructions, see [Compatible with OpenAI API](compatible-with-openai-sdk.md).

<span id="4b8511f6"></span>
# Related documents

[Common SDK usage examples](sdk-common-examples.md): Contains common SDK usage examples.
