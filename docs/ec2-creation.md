## Launching the EC2 Instance

Now, it's time to initially launch the EC2 instance that will host the Twoge application. This initial configuration is extremely important for later success when configuring the internal application and also the Auto Scaling Group (ASG).

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

By following these steps, we have ensured that the EC2 instance has all necessary configurations to make the Twoge application run effectively.
