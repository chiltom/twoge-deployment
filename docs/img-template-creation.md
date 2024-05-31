## Creating the Image and Launch Template

In order to create an Auto Scaling group for the Twoge application, an image and template must be created so each new instance that is created in the ASG can exactly replicate the Twoge instance that has been configured.

1. In the EC2 dashboard in the AWS Management Console, click "Instances" and select the Twoge application instance.
2. In the "Actions" dropdown on the top of the page, click "Image and templates" and click "Create image".
3. Configure the image as follows:
  - Image name: Give the image a unique name, like "twoge-app-image".
  - Image description: Optional, but it's good to give the image a description for later reference.

4. Leave all other configurations as default, and click "Create image".
5. Now, navigate back to the "Instances" tab from the navigation sidebar, and click the "Actions" dropdown, select "Image and templates", and click "Create template from instance".
6. For configuration of the launch template:
  - Launch template name: Give the template a unique name, such as "twoge-application-template".
  - Template version description: Give the template a brief description for later reference.
  - Auto Scaling guidance: Check this box for added assistance in creating a template fit for an Auto Scaling Group.
  - AMI: Click on the "My AMIs" tab and select the twoge image just created.
  - Network Settings -> Subnet: Change the subnet to "Dont include in launch template". This ensure that the Auto Scaling Group can launch instances in multiple subnets for maximum availability.
  - Advanced details -> Shutdown behavior: Change this value to "Don't include in launch template".
  - Advanced details -> Stop - Hibernate behavior: Change this value to "Don't include in launch template".

7. Once configured, click "Create launch template" in the "Summary" panel.

Now, a launch template with the Twoge application image has been created for later use. If desired, a new EC2 instance could be immediately launched from this template with all of the configurations and setups for the Twoge application that we have made so far. However, the next step is to create an Auto Scaling Group so that AWS handles this for us.
