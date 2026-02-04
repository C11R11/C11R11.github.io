[🏡 Home](index.md)

# Lambda Development 

## Create lambda

> THe best way to keep this proffesional is to generate a terraform module, with the bootstrap code and requirements

```sh
.
├── main.tf          # Terraform configuration
├── src/             # Your Lambda code folder
│   ├── bootstrap    # Your Bash script
│   └── jq           # The jq binary
└── mock_event.json  # For local testing
```

```yml
# Terraform code

# 1. Package the code into a ZIP automatically
data "archive_file" "lambda_zip" {
  type        = "zip"
  source_dir  = "${path.module}/src"
  output_path = "${path.module}/lambda_function.zip"
}

# 2. Define the Lambda Function
resource "aws_lambda_function" "github_api" {
  filename      = data.archive_file.lambda_zip.output_path
  function_name = "github-artifact-api"
  role          = aws_iam_role.lambda_role.arn
  
  # Crucial for Bash scripts
  runtime       = "provided.al2023" 
  handler       = "bootstrap" # Required but not used by custom runtimes

  source_code_hash = data.archive_file.lambda_zip.output_base64sha256

  environment {
    variables = {
      GITHUB_TOKEN = "your-secret-token"
    }
  }
}

# 3. Enable the Public URL
resource "aws_lambda_function_url" "api_url" {
  function_name      = aws_lambda_function.github_api.function_name
  authorization_type = "NONE" # Or AWS_IAM
}

output "lambda_url" {
  value = aws_lambda_function_url.api_url.function_url
}
```

> Local development
```sh
sam local invoke --event mock_event.json
```

> To automate you can create a github workflow or similar to apply the terraform stack, this will copy the boostrap code and requirements, and update. This after testing locally.

## Secure a lambda function

```sh
# 1. Create the user (to invoque the lambda)
aws iam create-user --user-name artifact-api-consumer

# 2. Create an Access Key (Save the output!)
# You will get an AccessKeyId and SecretAccessKey
aws iam create-access-key --user-name artifact-api-consumer

# 3. Create a policy to allow ONLY invoking the Function URL
# Replace YOUR_REGION and YOUR_ACCOUNT_ID
# This one is only for accountability and easy to read purposes, the real permission in on lambda level
# If I remove the user, I will remove all the grants, and the lambda removal gets denial
aws iam put-user-policy --user-name artifact-api-consumer \
  --policy-name AllowLambdaUrlInvoke \
  --policy-document '{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunctionUrl",
            "Resource": "arn:aws:lambda:YOUR_REGION:YOUR_ACCOUNT_ID:function:<lambda-name>"
        }
    ]
}'

# 4.- Grant the specific user permission on the Lambda resource
aws lambda add-permission \
  --function-name <lambda-name> \
  --statement-id AllowSpecificUserInvoke \
  --action lambda:InvokeFunctionUrl \
  --principal arn:aws:iam::YOUR_ACCOUNT_ID:user/artifact-api-consumer \
  --function-url-auth-type AWS_IAM

# 5.- Change or create the function URL with IAM auth (not NONE)

# 6.- Give credentials 

# Setup in postman (this goes to your lambda users)
- Url -> the 
- AccessKey -> AccessKeyId (provided)
- SecretKey -> SecretAccessKey (provided)
- AWS Region -> the-region (provided)
- Service Name -> lambda

# Setup in curl
curl --location '<url>' \
--header 'X-Amz-Date: ••••••' \
--header 'Authorization: ••••••'

```

# Architectural Best practices

## 🛠 Engineering Maturity Matrix

This project evolved from a proof-of-concept to a production-grade utility. The following table highlights the transition from basic implementation to a Senior DevOps architecture.

| Feature | Baseline Implementation (Junior) | Production-Grade (Senior/DevOps) |
| :--- | :--- | :--- |
| **Provisioning** | Manual configuration via AWS Console ("ClickOps"). | **Infrastructure as Code (Terraform)**; reproducible and versioned. |
| **Deployment** | Manual code entry or ZIP upload in browser. | **Automated CI/CD** via GitHub Actions; deploy on push. |
| **Security** | Public access or hardcoded API keys. | **Identity-Based Security** (IAM SigV4) with Least Privilege. |
| **Development** | Testing directly in the Cloud (Live environment). | **Local Simulation** using AWS SAM & Docker for fast feedback. |
| **Maintainability** | Knowledge lives in the developer's head. | **Self-Documenting** via Git history, README, and Terraform. |
| **Scalability** | Hard to replicate for other teams/repos. | **Modular Design**; easy to spin up new instances via TF modules. |

---
*Generated as part of the Artifact Redirector DevEx Initiative.*


### 🌐 API Exposure Strategy

Below is the evaluation of how we expose the Lambda function to the network. For this phase, we are utilizing **Function URLs** to maintain high performance and low complexity for internal tooling.

| Feature | Function URL (Current) | API Gateway | Custom DNS (Route 53) |
| :--- | :--- | :--- | :--- |
| **Setup Time** | ~10 Seconds | ~10 Minutes | ~5 Minutes |
| **Cost** | Included with Lambda | $1.00 per 1M requests | Domain cost (~$12/year) |
| **Auth Options** | IAM SigV4 or Public | IAM, API Keys, JWT, Cognito | N/A (Alias only) |
| **Best Use Case** | Internal tools, Webhooks | Public APIs, Monetization | Production branding |
| **Timeout Limit** | 15 Minutes | 29 Seconds | N/A |
| **Complexity** | Very Low | High | Low |

---
**Decision:** We are using **Function URLs with IAM Auth** because it provides the best balance of security and speed for our internal GitHub integration without the overhead of a managed gateway.