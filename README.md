### KCVPC Setup Documentation README.md file

## Objective
Design and set up a Virtual Private Cloud (VPC) with both public and private subnets.
Implement routing, security groups, and network access control lists (NACLs) to ensure proper
communication and security within the VPC.
Work in the AWS EU-West-1 (Ireland) region.

## Requirements

### Create a VPC
1. **VPC Name**: KCVPC
2. **IPv4 CIDR block**: 10.0.0.0/16

### Create Subnets
1. **Public Subnet**
   - **Name**: PublicSubnet
   - **IPv4 CIDR block**: 10.0.1.0/24
   - **Availability Zone**: eu-west-1a

2. **Private Subnet**
   - **Name**: PrivateSubnet
   - **IPv4 CIDR block**: 10.0.2.0/24
   - **Availability Zone**: eu-west-1a

### Configure an Internet Gateway (IGW)
1. Create and attach an IGW to KCVPC.

### Configure Route Tables
1. **Public Route Table**
   - **Name**: PublicRouteTable
   - Associate PublicSubnet with this route table.
   - Add a route to the IGW (0.0.0.0/0 -> IGW).

2. **Private Route Table**
   - **Name**: PrivateRouteTable
   - Associate PrivateSubnet with this route table.
   - Ensure no direct route to the internet.

### Configure NAT Gateway
1. Create a NAT Gateway in the PublicSubnet.
2. Allocate an Elastic IP for the NAT Gateway.
3. Update the PrivateRouteTable to route internet traffic (0.0.0.0/0) to the NAT Gateway.

### Set Up Security Groups
1. **Public Instances Security Group**
   - Allow inbound HTTP (port 80) and HTTPS (port 443) traffic from anywhere (0.0.0.0/0).
   - Allow inbound SSH (port 22) traffic from your local IP.
   - Allow all outbound traffic.

2. **Private Instances Security Group**
   - Allow inbound traffic from the PublicSubnet on required ports (e.g., MySQL port 3306).
   - Allow all outbound traffic.

### Network ACLs
1. **Public Subnet NACL**
   - Allow inbound HTTP, HTTPS, and SSH traffic.
   - Allow all outbound traffic.

2. **Private Subnet NACL**
   - Allow inbound traffic from the public subnet.
   - Allow outbound traffic to the public subnet and internet.

### Deploy Instances
1. **Launch an EC2 instance in the PublicSubnet**
   - Use the public security group.
   - Verify that the instance can be accessed via the internet.

2. **Launch an EC2 instance in the PrivateSubnet**
   - Use the private security group.
   - Verify that the instance can access the internet through the NAT Gateway and can communicate with the public instance.

## Step-by-Step Process

### 1. Create a VPC
- Go to the VPC Dashboard.
- Click on "Create VPC."
- Enter the name **KCVPC** and the IPv4 CIDR block **10.0.0.0/16**.
- Click on "Create VPC."

### 2. Create Subnets
#### Public Subnet
- Go to the Subnets section in the VPC Dashboard.
- Click on "Create Subnet."
- Select **KCVPC**, enter the name **PublicSubnet**, the IPv4 CIDR block **10.0.1.0/24**, and select the availability zone **eu-west-1a**.
- Click on "Create Subnet."

#### Private Subnet
- Repeat the above steps with the name **PrivateSubnet**, the IPv4 CIDR block **10.0.2.0/24**, and the same availability zone **eu-west-1a**.

### 3. Configure an Internet Gateway (IGW)
- Go to the Internet Gateways section in the VPC Dashboard.
- Click on "Create internet gateway."
- Enter a name for the IGW.
- Click on "Create internet gateway."
- Select the newly created IGW and click on "Actions" -> "Attach to VPC."
- Select **KCVPC** and click on "Attach internet gateway."

### 4. Configure Route Tables
#### Public Route Table
- Go to the Route Tables section in the VPC Dashboard.
- Click on "Create route table."
- Enter the name **PublicRouteTable** and select **KCVPC**.
- Click on "Create route table."
- Select the newly created route table and click on "Edit routes."
- Click on "Add route" and enter **0.0.0.0/0** as the destination and select the IGW as the target.
- Save the route.
- Click on "Subnet Associations" and associate the **PublicSubnet**.

#### Private Route Table
- Repeat the above steps with the name **PrivateRouteTable**.
- Associate the **PrivateSubnet** with this route table.

### 5. Configure NAT Gateway
- Go to the NAT Gateways section in the VPC Dashboard.
- Click on "Create NAT Gateway."
- Select **PublicSubnet** and allocate a new Elastic IP.
- Click on "Create NAT Gateway."
- Go back to the Route Tables section and select **PrivateRouteTable**.
- Click on "Edit routes," add a route with **0.0.0.0/0** as the destination, and select the NAT Gateway as the target.
- Save the route.

### 6. Set Up Security Groups
#### Public Instances Security Group
- Go to the Security Groups section in the VPC Dashboard.
- Click on "Create security group."
- Enter a name and description, and select **KCVPC**.
- Add inbound rules:
  - HTTP (80) from 0.0.0.0/0
  - HTTPS (443) from 0.0.0.0/0
  - SSH (22) from your local IP
- Allow all outbound traffic.
- Click on "Create security group."

#### Private Instances Security Group
- Repeat the above steps with the necessary inbound rules to allow traffic from the **PublicSubnet**.

### 7. Network ACLs
#### Public Subnet NACL
- Go to the Network ACLs section in the VPC Dashboard.
- Click on "Create network ACL."
- Select **KCVPC** and enter a name.
- Click on "Create."
- Select the newly created NACL, go to the "Inbound rules" tab, and add rules for HTTP, HTTPS, and SSH traffic.
- Go to the "Outbound rules" tab and allow all outbound traffic.
- Associate the **PublicSubnet** with this NACL.

#### Private Subnet NACL
- Repeat the above steps with the necessary inbound and outbound rules to allow traffic to and from the **PublicSubnet**.

### 8. Deploy Instances
#### Public EC2 Instance
- Go to the EC2 Dashboard.
- Click on "Launch Instance."
- Select an Amazon Machine Image (AMI).
- Choose an instance type.
- In the "Configure Instance" step, select the **PublicSubnet**.
- Add storage and tags as needed.
- Configure the security group to use the **Public Instances Security Group**.
- Review and launch the instance.
- Verify that you can access the instance via SSH and that HTTP/HTTPS traffic is allowed.

#### Private EC2 Instance
- Repeat the above steps, but select the **PrivateSubnet** and use the **Private Instances Security Group**.
- Verify that the instance can access the internet through the NAT Gateway and can communicate with the public instance.

### screenshots are in the screenshots directory
