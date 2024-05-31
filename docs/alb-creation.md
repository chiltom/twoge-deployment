## Creating the Application Load Balancer

To ensure that traffic is distributed evenly among the EC2 instances in our incoming Auto Scaling Group, an Application Load Balancer is necessary.

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
