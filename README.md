# How to transfer S3 bucket between AWS account

The dest user need to be able to create user policies

## Install AWS CLI

https://aws.amazon.com/cli/

## Copy config files

```
cp credentials{.sample,}
cp new_bucket_policy.json{.sample,}
cp new_user_policy.json{.sample,}
```

## Account ID

Get the dest user's account ID and put it in `new_bucket_policy.json`

(it's the account number in AWS Management Console)

## Keys

Set the `aws_access_key_id` and `aws_secret_access_key` in the `credentials` file

## Bucket names

Replace `SRC_BUCKET_NAME` and `DEST_BUCKET_NAME` in the json files

## Backup the src bucket policy

`AWS_CONFIG_FILE=credentials aws --profile src s3api get-bucket-policy --bucket SRC_BUCKET_NAME --query Policy --output text > old_bucket_policy.json`

## Update the src bucket policy

:warning: This will erase the current policy

`AWS_CONFIG_FILE=credentials aws --profile src s3api put-bucket-policy --bucket SRC_BUCKET_NAME --policy file://new_bucket_policy.json`

## Update the dest user policy

`AWS_CONFIG_FILE=credentials aws --profile dest iam put-user-policy --user-name DEST_USERNAME --policy-name MigrateBucketPolicy --policy-document file://new_user_policy.json`

## Start syncing the two buckets

`AWS_CONFIG_FILE=credentials aws --profile dest s3 sync s3://SRC_BUCKET_NAME s3://DEST_BUCKET_NAME
`

:ballot_box_with_check:  Done

## To restore the original bucket policy on the src bucket

`AWS_CONFIG_FILE=credentials aws --profile src s3api put-bucket-policy --bucket SRC_BUCKET_NAME --policy file://old_bucket_policy.json`
