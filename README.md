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
    - 