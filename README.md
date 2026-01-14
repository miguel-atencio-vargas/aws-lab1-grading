# AWS Lab 1 - Grading & Deployment

This project contains the CloudFormation template and verification tests for AWS Lab 1. It includes a GitHub Actions workflow to automatically deploy the infrastructure and run tests on every push to the `main` branch.

## Project Structure
- `s3-bucket.yaml`: CloudFormation template to provision an S3 bucket.
- `test_lab1.py`: Python `pytest` script to verify resources in the AWS account.
- `.github/workflows/deploy.yml`: CI/CD pipeline definition.
- `requirements.txt`: Python dependencies for verification tests.

## Prerequisites

To use the automated deployment, you must configure your AWS credentials in the GitHub repository.

### 1. Configure OpenID Connect (OIDC) in AWS
To allow GitHub Actions to deploy without long-lived credentials, you must set up an OIDC provider and an IAM Role.

**Step A: Add the Identity Provider**
1.  Go to the [IAM Console](https://console.aws.amazon.com/iam/).
2.  Navigate to **Identity providers** -> **Add provider**.
3.  Select **OpenID Connect**.
4.  **Provider URL**: `https://token.actions.githubusercontent.com`
5.  **Audience**: `sts.amazonaws.com`
6.  Click **Add provider**.

**Step B: Create the IAM Role**
1.  Navigate to **Roles** -> **Create role**.
2.  Select **Web identity**.
3.  Choose the provider you just created.
4.  Choose `sts.amazonaws.com` as the Audience.
5.  Click **Next**.
6.  **Add Permissions**: Attach the necessary policies (e.g., `AdministratorAccess` or a custom policy for CloudFormation, S3, EC2, and IAM).
7.  Name the role (e.g., `GitHubDeployRole`) and create it.

**Step C: Configure Trust Policy**
1.  Click on the newly created role.
2.  Go to the **Trust relationships** tab and click **Edit trust policy**.
3.  Ensure the policy looks like this (replace `OWNER/REPO` with your GitHub username and repository name):
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": {
                    "Federated": "arn:aws:iam::YOUR_ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
                },
                "Action": "sts:AssumeRoleWithWebIdentity",
                "Condition": {
                    "StringEquals": {
                        "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                    },
                    "StringLike": {
                        "token.actions.githubusercontent.com:sub": "repo:OWNER/REPO:*"
                    }
                }
            }
        ]
    }
    ```

### 2. Configure GitHub Secrets
1.  Copy the **ARN** of the Role you just created (e.g., `arn:aws:iam::123456789012:role/GitHubDeployRole`).
2.  Go to your GitHub repository -> **Settings** -> **Secrets and variables** -> **Actions**.
3.  Click **New repository secret**.
4.  **Name**: `AWS_ROLE_ARN`
5.  **Value**: Paste the Role ARN.
6.  Click **Add secret**.

## Local Development

If you want to run tests locally:
1. Install dependencies:
   ```sh
   pip install -r requirements.txt
   ```
2. Configure AWS CLI:
   ```sh
   aws configure
   ```
3. Run tests:
   ```sh
   pytest test_lab1.py
   ```