# CI/CD Deployment to S3 (Reference)

This file contains both the GitHub Actions workflow and the S3 bucket policy used for automated deployment.  
All sensitive values have been replaced with placeholders.

---

## GitHub Actions Workflow (example)


name: My-s3-static-website
on: push
jobs:
  deploy01:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code from repo
        uses: actions/checkout@v4
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: sample-region-1
      - name: Sync files to S3 bucket
        run: |
          aws s3 sync . s3://sample-bucket-name/ --exclude "*" --include "index.html"


S3 Bucket Policy (bucket-policy.json)
json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::sample-bucket-name/*"
    },
    {
      "Sid": "AllowListBucketForSync",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/sample-user"
      },
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::sample-bucket-name"
    },
    {
      "Sid": "AllowPutObjectForSync",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/sample-user"
      },
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::sample-bucket-name/*"
    }
  ]
}


Note:
The workflow file goes in .github/workflows/deploy.yml if you want it to run.

The bucket policy JSON must be applied in the AWS S3 console under your bucket’s permissions.

This combined file (reference.md) is for documentation only — it shows both the automation pipeline and the cloud permissions setup in one place.



