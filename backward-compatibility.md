# Best practices for backward compatibility

To help you integrate and use the ModelArk API more reliably, this topic describes ModelArk's general guidance on backward compatibility and recommended best practices.

<span id="scope"></span>
## Scope

This guidance applies to the following:


* REST APIs.

* ModelArk official SDKs.

* Model families.


<span id="compatibility-notes"></span>
## Compatibility notes

ModelArk strives to avoid introducing incompatible API changes within the same major version to reduce the impact of protocol evolution on existing integrations.

Model output and prompt behavior are inherently uncertain, and prompt behavior and model performance may vary by model version. To obtain more stable model output, we recommend using a fixed model version, and evaluating and validating it based on your actual business scenarios.

<span id="common-backward-compatible-changes"></span>
## Common backward\-compatible changes

The following changes are usually backward\-compatible:


* Add new API endpoints.

* Add new optional request parameters.

* Add parameters to JSON response objects or event data.

* Change the order in which parameters are returned in JSON response objects.

* Change the length or format of opaque strings such as resource identifiers.

* Add new event types to streaming APIs.


<span id="best-practices"></span>
## Best practices

To better support future evolution, we recommend following these principles during development:


* Use a permissive parsing policy for response content and ignore unrecognized parameters; unless required by your business, we do not recommend strict validation of the entire response JSON.

* Maintain compatibility with unknown values for enum\-type parameters to avoid errors caused directly by newly added values.

* Pass only request parameters required by your business, and do not rely on undocumented parameters or behavior.

* Handle errors based on stable structures such as HTTP status codes and error codes, and do not rely on the exact wording of error messages.

* Do not rely on the order of JSON parameters or the fixed length or format of opaque strings such as resource identifiers.


<span id="stay-tuned"></span>
## Stay tuned

For compatibility\-related updates, see [Product updates](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1159177), [Model releases](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1159178), and [Model deprecations](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1350667).
