# Add real-human assets to asset library

> **Tip**

1. > The real\-person verification process may occasionally **be affected by lighting, angles, and other factors, resulting in failure**. If this occurs, guide the artist to try again.

2. > For security reasons, a face consistency check will be performed when adding materials, **which may not pass**. It is recommended to upload **clear, front\-facing image(s).**

3. > To protect artist permissions and facilitate subsequent management of their own portraits, real\-person verification currently requires logging in to a BytePlus account. **A single BytePlus account supports creating multiple real portraits or portraits of multiple people**.

## Overview

To ensure that creators can fully leverage the powerful video generation capabilities of Dreamina Seedance 2.0 (Seedance 2.0) and Dreamina Seedance 2.5 (Seedance 2.5) to efficiently generate video content, while mitigating potential risks associated with AI\-generated content, ModelArk has launched a private trusted asset library. Trusted assets that have been successfully added will be included in your private asset library and can be used for video generation.

The private asset library supports adding **real\-human portrait** assets. Through **real\-person verification**, the ownership of portrait rights is secured at the source, reducing the risk of subsequent portrait rights disputes. Human actors only need to authorize once; subsequent additions of makeup and styling do not require repeating the real\-person verification process, significantly reducing costs.

> * For more information about the public digital character library, please refer to [Digital character library](avatar-library.md).

## Add assets of human actor to the asset library

The real human asset library helps you compliantly use real people as actors in video generation with Seedance 2.0 and Seedance 2.5, greatly improving the quality and production efficiency of AI\-generated videos.

You can generate a QR code invitation for adding real\-human assets in the ModelArk Model Playground. The authorizing party (such as an artist) can quickly complete real\-person verification, upload their own assets via mobile, and authorize your account to use their image.

After authorization is complete, you can use the valid real\-human assets uploaded and authorized by the artist to generate videos.

### Procedure

Real\-human assets must be used only after the actor's consent and authorization. The asset upload process is as shown below:

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/bf975cbcc48a424399a12f10136ae25c~tplv-goo7wpa0wc-image.image)

#### **Asset user (e.g., a production company)** : Generate an authorization QR code in the ModelArk console

1. Go to the [ModelArk Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128), and click the **My assets** tab at the bottom \> **Real\-human** \> **Add real\-human assets \>**  Create an asset group. (**The account must complete real\-human or enterprise authentication**)

   ![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e1e41210c79041ca9e76d74dcba1ec89~tplv-goo7wpa0wc-image.image)

2. Set the authorization validity period and agree to the **Personal Information Processing Rules**.

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/4fe7b936bf86405fb3e468e39091cfbd~tplv-goo7wpa0wc-image.image)

3. Generate an invitation QR code.

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/a762012f921a47d98fa40b8ae51b560d~tplv-goo7wpa0wc-image.image)

#### **Authorizer (e.g., an actor)** : Complete real\-person verification, upload materials, and grant authorization

* Scan the QR code shared by the authorization initiator and log in to your personal BytePlus account.

* After confirming that the authorization subject and purpose are correct, agree to the facial information processing and authorization rules, and start real\-person verification.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/40f1437d4ef841be988638fe24f7927b~tplv-goo7wpa0wc-image.image)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/2bee434c7cd644c686a69387af491f92~tplv-goo7wpa0wc-image.image)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/753f83fadc02478593fc0baaa9ca6161~tplv-goo7wpa0wc-image.image)

* Add assets:

   * Fill in the asset name (required) and asset description.

   * Upload image and video files.

   * Recommended image styles:

      > **Tip**

      > **Full\-body reference image requirements**

      * > **Layout**: Portrait orientation

      * > **Content**: A front\-facing, full\-body image of the person.

      ![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/372f80d7ac3547bca195f4f5f1b59cb4~tplv-goo7wpa0wc-image.image)

      > **Tip**

      > **Facial close\-up image requirements**

      * > **Layout**: Portrait orientation

      * > **Content**: Front\-facing close\-up with a neutral expression, framed above the shoulders; the face should occupy about **two\-thirds** of the image.

      ![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/94dcbab909bd4020a92ea8e20d164d43~tplv-goo7wpa0wc-image.image)

* Specific format requirements:

   > **Tip**

   > **Images**

   * > Formats: jpeg, png, webp, tiff, gif, heic/heif

   * > Aspect ratio (W/H): (0.4, 2.5)

   * > Width/height (px): (300, 6000)

   * <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">Size: < 30 MB per image   </div>

   > **Videos**

   * > Formats: mp4, mov

   * > Resolution: 480p, 720p, 1080p, 4K

   * > Duration: [2, 30] seconds

   * > Dimensions:

      * > Aspect ratio (W/H): [0.4, 2.5]

      * > Width/height (px): [300, 6000]

      * > Total pixels (W×H):

         * > **Dreamina Seedance 2.5 series:** [614 × 664 = 407696, 4096 × 2160 = 8847360]

         * > **Dreamina Seedance 2.0 series:** [614 × 664 = 407696, 3326 × 2494 = 8295044]

   * > Size: ≤ 200 MB per video

   * > FPS: [24, 60]

   > **Audio**

   * > Formats: wav, mp3

   * > Duration: [2, 30] seconds

   * > Size: ≤ 15 MB per audio

   > Different models support different input audio and video durations. We recommend recording the duration of uploaded audio and video files before upload, so end users can identify which assets can be used with Dreamina Seedance 2.0 and Seedance 2.5 models.

* After passing the consistency check, the materials are stored in the database.

> **Tip**

* > A single asset group supports uploading different makeup assets for one real actor, and only one real\-person verification is required when creating the material group. Each time materials are uploaded, a consistency check will be performed. Materials that do not pass the check cannot be stored.

* > Uploading materials of different individuals to the same material group is not supported.

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/76a99fae6e9642b2b72cad682e23f00f~tplv-goo7wpa0wc-image.image)

**Caution**: After uploading is complete, the account corresponding to the QR code will be automatically authorized to use the asset.

#### **Asset user**: Accept the authorized asset

A dedicated asset group will be generated for each artist. The assets authorized by the artist will appear in the corresponding asset group for unified management.

1. In [ModelArk Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128), go to the real\-human asset library, view authorized materials pending receipt, click Accept and acknowledge the risk notice.

Upon acceptance, asset status will change to Active.

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/714a841d699b47c5b4ee964aa802be13~tplv-goo7wpa0wc-image.image)

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/12b5840c2daa434d899398ab705db145~tplv-goo7wpa0wc-image.image)

> **Tip**

> If you select **Reject**, the status of the material asset will change to **Rejected**, and the asset will not be available for use. Please proceed with caution.

2. You can filter materials by authorization status, and also use natural language or filters to find the required real human assets.

## Generate videos using real human assets

You can generate videos in the [ModelArk Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128), or by using the [video generation API](create-video-generation-task-api.md).

**Playground**: You can use real human assets for video generation in the Model Playground.

> **Tip**

> The actual available status of the asset is subject to **the information displayed on the asset detail page** *(Assets that do not pass the consistency check are currently also displayed on the asset list page)*.

![](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/75ee2f9c4a1f435797a085f9284a8ce1~tplv-goo7wpa0wc-image.image)

**Video generation API**:

* You must first obtain the Asset ID of the material to be used.

* When using the video generation API, you can concatenate the material URI following this format: `asset://<asset_id>`, and use the corresponding material in the API to generate videos.

For details, please refer to [Use preset digital characters](seedance-portrait-asset-guide.md#preset-avatar).

```JSON
            {
                "type": "image_url",
                "image_url": {
                    "url": "asset://asset-20260222234430-mxpgh"
                },
                "role": "reference_image"
            },
```
