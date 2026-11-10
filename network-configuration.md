# Network configuration

<span id="1-intro"></span>
# 1. Function introduction

A Virtual Private Cloud (VPC) builds an isolated, self\-configured virtual network environment for cloud resources. Users can customize network features such as IP address ranges, security groups, and routing policies within the VPC, managing cloud resources simply, efficiently, and securely. PrivateLink establishes private network connections through endpoints and endpoint services, enabling private access to service resources from different VPCs. Private access offers higher security, service stability, and lower traffic costs compared to public access. ModelArk provides a convenient way to access ModelArk resources and services through a private network.

<span id="2-workflow"></span>
# 2. Usage process

<span id="2-1-prep"></span>
## 2.1 Preliminary steps

You can configure the private network on the ModelArk Network Configuration page, selecting existing VPCs and subnets to access resources and services on ModelArk. If you don't have a VPC and subnet yet, please refer to [Creating a VPC and Subnet](https://docs.byteplus.com/en/docs/vpc/Creating-a-VPC). Before using a VPC to access ModelArk resources and services, you need to complete two configuration steps: activating the PrivateLink service and configuring the VPC.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/dce3284b2445458ebda0acb2ce7a86bb~tplv-goo7wpa0wc-image.image) </span>

<span id="step-1-privatelink-activate"></span>
### Step 1: Activate PrivateLink service

The PrivateLink service is generally activated by default. If it shows as deactivated, click the "Activate PrivateLink Service" button in Step 1 on the Network Configuration page. This will redirect you to the cross\-service authorization page of the PrivateLink product. Click "Authorize Now" to activate and configure.

<span id="step-2-vpc-config"></span>
### Step 2: VPC configuration

Click the "VPC Configuration" button in Step 2 on the Network Configuration page to configure the VPC. Select the VPC and corresponding availability zones and subnets under your account that you need to connect, and click "Confirm." The Network Configuration page will display the private network record you created. ModelArk automatically associates the `default` security group and creates endpoints and endpoint services for you. Through this VPC, you can directly access ModelArk without modifying the access address.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/624ce712e1934570b5bde3291d5a5b8e~tplv-goo7wpa0wc-image.image) </span>

<span id="2-2-connectivity-test"></span>
## 2.2 Test connectivity

The private network connection created through the above steps allows you to access ModelArk resources and services under the corresponding VPC. Follow these steps to test connectivity:


1. Your endpoint domain name is: `ark.ap-southeast.bytepluses.com`

2. Before verification, ensure that the backend servers of the CLB instances for your service resources are configured with backend services. Refer to [Configuring Backend Services](https://docs.byteplus.com/en/docs/clb/Creating-an-HTTP-static-page) for instructions.

3. Remotely log in to a cloud server in your VPC. Refer to [Logging in to a Linux Instance](https://docs.byteplus.com/en/docs/ecs/Using-the-console-to-log-on-to-a-Linux-instance) for specific instructions.

4. Execute the following command to test network connectivity (replace `<YOUR_API_KEY>` with your actual API key, refer to [Manage API Keys](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1361424)):

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/ping -H 'Authorization: Bearer <YOUR_API_KEY>'
   ```
   


If the returned result displays the content of the backend service page configured on the backend server, it indicates that the network connection is normal. You can use the APIs and SDKs provided by ModelArk to access ModelArk's resources and services through this VPC. For detailed calling methods, refer to [SDK Overview](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1541595). The costs incurred by accessing ModelArk resources through the VPC will be charged according to the PrivateLink pricing standards.
