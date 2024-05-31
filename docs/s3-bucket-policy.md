## S3 Bucket Policy Creation

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

By updating the bucket policy, we ensure that only the role we created prior to this can access the S3 bucket, and no other roles or users can access the bucket.
