# Practice guidance for ModelArk bill split

Customers using BytePlus large model services, especially enterprise\-level customers, generally have strong demands for bill splitting. For example, bill splitting can be used to achieve accurate cost accounting of large model services, business performance evaluation, and internal responsibility division. To manage bill splitting effectively, internal design is usually required before using the large model, such as sorting out bill splitting dimensions and defining allocation rules. This article summarizes the typical bill splitting scenarios supported by BytePlus, and presents the solutions and operation methods for common scenarios to help enterprises implement bill splitting efficiently.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Each scenario is an independent solution. Scenarios 1, 2, and 3 can be used in combination to meet complex enterprise\-level bill splitting scenarios.</div>


<span id="69b4747e"></span>
# Overview of bill split

<span id="0acedc64"></span>
## What is bill split?

Bill split refers to the splitting of already confirmed bill data according to the actual usage within the user's enterprise or the user's own business logic, and allocating it to different businesses, departments, or even individuals. In simple terms, bill splitting shows who spent how much on which product or service.

<span id="94092f98"></span>
## Supported bill split dimensions

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/59c502ddf71748f1b60b8ed180a57e62~tplv-goo7wpa0wc-image.image) </span>


1. **Account (** [Scenario 1: Bill split by multiple departments](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#98062976) **)** : Resources are isolated between accounts, and different resource ownership naturally enables bill splitting. Users can, according to their business needs, have different departments or business lines use separate accounts to divide resources and costs, manage resources and bills independently, or centralize cost management through the enterprise's financial capabilities.

2. **Project (** [Scenario 2: Bill split by multiple project teams](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#85b52a5d) **)** : Under a single account, a single resource (End Point) can be associated with a unique project, as shown in Account_02 in the figure above. When costs are incurred by the resource, the costs will be automatically attributed to the corresponding project based on the project it belongs to, resulting in bill split data by project dimension.

3. **Tag (** [Scenario 3: Bill split by multiple products](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#009233db) **)** : In a single project, a single resource (End Point) can be associated with multiple tags, as shown in Project 02 of Account_02 in the figure above. Tags can mark detailed information about the resource. When costs are incurred by the resource, the costs will be automatically associated with the tags based on the tags bound to the resource, resulting in bill split data by tag dimension.

4. **Configuration Name/Billing Unit (** [Scenario 4: Bill split by multiple models (Simple scenario)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#94a74e62) **)** : The configuration name and billing unit record the model selected by the user when using the large model and the specific billing items. You can group and sum by configuration name & billing unit to obtain the specific costs of the model and the billing items within the model, as shown in Account_01 in the figure above.

5. **`createdBy`** **System Tag (** [Scenario 5: Bill split by multiple users (Simple scenario)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#b6bb9a1e) **)** : In the tag system, there is a type of tag automatically created by the system, typically the creator tag `createdBy`. When users enable this feature, regardless of the identity used to log in to the account to create resources, the system will automatically mark the user's login identity information on the resource and record the creator of the resource, as shown in Project 03 of Account_02 in the figure above. We can use the content of this tag to obtain bill split data by creator dimension.

6. **API key (** [Scenario 6: Split billing by API key (Simple scenario)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1884418#4d2a8324) **)** : A unique Resource ID is generated for each API key, which is automatically recorded by the billing system. You can group and sum by this ID to split bills by API key (see Scenario 6 for support scope and effective time).


<span id="5b36beca"></span>
# Preparation

[Enable the split billing feature](https://console.byteplus.com/finance/bill/split-bill).

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Only consumption incurred after enabling the bill split function can be queried in the bill split. Please be sure to enable the bill split function in advance!</div>


<span id="98062976"></span>
# Scenario 1: Bill split by multiple departments

<span id="fb2d71d4"></span>
## Scenario description

A company has purchased one or more ModelArk model services, and multiple departments are using these model services simultaneously, requiring bill statistics by department dimension.

<span id="65af4c65"></span>
## Solution

Select a BytePlus account as the financial trustee (trustee account, also known as the main trustee account) to manage the financial performance of each department's account (trusted account, also known as the sub\-trustee account). Each department's account can only view its own consumption bills, and bill settlement, invoicing, and repayment are all handled by the main trustee account. The main trustee account can view the bills generated by each department by account dimension.


1. Each department in the enterprise uses an independent BytePlus account. The sub\-trustee accounts can be applied for with the assistance of the main trustee account, eliminating the need for repeated real\-name authentication.

2. Use the finance department's account as the main trustee account, and invite other accounts to complete the financial trustee association on the [Cost Center > Financial Association](https://console.byteplus.com/finance/busniess-finance/relation) page.


<span id="a6f8fb7d"></span>
## Query bills

Log in with the main trustee account. On the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, the consumption bills of all sub\-accounts (Owner accounts) under the current main account (Payer account) are displayed by default. You can also filter the Owner accounts to query the consumption bills of the target sub\-accounts.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e2b823d71e1a470b84bc1cb28c6ec2c0~tplv-goo7wpa0wc-image.image) </span>

<span id="85b52a5d"></span>
# Scenario 2: Bill split by multiple project teams

<span id="140afd85"></span>
## Scenario description

A department has purchased one or more model services, and multiple project teams within the department are using these model services simultaneously, requiring bill statistics by project dimension.

<span id="4cb8536f"></span>
## Solution

Each project team creates a project, and selects the affiliated project when creating an inference End Point. When querying bills, sum by project grouping.


1. [Plan and create projects](https://console.byteplus.com/iam/resourcemanage/project). Each project team creates an independent project.

2. [Plan and create inference End Points](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint?config=%7B%22Filter%22%3A%7B%22Name%22%3A%22%22%7D%2C%22PageNumber%22%3A1%7D&projectName=ProjectforIAM001). For each project, create one or more inference End Points as needed, and select the affiliated project when creating the inference End Point.


<div style="text-align: center">
<img src="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ac76420c3a054c27b0f17cc781ee487b~tplv-goo7wpa0wc-image.image" width="1280px" /></div>


<span id="6c571d6a"></span>
## Query bills


1. On the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, select the target project and filter the product to be queried to view the consumption bills under a specific project.

2. Click **Query Project Summary Distribution** in the upper right corner to view the total consumption bills of different projects under the current account.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/8f7df59766ad40c78e7fcff0a07a6496~tplv-goo7wpa0wc-image.image) </span>


<span id="009233db"></span>
# Scenario 3: Bill split by multiple products

<span id="de2eaf81"></span>
## Scenario description

A project team has purchased one or more model services, and the project team includes multiple applications, products, and environments (development environment, testing environment, production environment, etc.), requiring bill statistics by application/product/environment.

<span id="4933f1e7"></span>
## Solution

Each project team creates a project, selects the affiliated project when creating an inference End Point, and tags it with application information. When querying bills, sum by tag grouping. Take a project team with development, testing, and production environments as an example:


1. [Create a project](https://console.byteplus.com/iam/resourcemanage/project).

2. [Plan and create tags](https://console.byteplus.com/resourcemanager/tag/list).

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/b345afc492444cebaa27b864e0f783de~tplv-goo7wpa0wc-image.image) </span>

3. [Enable the cost tag key](https://console.byteplus.com/finance/bill/tag). Ensure that the cost tag key is reflected in bill details and other data.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/8243e2a069714cbf860ababcb918dfaf~tplv-goo7wpa0wc-image.image) </span>

4. [Create inference End Points](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint?config=%7B%22Filter%22%3A%7B%22Name%22%3A%22%22%7D%2C%22PageNumber%22%3A1%7D&projectName=ProjectforIAM001). Create an inference End Point for each environment, and select the affiliated project and tag.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/a6ea1096884d476a9876883d52d5938f~tplv-goo7wpa0wc-image.image) </span>


<span id="f7c48b80"></span>
## Query bills

On the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, select the target project and tag, and filter the product to be queried to view the consumption bills under a specific project.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/d74833875a144092abd8721ac4bf11bc~tplv-goo7wpa0wc-image.image) </span>

<span id="94a74e62"></span>
# Scenario 4: Bill split by multiple models (Simple scenario)

<span id="d61a54f3"></span>
## Scenario description

A company or individual has purchased multiple model services and needs to group bills by different models. This applies to scenarios where users do not want to introduce custom inference End Points and prefer to call directly via Model ID.

<span id="db1ef9af"></span>
## Solution

Split bills by configuration name/billing unit.


* **Split by configuration name**: On the BytePlus platform, the configuration name usually corresponds to all post\-paid billing items of a model. Currently, the bill split page does not support filtering by configuration name.

* **Split by billing unit**: Can group the cost of a specific billing item of a model. For example, group the cache hit cost of a model to understand how much cost is saved by using the cache.


<span id="765145f7"></span>
## Query bills

On the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, select **Detail** for the group period, filter the product and billing unit to be queried, and you can view the consumption bills of a specific billing item.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/5fae7e8615a64a59b4782fc36146f6c5~tplv-goo7wpa0wc-image.image) </span>

<span id="b6bb9a1e"></span>
# Scenario 5: Bill split by multiple users (Simple scenario)

<span id="65326c16"></span>
## Scenario description

A company has purchased one or more model services, and multiple users are using these model services simultaneously, requiring bill statistics by users.

<span id="f36a2208"></span>
## Solution

Each IAM user creates multiple inference End Points as needed, and these inference End Points will be automatically bound with the `createdBy` system tag (`sys:ark:createdBy : IAMUser/xxx`). When querying bills, sum by IAMUser grouping.


1. [Enable the creator tag](https://console.byteplus.com/resourcemanager/tag/create_by).

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/c0347132c9134c90bb7db89ce75c634d~tplv-goo7wpa0wc-image.image) </span>

2. [Enable the cost tag key](https://console.byteplus.com/finance/bill/tag). Ensure that the cost tag key `sys:ark:createdBy` is reflected in bill details and other data.

3. [Create inference End Points](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint?config=%7B%22Filter%22%3A%7B%22Name%22%3A%22%22%7D%2C%22PageNumber%22%3A1%7D&projectName=ProjectforIAM001). Each IAM user creates multiple inference End Points as needed.


<span id="b6a9bcbc"></span>
## Query bills

On the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, select **Detail** for the group period, and select the target IAMUser for the tag to query bills by user.

<span id="4d2a8324"></span>
# Scenario 6: Split billing by API key (Simple scenario)

<span id="0e080acc"></span>
## Scenario description

Enterprises or developers assign different API keys for different business applications, test environments, or individual developers under the same project. To implement refined cost accounting, you may need to split the bills and costs by API key to know the exact usage and fees of each key.

<span id="f4211c8a"></span>
## Solution

ModelArk generates a unique **Resource ID** for each API key, which is used for split bill and usage statistics. You can get this ID on the [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey) page, and distribute different API keys to the corresponding businesses or personnel. After consumption is incurred, you can use **Resource ID** for group reconciliation in the billing system.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>



* <div data-tips="true" data-tips-type="tip">You will encounter the following terms regarding bill split. In this scenario, they refer to the same unique identifier of the API key, only with different names:</div>


   * <div data-tips="true" data-tips-type="tip"><strong>Resource ID</strong>: The column name displayed on the <strong>API keys</strong> page of the ModelArk console.</div>


   * <div data-tips="true" data-tips-type="tip"><strong>ApikeyID / apikey_id</strong>: The split billing dimension name in the split billing system, and the key name of the <code>Filters</code> query condition for the bill query API.</div>



<span id="15ce59d5"></span>
## Query bills

After obtaining the **Resource ID** of the target API key, you can query bills or usage split by API key in the console.


1. Obtain the **Resource ID** of the API key: Find the **Resource ID** column on the [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey) page, and copy the unique **Resource ID** of the API key to be queried.

2. Split bills by **Resource ID**:

   1. Go to the [Split bills](https://console.byteplus.com/finance/bill/split-bill) page, click "Set product split dimensions", and select `apikey_id`.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/67219291004e4aca980160cd825efa69~tplv-goo7wpa0wc-image.image) </span>

   2. Paste the copied Resource ID into the **Split item ID** search box for precise query.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/d215abc64c27473795b75d3a419e6fc7~tplv-goo7wpa0wc-image.image) </span>


<span id="d69c0adf"></span>
## Limits and notes


* **Effective time**: Bill data is available after  **(UTC+8) 2026\-05\-01 00:00**.

* **Supported scenarios**: Inference request usage with pay\-as\-you\-go billing.

* **Unsupported scenarios**:

   * Specific models: Embedding models, simultaneous speech translation models, LoRA fine\-tuned models.

   * Specific billing methods: Usage that is not postpaid/pay\-as\-you\-go (such as model units, Coding Plan, etc.).

   * API calls that are not authenticated using API key:

      * Inference usage of offline inference tasks;

      * Usage of non\-inference requests (such as cache storage fees, fine\-tuning task fees, inference outsourcing computing power fees, dynamic model unit computing power fees, asset library fees, etc.);

      * Requests authenticated using JWT token.


<span id="96f6fffa"></span>
# FAQs

<span id="147fda9c"></span>
## Is bill query supported for deleted API keys?

Not supported for now. This feature will be supported in the future. It is recommended that you keep an internal record of the corresponding **Resource ID** before deleting an API key.

<span id="17619c8a"></span>
## What is an inference End Point? What is its role?

When using large language models for inference services, the inference End Point (Endpoint) is the key entry for model calls. Developers can initiate API/SDK inference requests to the large model simply by specifying the ID, enabling quick access and flexible calls. Inference End Points are divided into preset inference End Points and custom inference End Points. The inference End Points actively created by users are custom inference End Points, which support more comprehensive configuration capabilities and are suitable for enterprise\-level or business integration scenarios that require fine\-tuned model access, permission control (project and tag configuration), computing power guarantee, data flowback, etc. For more details, please refer to [Manage inference endpoints](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1182403).

<span id="6e32bc6d"></span>
## What is financial trusteeship? What is its role?

Financial trusteeship means that an account authorizes another account to manage the bills generated by its cloud resource consumption and is responsible for subsequent performance such as reconciliation, invoicing, and repayment. The main trustee account (trustee account) manages the financial performance of the sub\-trustee accounts (trusted accounts), and the sub\-trustee accounts (trusted accounts) only have the permission to operate resources. In enterprise finance, when the trustee account associates with the trusted account, it can set the relationship type as "financial trusteeship", which means the financial performance of the trusted account is managed by the trustee account.

<span id="03aac288"></span>
## What is a project? What is its role?

A resource project (project) is a resource grouping management mechanism provided by BytePlus, which helps achieve logical resource isolation. You can use resource projects for project\-based authorization and bill splitting. For more details, please refer to [Overview of Projects](https://docs.byteplus.com/en/docs/byteplus-platform/docs-managing-projects).

<span id="87e3d625"></span>
## What is a tag? What is its role?

A resource tag consists of a set of key\-value pairs. You can use resource tags to classify and aggregate cloud resources from different dimensions, and apply them to scenarios such as tag\-based authorization and tag\-based bill splitting. For more details, please refer to [Overview of Tags](https://docs.byteplus.com/en/docs/byteplus-platform/bill_tags).

<span id="22bd90f9"></span>
## How to quickly view the cost of a specific inference End Point?

On the [Online inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint?config=%7B%22Filter%22%3A%7B%22Name%22%3A%22%22%7D%2C%22PageNumber%22%3A1%7D&projectName=ProjectforIAM001) page of the ModelArk console, switch to the **Custom Inference End Points** tab, click the target inference End Point name to enter the **Overview Information** page. Click **Go to View Bills** to jump to the Split bills page and view the bill information of this inference End Point.
