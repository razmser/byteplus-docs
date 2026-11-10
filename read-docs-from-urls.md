# Read documentation from URLs

When a documentation page URL is known, an AI agent should try `<DOC_URL>.md` for a Markdown mirror first. If the mirror is unavailable, fetch the HTML page. A Markdown mirror primarily contains the article body, while HTML also includes navigation, scripts, and styles that require additional extraction.

## Compare URL types

|URL type |Response content |Advantage |Limitation |
|---|---|---|---|
|Markdown mirror |Markdown text |Primarily contains the title and article body, suitable for direct agent context. |Requires a known documentation page and a site that provides `<DOC_URL>.md`. |
|HTML page |`text/html` |An agent with a web\-reading tool can fetch it without changing the URL. |Raw HTML also contains navigation, scripts, styles, and page metadata, which consume context and affect article extraction. |

## Read a Markdown mirror

Append `.md` to a documentation page URL. For example:

* **Page URL**: [Quick start](quick-start.md).

* **Markdown URL**: `https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start.md`.

Markdown mirrors are suitable when:

* The agent obtained the target page from site navigation, search results, or user input.

* The task needs only the article body, without navigation or interactive components.

* The documentation needs to be added to conversation context or stored as a text corpus.

## Read an HTML page

An agent with web access and article extraction can fetch the page URL directly. The HTML response contains the complete article along with site navigation, interactive scripts, styles, and page metadata.

When reading an HTML page, the agent should:

1. Extract the article title and body.

2. Ignore site navigation, the footer, sign\-in entry points, and interactive controls.

3. Ignore scripts, styles, and their configuration data.

4. Preserve code blocks, tables, and link semantics in the article.

Article extraction quality varies by web\-reading tool. If a tool returns raw HTML, do not inject the complete response into model context.

## Configure direct\-reading instructions

Add the following rule to the system prompt of an agent:

```text
When a documentation page URL is known, fetch <DOC_URL>.md first.
If <DOC_URL>.md is unavailable, fetch <DOC_URL> for the HTML page.
For HTML pages, retain only the article title and body, and ignore navigation,
scripts, styles, the footer, and interactive controls.
```

## Related documents

* Compare all retrieval methods. See [Choose a retrieval method](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/read-docs-with-ai-agents).

* Discover documents through the index. See [llms.txt](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/read-ark-docs-with-llms-txt).
