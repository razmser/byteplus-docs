# Overage billing rules

After overage postpaid billing is enabled, available plan quota is deducted first during each plan cycle (five hours, weekly, or monthly). When the quota for the current cycle is exhausted, supported models or harnesses automatically switch to overage postpaid billing without changes to configuration such as the Base URL, API key, or model name. The applicable requests are billed on a postpaid basis. When the quota for the corresponding cycle is refreshed, the system automatically resumes deducting plan quota first.

> **warning**

> Voice models and harnesses do not have five\-hour or weekly limits. After overage postpaid billing is enabled, they switch to overage postpaid billing only when the monthly limit is reached.

# Model overage postpaid rules

To continue using models after plan quota is exhausted, enable model overage postpaid billing. For details, see [Model overage postpaid billing management](agent-plan-enterprise-postpaid-management.md#a1b2c3d4).

The following table lists the models that support overage postpaid billing and their postpaid billing rules.

> **tip**

> Overage postpaid billing is not supported in auto mode.

|Model category |Model |Supports overage postpaid billing |Postpaid model price |Postpaid billing details |
|---|---|---|---|---|
|Text generation (fast) |seed\-2.0\-mini |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Text generation (standard) |seed\-2.0\-lite |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Text generation (standard) |deepseek\-v4\-flash |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Text generation (advanced) |dola\-seed\-2.1\-turbo |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Text generation (advanced) |deepseek\-v4\-pro |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Text generation (advanced) |deepseek\-v4.1\-flash |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Embedding |skylark\-embedding\-vision |● |[Model pricing](model-service-pricing.md) |[Model billing details](model-service-pricing.md) |
|Image generation |dola\-seedream\-5.5 |— |\- |\- |
|Image generation |dola\-seedream\-5.0\-flash |— |\- |\- |
|Image generation |dola\-seedream\-5.0\-pro |— |\- |\- |
|Video generation |dreamina\-seedance\-2.5\-flash |— |\- |\- |
|Video generation |dreamina\-seedance\-2.5 |— |\- |\- |

# Harness overage postpaid rules

To continue using harnesses after plan quota is exhausted, enable harness overage postpaid billing. For details, see [Harness overage postpaid billing management](agent-plan-enterprise-postpaid-management.md#f5e6d7c8).

## BytePlus Searchinfinity

|Harness |Postpaid overage charge |
|---|---|
|Searchinfinity |0.003 USD/call |
