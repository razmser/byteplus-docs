# Read documentation with AI agents

Use the ModelArk `llms.txt` index to discover documentation and fetch only the required Markdown content. When the target page is known, fetch its Markdown mirror. Use the HTML page only as a fallback.

## Choose a retrieval method

|Priority |Method |Use case |Description |
|---|---|---|---|
|1 |[llms.txt files](read-ark-docs-with-llms-txt.md) |The agent needs to discover relevant pages or retrieve bulk content |Use `llms.txt` to discover documents. Use `llms-full.txt` or a full\-section file for a knowledge base or offline retrieval. |
|2 |[Markdown mirror](read-docs-from-urls.md#read-markdown) |The target page URL is known and only the document body is needed |Append `.md` to the documentation page URL to receive `text/markdown` without navigation and styling code from the HTML page. |
|3 |[HTML page](read-docs-from-urls.md#read-html) |The agent can only use a web\-reading tool, or no Markdown mirror is available |Web\-reading tools can usually extract the body, but raw HTML also includes navigation, scripts, styles, and page metadata that add irrelevant context. |

## Use the documentation retrieval Skill

Install `ark-docs-reader` so that the Skill selects the retrieval path for the target site:

* ModelArk: Use the ModelArk `llms.txt` index and matched English Markdown documents, keeping every source within the current site.

Before installation, prepare Node.js 22.20.0 or later and npm. The Node.js installer includes `npx`. Run the following command to install `ark-docs-reader`:

```Bash
npx --yes skills@latest add "https://arkdocs-en.tos-ap-southeast-1.volces.com/skills/" \
  --skill ark-docs-reader \
  --yes
```

After installation, ask the agent to retrieve documentation for the target site. For example:

```text
Use ark-docs-reader to find the latest ModelArk documentation about structured output with the Responses API, and list the sources.
```

## Configure retrieval rules

Add the following rules to the system prompt of an agent.

```text
Before answering product questions, read the latest official documentation in this order:
The current site documentation URL prefix is https://ai.byteplus.com/ark/region:ap-southeast-1/docs.
1. Fetch https://ai.byteplus.com/ark/llms.txt
   and locate relevant pages by title and summary, then fetch their Markdown links.
2. For a knowledge base or offline retrieval, fetch
   https://ai.byteplus.com/ark/llms-full.txt or a Full section link in llms.txt.
   Do not inject the complete full-site file into one conversation.
3. When a documentation slug is known, fetch https://ai.byteplus.com/ark/region:ap-southeast-1/docs/<slug>.md first.
4. If the Markdown mirror is unavailable, fetch https://ai.byteplus.com/ark/region:ap-southeast-1/docs/<slug>. Retain only the
   article title, body, code blocks, tables, and links. Ignore navigation,
   scripts, styles, the footer, and interactive controls.
5. Base the answer on the retrieved official documentation instead of prior knowledge alone.
```

## Follow the recommended flow

1. Fetch [llms.txt](https://ai.byteplus.com/ark/llms.txt) and locate relevant pages by title and summary.

2. For a knowledge base or offline retrieval, fetch [llms-full.txt](https://ai.byteplus.com/ark/llms-full.txt) or a full\-section file.

3. When the target slug is known, use Quick start as an example and fetch [Quick start Markdown](quick-start.md).

4. If the Markdown mirror is unavailable, fetch the [Quick start HTML page](quick-start.md) and retain only the article body.

## Related documents

* Discover pages or retrieve a section or the full site. See [llms.txt](read-ark-docs-with-llms-txt.md).

* Read a Markdown mirror or an HTML page directly. See [From URLs](read-docs-from-urls.md).

* Install capabilities provided with the documentation. See [Documentation Skills](ark-document-skills.md).
