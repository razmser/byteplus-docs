# Knowledge base billing

<span id="294ba249"></span>
# Billing instructions

This document outlines the billing instructions for the Knowledge Base product, including **billing methods**, **billing cycles**, and **billing items**.

<span id="1eb9d22f"></span>
## Billing methods

Pay\-as\-you\-go (Post\-payment): You use the service first and pay later. Fees are included in the bill, and you can settle the payment after the bill is issued.

<span id="d310b057"></span>
## Billing cycles

Pay\-as\-you\-go

The Knowledge Base calculates the usage of all billing items and generates bills on an **hourly** basis.


* After creating a Knowledge Base, uploading documents will trigger the allocation and reservation of computing resources for the Knowledge Base, and hourly billing will start continuously.

* Deleting documents does not affect the occupation of computing resources. If you no longer need to use the Knowledge Base, please delete it proactively.


<span id="db31c807"></span>
## Billing items

Pay\-as\-you\-go

To meet the different capacity needs of individual developers and enterprise customers, the Knowledge Base currently offers two specifications (**Standard Version** and **Pro Version**) under the pay\-as\-you\-go model. The detailed comparison is as follows:

The bill composition for the pay\-as\-you\-go Knowledge Base includes three parts:


1. **Hourly resource billing**: For computing. After documents are uploaded, data is vectorized and written to the index in a streaming manner, which requires continuous occupation of storage and computing resources. The fee for each hour is calculated based on the actual usage during that hour.

   ⚠️ Note: The size of computing and storage resources is proportional to the amount of uploaded data. The system will automatically scale resources up or down based on actual usage.

   ⚠️ Note: Fees are incurred every hour. If you no longer need to use the service, please clean up the Knowledge Base in a timely manner.

2. **Model billing based on call volume**: For the inference of vectorization and LLM/VLM, which is billed based on the actual token consumption.

   ⚠️ Note: If multiple vector model combinations are selected when creating the Knowledge Base, tokens will be counted separately for the two models, and bills will be issued independently for the two models.

3. **Parse billing based on document pages**: For unstructured documents, a parsing service is required to fully understand and split information such as text, tables, images, and headings within the content. This process is billed based on the actual number of pages processed.


The detailed explanation of billing items is as follows:


|Billing Item |Definition |Measurement Method |Unit Price |
|---|---|---|---|
|standard_compute_knowledge (Standard) |When using a Knowledge Base, the computing resources are relied on for index building, automatic optimization and analysis, and online retrieval. Under the Standard Edition Knowledge Base, the computing resources adopt a shared mode. |Number of Knowledge Bases |0.0416 USD/Hour |
|compute_knowledge (Pro) |When using a Knowledge Base, the computing resources are relied on for index building, automatic optimization and analysis, and online retrieval. Under the Enterprise Edition Knowledge Base, the computing resources adopt an exclusive mode.<br><br>The computing resources will be scaled up or down based on actual usage. |by CU<br><br>1 CU = Max[CPU core, Memory/8GB] |0.25 USD/Hour |
|rerank_knowledge (All) |Rerank the original question and retrieval results. |by input tokens |0.00004 USD/K tokens |
|parser_basic_knowledge (All) |For unstructured documents, a parsing service is required to fully understand and split information such as text, tables, images, and headings within the content. |by doc pages |10 USD/K pages |
|text_embedding_knowledge (All) |It helps developers quickly convert text data into vector data. It supports multi\-language and long text, only dense index. |by input tokens |0.00015 USD/K tokens |
|text_embedding_multifunctional_knowledge (All) |It helps developers quickly convert text data into vector data. It supports multi\-language and long text, and can simultaneously build and retrieve sparse and dense indexes. |by input tokens |0.00015 USD/K tokens |
|Skylark\-embedding\-vision\-text_knowledge (All) |It helps developers quickly convert text data into vector data. It supports multi\-language and long text, only dense index. |by input tokens |0.000125 USD/K tokens |
|Skylark\-embedding\-vision\-image_knowledge (All) |It helps developers quickly convert image data into vector data. It supports multi\-language and long text, only dense index. |by input tokens |0.000325 USD/K tokens |
|VLM/LLM\-related Billing Items (All) |Follow Ark |||
