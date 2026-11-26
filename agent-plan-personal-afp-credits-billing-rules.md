# AFP credit deduction rules

Usage within a ModelArk Agent Plan plan is measured in AFP, a unified usage unit. This topic explains how plan usage is deducted.

# AFP definition

Agent Fuel Point (AFP) is the unified usage billing unit for Agent Plan plans. It quantifies the consumption of agent resources.

# Deduction coefficients

AFP deduction coefficients vary by model and harness tier.

## Models

### Text generation models

> AFP formula = `(cache-miss input tokens × cache-miss input deduction coefficient + cache-hit input tokens × cache-hit input deduction coefficient + output tokens × output deduction coefficient) / 10,000`

|Model category |Model |Cache\-miss input deduction coefficient |Cache\-hit input deduction coefficient |Output deduction coefficient |
|---|---|---|---|---|
|Text generation (ultra\-fast) |seed\-2.0\-mini |2 |0.4 |8 |
|Text generation (standard) |seed\-2.0\-lite |5 |1 |40 |
|Text generation (advanced) |dola\-seed\-2.1\-turbo |5 |1 |25 |
|Text generation (advanced) |deepseek\-v4.1\-flash\-economy |2.4 |0.05 |9.6 |
|Text generation (advanced) |deepseek\-v4.1\-flash |3 |0.06 |12 |
|Text generation (advanced) |deepseek\-v4\-pro |13.2 |0.44 |39.6 |

### Video generation models

> **warning**

> For the formula for estimating tokens consumed to generate videos, see [Billing examples](model-pricing.md#video-generation-price-examples).

> AFP formula = `consumed tokens / 10,000 × deduction coefficient`

|Model |Output condition |Input condition |Deduction coefficient |
|---|---|---|---|
|dreamina\-seedance\-2.5\-flash |Output video resolution: 480p or 720p |Input without video |70 |
||Output video resolution: 480p or 720p |Input with video |43 |
|dreamina\-seedance\-2.5 |Output video resolution: 480p or 720p |Input without video |107 |
||Output video resolution: 480p or 720p |Input with video |64 |
||Output video resolution: 1080p |Input without video |117 |
||Output video resolution: 1080p |Input with video |70 |

### Image generation models

> AFP formula = `∑(number of billable input images × corresponding input image deduction coefficient) + ∑(number of successfully generated output images × corresponding output image deduction coefficient)`

|Model |Input image deduction coefficient (AFP/image) |Output condition |Output image deduction coefficient (AFP/image) |
|---|---|---|---|
|dola\-seedream\-5.0\-flash |Free |Successfully generated images |20 |
|dola\-seedream\-5.5 |8 |≤ 2.61M pixels |20 |
|||2.61M < pixels ≤ 4.63M |35 |
|dola\-seedream\-5.0\-pro |* First image: Free<br><br>* Starting from the second image: 3 |Single\-image generation: ≤ 2.61M pixels |45 |
|||Single\-image generation: \> 2.61M pixels |90 |
|||Layer separation: ≤ 2.61M pixels |22.5 |
|||Layer separation: \> 2.61M pixels |45 |

### Embedding models

> AFP formula = `(text input tokens × text input deduction coefficient + image input tokens × image input deduction coefficient) / 10,000`

|Model |Text input deduction coefficient |Image input deduction coefficient |
|---|---|---|
|skylark\-embedding\-vision |1.25 |3.25 |

## Harness

### BytePlus Searchinfinity

Each account receives 2,000 free calls per month. After the free quota is exhausted, calls within the plan are deducted at 3 AFP per call.

|Harness |Deduction coefficient |Unit |
|---|---|---|
|BytePlus Searchinfinity |3 |call |

### Agent Evolve

## Deduction examples

* **Text generation model example**: The model is set to seed\-2.0\-mini. A request has 40K cache\-miss input tokens, 10K cache\-hit input tokens, and 500 output tokens.

   ```Plain
   AFP consumption for this request
   = (cache-miss input tokens * cache-miss input deduction coefficient + cache-hit input tokens * cache-hit input deduction coefficient + output tokens * output deduction coefficient) / 10,000
   = (40000 * 2 + 10000 * 0.4 + 500 * 8) / 10000
   = 8.8 AFP
   ```

* **Video generation model example**: The model is set to dreamina\-seedance\-2.5\-flash. A video task has video in the input and successfully generates a 720p video, consuming 150K tokens.

   ```Plain
   AFP consumption for this request
   = consumed tokens / 10000 * deduction coefficient
   = 150000 / 10000 * 43
   = 645 AFP
   ```
