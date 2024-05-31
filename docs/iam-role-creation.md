## Creating the IAM Role for S3 Access

In order for the Twoge application's EC2 instance to access the static content in the S3 bucket, we must configure an IAM role and policy for the EC2 instance to assume. This role will allow the EC2 instance to gain full access to the S3 resource while it is assumed.

1. Navigate to the IAM service in the AWS Management Console.
2. Select "Roles" from the navigation sidebar.
3. Select "Create role" in the upper right-hand corner.
4. Configure the role in the following way:
   - Trusted entity type: Leave the default "AWS service".
   - Use case: Select "EC2" for the "Service or use case" dropdown.
   - Leave "EC2" selected. Click "Next" in the lower right-hand corner.
   - Type "AmazonS3FullAccess" in the search bar underneath "Permissions policies". Check the box next to it and select "Next" in the lower right-hand corner.
   - Under "Role details" enter a unique name and a short description for the role.

5. Leave all other values as default, and click "Create role" in the bottom right-hand corner of the page.

Ensure that the configuration is correct by clicking on the IAM Role and visiting the "Trust relationships" tab. The "Trusted entities" should look like this:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

Additionally, by using the AWS managed policy for AmazonS3FullAccess, the policy looks like this:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "s3-object-lambda:*"
            ],
            "Resource": "*"
        }
    ]
}
```

Both of these configurations ensure that the EC2 instance that assumes this role will have access to S3 resources within the IAM account.
