# Twoge Deployment Assessment

> Code Platoon Bravo DevOps Cohort Assessment One

## Purpose

This assessment covers the first modules of the Code Platoon DevOps curriculum. This comprises of Amazon Web Services resources and services, including:

- EC2 (Elastic Compute Cloud): To host the Twoge application.
- S3 (Simple Storage Service): To store static files, such as images, videos, and other assets.
- IAM (Identity and Access Management): To manage services' access and permissions to AWS resources.
- VPC (Virtual Private Cloud): To create a secure and isolated network environment for the application.
- ALB (Application Load Balancer): To distribute incoming traffic across multiple EC2 instances.
- ASG (Auto Scaling Group): To automatically scale EC2 instances up or down based on the demand.
- SNS (Simple Notification Service): To receive notifications about your application's performance and health.
- RDS (Relational Database Service): To host the database for the application.

The goal of this assessment is to deploy the Twoge application to AWS using AWS' many services and their respective advantages. The [source code is located here](https://github.com/chandradeoarya/twoge).

Clone the code into your local machine for certain portions of this assessment with the following command:
```shell
git clone https://github.com/chandradeoarya/twoge
```

## Steps for Accomplishment

### Create the VPC

1. Go to the Amazon VPC service in the AWS Management Console.
2. Click "Your VPCs" in the navigation sidebar.
3. Click "Create VPC" in the upper right-hand corner.
4. For the Twoge application, select the following configurations:
   - Resources to create: VPC and more.
   - Name tag auto-generation: Leave selected if desired, and enter a unique identifier for your Twoge application.
   - IPv4 CIDR block: Enter a valid CIDR block that is unique to the VPC and allocates the necessary number of IP addresses for the amount of resources that will connect to this network.
     - In this assessment, the CIDR block 192.168.0.0/24 allows for 256 different IP addresses, which will be more than enough for the purposes of the Twoge application.
   - IPv6 CIDR block: Leave "No IPv6 CIDR block" selected.
   - Number of Availability Zones: Leave the default of 2.
   - Number of public subnets: Leave the default of 2. One subnet will host the application's EC2 instances, and the other will host the application's RDS postgres database.
   - Number of private subnets: Select 0, as no private subnets are needed for this application.
   - NAT Gateways: Leave the default value of "None".
   - VPC endpoints: Leave the default of "S3 Gateway"; we will need this gateway to connect to the S3 bucket hosting Twoge's static assets.
5. Once configured, click "Create VPC".
6. Once the VPC is fully created and configured, select "View VPC" to view the VPC and all of its assets.

> [!NOTE]
> Note the other resources that are created by this VPC launch wizard. It creates an internet gateway, attaches it to the VPC for internet access, creates a route table along with its routes, and associates it with the public subnets. This is important to understanding the architecture and communication of the VPC and all of its internal components.

### Create the S3 Bucket

1. Go to the S3 service in the AWS Management Console.
2. Click on "Create bucket".
3. Configure the S3 bucket as such:
    - Bucket type: Leave the default value of "General purpose".
    - Bucket name: Give the bucket a unique name.
    - Object Ownership: Leave the default value of "ACLs disabled". An IAM role will be configured in the next step to allow public access of the S3 bucket.
    - Block Public Access settings for this bucket: Leave the default value of "Block *all* public access" enabled. The S3 endpoint attached to the VPC along with the IAM role for access will allow our VPC to access this S3 bucket.
    - Bucket Versioning: Leave this disabled.
    - Default encryption: Leave these values as default.
4. Click "Create bucket" once it is configured.
5. Click on the name of your new S3 bucket in the S3 dashboard.
6. Click "Upload" in the upper right hand corner of the S3 bucket.
7. Click "Add Folder" and select the `static` folder within the `twoge` application on your local machine. Click "upload".
8. Finally, select "Upload" in the AWS Management Console to upload the selected files to the S3 bucket.

### Create the IAM Role for S3

1. Navigate to the IAM service in the AWS Management Console.
2. Select "Roles" from the navigation sidebar.
3. Select "Create role" in the upper right-hand corner.
4. Configure the role in the following way:
    - Trusted entity type: Leave the default "AWS service".
    - Use case: Select "EC2" for the "Service or use case" dropdown.
    - Leave "EC2" selected. Click "Next" in the lower right-hand corner.
    - Type "AmazonS3FullAccess" in the search bar underneath "Permissions policies". Check the box next to it and select "Next" in the lower right-hand corner.
    - Under "Role details" enter a unique name and a short description for the role.
    - Leave all other values as default, and click "Create role" in the bottom right-hand corner of the page.

### Launch the EC2 Instance

1. Navigate to the EC2 service in the AWS Management Console.
2. Click "Instances" in the navigation sidebar.
3. Click "Launch instances" in the upper right-hand corner.
4. Configure your EC2 instance in the following way:
    - Name: Give your EC2 instance a unique name.
    - Application and OS Images: Select "Amazon Linux" under the Quick Start tab, and select "Amazon Linux 2 AMI" under Amazon Machine Image.
    - Instance type: Leave the default "t2.micro".
    - Key pair: Select your existing key pair or create a new RSA key and select it here to be able to SSH into the instance.
    - Network settings: Click "Edit" in the upper right-hand corner:
        - VPC: Select the VPC that was created earlier in this process.
        - Subnet: Select one of your public subnets. Note which subnet it is in.
        - Auto-assign public ID: Make sure that this setting is enabled for internet access from the EC2 instance.
        - Firewall (security groups): Select or configure a security group that allows for both SSH connection from your machine's IP address and HTTP requests from all IP addresses (0.0.0.0/0).
    - Leave all other values as default and select "Launch instance" in the right-hand summary panel.
5. Select "View all instances" in the bottom right-hand corner of the page.

### Set Up the Twoge Application

1. Click the checkbox for the new twoge application instance that was created.
2. Click the "Connect" box among the options on the top of the page.
3. Click on the "SSH client" tab and follow the steps to connect to the instance. An example is:
```shell
cd <LOCATION-OF-MY-KEY>
chmod 400 <MY-KEY.pem>
ssh -i <MY-KEY.pem> ec2-user@<INSTANCE-PUBLIC-IP>
```
> [!TIP]
> The console may display a warning regarding the fact that the IP address is unknown and that it does not know the authenticity of it. This is due to the fact that the IP address is new to your machine's known hosts. If the IP address is the correct one from your EC2's public IP address, type "yes" and the IP address will be added to your machine's known hosts. It will then connect to the instance.
4. Once

### CREATE AN IMAGE FROM THE EXISTING EC2 AND SELECT THAT AMI FROM THE LAUNCH TEMPLATE