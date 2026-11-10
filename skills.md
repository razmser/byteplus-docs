# Skills

Skills are reusable capability packs used to add domain knowledge, operation processes, and best practices to an agent. Unlike writing a large number of rules directly into `system`, skills are better suited for reusable and maintainable professional capabilities.

## Get skills

ModelArk Managed Agents supports uploading local skills through the `CreateSkill` API.

Two upload methods are currently supported:

* **File upload**: upload files from the same skill directory one by one while preserving the directory structure.

* **ZIP upload**: package an entire skill directory into a ZIP file and upload it.

> **Tip**

> Each request can create only one skill, and the skill files must contain exactly one `SKILL.md`.

#### Upload limits and directory recommendations

Prepare custom skills under the following rules:

* The total size of raw files uploaded in each request must not exceed **30 MiB**.

* A single version can contain up to **500** files.

* After ZIP extraction, each individual file must not exceed **30 MiB**, and the total size of all files must not exceed **120 MiB**.

* All files must be in the same top\-level directory, and that directory must directly contain exactly **1** `SKILL.md`.

We recommend using a unified top\-level directory for the ZIP package, with `SKILL.md` placed directly under the top\-level directory:

```text
basic_math.zip
└── basic_math/
    ├── SKILL.md
    └── meta.json
```

#### File upload

File upload is suitable for uploading files one by one in the build process. Pass in `files[]` repeatedly, and specify the path of each uploaded file in the skill in `filename=` for each parameter.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/skills \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -F "display_title=Web Artifacts Builder" \
  -F "files[]=@./basic_math/SKILL.md;filename=basic_math/SKILL.md;type=text/markdown" \
  -F "files[]=@./basic_math/scripts/init.sh;filename=basic_math/scripts/init.sh;type=text/plain"
```

Note the following:

* `files[]` is the parameter name used for file upload.

* The content after `@` is the actual local path.

* `filename=` specifies the path of the file in the skill after upload, for example, `basic_math/SKILL.md`.

* All `filename=` values must use the same top\-level directory, for example, `basic_math/`.

* `type=` is the MIME type and can be omitted.

#### ZIP upload

The ZIP method is more suitable for packaging and uploading the entire skill directory at once:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/skills \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -F "files=@./basic_math.zip;type=application/zip"
```

After the upload succeeds, the API returns a skill record. Pay attention to `id`, `latest_version`, `display_title`, and `source`:

```json
{
  "id": "skill-20260926143025-a1b2c",
  "object": "skill",
  "created_at": 1790433025,
  "description": "Perform basic arithmetic operations.",
  "latest_version": "1",
  "display_title": "basic-math",
  "source": "custom",
  "updated_at": 1790433025,
  "name": "basic-math"
}
```

Here, `id` is the `skill_id` to reference when mounting the skill to an agent later.

## Reference skills on an agent

After getting skills, you need to reference the skill in the `skills` parameter of `CreateAgent` or `UpdateAgent`.

First call `CreateSkill` to upload the skill. After you get the `skill_id`, write it to `skills`. If you need to specify a custom skill version, explicitly pass in `version`. Otherwise, the default version is usually used.

> **Tip**

> A single agent can be configured with at most 50 skills.

### `skills` parameter structure

In a `CreateAgent` or `UpdateAgent` request, each skill entry is declared through the `skills` array. For details about the parameter structure, see [Create agent](create-agent-api.md).

### Code samples

See the example below:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "DataProcessingAgent",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "skills": [
      {
        "type": "custom",
        "skill_id": "skill-20260812080348-****",
        "version": "1"
      }
    ]
  }'
```

Note the following:

* `skill-20260812080348-****` indicates the custom skill ID returned by `CreateSkill`.

## Usage tips

* Use custom skills for team\-private processes, internal standards, or custom operation manuals.

* Do not write one\-off task descriptions into a skill. A skill defines capabilities, while specific tasks should be passed in through session events.

* Before uploading, we recommend checking the path relationships between `SKILL.md`, scripts, and dependency files locally to avoid reference failures after upload.

## Related documents

[Agent](define-agent.md)

An agent is a configuration template that contains basic information, system prompt, and extended capabilities.

[MCP](mcp.md)

MCP (Model Context Protocol) is used to connect tools and data sources from third\-party systems to an agent.

[Tools](tools.md)

Tools determine which execution capabilities an agent can call in a session.

[Tool permission policies](tool-permission-policy.md)

Tool permission policies are used to control whether tool calls initiated by an agent are executed automatically or paused for confirmation from business applications.
