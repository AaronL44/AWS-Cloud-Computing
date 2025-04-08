# VPC Guide 

Custom VPC diagram:

![VPC DIAGRAM](images/VPC%20DIAGRAM.png)
## What Are VPCs?
A Virtual Private Cloud is a private, isolated network within the AWS cloud where you can securely deploy and manage resources. It gives you full control over your network settings, including IP addresses, routing and security. E.g. It's like a gated community.

## How Do VPCs Help A Business?
VPCs help businesses by providing a secure, isolated network within the AWS cloud, ensuring that only authorized users or systems can access the company's resources. This increased security helps protect sensitive data, maintains confidentiality, integrity, and availability (CIA), and reduces the risk of unauthorized access or breaches.

## How VPCs Help DevOps?
VPCs help DevOps teams by providing a flexible, secure, and isolated environment where they can deploy, manage, and test applications in a controlled network. Here’s how:

1. **Isolation and Security:** DevOps can create different environments (e.g., development, staging, production) in separate VPCs or subnets to isolate them, reducing the risk of accidental changes or security breaches.
2. **Customization and Flexibility:** VPCs allow DevOps teams to design networks according to the needs of their applications, such as setting up private subnets for databases and public subnets for web servers, and controlling access between them.
3. **Automation:** DevOps can automate the creation and management of VPCs, allowing for quick replication of environments, which is essential for continuous integration/continuous deployment (CI/CD) pipelines.
4. **Scalability and Reliability:** VPCs help DevOps teams ensure their infrastructure can scale as needed and is fault-tolerant by using features like multi-AZ (availability zone) deployment and load balancing.

In short, VPCs help DevOps by providing secure, scalable, and customizable environments for building, testing, and deploying applications efficiently.

## Why AWS Introduced VPCs?

AWS introduced VPCs (Virtual Private Clouds) to address several key concerns, mainly around security, isolation, and control for customers using their cloud services. Here are the main reasons:
1. **Security:** One of the primary reasons for introducing VPCs was to give users more control over the networking and security of their resources. Before VPCs, all AWS resources were in a shared network, making it harder for users to secure their instances and manage network traffic. VPCs allowed users to isolate their resources, control inbound and outbound traffic, and create more secure environments, much like on-premises networks.
2. **Isolation and Control:** Many businesses were already using on-premises data centers, where they had full control over networking and could create secure, isolated environments for different types of applications. AWS needed to provide a similar level of network isolation and flexibility to make the cloud more attractive and comparable to on-premise solutions.
3. **Network Customization:** VPCs allowed AWS users to customize their network architecture to fit their specific needs. This is especially important for complex applications that require multiple layers (public-facing web servers, private databases, etc.).
4. **Compliance and Regulatory Requirements:** With more organizations moving to the cloud, there was an increasing need to meet compliance standards (such as HIPAA, PCI-DSS, etc.), which often require specific network security configurations. VPCs allowed AWS customers to meet these requirements by providing a secure, isolated network environment.

## Different Components:
1. **Subnets:** A subnet is a smaller network within a larger network (VPC), dividing resources into isolated segments.
2. **Public vs Private Subnets:**
   - Public Subnets: Accessible from the internet (e.g., web servers).
   - Private Subnets: Not directly accessible from the internet (e.g., databases).
3. **CIDR Blocks:** A way to define IP address ranges for subnets. Example: 10.0.0.0/24 defines a range of IP addresses from 10.0.0.0 to 10.0.0.255.
4. **Internet Gateways:** A gateway that connects a VPC to the internet, allowing communication between resources in a VPC and the external world.
5. **NAT Gateway:** Allows instances in private subnets to access the internet (for updates, etc.) while blocking inbound traffic from the internet. It’s placed in a public subnet.
6. **Route Tables:** Defines how traffic should be directed. A route table maps network traffic to specific destinations (e.g., through an Internet Gateway or to another subnet).
7. **Security Groups (SG):** Firewalls that control inbound and outbound traffic to instances. They work at the **instance level**, meaning each instance can have its own SG settings, controlling what traffic is allowed or denied.


# Step By Step Guide:

## Step 1: Create a VPC
Navigate to the **VPC Dashboard** in AWS.

![vpc dash](images/VPC%20dash.png)

Click **Create VPC**. Once inside the creator dashboard select "VPC only" as this is the most simple version of the VPC.

![VPC ONLY](images/VPC%20ONLY.png)

Choose a name for your VPC.

Next, select IPv4 and choose an IPv4 CIDR. In my case I chose 10.0.0.0/16 as seen in the image below:

![IPV4 CIDR BLOCK](imageS/CIDR%20BLOCK.png)

What this means:
- 10.0.0.0: This is the starting IP address of your range.
- /16: This is the subnet mask, which defines how many IP addresses are available.

What does /16 mean?
- A /16 subnet means the first 16 bits of the IP address are fixed.
- This leaves 16 bits for hosts → 2¹⁶ = 65,536 total IP addresses.
- Your VPC will have IPs ranging from:
  - 10.0.0.0 to 10.0.255.255

Now click create. You have now created the "blank canvas" for the VPC. We now need to setup the components to go inside the VPC.

## Step 2: Create Subnets

### 2.1 Public Subnet (for your application)

Navigate to **Subnets** on the left side of your AWS screen and click **Create Subnet**.

Search for the VPC ID which should be under the name you chose inthe previous step when creating it.

![VPC ID for public subnet](images/VPC%20ID%20for%20public%20subnet.png)

Name this one "public-subnet". 
- The reason we don't need a specific name for our own one is because this subnet lives inside your VPC only.

Select an availability zone from the dropdown menu:

![AV VPC](images/AV%20VPC.png)

Choose an IPv4 subnet CIDR block:

![Public subnet CIDR block](images/Public%20subnet%20CIDR%20block.png)

What does 10.0.2.0/24 mean?
- 10.0.2.0 = Starting IP address of the subnet
- /24 = 24 bits fixed for the network, leaving 8 bits for host addresses
How many IPs does that give?
- 2⁸ = 256 IP addresses total
However, AWS reserves 5 IPs in every subnet, so:
- 251 usable IPs

Click "Create Subnet" to create it.

### 2.2 Private Subnet (for MongoDB (Your Database))
Select "add new subnet" and name it "private-subnet"

Same as you did with the public one, choose an IPv4 subnet CIDR block:

![Private subnet CIDR block](images/Private%20subnet%20CIDR%20block.png)

Click "Create subnet" to finish creating it.

Subnet creation is now **COMPLETE**

## Step 3: Create and Attach Internet Gateway
### 3.1 Create Internet Gateway:
Navigate to the "Virtual private cloud" dropdown on the left side of your AWS screen and select "Internet gateways".

![INTERNET GATEWAYS BUTTON](images/INTERNET%20GATEWAYS%20BUTTON.png)

Choose a name for your internet gateway and select your VPC in the tags dropdown:

![Internet gateways settings](images/Internet%20gateways%20settings.png)

Click "create" to create it. 

### 3.2 Attach The Internet Gateway TO Your VPC:

On the internet gateway "details" page click the actions button and select "Attach to VPC".

![Attach IG to VPC](images/Attach%20IG%20to%20VPC.png)

Select your VPC name inside the "available VPCs" dropdown:

![Attaching the IG to VPC page](images/Attaching%20the%20IG%20to%20VPC%20page.png)

*Step 3 Complete*

## Step 4: Create Route Tables
### 4.1 Public Route Table
You need to create a route table and not use the default one because it will allow traffic to and from the internet and your application.

Select the "Route tables" on the left side of your AWS screen and click "create".

Choose a name for the public route table:

![Name of public route table](images/Name%20of%20public%20route%20table.png)

Assign the public route table to the VPC by selecting your VPC in the options and click "Create"

### 4.1.1 Associate it with the public subnet:
Navigate to the "Subnet associations" tab on your new route table options:

![Subnet associations tab](images/Subnet%20associations%20tab.png)

Inside the "subnet associations" tab select "edit subnet associations". Next, find the public subnet you created earlier and select it.

![public subnet association](images/public%20subnet%20association.png)

### 4.1.2 Facilitate connections to the internet:
As of now the route table is the same as the default route table AWS supplies. Our goal is to make the route table facilitate connections to the internet. 

Navigate to the "Routes" tab on the route table page and add a route.

![Add a route](images/Add%20a%20route.png)

Add 0.0.0.0/0 as the route because this allows **ANY** ip address to connect to the application, which gives it internet access. Select internet gateway as the route name and choose your VPC ID in the dropdown.

![NEW ROUTE FOR PUB ROUTE TABLE](images/NEW%20ROUTE%20FOR%20PUB%20ROUTE%20TABLE.png)

Why we did this?
- When you create a VPC, it gets a main Route Table with a route like:
  - Destination: 10.0.0.0/16 → Target: local
- This allows internal communication within the VPC, but no internet access.
- If you create a public subnet, you:
  - Create a new route table
  - Add a route: 0.0.0.0/0 → Internet Gateway
  - Associate that route table with the public subnet
- Essentially this is saying:
  - Go to the internet gateway and facilitate connections to the internet.

### 4.2 Private Route Table - This is set up by default:
- The private subnet can continue using the main route table, which has no route to the internet — making it effectively private (as a result of setting up the public route table).

*Route table section complete*

If you go back to YOUR vpc details page it will show the "Resource Map". This shows you the current state of the VPC and what we've done so far:

![VPC Resource Map](images/VPC%20Resource%20Map.png)

## Step 5: Test it

### 5.1 Launch the MongoDB Instance: (Now running inside the private subnet on the new VPC)

Select EC2 instance and launch an instance.

Choose a name, navigate to "My AMIs" and select your DataBase image (which has MongoDB pre loaded and ready to go).

Next, inside "Network settings", choose edit and chanfe the VPC from the "default" one to your new VPC from the drop down inside the "VPC - required" section.

Next, ensure "Auto-assign public IP" is set to "Disable" as we don't need a public IP for a private section of the VPC.

![MongoDB network settings new VPC](imageS/MongoDB%20network%20settings%20new%20VPC.png)

Ensure you select your private subnet when choosing the "subnet" setting.

Next, create a new security group. Choose a name and move to "add rule".

Choose the MongoDB port of "27017" and add the source as 0.0.0.0/0 which will allow anyone within the VPC to connect to it.

![Security group for VPC db](images/Security%20group%20for%20VPC%20db.png)

It is now ready so select "Launch Instance". An example of what it should look like can be seen below:

![DB in VPC Summary](images/DB%20in%20VPC%20Summary.png)

- It doesn't have a public IP as we disabled it.

### 5.2 Launch the App Instance: (Now running inside the public subnet on the new VPC)

Select launch instance and choose a name. Then select "My AMIs" and choose your App Image which contains the pre loaded application ready to go form boot.

It is **crucial to edit network settings** again. Select your new VPC as the "vpc required" and choose your "public subnet" as the subnet. 
- The application will run inside the public subnet on the VPC so people can connect to the app on the internet.

![App VPC network settings](images/App%20VPC%20network%20settings.png)

Ensure that you select **"ENABLE"** inside the "Auto-assign public IP" as this time it is essential to have one.

Next, create a new security group for the app. Choose a name as usual. 

Make sure to navigate to "add rule" and select "HTTP" as the "Type" on port 80 to allow connections via HTTP (internet). Also ensure the "source" 0.0.0.0/0 is added as this allows connection from any IP address to connect to the app.

Lastly, scroll down to the "user info" inside the "advanced settings" section and input your user info script. Make sure it's the script which contains the enviroment variable to connect to the MondgoDB. 

![USER INFO TO MONGODB](imageS/USER%20INFO%20TO%20MONGODB.png)

Select "Launch Instance". 

*App instance launched*

### 5.3 Connect to the app on google and test /posts for MongoDB test

Navigate to google and type the application public ip address into google. Then add /posts to the url in order to test whether the MongoDB connection was succesful also. 

This test confirms whether or not the VPC was created and works as intended. 

![Proof VPC worked](images/Proof%20VPC%20worked.png)

## Summary:

In this guide, we built a custom Virtual Private Cloud (VPC) in AWS to securely deploy a two-tier application architecture. We created a VPC with both public and private subnets to separate application logic (frontend) from the database (backend). The public subnet hosts the application server, while the private subnet securely hosts the MongoDB instance.

We set up essential components:

- Subnets for logical separation

- Internet Gateway for internet access

- Route Tables to control traffic routing

- Security Groups to manage traffic rules

We launched two EC2 instances from custom AMIs:

- MongoDB instance in the private subnet without a public IP

- App instance in the public subnet with a public IP and HTTP access

Finally, we verified successful deployment by accessing the app's public IP and confirming a working connection to MongoDB via the /posts route.

This setup provides a secure, scalable foundation for deploying cloud-based applications using AWS networking best practices.

