## Creating the Twoge Target Group

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
4. Once configured, click "Create target group".

Now the Target Group is created and ready for the Application Load Balancer.
