## Creating the RDS Database

Next, we will launch an RDS instance using the PostgreSQL engine to create the database for the Twoge application to use.

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

> [!NOTE]
> By setting up the connection between the RDS and EC2 instances in this way, AWS automatically configures additional security groups for the two services to ensure that they can connect to each other.

Now, the Twoge application has a database to connect to and utilize.
