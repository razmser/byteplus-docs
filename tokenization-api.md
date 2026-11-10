# Tokenization API

`POST https://ark.ap-southeast.bytepluses.com/api/v3/tokenization`

Tokenizes the input text and returns the ID and offset of each token. Only text input is supported.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**model** `string` `Required`  |  Model ID

The model ID or inference endpoint (endpoint ID) used for this request.

**text** `string / string[]` `Required`  |  Content to tokenize

The content to tokenize. A string or a list of strings is supported. To tokenize multiple texts in one request, pass in a list of strings.

**user** `string`  |  User identifier

The unique identifier of the end user. The server currently accepts but does not use this parameter. It is reserved for alignment with other APIs.

## Response parameters

**created** `integer`  |  Creation time

UNIX timestamp (in seconds) of the request creation time.

**data** `object[]`  |  Tokenization results

The list of tokenization results for this request. Each element corresponds to the tokenization result for one input text.

**index** `integer`  |  Index

`data.index`

The index of the current element in the `data` list, consistent with the order of the `text` array in the request.

**object** `string`  |  Object type

`data.object`

Object type, fixed as `tokenization`.

**offset_mapping** `integer[]`  |  Offset

`data.offset_mapping`

The word offsets after tokenizing the text. Each element in the list is a list containing two integers: the first integer indicates the start index of the word or token in the original text (starting from 0), and the second integer indicates the end index (excluding the character at that index).

**token_ids** `integer[]`  |  Token ID list

`data.token_ids`

A list of IDs for each token in the vocabulary after tokenizing the text, in the same order as the original text.

**total_tokens** `integer`  |  Total number of tokens

`data.total_tokens`

The total number of tokens in the corresponding content.

**id** `string`  |  Request ID

Unique identifier of the request.

**model** `string`  |  Model used

The model ID actually used for this request (`model name-version`). For example, if you call an inference endpoint that deploys the `dola-seed-2-1-turbo-260628` model, the returned model parameter is `dola-seed-2-1-turbo-260628`.

**object** `string`  |  Object type

Fixed as `list`.
