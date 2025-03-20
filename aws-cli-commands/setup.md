#Initial CLI setup for AWS

1. Created AWS free tier and logged in as root user account
2. Created IAM user, attached (Administrator) policy and downloaded Access key and secret access key
3. In order to give CLI access also (along with console access), below commands were used

aws --version

aws configure
AWS Access key id:
Secret Access key:
Region:
output format:

Verify:
aws iam list-users
aws s3 ls