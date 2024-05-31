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
   - VPC endpoints: Uncheck the "S3 Gateway" and don't create any extra endpoints.
5. Once configured, click "Create VPC".

> [!IMPORTANT]
> Note the other resources that are created by this VPC launch wizard. It creates an internet gateway, attaches it to the VPC for internet access, creates a route table along with its routes, and associates it with the public subnets. This is important to understanding the architecture and communication of the VPC and all of its internal components.

6. Once the VPC is fully created and configured, select "View VPC" to view the VPC and all of its assets.

### Create the S3 Bucket

1. Go to the S3 service in the AWS Management Console.
2. Click on "Create bucket".
3. Configure the S3 bucket as such:
   - Bucket type: Leave the default value of "General purpose".
   - Bucket name: Give the bucket a unique name.
   - Object Ownership: Leave the default value of "ACLs disabled". An IAM role will be configured in the next step to allow public access of the S3 bucket.
   - Block Public Access settings for this bucket: Leave the default value of "Block _all_ public access" enabled. The S3 endpoint attached to the VPC along with the IAM role for access will allow our VPC to access this S3 bucket.
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

### Configure S3 Bucket Policy

Now, a policy can be added to the S3 bucket to allow access to the IAM Role.

1. Navigate to the S3 service in the AWS Management Console, and click on the Twoge S3 bucket.
2. Select the "Permissions" tab, find the "Bucket policy" section, and click "Edit".
3. Add the following statement, replacing the specified values with your specific resource names:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT_ID:role/ROLE_NAME"
      },
      "Action": "s3:*",
      "Resource": ["arn:aws:s3:::BUCKET_NAME", "arn:aws:s3:::BUCKET_NAME/*"]
    }
  ]
}
```

4. Finally, click "Save changes".

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
     - Firewall (security groups): Select or configure a security group that allows for SSH connection from your machine's IP address and HTTP requests from all IP addresses (0.0.0.0/0).
   - Leave all other values as default and select "Launch instance" in the right-hand summary panel.
5. Select "View all instances" in the bottom right-hand corner of the page.
6. Select the "Actions" tab in the options bar at the top of the page, select "Security", and select "Modify IAM role".
7. Select the "IAM role" dropdown and select the IAM role created for the instance to access S3.
8. Click "Update IAM role".

### Launch the RDS Database

1. Navigate to the RDS service in the AWS Management Console.
2. Click on "Databases" in the navigation sidebar.
3. Click "Create database" in the top right-hand corner of the databases view.
4. Configure the RDS instance as follows:
   - Creation method: Leave "Standard create" selected.
   - Engine type: Select "PostgreSQL". This is the database that Twoge uses.
   - Engine version: Leave the default values.
   - Templates: Select "Free tier".
   - DB instance identifier: Give the RDS instance a unique name.
   - Credentials Settings: Use the master username of "postgres" and select the "Self managed" option for credentials. Enter a password and note it for later use.
   - Compute resource: Select "Connect to an EC2 compute resource".
   - EC2 instance: Select the EC2 instance that will host your Twoge application.
   - VPC security group (firewall): Select "Create new" and give it a unique name. RDS will configure this security group to connect with the EC2 instance.
   - Monitoring: Turn off "Performance Insights".
   - Additional configuration:
     - Initial database name: Use the name "twoge_db".
     - Backup: De-select "Enable automated backups".
     - Encryption: De-select "Enable encryption".
     - Maintenace: De-select "Enable auto minor version upgrade".
5. Once the RDS instance is configured, click "Create database".
6. After the RDS instance is set up successfully, click on the "DB identifier".
7. Ensure the security group is configured correctly, and fix the inbound and outbound rules if it is not.
8. Scroll down to "Connected compute resources" and click "Set up EC2 connection".
9. In the "EC2 instance" dropdown, select the EC2 instance hosting the Twoge application. Then, click "Continue".
10. Click "Set up".

### Set Up the Twoge Application

1. Navigate to the EC2 service again in the AWS Management Console and select your Twoge application EC2.
2. Click the "Connect" box among the options on the top of the page.
3. Click on the "SSH client" tab and follow the steps to connect to the instance. An example is:

```shell
cd <LOCATION-OF-MY-KEY>
chmod 400 <MY-KEY.pem>
ssh -i <MY-KEY.pem> ec2-user@<INSTANCE-PUBLIC-IP>
```

> [!TIP]
> The console may display a warning regarding the fact that the IP address is unknown and that it does not know the authenticity of it. This is due to the fact that the IP address is new to your machine's known hosts. If the IP address is the correct one from your EC2's public IP address, type "yes" and the IP address will be added to your machine's known hosts. It will then connect to the instance.

4. Once a connection is established to the instance, run the following commands to configure the Twoge application environment.

```shell
sudo yum update
sudo yum install git
git clone https://github.com/chandradeoarya/twoge
cd twoge
vim .env # Add SQLALCHEMY_DATABASE_URI=postgresql://postgres:password@rds-endpoint
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

5. Install Nginx on the EC2 instance with the following commands:

```shell
sudo amazon-linux-extras install nginx1 -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

6. Create a wsgi.py file in the twoge directory and add the following lines to it:

```python
from app import app

if __name__ == '__main__':
    app.run()
```

6. Create a custom Twoge daemon using the following command:

```shell
sudo vim /etc/systemd/system/twoge.service
# Add the following lines within
[Unit]
Description=Gunicorn instance to serve the Twoge application
After=network.target

[Service]
User=ec2-user
Group=nginx
WorkingDirectory=/home/ec2-user/twoge
Environment="PATH=/home/ec2-user/twoge/.venv/bin"
ExecStart=/home/ec2-user/twoge/.venv/bin/gunicorn -w 3 -b 0.0.0.0:8000 wsgi:app

[Install]
WantedBy=multi-user.target
```

7. Start and enable the twoge daemon:

```shell
sudo systemctl start twoge
sudo systemctl enable twoge
```

8. Create an Nginx configuration for the Flask application:

```shell
sudo touch /etc/nginx/conf.d/twoge.conf
```

9. Add the following content to the Nginx configuration:

```nginx
server {
    listen 80;
    server_name <your_domain_or_IP>;

    location / {
        proxy_pass http://0.0.0.0:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

10. Test the Nginx configuration and, if successful, restart Nginx:

```shell
sudo nginx -t
sudo systemctl restart nginx
```

Now, your Twoge application is successfully hosted at your EC2 instance's public IP address.

### Create a Target Group

In order to use an Application Load Balancer to distribute traffic across the EC2 instances, a Target Group must be created to instruct the ALB where to send traffic.

1. In the EC2 dashboard in the AWS Management Console, click "Target groups" in the navigation sidebar.
2. Click "Create target group".
3. For the configuration, use the following values:
  - Target type: Select "Instances".
  - Target group name: Give the target group a unique name, like "twoge-TG".
  - Protocol: Port: Leave the default of "HTTP: 80".
  - IP address type: Leave the default of "IPv4".
  - VPC: Select the VPC for the Twoge application.
  - Protocol version: Leave the default value of "HTTP1".
  - Click "Next".
  - Register targets: Select the EC2 instance hosting the Twoge application, and click "Include as pending below".
  - Click "Create target group".

### Create the Application Load Balancer

1. In the EC2 service dashboard in the AWS Management Console, click "Load Balancers" in the navigation sidebar.
2. Click "Create load balancer" in the upper right-hand corner.
3. Under "Application Load Balancer", click "Create".
4. For the ALB, use the following configuration:
  - Load balancer name: Use a unique name for the load balancer, such as "twoge-alb".
  - Scheme: Leave default value of "Internet-facing".
  - IP address type: Leave default of "IPv4".
  - VPC: Select the VPC created for the Twoge application.
  - Mappings: Select all availability zones that are available.
  - Security groups: Remove the default security group and select the security group configured for the EC2 instance with SSH and HTTP inbound access, and all outbound traffic access.
  - Listeners and routing: Leave the Protocol and Port values alone, but select the previously created target group for the "Default action" dropdown.
5. Leave all other values alone, and click "Create load balancer".

Now, the ALB will be provisioned and created by AWS. Once completed, you will be able to visit the DNS name for the load balancer and see it loads the application, meaning that the load balancer is forwarding traffic correctly to the associated target group.

### Create an Image and Template of the EC2 instance

In order to create an Auto Scaling group for the Twoge application, an image and template must be created so each new instance that is created in the ASG can exactly replicate the Twoge instance that has been configured.

1. In the EC2 dashboard in the AWS Management Console, click "Instances" and select the Twoge application instance.
2. In the "Actions" dropdown on the top of the page, click "Image and templates" and click "Create image".
3. Configure the image as follows:
  - Image name: Give the image a unique name, like "twoge-app-image".
  - Image description: Optional, but it's good to give the image a description for later reference.
4. Leave all other configurations as default, and click "Create image".



### If error occurs, change nginx configuration to an elastic IP that needs to be created