[back](cli-index)

# Commands

```sh
# configuration 
cat .aws/config   
# output
[profile dev]
region = us-east-1
output = json
[default]
region = us-east-1

s
```

# ==========================================
# AWS CLI Cheatsheet
# ==========================================

# ------------------------------------------
# 1. Global Configuration & Basics
# ------------------------------------------

# Configure the AWS CLI (prompts for Access Key, Secret Key, Region, Output format)
aws configure

# Configure a specific profile
aws configure --profile my-profile

# Execute a command using a specific profile
aws s3 ls --profile my-profile

# Verify your current identity/authentication status
aws sts get-caller-identity

# Check AWS CLI version
aws --version


# ------------------------------------------
# 2. IAM (Identity and Access Management)
# ------------------------------------------

# List all IAM users
aws iam list-users

# Create a new IAM user
aws iam create-user --user-name my-new-user

# Get user info
aws iam get-user --user-name my-new-user

# Create access keys for a user (save the output!)
aws iam create-access-key --user-name my-new-user

# List all IAM roles
aws iam list-roles

# Attach a managed policy to a user
aws iam attach-user-policy \
    --user-name my-new-user \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Show user policies
aws iam list-attached-user-policies --user-name my-new-user

# Shows all IAM groups the designated user belongs to.
aws iam list-groups-for-user --user-name my-new-user

# Displays assigned physical or virtual security MFA configurations protecting the identity
aws iam list-mfa-devices --user-name my-new-user

# ------------------------------------------
# 3. S3 (Simple Storage Service)
# ------------------------------------------

# List all S3 buckets
aws s3 ls

# List contents of a specific bucket
aws s3 ls s3://my-bucket-name

# Make a new bucket
aws s3 mb s3://my-new-bucket-name

# Copy a local file to an S3 bucket
aws s3 cp my-file.txt s3://my-bucket-name/

# Download a file from S3 to local
aws s3 cp s3://my-bucket-name/my-file.txt .

# Sync a local directory to an S3 bucket
aws s3 sync ./my-local-dir s3://my-bucket-name/

# Remove/Delete a file from a bucket
aws s3 rm s3://my-bucket-name/my-file.txt

# Delete an empty bucket
aws s3 rb s3://my-bucket-name


# ------------------------------------------
# 4. EC2 (Elastic Compute Cloud)
# ------------------------------------------

# List all EC2 instances (filtering for Instance ID and State)
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId, State.Name]' \
    --output table

# Launch a new EC2 instance
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type t2.micro \
    --key-name my-key-pair

# Start an EC2 instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Stop an EC2 instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Terminate an EC2 instance
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0


# ------------------------------------------
# 5. Lambda
# ------------------------------------------

# List all Lambda functions
aws lambda list-functions \
    --query 'Functions[*].FunctionName' \
    --output table

# Invoke a Lambda function manually
aws lambda invoke \
    --function-name my-function-name \
    --payload '{"key": "value"}' \
    --cli-binary-format raw-in-base64-out \
    response.json

# Update the code of an existing Lambda function
aws lambda update-function-code \
    --function-name my-function-name \
    --zip-file fileb://my-deployment-package.zip

# Get Lambda function configuration details
aws lambda get-function --function-name my-function-name


# ------------------------------------------
# 6. API Gateway
# ------------------------------------------

# List all REST APIs
aws apigateway get-rest-apis

# Create a new REST API
aws apigateway create-rest-api --name 'MyNewAPI'

# Deploy an API to a specific stage
aws apigateway create-deployment \
    --rest-api-id a1b2c3d4e5 \
    --stage-name prod

# Get resources for a specific API
aws apigateway get-resources --rest-api-id a1b2c3d4e5