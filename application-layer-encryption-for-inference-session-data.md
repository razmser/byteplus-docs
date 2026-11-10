# Inferential session data application layer encryption scheme

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">When you call the SDK, you only need to add one line of code to use this function for free. See <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1544136#23274b89">Data encryption</a> for example code.</div>


To ensure the secure transmission of inference session data, the ModelArk security team provides an end\-to\-end application\-layer encryption scheme for online inference, allowing users to encrypt inference session content locally using the SDK. This complements the default network\-layer encryption.

Why both network and application layer encryption? Think of it this way: network\-layer encryption is like putting a file in a safe, while application\-layer encryption is like adding a lock to that safe. This double layer of protection ensures that even if someone intercepts the file, they cannot open it.

<span id="a1bc4605"></span>
## **Scheme introduction**

The specific process of session data application\-layer encryption is shown in the following video:

Users can encrypt the inference session content locally using the SDK. After the encrypted ciphertext is securely transmitted to the sandbox, it's decrypted into plaintext only within the secure sandbox memory for model inference. After the model inference within the secure sandbox is complete, the generated results are encrypted by the secure sandbox and transmitted back to the user's local environment as ciphertext. Finally, the user decrypts the ciphertext locally using the SDK to obtain the plaintext inference results.

<span id="be035c68"></span>
## **Usage instructions**

The inference session data application\-layer encryption function is integrated into the BytePlus SDK. You can use this feature for free by adding just one line of code. See [Data encryption](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1544136#23274b89) for example code.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>



* <div data-tips="true" data-tips-type="tip"><strong>Language Support</strong>: Currently only supports Python SDK.</div>


* <div data-tips="true" data-tips-type="tip"><strong>Version Requirements</strong>: It is recommended to use the latest version of the SDK. The latest version of the SDK can be obtained through <code>pip install byteplus-python-sdk-v2</code>.</div>


* <div data-tips="true" data-tips-type="tip"><strong>Third\-Party Support</strong>: Does not support third\-party SDKs, including the OpenAI SDK.</div>


* <div data-tips="true" data-tips-type="tip"><strong>Capability Support</strong>: Supports Seed and Skylark text generation models. Supports single\-turn/multi\-turn conversations within Chat Completions, including streaming/non\-streaming and synchronous/asynchronous interfaces.</div>



<span id="cba262a7"></span>
## **Principle explanation**

Using Alice and Bob to represent the client SDK and the secure sandbox container where the inference model resides, the basic principle is to implement a 0\-RTT protocol (a protocol that allows the client to send encrypted payload in the first message) using ECIES. The protocol is as follows:


1. Alice obtains Bob's public key `PK[Bob]` through public channels. This public key must be an elliptic curve public key.

2. Alice generates a random number `r` and obtains `R = r × G` by multiplying it with the elliptic curve base point. `R` is the ciphertext of the session key. The session key `S = r × PK[Bob]`. Alice uses the session key `S` to derive an encryption key and encrypts the plaintext to obtain the ciphertext `Payload`. Alice sends `(R, Payload)` to Bob.

3. Bob receives `R` and decrypts the session key using his private key: `SK[Bob] × R = SK[Bob] × r × G = S`. Bob uses `S` to derive the decryption key and decrypts the ciphertext `Payload` to obtain the plaintext.


<span id="23eca6db"></span>
## Verification instructions


* **Verifying Message Encryption**:

   * **Action**: When the SDK is called, the `content` field will be encrypted and Base64 encoded.

   * **Verification**: Use a packet capture tool to capture HTTPS traffic and check if the session data in the request body is ciphertext.

* **Verifying Certificate Acquisition**:

   * **Action**: When initializing the client SDK with AK/SK, the `GetEndpointCertificate` interface will be called the first time a request is made for application\-layer encryption of session data for each inference endpoint to obtain the corresponding certificate.

   * **Verification**: You can view the call logs of the `GetEndpointCertificate` interface in Cloud Audit.


<span id="83d556f4"></span>
## **Certificate description**

This certificate is only applicable to BytePlus's Skylark series models (see [Model releases - 2025](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2172655) announcement) and is not applicable to models from other companies.

The elliptic curve public key obtained by the client from public channels comes from the self\-signed certificate of the Skylark model. All Skylark models use the same public key. The corresponding private key is managed by ModelArk's private CA service and uses the P\-256 elliptic curve.

The Skylark model's certificate can be obtained through the ModelArk OpenAPI using the `GetEndpointCertificate` interface.

Certificate Details:


|Certificate key algorithm ||ECDSA 256 |
|---|---|---|
|Certificate Signing Algorithm ||ECDSA\-SHA256 |
|The serial number of the certificate ||1BC6311337412676230446127358F676227CFEE3 |
|The subject information of the certificate |Country (C) |CN |
| |Company Name (O) |Volcano Engine Ark |
| |Department (OU) |Security |
| |City (L) |Beijing |
| |Province (ST) |Beijing |
| |Common Name (CN) |MaaS Crypto SDK Online |
|Certificate information |Leaf certificate |```Plain```<br>```-----BEGIN CERTIFICATE-----```<br>```MIICxjCCAm2gAwIBAgIUG8YxEzdBJnYjBEYSc1j2diJ8/uMwCgYIKoZIzj0EAwIw```<br>```gYQxITAfBgNVBAMMGE1hYVMgU0RLIFN1YiBDQSBFQ0MgUDI1NjEbMBkGA1UECgwS```<br>```Vm9sY2FubyBFbmdpbmUgQXJrMREwDwYDVQQLDAhTZWN1cml0eTELMAkGA1UEBhMC```<br>```Q04xEDAOBgNVBAgMB0JlaWppbmcxEDAOBgNVBAcMB0JlaWppbmcwHhcNMjQwMTAz```<br>```MDY1MjM4WhcNMjkwMTAzMDY1MjM4WjCBgjELMAkGA1UEBhMCQ04xEDAOBgNVBAgT```<br>```B0JlaWppbmcxEDAOBgNVBAcTB0JlaWppbmcxGzAZBgNVBAoTElZvbGNhbm8gRW5n```<br>```aW5lIEFyazERMA8GA1UECxMIU2VjdXJpdHkxHzAdBgNVBAMTFk1hYVMgQ3J5cHRv```<br>```IFNESyBPbmxpbmUwWTATBgcqhkjOPQIBBggqhkjOPQMBBwNCAATD7RyZC51FCwuK```<br>```xYKh2ZCwIvbNDvQfosUk3gKc0B/+euemDWDAaVazuBOm8okHsSmUrsEudeksRruG```<br>```LTGu1lBxo4G8MIG5MA4GA1UdDwEB/wQEAwIDiDBYBgNVHR8EUTBPME2gS6BJhkdo```<br>```dHRwOi8vY3JsLnZvbGNjYS5jb20vdm9sYzE/c249NURCNEUwNDNFRjQ3MEY3RDBB```<br>```REU0RDIyMTNBNTU1NTEzMjEyOEM3MzA4BggrBgEFBQcBAQQsMCowKAYIKwYBBQUH```<br>```MAGGHGh0dHA6Ly9vY3NwLnZvbGNjYS5jb20vdm9sYzEwEwYDVR0lBAwwCgYIKwYB```<br>```BQUHAwEwCgYIKoZIzj0EAwIDRwAwRAIgFSel9WBkyPmpdG6YzReb7ShCVKq3scT3```<br>```9T2mfBgooPkCIHpRalKvIJX5S9oSyTf6iQiUcwzfUHd47Ew+PUV7O6aq```<br>```-----END CERTIFICATE-----```<br> |
| |Intermediate root certificate |```Plain```<br>```-----BEGIN CERTIFICATE-----```<br>```MIIDCDCCAq+gAwIBAgIUXbTgQ+9HD30K3k0iE6VVUTISjHMwCgYIKoZIzj0EAwIw```<br>```gYUxIjAgBgNVBAMMGU1hYVMgU0RLIFJvb3QgQ0EgRUNDIFAyNTYxGzAZBgNVBAoM```<br>```ElZvbGNhbm8gRW5naW5lIEFyazERMA8GA1UECwwIU2VjdXJpdHkxCzAJBgNVBAYT```<br>```AkNOMRAwDgYDVQQIDAdCZWlqaW5nMRAwDgYDVQQHDAdCZWlqaW5nMB4XDTI0MDEw```<br>```MzA2NDUyMVoXDTM0MDEwMzE1NTk1OVowgYQxITAfBgNVBAMMGE1hYVMgU0RLIFN1```<br>```YiBDQSBFQ0MgUDI1NjEbMBkGA1UECgwSVm9sY2FubyBFbmdpbmUgQXJrMREwDwYD```<br>```VQQLDAhTZWN1cml0eTELMAkGA1UEBhMCQ04xEDAOBgNVBAgMB0JlaWppbmcxEDAO```<br>```BgNVBAcMB0JlaWppbmcwWTATBgcqhkjOPQIBBggqhkjOPQMBBwNCAATc+JkMHj6F```<br>```22yAIQWIDlbGikKHcy77VNfQRLi8HNCAipNfKktlocMJUD7uvaomfn3cvcI/YAEk```<br>```r1NA+OyINzeno4H7MIH4MBIGA1UdEwEB/wQIMAYBAf8CAQAwDgYDVR0PAQH/BAQD```<br>```AgGGMFgGA1UdHwRRME8wTaBLoEmGR2h0dHA6Ly9jcmwudm9sY2NhLmNvbS92b2xj```<br>```MT9zbj0yRjU0Q0U1NUJDMjI2MzEzQ0Q1RUI4NkZDQTU0RTZGOUFFOEIwMzQwMDgG```<br>```CCsGAQUFBwEBBCwwKjAoBggrBgEFBQcwAYYcaHR0cDovL29jc3Audm9sY2NhLmNv```<br>```bS92b2xjMTAdBgNVHQ4EFgQU53TjY9gSc5gAQDq4lrjIfOAj1DEwHwYDVR0jBBgw```<br>```FoAUjqqup74+HmschrVwON2NKvZtp9IwCgYIKoZIzj0EAwIDRwAwRAIgVrljBWvr```<br>```a4LfaYVKqRANxpqS6YLGLCHtDdnhYtim+ZICICbtI8mtHkKLxhqvJDEjf7GU3ssX```<br>```6Nq8pRWHxAmsx6DC```<br>```-----END CERTIFICATE-----```<br> |
| |Root certificate |```Plain```<br>```-----BEGIN CERTIFICATE-----```<br>```MIIDCzCCArCgAwIBAgIUL1TOVbwiYxPNXrhvylTm+a6LA0AwCgYIKoZIzj0EAwIw```<br>```gYUxIjAgBgNVBAMMGU1hYVMgU0RLIFJvb3QgQ0EgRUNDIFAyNTYxGzAZBgNVBAoM```<br>```ElZvbGNhbm8gRW5naW5lIEFyazERMA8GA1UECwwIU2VjdXJpdHkxCzAJBgNVBAYT```<br>```AkNOMRAwDgYDVQQIDAdCZWlqaW5nMRAwDgYDVQQHDAdCZWlqaW5nMB4XDTI0MDEw```<br>```MzA2NDQwOVoXDTM0MDEwMzE1NTk1OVowgYUxIjAgBgNVBAMMGU1hYVMgU0RLIFJv```<br>```b3QgQ0EgRUNDIFAyNTYxGzAZBgNVBAoMElZvbGNhbm8gRW5naW5lIEFyazERMA8G```<br>```A1UECwwIU2VjdXJpdHkxCzAJBgNVBAYTAkNOMRAwDgYDVQQIDAdCZWlqaW5nMRAw```<br>```DgYDVQQHDAdCZWlqaW5nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEgT4ZCpVh```<br>```Bas9kCTPnA22SoE4QGyK5VZiy6a5NQ/q99EZT2jxFyP8xqsKPUWAdike2l93uSAC```<br>```G7Jd4yK5nn58BqOB+zCB+DASBgNVHRMBAf8ECDAGAQH/AgEBMA4GA1UdDwEB/wQE```<br>```AwIBhjBYBgNVHR8EUTBPME2gS6BJhkdodHRwOi8vY3JsLnZvbGNjYS5jb20vdm9s```<br>```YzE/c249MkY1NENFNTVCQzIyNjMxM0NENUVCODZGQ0E1NEU2RjlBRThCMDM0MDA4```<br>```BggrBgEFBQcBAQQsMCowKAYIKwYBBQUHMAGGHGh0dHA6Ly9vY3NwLnZvbGNjYS5j```<br>```b20vdm9sYzEwHQYDVR0OBBYEFI6qrqe+Ph5rHIa1cDjdjSr2bafSMB8GA1UdIwQY```<br>```MBaAFI6qrqe+Ph5rHIa1cDjdjSr2bafSMAoGCCqGSM49BAMCA0kAMEYCIQCzOoAV```<br>```vy4XHibX6+8gi9KkGCG9WLzYPKMrI1BlFN7DNAIhAKEiFkaMn/tBuytndKduE2Bx```<br>```G2KI5sUSpiB8EEYvuoNP```<br>```-----END CERTIFICATE-----```<br> |
|Creation time of certificate ||1704264758 |
|Certificate expiration time ||1862117558 |
|The effective time of the certificate ||1704264758 |
|The validity period of the certificate ||5 years |


Leaf certificate information:

<Attachment link="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/d4ea207203ae48ef928935d6da33b6c5~tplv-goo7wpa0wc-image.image" name="certificate.pem">certificate.pem</Attachment>


Intermediate root certificate and root certificate information:

<Attachment link="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f62e5a90d74848c3a150734fc6521f2d~tplv-goo7wpa0wc-image.image" name="certificate_chain.pem">certificate_chain.pem</Attachment>


<span id="11423135"></span>
## **Performance information**

On a single core of an Intel(R) Xeon(R) Platinum 8260 CPU @ 2.40GHz, with a message length of 256 bytes, the average additional time spent on encryption and decryption for a single conversation request is 0.5786 ms.
