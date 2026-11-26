# Read ModelArk documentation with llms.txt

The `llms.txt` files are suitable for AI agents that do not use a documentation MCP but can fetch public URLs. An agent can discover documents through the index and fetch only the matched Markdown content to reduce irrelevant context.

## Get file addresses

The documentation site provides the following files:

|File |Address |Description |
|---|---|---|
|Documentation index |[llms.txt](https://ai.byteplus.com/ark/llms.txt) |Lists public documents by section with their titles, summaries, and Markdown links. |
|Full documentation |[llms-full.txt](https://ai.byteplus.com/ark/llms-full.txt) |Combines all public documentation in the current release. |
|Full section |Use the "Full section" link in [llms.txt](https://ai.byteplus.com/ark/llms.txt) |Combines public documents in one section, for example, [AI Suite full section](https://ai.byteplus.com/ark/llms-full/ai-suite.txt). |

These files require no registration or API key and are updated automatically with documentation releases.

## Understand file contents

`llms.txt` is a Markdown documentation index:

* Each level\-2 heading represents one navigation section.

* The "Full section" link points to the Markdown collection for that section.

* Each document uses the format `- [title](URL): summary`, and its URL points directly to the Markdown mirror of the page.

Example index excerpt:

```Markdown
## AI Suite

- [Full section](https://ai.byteplus.com/ark/llms-full/ai-suite.txt)
- [Ark CLI guide](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ark-cli.md): An introduction to Ark CLI...
```

`llms-full.txt` and the per\-section files contain plain Markdown. The full\-site file is best persisted and indexed for retrieval. For a single question, fetch `llms.txt` and only the matched pages.

## Choose a usage pattern

### Fetch individual documents on demand

Use this pattern for question answering, troubleshooting, and code generation:

1. Fetch [llms.txt](https://ai.byteplus.com/ark/llms.txt).

2. Locate relevant documents by title and summary.

3. Fetch the Markdown links of the matched documents.

4. Answer from the documentation and fetch related pages when needed.

### Fetch one section

Use this pattern when a task needs multiple documents about one subject. Fetch the "Full section" link under the relevant heading in [llms.txt](https://ai.byteplus.com/ark/llms.txt) instead of loading unrelated sections.

### Build a documentation knowledge base

Use this pattern for offline retrieval or knowledge base construction. Periodically fetch [llms-full.txt](https://ai.byteplus.com/ark/llms-full.txt) or per\-section files, preserve their versions, and build a retrieval index. Do not inject the complete full\-site file into one conversation.

## Configure agent retrieval instructions

Add the following rule to the system prompt of an agent or send it directly in a conversation:

```text
When a question involves this product, first fetch
https://ai.byteplus.com/ark/llms.txt
and locate relevant documents by title and
summary, then fetch their Markdown links. Base the answer on the latest
official documentation instead of prior knowledge alone.
```

## Related documents

* Compare all retrieval methods. See [Choose a retrieval method](read-docs-with-ai-agents.md).

* Read a Markdown mirror or an HTML page directly. See [From URLs](read-docs-from-urls.md).

* Install the documentation retrieval Skill. See [ark-docs-reader](ark-document-skills.md#ark-docs-reader).
