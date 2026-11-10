# Seedance-1.5-pro prompt guide

This article introduces the prompt usage methods and related techniques for Seedance 1.5 Pro, helping you more efficiently harness the model to generate high\-quality videos that meet your requirements.

<span id="f99a644f"></span>
# Model introduction & highlights

<span id="7923fd40"></span>
## Introduction

Seedance 1.5 Pro is a foundational model purpose\-built for **native, joint audio\-video generation**. It adopts a dual\-branch Diffusion Transformer architecture, combining a cross\-modal fusion module with a specialized multi\-stage data pipeline to deliver exceptional audio\-visual synchronization and high\-quality outputs.

<span id="d405d3a0"></span>
## Highlights


1. **High\-precision audio\-visual synchronization**: Delivers high\-fidelity, fully integrated audio\-visual outputs, supporting a wide range of sound types including environmental sounds, action effects, synthesized audio, musical instruments, background music, and vocals.

2. **Multi\-person and multi\-language dialogue**: Supports both monologues and multi\-speaker conversations with millisecond\-level lip\-sync precision. The model covers Mandarin and major Chinese dialects (including Cantonese, Shaanxi, and Sichuan), as well as English, Japanese, Korean, Spanish, and Indonesian, faithfully capturing the natural rhythm, articulation, and realism of real\-world conversations.

3. **Film\- and television\-grade narrative tension**: Delivers natural motion ranges with strong rhythmic flow and precise capture of motion details. The visuals exhibit high perceptual impact, with nuanced rendering of character emotions and expressions, significantly enhancing vividness and achieving a cinematic, film\- and television\-grade creative texture.


<span id="92cc038a"></span>
## Prompt parameters

In [Create video generation task API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1520757), the parameters related to the prompt are as follows:


* **Prompts (required)** : Support Chinese and English.

* **Model text command (optional)** : You can control video output specifications using parameters such as `resolution`, `ratio`, `duration`, `seed`, `camera_fixed`, and `watermark`.

   * Recommended way: Pass directly in the request body

      ```JSON
      ...
      // Strongly recommended
      // Specify the aspect ratio of the generated video as 16:9, duration as 5 seconds, resolution as 720p, seed as 11, and include a watermark. The camera is not fixed.
          "model": "doubao-seedance-1-5-pro-251215",
          "content": [
              {
                  "type": "text",
                  "text": "The kitten is yawning at the camera"
              }
          ],
          // All parameters must be written in full; abbreviations are not supported
          "resolution": "720p",
          "ratio":"16:9",
          "duration": 5,
          // "frames": 29, Either duration or frames is required
          "seed": 11,
          "camera_fixed": false,
          "watermark": true
      ...
      ```
      

   * Legacy way: Append \-\-[parameter] after the text prompt

      ```JSON
      ...
      // Specify the aspect ratio of the generated video as 16:9, duration as 5 seconds, resolution as 720p, seed as 11, and include a watermark. The camera is not fixed.
      "content": [
              {
                  "type": "text",
                  "text": "The kitten is yawning at the camera --rs 720p --rt 16:9 --dur 5 --seed 11 --cf false --wm true"
                  // "text": "The kitten is yawning at the camera --resolution 720p --ratio 16:9 --duration 5 --seed 11 --camerafixed false --watermark true"
              }
      ]
      ...
      ```
      


<span id="a6dc7747"></span>
## General techniques

**Prompt formula: Subject + Movement + Environment (optional) + Camera movement (optional) + Aesthetic description (optional) + Sound (optional)** 

> By detailing elements such as **dialogue content, language choices, emotional progression, camera movement, and narrative structure**, the model can generate audio and visuals that are more closely aligned, meeting the high demand for audio\-visual synchronization in professional production settings.


<span id="5951a1a6"></span>
### Basic principles


1. **Describe necessary information**



<span aceTableMode="list" aceTableWidth="3,3,2"></span>
|**Video Generation Example** |||
|---|---|---|
|Provide clear, constrained descriptions of the subject and motion |Specify the key visual cues the scene should convey |Use degree adverbs effectively |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/433a4f63bcc4422c8fd1c9b324f791ab" controls></video><br><br><br>> A **man with a weathered face and dressed in medieval pirate costumes** stands **on the black reef by the sea. The man's expression is passionate**, and he **raises his hands powerfully toward** the sky, revealing a desire for freedom. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/bdf2446ea317422e9a0e88c5422a2b36" controls></video><br><br><br>> In a violent storm, **huge waves** rolled up on the sea. The seawater rushed into the city and **destroyed houses on the shore**. Hundreds of citizens **fled in terror**. Eventually, the tsunami engulfed everything. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/234eed3946e3496db83caa093d873b92" controls></video><br><br><br>> The doll first **rotates slowly**, then she **stops rotating** and shows her cuteness in front of the camera. |



2. **Clearly described information**



<span aceTableMode="list" aceTableWidth="4,7"></span>
|**Video Generation Example** ||
|---|---|
|The prompt accurately aligns with both the visual content and the audio |Use feature\-based descriptions to define the subject consistently across the prompt |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/12ea1a27f3744e04af91640b4b0d0ec4" controls></video><br><br><br>> A model gracefully showcases the cheongsam she is wearing, exuding elegance and highlighting the garment's refined allure. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/01ebbeb3064345148e8d8d8291fb0afd" controls></video><br><br><br>> Inside a small street\-side restaurant, four people squeeze around a small table to eat: an Indian woman, a Black man, a white woman, and a Japanese man. Only drinks are placed on the table. The environment is noisy but warm, and the camera moves with a slight lateral slide. English dialogue:<br><br>> Indian woman: "This place looks sketchy, but the food smells amazing."<br><br>> Black man: "That's always a good sign."<br><br>> White woman: "I'm starving, I don't even care anymore."<br><br>> Japanese man: "Let's just order. Worst case, we laugh about it later." |


<span id="09baade3"></span>
### Sound generation

<span id="59b43290"></span>
#### **Dialogue/voiceover**


1. **High voice timbre stability**

> Seedance 1.5 Pro supports multiple emotional expressions, intonation patterns, and speech rates while maintaining the same underlying timbre.



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/97c9c78f5e6a40d1a64f4c135b1bb9db" controls></video><br><br><br>> In a calm emotional state, with an even tone and a normal speaking pace, say: "Let's begin with what matters most." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/4868f7524f494c5cac036525b784369b" controls></video><br><br><br>> In a gentle emotional state, with a soft tone and a slow speaking pace, say: "You can take your time. I'm not rushing you." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/263e6c7a26f74c5696493535c1be2da8" controls></video><br><br><br>> In a restrained emotional state, with a low tone and a very slow speaking pace, say: "If you want an answer, listen carefully." |



2. **Capable of responding to multiple languages and dialects**

> Supports multilingual conversations, including Chinese, English, Spanish, Japanese, Korean, and Indonesian.

> Supports a variety of Chinese dialects, including Cantonese, Sichuan dialect, Taiwanese accent, and Shaanxi dialect.



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|**Cantonese** |**Mandarin** |**Korean** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/660d427a15834fc58564e9d20e1a53fa" controls></video><br><br><br>> He said in Cantonese, "你好靓呀！，我好中意你呀" |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/9882f9c7961d4051bfb0db462784b9d1" controls></video><br><br><br>> He said in Mandarin: “你好漂亮呀，我好喜欢你呀” |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/38c50e04090740f9a880ff4a14196015" controls></video><br><br><br>> Inside a high\-tech laboratory, two researchers stand at a workbench operating analytical instruments. The cool light from the screens reflects across their faces. The camera gently slides from a front\-side angle, emphasizing the reflections on the equipment and the precision of their actions.<br><br>> **Korean dialogue**:<br><br>> A: "측정값이 불안정해. 다시 보정해야 해."<br><br>> B: "알겠어. 설정값을 바꿔볼게." |
|**Indonesian** |**Spanish** |**English** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/434a5353c0204f6ead536e6508d75416" controls></video><br><br><br>> On a wooden boardwalk by the sea, three Indonesian friends—two girls and one boy—sit together. The setting sun casts golden reflections across the surface of the ocean. The camera dolly in from a low angle toward the three of them, capturing their laughter and body language. The overall mood is warm and comforting.<br><br>> **Indonesian dialogue**:<br><br>> Girl 1: "Hari ini indah sekali, ya?"<br><br>> Girl 2: "Iya, seperti mimpi."<br><br>> Boy: "Dan kita di sini bersama. Itu yang paling penting." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/77a1c21484f04d5da5da8c84ddcb994c" controls></video><br><br><br>> On a rainy night, in a dim and rundown underground parking garage, two people meet briefly in the shadows beneath a concrete support pillar. The man in a trench coat hands over a sealed document envelope, his expression alert as his eyes scan the surroundings. Lowering his voice and speaking rapidly, he says: "La cosa está dentro. La contraseña es la fecha de nacimiento de tu madre." The woman takes it and responds: "Entendido. El próximo punto de contacto ha cambiado. Espera la señal segura." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/99cb5fb87b044038914881f50eb6f93f" controls></video><br><br><br>> In an office pantry, the atmosphere is relaxed with a touch of humor. A middle\-aged Indian man and a young Japanese male colleague stand beside the coffee machine. The Japanese man asks calmly, "What materials will be prepared for this afternoon's project?" The Indian man immediately responds in a fast\-paced voice with a strong accent, "Why did you only ask? Where is the competing product analysis report that the client wants? Hurry up and get it, it's due at two o'clock!" The Japanese man replies in a low voice, flustered and helpless, "I'll go right away, I was just editing the PPT…" Then he nods and exits the frame from one side. |



3. **In dialogue scenarios, lip movements can be accurately matched to each character.** 

> Accurately specify each character's personalized attributes (gender, age, clothing, actions).



<span aceTableMode="list" aceTableWidth="1,1"></span>
|**Video Generation Example** ||
|---|---|
|**Two\-person dialogue** |**Multi\-person conversation** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/05ab0f0c04d7425ebd521b8996e0dfad" controls></video><br><br><br>> In a warm, softly lit independent bookstore, two Americans—a man and a woman—stand shoulder to shoulder, flipping through the same book. The light falls across the pages and their faces. The camera makes an extremely subtle dolly\-in, creating a quiet and intimate atmosphere.<br><br>> **English dialogue**:<br><br>> Man: "Did you ever read this one before?"<br><br>> Woman: "No, but… I think I want to, with you." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/c83a9545cf4e436db30bccaaef054c2c" controls></video><br><br><br>> In a library filled with diffused warm light, four students sit around a long table discussing their project: a white female, a Black male, an Asian female, and a white male. The warm light falls across their side profiles and the tabletop. The camera moves with a slight lateral slide, presenting a quiet yet contemplative atmosphere.<br><br>> The group speaks in English:<br><br>> White female: "So… what's our next step?"<br><br>> Black male: "We need a clearer direction."<br><br>> Asian female: "Agreed. Let's break it down."<br><br>> White male: "Okay, let's start from the beginning." |



4. **Supports responsive voiceover**

> Seedance 1.5 Pro supports fine\-grained control over voiceover timbre, emotion, intonation, and speech rate—clear and precise descriptions are sufficient.



<span aceTableMode="list" aceTableWidth="1,1"></span>
|**Video Generation Example** ||
|---|---|
|**Documentary** |**Commercial** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/8b44a68b22544456a3de04ac8a8171bf" controls></video><br><br><br>> Generate a video with voiceover: A deep, calm male voice says, "In the vast silence of the universe, our world is but a fleeting moment. Yet, within it, life thrives against all odds." The scene should slowly transition from night to dawn, with the stars gradually disappearing and the sun rising from behind the mountains. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/fe5f523951be4c5ea72fdfb909d3bc95" controls></video><br><br><br>> Generate a video based on the input lipstick product keyframe image, keeping the appearance, proportions, and materials of both the lipstick and the model accurate and consistent, with no unnecessary elements added. The overall style is a high\-end e\-commerce beauty commercial, clean, refined, and premium in tone. The video consists of three continuous shots: in the first shot, the camera performs an extremely slow and smooth push\-in to showcase the lipstick's overall appearance and design details, with a rich, saturated lipstick bullet under soft, stable diffused warm lighting and a clean, blurred background; in the second shot, the view cuts to a close\-up of the model's face, with the model holding the lipstick near her face in a portrait\-style composition, featuring natural, luminous skin tone and a calm, confident expression, centered and stable to emphasize the connection between the product and the wearer; in the third shot, the camera cuts to a close\-up of the lipstick placed on a woman's tabletop, resting on a clean surface with a minimal surrounding environment, soft and delicate lighting, and a stable frame, reinforcing product texture and final brand recall. A clear, confident female commercial voice\-over with a refined tone and moderate speaking pace is synchronized with the visuals, delivering the following lines: "Rich color. Smooth texture. One swipe delivers radiant lips. Lightweight, comfortable, and effortlessly elegant." |


<span id="a23e0ba2"></span>
#### Sound effects (SFX)

> Seedance 1.5 Pro supports basic sound effect generation with direct audio\-visual output.



<span aceTableMode="list" aceTableWidth="1,1"></span>
|**Video Generation Example** ||
|---|---|
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/1f63bfdbec8749f794772f4fc801d231" controls></video><br><br><br>> Based on this image, generate a video that shows the rain outside the window getting slightly heavier, raindrops merging into streams and flowing down the glass, and a pedestrian with an umbrella hurrying past outside the window. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/7f9f395e69bb4e4bb4867192be7a99fe" controls></video><br><br><br>> At dusk, a large fuel depot exploded, with a fireball soaring into the sky. |


<span id="da0455b8"></span>
#### Background music (BGM)

> By default, Seedance 1.5 Pro automatically generates background music that matches the prompt.



<span aceTableMode="list" aceTableWidth="4,5,3"></span>
|**Video Generation Example** ||Background music mood control |
|---|---|---|
|Music style control |Prompt\-driven pacing control | |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/f48d949474a5469abae16174c9cf127e" controls></video><br><br><br>> A grand epic aerial video, with the camera passing through the magnificent mountains shrouded in clouds and ancient castles, accompanied by a heart\-stirring symphony as background music, featuring a theme melody full of strength and hope. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/62ce5702048b497da7e5797a02c9a12b" controls></video><br><br><br>> A cartoon character is in the center of the screen. The background music is a snippet of a fast\-paced pop song. The requirement is for this cartoon character to clap hands in time with the drumbeats of the music. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/8bbdd5cc48e14926b6a95a8eff649361" controls></video><br><br><br>> Gently brush one's fingers across the face of each person in the photo. The background music should be a gentle, nostalgic, and melodious guitar or piano solo, with a complex emotion of faint reminiscence intertwined with happiness, and a gentle touch of sadness about the passage of time beneath the warmth |


<span id="b6e289bc"></span>
### **Shot transition writing**


1. **Supports consistent style before and after camera switching**



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|**Disney\-style Animation** |**Pixar\-Style Animation** |**Realistic** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/9c24d9f790a44fd388896df997928bdf" controls></video><br><br><br>> The shot begins with a medium shot of both characters in frame. The girl turns to look at the boy and says with a confident smile, "We can do this!" The scene cuts to a close\-up of the boy as he replies hesitantly, "Are you sure?" The camera then cuts back to a medium close\-up of the girl. She turns around and opens her arms, saying in a light, upbeat tone, "Of course! Because we've already come this far." The camera settles naturally with her movement, the mood bright and resolute. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/6ecb827879b14d76bd8d151dad73fc0a" controls></video><br><br><br>> The shot opens on a medium shot of the father and son together. The boy lowers his head and says softly, "I just want to know if I disappointed you." After a brief silence, the father answers gently, "No." The scene then cuts to a close\-up of the boy as he lifts his head. Finally, the camera cuts back to the father, who smiles and says, "I just worry you'd be disappointed in yourself first." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/2712460ef13b4aee8829db0ed4595e34" controls></video><br><br><br>> The shot starts with a close\-up of the cat food bowl. Then it cuts to the second shot, which is a close\-up of the cat's face. It doesn't approach immediately but quietly watches. Next, the shot cuts to the third shot, which is a medium close\-up of the owner. She whispers, "This time it's a new one." Then the shot cuts to the fourth shot, returning to a close\-up of the cat's side. It slowly approaches, lowers its head to sniff, and finally starts to eat. Finally, it cuts to a close\-up of the owner. She softly says, "It seems to suit your taste." Finally, it cuts to a stable shot of the cat and the cat food in the same frame, with the pace slowing down. |



2. Supports reverse\-shot editing for dialogue scenes.



<span aceTableMode="list" aceTableWidth="1,1"></span>
|**Video Generation Example** ||
|---|---|
|**Double** |**Three people** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/1b909e5ba8f244d08f00847715a2a2ad" controls></video><br><br><br>> The shot starts with a medium close\-up of the detective, who says calmly, "You returned to that alley at 11:47, and that's no coincidence." The shot cuts to a close\-up of the suspect, who gives a soft sneer, averts his gaze, and says, "Coincidences always seem well\-planned in hindsight." The shot cuts back to an even closer close\-up of the detective, whose expression visibly turns cold but remains silent, and the tension in the air intensifies with each cut. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/bc7769dc55b3487ebc9a84077de1fb73" controls></video><br><br><br>> The shot starts with a medium shot of the three people together. The short man glances at his watch and says, "It's about time."<br><br>> Then the shot cuts to the medium close\-up of the girl in the middle. She frowns and replies, "Let's wait a little longer. They might arrive any minute."<br><br>> After that, the shot cuts to the third shot, a close\-up of the tall man. He looks towards the end of the street, his tone calm yet impatient: "We've been waiting for a long time."<br><br>> Finally, the shot cuts back to the medium shot of the three people together. Their gazes briefly meet, and no one says a word. The atmosphere is awkward. |



3. **Supports responding to shot switching timing**



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|**Visual Effects Transformation** |**Film & Television** |**Animation** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/9d6628b4a06946c782c834a3ecf8a43a" controls></video><br><br><br>> Shot 1: Medium shot. The character raises her hand and looks at her palm. Shot 2: Cut to a close\-up of the hand. Faint glowing particles begin to appear around the palm, slowly drifting in the air. Shot 3: Cut to a close\-up of the character's neck. The glowing particles spread upward. Shot 4: Cut to a medium shot of the character. As the particles continue to rise, a Christmas hat glowing with golden light appears on her head. Shot 5: Cut to a close\-up of her face. Wearing the Christmas hat, she smiles with satisfaction and says, "Merry Christmas." |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/c5c8a5bb620043f98c8a841ad77a8c3b" controls></video><br><br><br>> The shot starts with a medium\-long shot of the interior, where the natural light of the evening shines in through the window. An adult man stands alone by the window, holding a phone in his hand. The shot cuts to a medium shot, where he glances down at the phone screen, the light of which reflects on his face. The shot cuts to a close\-up of his hand, with his finger pausing on the screen without pressing the send button. The shot cuts to a close\-up of his profile, where he gently exhales and whispers, "I think I'll skip it." Finally, the shot cuts back to the medium\-long shot, where he puts the phone in his pocket, turns away from the window, the room falls silent again, and the frame freezes. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/de836bba8d724db29cb8d340e3ef02f2" controls></video><br><br><br>> Shot 1: The shot starts with a medium\-long shot from the rooftop, where two people stand together in the frame above the city illuminated by neon lights. The male character says, "This city eats people alive." Shot 2: The shot cuts to a close\-up of the female character, who sneers and replies, "Only if you let it." Shot 3: The shot cuts back to a close\-up of the male character, who turns to look at the other, his tone turns solemn: "You really think we can change it?" Shot 4: The shot cuts to a medium\-close shot of the female character, who steps forward, the neon light dancing on her face, and firmly says, "No. But we can survive it." Shot 5: Finally, the shot cuts back to a medium shot of the two people together in the frame |


<span id="613a1c39"></span>
## Advanced techniques

<span id="f3026c79"></span>
### **Aesthetic style**

> When aiming to generate videos with a distinct visual identity and harmonious audio\-visual coherence, consider specifying a clear aesthetic or stylistic reference in the prompt. Beyond descriptive language alone, providing an explicit reference helps the model more accurately capture the intended mood, tone, and overall artistic direction.



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|the style of the Japanese drama "Little Forest" |the style of Hayao Miyazaki's anime |the style of Disney's 2D animated movies |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/0e29d374075c4c99a05c949a09967756" controls></video><br><br><br>> Imitate the style of the Japanese drama "Little Forest" to generate a video of a girl picking apples in an orchard. The girl wears a pink plaid headscarf, has a sweet face, and carries a lite version canvas bag. She picks an apple from the tree, carefully wipes it clean, and then tastes the apple. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b29492b1b29a48e4951cafb7d9fcbf55" controls></video><br><br><br>> Imitate the style of Hayao Miyazaki's anime to generate a video of a girl picking apples in an orchard. The girl wears a pink plaid headscarf, has a sweet face, and carries a lite version canvas bag. She picks an apple from the tree, carefully wipes it clean, and then tastes the apple. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/5f5d30ca24714091849c9818d025c5f9" controls></video><br><br><br>> Referring to the style of Disney's 2D animated movies, generate a video of a girl picking apples in an orchard. The girl wears a pink plaid headscarf, has a sweet appearance, and carries a lite version canvas bag. She picks an apple from the tree, carefully wipes it clean, and then tastes the apple. |


<span id="baa52f9a"></span>
### Lens control

> Using photographic terminology correctly can improve the quality of camera movements and enhance the viewing experience of videos.


1. **Perspective**

* Camera Angle: High Angle / Low Angle / bird View /low\-angle shot / Eye\-level shot / high Angle shot, etc.



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|**High\-angle Position** |**Eye\-level Position** |**Low\-angle Position** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/37ce1a0bb4164764916457f14a664ff2" controls></video><br><br><br>> From a high vantage point, a static shot overlooks a tranquil forest. The autumn wind sweeps ginkgo leaves across the bluestone slabs, and the camera slowly pans in to focus on the bronze key half\-buried in the pile of fallen leaves. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/ed03a3e885f24122a4df9a0ca2fcfbb7" controls></video><br><br><br>> A medium shot from a flat position follows a skateboarding teenager, with a 45mm wide\-angle lens level with his shoulder. When the front wheel rolls over a puddle, water splashes horizontally across the frame. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/19fea3bcda8340c993e293237f4fc981" controls></video><br><br><br>> In the heavy rain, a homeless man hugged his knees tightly and curled up under a fire escape. The low\-angle camera shot the homeless man's expression from between his knees, with his face magnified in the wide\-angle distortion. As a thunderclap roared, the homeless man was startled and looked up at the sky, while the camera also panned up to the dark, rainy sky following his movement. |



* Narrative Perspective: Over\-the\-shoulder Perspective / xx Subjective View / Surveillance View / Telescope View / Ant Perspective / Peeping Perspective, etc.



<span aceTableMode="list" aceTableWidth="4,4,3"></span>
|**Video Generation Example** |||
|---|---|---|
|**Over\-the\-Shoulder** |**Telescope View** |**Monitoring fisheye perspective** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/a35649d51b994bb5a1222c46e4c23a75" controls></video><br><br><br>> In a British café, the camera shoots from behind character A's shoulder, focusing on the facial expression of character B across from them. The two converse in English by the café window, as character B slowly sets down the coffee cup in their hand. The camera gently dolly in forward in response to B's body language, while pedestrians walking outside the window appear softly blurred in the background. Light streams in from the side window, creating a rim light along character A's shoulder. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b66999aa9de243d9b2265183e018d935" controls></video><br><br><br>> The telescope reveals a minstrel in a black cloak walking towards them from across the bridge. First, notice a deep scar running across his eye and left cheek. A ceramic widget with a sun totem hangs around his neck, and a well\-used water bottle is tucked into his waist. Then, look at his boots, covered in a lot of dust and scratches, as if he has traveled a long way. Finally, look at the horse behind him; its eyes are deep and full of wisdom. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/be611dad1611455ebca1446bc2ef7d33" controls></video><br><br><br>> Fixed fisheye surveillance lens. In the center of the frame, a person is pacing anxiously in a closed room, and his figure appears relatively normal in the central area. However, the four walls, ceiling, and floor of the room are squeezed and bent towards the center under the fisheye effect, as if the entire space were collapsing towards him. |



* Subject Angle: Front / Profile / half\-profile / Back / Top / Bottom, etc.



<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|**Video Generation Example** |||
|---|---|---|
|**Profile** |**Back view** |**Frontal perspective** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/f41e04e06c8b42e791cdbc3b2dd65e99" controls></video><br><br><br>> The Profile of a young woman opened the curtains and saw the floor\-to\-ceiling windows, which were almost completely blocked by the thick striped fabric curtains. There were many green plants, and the interior of the gallery was dim and cold in color. The side of the window was a close\-up with a small depth of field. Through the gaps in the curtains, one could see the morning sunlight and Tyndall light outside the window. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/05bc77275a934e9dbd003f327fa95a75" controls></video><br><br><br>> A woman, wearing a white long windbreaker, with short hair and a well\-proportioned posture, stands on the edge of the city rooftop, overlooking the night view with her back to the camera. The camera starts from her close\-up back view and gradually zooms back, allowing more rooftop and city lights to enter the frame. The camera is required to be dolly out steadily and the light should not be overexposed. The woman's windbreaker moves gently with the environmental breeze, but her posture remains unchanged. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/3f3aced243a541c5acb5d615310a28b0" controls></video><br><br><br>> A man around thirty\-five years old with slightly wavy black hair stands in front of neon lights on a city street, facing the camera directly. He wears a knee\-length black trench coat, a dark gray turtleneck sweater, and leather gloves with metal buckles. His expression is calm yet oppressive. Neon reflections from the nighttime cityscape trace the contours of his face. The camera maintains a frontal perspective and slowly dolly in from a medium shot to a close\-up, with smooth motion, stable composition, steady lighting, and a natural rise and fall of his chest as he breathes. |



2. **View**

> Standard grammar when using shot size terms: Subject + Shot Size (e.g., Close\-up of the man on the left, A bust of the woman in red)


* Photography professional shot terms: wide shot/full shot/medium shot/close up shot/big close\-up, etc.

* Art professional shot terms: headshot/bust/half\-length portrait/full\-length portrait, etc.



<span aceTableMode="list" aceTableWidth="5,4,4"></span>
|**Video Generation Example** |||
|---|---|---|
|**Full shot/Wide Shot** |**Medium shot** |**Close\-up shot** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/0eaf3a234372460c90ff63dda613ec8d" controls></video><br><br><br>> In the desert sand, a traveler with a canvas backpack, wearing a robe and goggles, walks alone. The camera captures the vast horizon from a panoramic perspective, smoothly panning from left to right, with the traveler remaining small in the frame. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/d2b726cd1dbc42c0b8975dcacaf1ac9c" controls></video><br><br><br>> A short\-haired girl is sketching at the street corner, wearing a light\-colored shirt and a pinafore. The camera remains at medium shot, slowly circling from the front to the right front. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/de6e4d9978fe4c68b13b46ebee3572cb" controls></video><br><br><br>> The camera focuses on a white woman's lip area, fully encompassing the triangular region from the corner of her mouth to below her nose. She is wearing a rose\-colored lip makeup with a slight wet sheen on the surface. She is speaking a sentence, with the movement of her lips being small but the rhythm natural. The background is a blurred light spot created by soft light, not distracting enough. The camera maintains the close\-up composition and slowly zooms in on the lips. |



3. **Camera Movement**

> Shot movement description formula: Starting frame composition description + Shot movement + Shot movement amplitude + Ending frame composition description


* Camera Movements: dolly\-in/dolly\-out/Pan/track/Follow/Rise/Fall/Whirl/Rotate/Surround/Zoom,etc

* Camera movement methods can be combined, for example: Hitchcock shot = dolly\-in/out + zoom\-out/in; Bullet time shot = time slowdown + surround



<span aceTableMode="list" aceTableWidth="4,4,5"></span>
|**Video Generation Example** |||
|---|---|---|
|**Pan up** |**Hitchcock Zoom** |**dolly\-in** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/6a704171b9084c22b5ac65927dbc36bf" controls></video><br><br><br>> The elven teenager stands under a huge, glowing tree, points upward, then raises his head to look at the crown. The camera rises, and between the main branches of the tree is a nest made of twigs, inside which lies a large dinosaur egg emitting a mysterious glow. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/e86387dba71b4ad1852cbf4b7329a290" controls></video><br><br><br>> A close\-up shot shows a girl with glasses and well\-defined features, dyed short red hair, frowning, looking straight into the camera. The background is a dilapidated amusement park, with a stationary carousel and a water slide in the distance, the ground covered in dirt and overgrown with weeds, and the girl stubbornly biting her nails. Hitchcockian camera movement: Keep the girl's main composition unchanged, dolly out + increase the focal length of the lens. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/9bf4bef4dc344433bed98f0d873f35a5" controls></video><br><br><br>> The scene is a wet late\-night alley, with water stains on the ground reflecting light, and neon signs flashing alternately in blue and red. A man, about 35 years old, with short, slightly messy black hair and a light stubble, stands with his back against a brick wall, facing the camera head\-on. He is wearing a deep black leather trench coat (with slight abrasions on the surface) and a dark gray turtleneck sweater, with natural shadows forming on the fabric at his collarbone. His eyes are vigilant, his brows are furrowed, and his nostrils are slightly flaring. The camera starts with a medium shot (above the chest), slowly zooms in at a steady sliding speed, approaching his face, and finally reaches an extreme close\-up (showing only the area of his eyes and nose bridge). |


<span id="f61df85f"></span>
### Effects


|**Gameplay Effects** ||**Visual Effects** | |
|---|---|---|---|
|**Accurately describe the trigger timing** |**Accurately describe the transformation process** |**Accurately describe the details after transformation** |**Audio Design** |
|<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/16ee46894e3a4da1a7298b4d073d87eb" controls></video><br><br><br>> She inadvertently gently touched the old Christmas ball with her finger, and instantly, the inside of the ball lit up with a soft golden light like snowflake crystals. This light spread out from the ball like ripples, and wherever it reached, tiny light spots condensed in the air. The light first wrapped around the girl's entire body, her clothes were reshaped into Christmas attire, and her makeup was delicate; at the same time, the Christmas tree grew from the ground, the colored lights lit up one by one, and snowflakes condensed and fell out of thin air outside the window. The entire scene transformed into a Christmas\-themed bedroom |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b553f1f2e65f4d1faa9e0c258178cd38" controls></video><br><br><br>> The cat is wrapped in a soft and warm bubble halo, its body gradually elongating as it stands up. Its fur evolves into fluffy orange short hair, while its ears remain as cute cat ears, and its tail sways gently. Its clothing changes to a Japanese\-style casual sweatshirt and skirt. Finally, it transforms into an anime\-style girl with cat pupils and cat ears, making a "meow" gesture at the camera with a cute and playful expression. Please focus on depicting the cute continuity from the cat to the character's demeanor, with the transformation process being as soft and smooth as a marshmallow. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/0b5c65e71ddd4912af2d024ab3356fc5" controls></video><br><br><br>> Her pupil color changed from blue to red. Starting from the corner of her eye, her once delicate skin began to harden and bulge. Deep black dragon scales seemed to pierce through from beneath the skin, quickly spreading along the cheekbones towards the neck. Along with a small amount of dark red sparks spilling out from the gaps between the scales, half of her face completed the material transformation from human skin to hard dragon armor within two seconds. Dark Fantasy, Cthulhu style, body horror aesthetics, extremely realistic 8K material details. |<video src="https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/be1bc9f0208d43a49ba34c19db68d2d3" controls></video><br><br><br>> A warm beam of sunlight pierces through the dark clouds and shines precisely on the center point of the concrete wall. Taking the light spot as the center, the gray concrete surface instantly fades and softens. Fresh green moss and vines spread out wildly in all directions at the speed of time\-lapse photography. Immediately afterwards, countless colorful wildflowers burst into bloom on the vines. In just a few seconds, the once lifeless wall transforms into a vertical sea of flowers swaying in the wind. Solarpunk, with a Ghibli content style, is full of vitality, and the colors instantly shift from dull gray to highly saturated splendor. |
