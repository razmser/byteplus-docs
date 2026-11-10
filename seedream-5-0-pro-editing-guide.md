# Seedream 5.0 pro / flash interactive editing guide

Dola Seedream 5.0 pro (hereinafter referred to as Seedream 5.0 pro) and Dola Seedream 5.0 flash (hereinafter referred to as Seedream 5.0 flash) both provide interactive editing capabilities and support precise image editing by specifying position coordinates in the prompt. You can mark positions on a reference image by using coordinate points or annotations to establish positional relationships. The models then perform edits based on the marked positions, enabling fine\-grained operations such as object replacement, element positioning, and partial repainting.

This document describes how to implement point\-based and bounding\-box\-based interactive editing with Seedream 5.0 pro and Seedream 5.0 flash. After a user uploads a reference image and specifies an edit position by selecting a point or drawing a bounding box, the frontend converts the selected position into **normalized coordinates**. The coordinate range is **0 to 999**, where the top\-left corner is 0,0 and the bottom\-right corner is 999,999. The frontend then marks the coordinates by using `<point>` or `<bbox>` and submits them together with the natural\-language prompt to the model. The model generates the edited image based on the reference image, coordinate positions, and text instructions.


<span aceTableMode="list" aceTableWidth="2.5,1"></span>
|Input image and bounding\-box selection |Preview |
|---|---|
|<video src="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream_5.0_editing_demo.mp4" controls></video><br><br><br>&nbsp;<br><br>> Convert the target area selected by the user into spatial coordinates, and assemble them into a prompt that the model can understand.<br><br>> <br><br>> Prompt: `Use the subject in Image 2 <bbox>118 331 933 871</bbox> to replace the subject in Image 1 <bbox>179 283 796 986</bbox>.` |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_5.0_output.jpeg) </span><br><br>&nbsp;<br><br>> Generate the edited image based on the assembled prompt and the reference image. |


<span id="key-steps"></span>
# Key step: Convert the target area into normalized coordinates

The model requires two key inputs: **the image to edit** and a **prompt** that contains normalized coordinates and editing instructions. Normalized coordinates map the point or bounding box selected on the image to a **1000 \* 1000 proportional coordinate system with values in the range [0,999]** . After the image width and height are divided into 1000 units, the top\-left corner of the image is `0,0`, and the bottom\-right corner is `999,999`.

**Coordinate formats supported in prompts**:


* **Point coordinates**: `<point>x y</point>`. This specifies a point, and the model determines the affected area.

* **Bounding\-box coordinates**: `<bbox>x1 y1 x2 y2</bbox>`. This specifies the top\-left and bottom\-right coordinates to precisely control the size of the edit area.


**How to process normalized coordinates**:


1. Obtain the location information: After the user selects a point or draws a bounding box, first obtain the relative coordinates of the point or box within the displayed image area. The coordinates are relative to the top\-left corner of the image.

   * Point: `x_px, y_px`, which indicates the click position.

   * Bounding box: `x1_px, y1_px, x2_px, y2_px`, which indicate the top\-left and bottom\-right corners of the box.

2. Convert the coordinates to normalized coordinates in the range 0 to 999: Convert the obtained location coordinates into integer coordinates in the range 0 to 999 based on the displayed width and height of the image.

   * Point:

      * `x = round(x_px / width * 1000)`

      * `y = round(y_px / height * 1000)`

      <div data-tips="true" data-tips-type="warning" data-tips-is-title="true" data-wrapper-indent="2">Note      </div>
      

      * <div data-tips="true" data-tips-type="warning" data-wrapper-indent="2"><code>x</code> and <code>y</code>: The normalized coordinates.      </div>
      

      * <div data-tips="true" data-tips-type="warning" data-wrapper-indent="2"><code>x_px</code> and <code>y_px</code>: The coordinates of the selected point relative to the top\-left corner of the image.      </div>
      

      * <div data-tips="true" data-tips-type="warning" data-wrapper-indent="2"><code>width</code> and <code>height</code>: The displayed width and height of the image on the canvas.      </div>
      

   * Bounding box:

      Convert `x1_px, y1_px, x2_px, y2_px` by using the same rule to obtain `x1 y1 x2 y2`.


<span id="supported-scenarios"></span>
# Scenarios

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">For information about how to explicitly specify the target object in multi\-subject scenarios, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/seedream-5-0-pro-editing-guide#usage">Usage instructions</a>.</div>



<span aceTableMode="list" aceTableWidth="2,1,3"></span>
|Scenario |Interaction mode |Prompt |
|---|---|---|
|Edit an object near a specified point |Point |`Replace the object at <point>520 460</point> in Image 1 with a crown.` |
|Edit an object in a specified area |Bounding\-box |`Replace the area <bbox>120 180 640 760</bbox> in Image 1 with a garden.` |
|Cross\-image editing |Point selection and bounding\-box |`Place the subject from Image 1 <bbox>179 283 796 986</bbox> at the position of Image 2 <bbox>118 331 933 871</bbox>, and replace the object at Image 2 <point>50 50</point> with a crown.` |


<span id="demo-and-overall-flow"></span>
# Demo and flow overview

<span id="demo-project"></span>
## Demo project

You can download the [TouchEdit demo](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/image-generation/touch_edit_demo_arkruntime-20260909.zip) to try interactive editing.

After extracting the package, install its dependencies with `python3 -m pip install -r requirements.txt`, set `ARK_API_KEY`, and run `bash run.sh`.

<span id="flow"></span>
## Flow overview

The following flowchart shows the complete process for implementing interactive editing based on the code example.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/image-generation/new-202607111020_en.svg) </span>

<span id="implementation"></span>
# Implementation

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">This document provides code snippets that demonstrate the key logic. The following snippets alone are not sufficient to implement the complete workflow. For the complete implementation, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/seedream-5-0-pro-editing-guide#demo-project">Demo project</a>.</div>


<span id="upload-image-to-canvas"></span>
## 0. Upload and place the image on the canvas

This step is mainly used to obtain the information of the image(s). In this example, the images uploaded by the user are converted into operable objects on the canvas. In addition to saving the image file itself, you must record the position, displayed size, original size of the images, and the total image count. This information provides the basis for subsequent point selection, bounding\-box selection, and model calls.

<span id="upload-image-to-canvas-logic"></span>
### Key logic

```JavaScript
function addImageFromFile(file, dataUrl) {
  const probe = new Image();
  probe.onload = () => {
    const maxSide = 360;
    const ratio = Math.min(1, maxSide / Math.max(probe.naturalWidth, probe.naturalHeight));
    const id = `img-${state.nextId}`;
    const image = {
      id,
      label: `Image ${state.nextId}`,
      name: file.name,
      dataUrl,
      element: probe,
      naturalWidth: probe.naturalWidth,
      naturalHeight: probe.naturalHeight,
      x: 80 + (state.nextId - 1) * 40,
      y: 80 + (state.nextId - 1) * 40,
      width: Math.round(probe.naturalWidth * ratio),
      height: Math.round(probe.naturalHeight * ratio),
    };
    state.nextId += 1;
    state.images.push(image);
    selectImage(id);
    setStatus(`${image.label} uploaded. You can drag it or add point or bounding-box annotations.`);
  };
  probe.src = dataUrl;
}
```


<span id="select-point-or-bbox"></span>
## 1. Convert the target area into normalized coordinates

This step converts the point selection or bounding\-box selection performed by the user on the canvas into coordinate location markers that can be used in the seedream\-5\-0\-pro prompt.

<span id="select-point-or-bbox-principle"></span>
### How it works

The area selected by a point or bounding box on the image must be converted into normalized coordinates.

The coordinate range is 0 to 999, where the top\-left corner is 0,0 and the bottom\-right corner is 999,999. This explicitly writes the area selected by the user into the prompt. For example:

```text
Replace the object at Image 1 <point>520 460</point> with a crown.
Replace the area <bbox>120 180 640 760</bbox> in Image 1 with a garden.
```



* **Types of Coordinate**

   
   <span aceTableMode="list" aceTableWidth="1,2,2"></span>
   |Coordinate type |Description |Usage |
   |---|---|---|
   |Client coordinates |The mouse position relative to the top\-left corner of the browser window, i.e., `event.clientX / event.clientY`. |Raw input from browser events. |
   |World coordinates |The logical coordinate system inside the canvas. The `x / y / width / height` values of an image are all stored in this coordinate system. |Ensures that the image can still be correctly selected after the canvas is zoomed or panned. |
   |Normalized coordinates |Coordinates normalized to 0 to 999 relative to a single image. |The final coordinates written into `<point>` or `<bbox>`. |
   

* **Coordinate conversion**

   1. Convert the mouse client coordinates into world coordinates to eliminate the impact caused by canvas position, panning, and zooming.

   2. Convert the world coordinates into normalized coordinates in the range 0 to 999 within the image. For more information, see [Key operation: Convert the target area into normalized coordinates](seedream-5-0-pro-editing-guide.md#key-steps).

      * Point selection mode: Generate `<point> x y</point>`.

      * Bounding\-box selection mode: Generate `<bbox> x1 y1 x2 y2</bbox>`.


<span id="select-point-or-bbox-logic"></span>
### Key logic


* Coordinate conversion

   ```JavaScript
   // Convert client coordinates to world coordinates.
   function clientToWorld(clientX, clientY) {
     const rect = viewport.getBoundingClientRect();
     return {
       x: (clientX - rect.left - state.view.x) / state.view.scale,
       y: (clientY - rect.top - state.view.y) / state.view.scale,
     };
   }
   
   // Convert world coordinates to normalized coordinates from 0 to 999.
   function normalizedPoint(worldPoint, image) {
     return {
       x: clamp1000(((worldPoint.x - image.x) / image.width) * 1000),
       y: clamp1000(((worldPoint.y - image.y) / image.height) * 1000),
     };
   }
   ```
   

* Generate `<point>` or `<bbox>` coordinate markers based on point selection or bounding\-box selection

   ```JavaScript
   if (state.mode === "point" && image) {
       const p = normalizedPoint(worldPoint, image);
       const ann = buildAnnotation("point", image, { x: p.x, y: p.y });
       ann.token = `${image.label}<point>${p.x} ${p.y}</point>`;
       state.annotations.push(ann);
       appendAnnotationChip(ann, image);
       renderImages();
       setStatus(`Added ${ann.token}`);
       return;
   }
   
   if (state.box) {
       const image = state.images.find((item) => item.id === state.box.imageId);
       const dx = Math.abs(state.box.current.x - state.box.start.x);
       const dy = Math.abs(state.box.current.y - state.box.start.y);
       state.box.selection.remove();
       if (image && dx > 4 && dy > 4) {
         const b = normalizedBox(state.box.start, state.box.current, image);
         const ann = buildAnnotation("bbox", image, b);
         ann.token = `${image.label}<bbox>${b.x1} ${b.y1} ${b.x2} ${b.y2}</bbox>`;
         state.annotations.push(ann);
         appendAnnotationChip(ann, image);
         setStatus(`Added ${ann.token}`);
       }
       state.box = null;
       viewport.releasePointerCapture(event.pointerId);
       renderImages();
   }
   ```
   


<span id="assemble-prompt"></span>
## 2. Assemble the prompt

This step combines the user's natural\-language input with the spatial coordinates generated from point selection or bounding\-box selection to form the final prompt that seedream\-5\-0\-pro can understand.

<span id="prompt-suggestions"></span>
### Prompt suggestions


<span aceTableMode="list" aceTableWidth="1,3"></span>
|Scenario |Reference format |
|---|---|
|Edit an object near a specified point |`Replace the object at Image 1 <point>520 460</point> with a crown.` |
|Edit an object in a specified area |`Replace the area of Image 1 <bbox>120 180 640 760</bbox> with a garden.` |
|Cross\-image editing |`Place the subject from Image 1 <bbox>179 283 796 986</bbox> at the position of Image 2 <bbox>118 331 933 871</bbox>.` |


<span id="assemble-prompt-logic"></span>
### Key logic

```JavaScript
// Build the final prompt from the editor and collect the images to submit.
function buildModelInputFromPrompt() {
  const assignedImages = new Map();
  const inputImages = [];

  // Map canvas images to model input labels and collect the referenced images.
  function assignImage(image) {
    if (!image) return "";
    if (!assignedImages.has(image.id)) {
      const inputLabel = `Image ${inputImages.length + 1}`;
      assignedImages.set(image.id, inputLabel);
      inputImages.push({ ...image, inputLabel });
    }
    return assignedImages.get(image.id);
  }

  // Remap image labels in the editor to the labels used in the submitted input.
  function remapImageLabels(text) {
    return text.replace(/Image\s+\d+/g, (label) => {
      const image = state.images.find((item) => item.label === label);
      return image ? assignImage(image) : label;
    });
  }

  // Combine editor text and coordinate annotations into the prompt.
  function walk(node) {
    if (node.nodeType === Node.TEXT_NODE) {
      return remapImageLabels(node.textContent || "");
    }
    if (node.nodeType !== Node.ELEMENT_NODE) {
      return "";
    }
    if (node.classList?.contains("annotation-inline")) {
      const ann = state.annotations.find((item) => item.id === Number(node.dataset.annotationId));
      const image = ann ? state.images.find((item) => item.id === ann.imageId) : null;
      const inputLabel = assignImage(image);
      if (!ann || !inputLabel) return "";
      return ` ${annotationTokenForLabel(ann, inputLabel)} `;
    }
    if (node.tagName === "BR") {
      return "\\n";
    }
    return [...node.childNodes].map(walk).join("");
  }

  const prompt = walk(promptInput)
    .replace(/\\u00a0/g, " ")
    .replace(/[ \\t]{2,}/g, " ")
    .replace(/\\n{3,}/g, "\\n\\n")
    .trim();

  return { prompt, images: inputImages };
}
```


<span id="generate-image"></span>
## 3. Generate an image

This step calls the Image Generation API based on the assembled prompt and input images, and returns the generated result to the frontend for display.

<span id="generate-image-logic"></span>
### Key logic


* The frontend submits an image generation request

   ```JavaScript
   generateBtn.addEventListener("click", async () => {
     const modelInput = buildModelInputFromPrompt();
     const resp = await fetch("/api/generate", {
       method: "POST",
       headers: { "Content-Type": "application/json" },
       body: JSON.stringify({
         prompt: modelInput.prompt,
         images: modelInput.images.map((image) => ({
           inputLabel: image.inputLabel,
           name: image.name,
           dataUrl: image.dataUrl,
           naturalWidth: image.naturalWidth,
           naturalHeight: image.naturalHeight,
         })),
       }),
     });
   
     const data = await resp.json();
     renderGeneratedImage(data.url);
   });
   ```
   

* The backend calls the image generation API

   ```Python
   ARK_MODEL = "dola-seedream-5-0-pro-260628"
   ARK_BASE_URL = "https://ark.ap-southeast.bytepluses.com/api/v3"
   
   try:
       client = Ark(
           api_key=os.getenv("ARK_API_KEY"),
           base_url=ARK_BASE_URL,
       )
       resp = client.images.generate(
           model=ARK_MODEL,
           prompt=prompt,
           image=image_arg,
           size="2K",
           output_format="png",
           response_format="url",
           watermark=True,
       )
   except Exception as exc:
       print(f"[generate] error: {exc!r}")
       return self._send_json(500, {"error": str(exc)})
   
   try:
       url = resp.data[0].url
   except (AttributeError, IndexError) as exc:
       return self._send_json(502, {"error": f"unexpected ModelArk response: {exc}"})
   
   return self._send_json(
       200,
       {
           "model": ARK_MODEL,
           "prompt": prompt,
           "url": url,
       },
   )
   ```
   


<span id="preview"></span>
### Output preview


<span aceTableMode="list" aceTableWidth="1,1"></span>
|Input image |Preview |
|---|---|
|<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_5.0_input_combined.png) </span><br><br>> Prompt: `Use the subject in Image 2 <bbox>118 331 933 871</bbox> to replace the subject in Image 1 <bbox>179 283 796 986</bbox>.` |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_5.0_output.jpeg) </span> |


<span id="usage"></span>
# Tips for specifying the subject

In multi\-subject scenarios, you can use the following approaches to specify the target object more clearly.


* Explicitly specify the target object when a bounding box contains multiple subjects.

   If the area covered by `<bbox>` contains multiple subjects or elements, we recommend adding a description in the prompt to specify the target object to edit, such as "the person on the left", "the cat wearing a hat", or "the flower in the foreground". This helps the model select the correct edit target more reliably.

   Example: `Replace the person on the left in Image 1 <bbox>120 180 640 760</bbox> with a robot.`

* Mark objects that must remain unchanged.

   If you want some objects to remain unchanged, you can also include them in bounding boxes and explicitly state "keep unchanged" or "do not modify" in the prompt.

   Example: `Replace the area Image 1 <bbox>120 180 640 760</bbox> with a garden, and keep the area Image 1 <bbox>700 120 920 360</bbox> unchanged.`


<span id="related-docs"></span>
# Related documents


* [Seedream 5.0 pro / flash tutorial](seedream-5-0-pro.md)

* [Image generation API](image-generation-api.md)
