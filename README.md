<img src="./assets/twoge.png" width="200px" height="200px" />

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

[**1. Create the VPC**](./docs/vpc-creation.md): Creates the VPC required to house and connect all required AWS services and resources.

[**2. Create the S3 Bucket**](./docs/s3-bucket-creation.md): Creates the S3 bucket to hold all static assets.

[**3. Create the IAM Role for S3**](./docs/iam-role-creation.md): Creates the IAM role for the Twoge application EC2 to assume for access to S3 buckets.

[**4. Configure S3 Bucket Policy**](./docs/s3-bucket-policy.md): Updates the S3 bucket's policy to allow the new IAM role to access it.

[**5. Launch the EC2 Instance**](./docs/ec2-creation.md): Launch the initial EC2 instance with necessary hardware, software, network, and security configurations.

[**6. Launch the RDS Database**](./docs/rds-postgres-creation.md): Create the RDS instance hosting PostgreSQL for the Twoge application to utilize.

[**7. Set Up the Twoge Application**](./docs/twoge-setup.md): Configure and launch the Twoge daemon and Nginx configuration for hosting the application.

**8. Test the RDS and EC2 Connection**

- To ensure that the RDS and EC2 are communicating correctly, navigate to the "Twoges" tab of the application and create a new post.

- If the post registers successfully and displays on the page, then the RDS connection is established and working correctly.

[**9. Create a Target Group**](./docs/target-group-creation.md): Create the Target Group for the Application Load Balancer.

[**10. Create the Application Load Balancer**](./docs/alb-creation.md): Create the ALB for the Auto Scaling Group.

[**11. Create the Launch Template of the EC2 instance**](./docs/img-template-creation.md): Create the Launch Template for the ASG to use.

[**12. Create the ASG**](./docs/asg-creation.md): Create the Auto Scaling Group to both scale in and scale out the Twoge application.

[**13. Test SNS**](./docs/testing-sns.md): Ensure that SNS scaling notifications work correctly.
