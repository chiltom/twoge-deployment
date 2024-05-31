## Creating the S3 Bucket

In this step of the deployment, we will create an S3 bucket to store all of the static assets for the Twoge application.

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

Now all of the Twoge application's static assets are stored in an S3 bucket for ease of content delivery.
