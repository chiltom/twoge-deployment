## Creating the Auto Scaling Group

To ensure that our application has high availability, scalability, and elasticity, we will create an Auto Scaling Group (ASG).

1. In the EC2 dashboard, click "Auto Scaling Groups" in the navigation sidebar.
2. Click "Create Auto Scaling group".
3. Use the following configuration:
  - Auto Scaling group name: Give the ASG a unique name, such as "twoge-ASG".
  - Launch template: Select the launch template that was just created and click "Next".
  - VPC: Select the twoge application VPC.
  - Availability Zones and subnets: Select the two public subnets available (not the RDS subnet) and click "Next".
  - Load balancing: Select "Attach to an existing load balancer".
  - Attach to an existing load balancer: Leave "Choose from your load balancer target groups" selected and select the Target Group created for the Twoge application from the dropdown.
> [!NOTE]
> This Target Group is already associated with our Application Load Balancer so, as a result, the Load Balancer is automatically associated with it as well.
  - Click "Next" at the bottom of this page.
  - Desired capacity: Increase this to 2 instances.
  - Min desired capacity: Leave the default value of 1.
  - Max desired capacity: Increase this to 4 instances.
  - Automatic scaling: Select "Target tracking scaling policy".
  - Scaling policy name: Give the policy a meaningful name, like "CPU utilization policy".
  - Metric type: Leave the default of "Average CPU utilization".
  - Target value: Increase this to 70.
  - Scroll past all other defaults and click "Next".
  - Add notifications: Click "Add notification".
  - Click "Create a topic".
  - Send a notification to: Enter a SNS topic name, such as "twoge-scaling"
  - With these recipients: Enter your email address.
  - Leave all other defaults checked and click "Next".
  - Click "Next" again.
  - Scroll down and click "Create Auto Scaling group".
4. Navigate back to the EC2 Dashboard in the AWS Management Console and select the original EC2 instance that was manually configured.
  - Shut this instance down, the Auto Scaling Group will now handle the Twoge Application instances.
5. To see if the ASG is behaving correctly and the ALB is communicating with it, navigate to the ALB's DNS name and ensure that the application loads successfully.
  - If so, the application will load as expected.
