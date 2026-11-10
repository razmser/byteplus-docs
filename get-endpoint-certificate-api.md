# GetEndpointCertificate

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetEndpointCertificate&Version=2024-01-01`

Gets the application\-layer encryption certificate chain for a specified inference endpoint. The client uses the chain for end\-to\-end encryption and decryption of request and response content before calling inference APIs. The certificate is issued by the Volcengine Private Certificate Authority (PCA) and includes a leaf certificate, an intermediate certificate, and a root certificate.

> **Note**

> The returned certificate chain must be used with the ModelArk encryption SDK (MaaS Crypto SDK). The certificate has a validity period defined by *NotBefore* and *NotAfter*. Shortly before the certificate expires, call this API again to obtain the latest certificate.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Inference endpoint ID

The inference endpoint ID. Call *ListEndpoints* to obtain it.

**Type** `string`  |  Certificate type

The certificate type. Only the default value is currently supported. You can omit this parameter.

## Response parameters

**NotAfter** `number`  |  Certificate expiration time

The certificate expiration time, represented as a Unix timestamp in seconds. After the certificate expires, it is no longer valid. Call this API again to obtain a new certificate.

**NotBefore** `number`  |  Certificate validity start time

The start of the certificate validity period, represented as a Unix timestamp in seconds. Validation fails if the certificate is used before this time.

**PCAHost** `string`  |  Certificate issuer

The name of the Private Certificate Authority (PCA).

**PCAInstanceCertificate** `string`  |  Leaf certificate (PEM)

The leaf certificate in PEM format. It is issued by the intermediate certificate and used by the client for encryption and decryption.

**PCAName** `string`  |  Certificate product name

The certificate product name, which identifies the encryption solution associated with the certificate.

**PCARootCACertificate** `string`  |  Root CA certificate (PEM)

The root CA certificate in PEM format. It is at the top of the chain of trust and validates the entire certificate chain.

**PCASubCACertificate** `string`  |  Intermediate CA certificate (PEM)

The intermediate CA certificate in PEM format. It establishes the chain of trust from the leaf certificate to the root certificate.
