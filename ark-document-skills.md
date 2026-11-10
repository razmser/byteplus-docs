# ModelArk documentation Skills

This topic lists the Skills currently available from the ModelArk documentation.

<span id="skill-list"></span>
## Skill list


|Skill |Function |
|---|---|
|`sd25-pe` |Optimize prompts for Seedance 2.5 video generation |
|`sd2-pe` |Optimize prompts for Seedance 2.0 video generation |


<span id="install-skills"></span>
## Install Skills

Before installation, prepare Node.js 22.20.0 or later and npm. After installation, reopen the AI Agent session before using the Skill.

<span id="sd25-pe"></span>
### sd25\-pe

`sd25-pe` processes text, story segments, and multimodal reference assets. It organizes prompts for tasks such as video generation, keyframes, storyboards, video editing, audio editing, and video extension.

Run the following command to install the Skill:

```bash
npx --yes skills@latest add "https://arkdocs-en.tos-ap-southeast-1.volces.com/skills/" \
  --skill sd25-pe \
  --yes
```


After installation, enter the following in the AI Agent dialog box:

```text
/sd25-pe + your prompt
```


This Skill only generates or optimizes prompts. It does not submit video generation tasks. For information about writing Seedance 2.5 prompts, see [Dreamina Seedance 2.5 prompt guide](seedance-2-5-prompt-guide.md).

<span id="sd2-pe"></span>
### sd2\-pe

`sd2-pe` organizes the subjects, actions, scenes, camera movements, styles, and asset references in Seedance 2.0 video generation prompts based on the task type.

Run the following command to install the Skill:

```bash
npx --yes skills@latest add "https://arkdocs-en.tos-ap-southeast-1.volces.com/skills/" \
  --skill sd2-pe \
  --yes
```


After installation, enter the following in the AI Agent dialog box:

```text
/sd2-pe + your prompt
```


For information about Seedance 2.0 model capabilities and prompt rules, see [Dreamina Seedance 2.0 series tutorial](seedance-2-0.md).
