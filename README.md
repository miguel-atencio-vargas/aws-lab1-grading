# AWS Lab 1 - Grading & Deployment

This project contains the CloudFormation template and verification tests for AWS Lab 1. It includes a GitHub Actions workflow to automatically deploy the infrastructure and run tests on every push to the `main` branch.

## Project Structure
- `s3-bucket.yaml`: CloudFormation template to provision an S3 bucket.
- `test_lab1.py`: Python `pytest` script to verify resources in the AWS account.
- `.github/workflows/deploy.yml`: CI/CD pipeline definition.
- `requirements.txt`: Python dependencies for verification tests.

## Prerequisites

To use the automated deployment, you must configure your AWS credentials in the GitHub repository.

### 1. Get AWS Credentials
You need an **Access Key ID** and **Secret Access Key** for an IAM User with sufficient permissions (e.g., `AdministratorAccess` or specific permissions for CloudFormation, S3, EC2, and IAM).

**If you don't have keys yet:**
1. Log in to the [AWS Console](https://console.aws.amazon.com/).
2. Navigate to the **IAM** service.
3. Click **Users** in the sidebar.
4. Click on your user name (create one if you haven't already).
5. Go to the **Security credentials** tab.
6. Scroll down to **Access keys** and click **Create access key**.
7. Select **Command Line Interface (CLI)** as the use case.
8. Click **Next**, give it a description tag if you want, and click **Create access key**.
9. **IMPORTANT**: Copy or download the `.csv` file containing your `Access Key ID` and `Secret Access Key`. You will not be able to see the Secret Key again.

### 2. Configure GitHub Secrets
Securely store these credentials in your repository so the GitHub Action can use them without exposing them in code.

1. Go to your GitHub repository page.
2. Click on the **Settings** tab (top right).
3. In the left sidebar, click **Secrets and variables**, then select **Actions**.
4. Under "Repository secrets", click the **New repository secret** button.
5. Add the Access Key:
    - **Name**: `AWS_ACCESS_KEY_ID`
    - **Secret**: Paste your Access Key ID (e.g., `AKIA...`).
    - Click **Add secret**.
6. click **New repository secret** again.
7. Add the Secret Key:
    - **Name**: `AWS_SECRET_ACCESS_KEY`
    - **Secret**: Paste your Secret Access Key.
    - Click **Add secret**.

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