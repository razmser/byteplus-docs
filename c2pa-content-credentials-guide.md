# C2PA Content Credentials Guide

> **Note**

> **This document explains the C2PA Content Credentials mechanism embedded by BytePlus ModelArk in eligible image and video generation outputs by default:**

* > **BytePlus ModelArk embeds C2PA Content Credentials by default for currently supported image and video generation model outputs.** No additional configuration is required from customers. After downloading the original file generated directly by ModelArk, users can upload it to [Content Credentials Verify](https://verify.contentauthenticity.org/) to view the declared source entity, AI tool, creation action, and related information.

* > **C2PA is used to verify the declared provenance and file integrity of digital content. It does not determine whether the depicted event is real, nor does it confirm content ownership, legality, or legal effect.** Screenshots, transcoding, compression, or processing by others may remove or invalidate the Content Credential.

# Overview

C2PA, short for Coalition for Content Provenance and Authenticity, is an **open technical standard** for digital media such as images, video, and audio. It embeds cryptographically signed metadata, commonly known as **Content Credentials**, into content to record creation, editing, transformation, and distribution history. This creates a provenance record that is traceable, verifiable, and capable of detecting certain forms of improper tampering.

The core C2PA data structure is the **Manifest**, which consists of the following three parts:

* **Claim:**  References the Assertions and the signature associated with the current content, and includes information used to bind the claim to the current file version.

* **Assertions:**  Records specific statements about the content, such as creation or editing actions, tools used, and content binding information for file integrity verification. The actual fields included depend on the product that generated or processed the content.

* **Claim Signature:**  A digital signature generated over the Claim by the signing entity using its private key. It is used to verify the issuer of the claim and detect whether the claim has been tampered with after signing.

For eligible generated outputs, including image generation and video generation, **BytePlus ModelArk embeds a C2PA Manifest by default**. This is zero\-configuration for customers: outputs returned through the ModelArk API already carry Content Credentials and can be checked using Content Credentials Verify after download.

> **Note**

> **Please note: detecting a valid C2PA Content Credential means that the credential's digital signature has passed validation, and the current file can be integrity\-checked against the content and provenance information declared in the credential.** This indicates that the relevant statements were issued by the corresponding signing entity and that the file has not undergone changes that would invalidate the credential.

> However, a valid Content Credential **does not mean the events shown in an image or video are necessarily true, nor does it confirm content accuracy, ownership, legality, or legal effect**. C2PA is primarily used to verify digital content provenance claims and processing history. It does not replace journalistic fact\-checking, rights review, forensic examination, or platform compliance assessment.

# Supported Model Scope

The following model list currently supports C2PA Content Credentials by default:

|Content Type |Models |
|---|---|
|Image generation |ByteDance\-Seedream\-4.0<br><br>ByteDance\-Seedream\-4.5<br><br>Dola\-Seedream\-5.0\-pro<br><br>Dola\-Seedream\-5.0\-lite |
|Video generation |Dreamina\-Seedance\-2.0<br><br>Dreamina\-Seedance\-2.0\-mini<br><br>Dreamina\-Seedance\-2.0\-fast |

# Verification Method

Content Credentials Verify is a official public verification tool provided by the Content Authenticity Initiative (CAI). It can be used directly in a browser, with no software installation or account registration required.

* **Verification entry:**  [https://verify.contentauthenticity.org/](https://verify.contentauthenticity.org/)

* **Supported formats:**  Common image and video files can be checked. The supported file formats and size limits are subject to the prompts shown on the Content Credentials Verify page.

* **Verification method:**  Upload a local file.

**Operation steps:**

1. Visit the Content Credentials Verify website.

2. Click the upload area in the center of the page and choose "Select a file from your device" or drag and drop the file anywhere on the page.

3. Wait a few seconds for parsing. The page will display the Content Credentials panel.

4. Expand the panel to view the fields in the Manifest, such as issuer, AI tool, and action history.

> **Tip**

> **Recommendation:** To obtain complete and stable verification results, upload the **original generated output file**, meaning the file returned directly by BytePlus ModelArk and not secondarily compressed, re\-encoded, or transcoded by a social platform. After secondary processing, the Content Credential may remain readable, may be removed or damaged, or may fail integrity verification depending on the processing method.

# Verification Result

After uploading a BytePlus ModelArk\-generated output to Content Credentials Verify, the Manifest displays the following core fields.

|Field |Sample Value |
|---|---|
|Content summary |This video was generated with an AI tool. |
|App or device used |certificate@byteplus.com |
|AI tool used |BytePlus_ModelArk |
|Actions |Created \- The asset was created. |
|Issued by |BytePlus Pte. Ltd. |

**Field\-by\-field explanation:**

* **Content summary:**  A human\-readable description written by the issuer to help end users quickly understand the nature of the content source, such as AI\-generated content.

* **App or device used:**  Identifies the service or producer used to generate the content. In this scenario, the displayed value is `certificate@byteplus.com`.

* **AI tool used:**  Identifies the AI service used to produce or process the content. For BytePlus ModelArk outputs, the displayed value is `BytePlus_ModelArk`. This field does not represent the specific model ID.

* **Actions:**  Records the core action performed on the asset. `Created` indicates newly created content. If secondary editing is supported in the future, values such as `Edited` may appear.

* **Issued by:**  The entity that digitally signs the Manifest and is used for signature verification. In the current overseas scenario, the issuer appear as `Byteplus Pte. Ltd.`.

**Typical use cases:**

* **Content provenance:**  Recipients can view the declared source entity, AI tool, and creation action associated with the content.

* **Compliance review:**  In AIGC content governance, platform review, and compliance audit scenarios, C2PA can serve as machine\-readable provenance information and an auxiliary signal for assessment.

* **Consumer disclosure:**  It can help present that the content was generated with an AI tool to end users. Whether this satisfies specific legal or platform requirements still needs to be assessed against applicable rules.

> **Note**

> **Privacy boundary:** C2PA credentials disclose the declared source entity, AI tool, and creation action of the content. **They do not include the user account, API Key, full prompt, or input materials.**

# Frequently Asked Questions (FAQ)

### Q1: What is C2PA? Does detecting C2PA mean the content is true?

**C2PA is an explicit Content Credential for digital content.**  It records provenance and history in the form of cryptographically signed metadata. It is **readable and verifiable**, but it may be lost during secondary compression or transcoding.

**Detecting C2PA does not mean the content is true.**  It means the provenance claim and file integrity can be verified. It does not make factual judgments about the people, events, or text shown in the content.

### Q2: Why does the verification result say "The Content Credential issuer couldn't be recognized. This file may not come from where it claims to."?

This message appears because **the verification tool does not currently recognize the signing credential used by ModelArk as trusted**. Information declared in the Manifest, including the issuer, AI tool used, and action history, can still be read normally.

It is important to note that an  **"unrecognized issuer" does not mean that digital signature validation has failed**. For the original files generated by ModelArk, **both the C2PA Claim signature and the cryptographic binding between the Manifest and the file content pass integrity validation.**  However, the verification tool does not yet display the issuer as trusted. ModelArk is working to establish the applicable trust recognition for its signing credentials.

### Q3: Why does verification fail?

If verification fails or Content Credentials Verify does not display Content Credentials for a BytePlus ModelArk output, common causes include the following. A failed verification result does not necessarily mean that the content was not generated by BytePlus ModelArk.

1. **The file was captured as a screenshot or saved after copying pixels:**  C2PA Content Credentials are written into the original media file. Screenshots, screen recordings, or pixel copying usually preserve only visual content and do not preserve the Content Credential in the original file. The new file may therefore no longer contain C2PA information.

2. **The file was processed by others:**  Social platforms, messaging tools, or other content processing services may compress, transcode, convert formats, or clean metadata, thereby removing C2PA information from the file.

3. **The model version is not within the supported scope:**  This document lists the supported model scope. Other models are not currently covered.
