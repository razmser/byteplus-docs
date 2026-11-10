# BytePlus ModelArk Data Processing

**BytePlus ModelArk** provides powerful, secure, and comprehensive model capabilities, along with end\-to\-end services, including data management, fine\-tuning, inference, and evaluation—ensuring the successful deployment of your AI applications.

As the platform of choice for many top brands, we understand the importance of Data Security & Privacy. We always review and improve our Documentation, Policies and Product features with a Security & Privacy First mindset. **BytePlus ModelArk** takes Product Security & Privacy compliance very seriously.

This topic describes Security & Privacy features across the entire data that is processed by BytePlus on behalf of customers in connection with its provision of ModelArk services (the "**Customer Data**") processing lifecycle on the **BytePlus ModelArk** platform, with the aim of providing transparency to our customers about how their Customer Data is handled and protected on the **BytePlus ModelArk** platform.

<span id="d40d9870"></span>
## Data protection throughout the lifecycle

<span id="f9934478"></span>
### Data flow

The following diagram illustrates the data flow of the **BytePlus ModelArk** platform:


* Platform data flow diagram:


<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/fa45b26084c845848ed066c3596e5dfc~tplv-goo7wpa0wc-image.image) </span>


* Security sandbox architecture detailed data flow diagram:


<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e1150aef58a145018682b5084819424c~tplv-goo7wpa0wc-image.image) </span>

<span id="f0aaaf7f"></span>
### Data center locations

**BytePlus ModelArk** may use data centers, including those located in Malaysia, Indonesia, and/or the EU/EEA, for model deployment and Customer Data processing.

<span id="1f6306de"></span>
### Load balancing

Load balancing is a technology that evenly distributes network traffic or workloads to multiple servers or computing resources, aiming to improve the performance, reliability and scalability of the system, ensure that the load of each server is relatively balanced, and avoid the situation that some servers are too busy while others are idle, thereby optimizing resource utilization and improving the responsiveness and stability of the overall system.

**BytePlus ModelArk** provides a load balancing service for global use, responsible for routing traffic to globally distributed regional services.

**BytePlus ModelArk**'s load balancing service is currently deployed in *Malaysia, Indonesia, and/or EU/EEA*. The global load balancing service is responsible for routing user traffic to available computing resources; the load balancing service reacts to changes in customer traffic to maximize availability and performance.

<span id="90b41cec"></span>
### Data minimization

**BytePlus ModelArk** processes Customer Data for the purpose of providing **BytePlus ModelArk** services.

<span id="9369230c"></span>
### Data processing

As a data processor, **BytePlus ModelArk** collects and processes Customer Data solely under customer instructions. Customer Data processing is located in *Malaysia*, *Indonesia,*  and/or *EU/EEA*. Without the customer's prior authorization, **BytePlus ModelArk** will neither interfere with the data processing nor use Customer Data for its own model training.

<span id="c9c14731"></span>
### Data transmission

**BytePlus ModelArk** provides customers with two transmission encryption mechanisms, network layer encryption and application layer encryption.


* Network layer encryption:


Using the HTTPS protocol ensures that data is securely encapsulated during network transmission, preventing data from being easily stolen or tampered with.


* Client application layer encryption mechanism (optional):


If the customer enables the application layer end\-to\-end encryption function, the inference instance of **BytePlus ModelArk** will be given a unique identity authentication certificate. The Customer Data will be encrypted using the public key of this certificate. Only when the data arrives in the **BytePlus ModelArk** sandbox with the corresponding private key will it be decrypted. This is like giving the Customer Data a unique mark. Only the platform security environment that holds the matching "key" can decrypt and use it, ensuring that even if the data falls into the incorrect receiving end, it cannot be decrypted and read.


* Access control


Based on the ZTI (Zero Trust Identity) system, the authorization and authentication within the **BytePlus ModelArk** system are implemented using a short\-term dynamic token mechanism.

Learn more about the capabilities of the **BytePlus ModelArk** Trusted Computing Framework at [White Paper on ModelArk Trusted Collaborative Computing Framework](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1099355).

<span id="d6cecc22"></span>
### Data sharing

The Customer Data transmitted by customers will not be disclosed to any other unauthorized third party. Each customer's data is isolated from other customers, and no data is shared between customers.

**BytePlus ModelArk** has a strict internal account and data rights management policy and cannot access Customer Data unless authorized by the customer.

<span id="ba9135eb"></span>
### Data retention and deletion

**BytePlus ModelArk** service may store your Customer Data for content safety purposes; input and output triggered by the filter are retained for 180 days in *Malaysia*. If you want to know about the content filter function, please refer to our [ModelArk Content filter Overview](Content_Pre-filter.md).
