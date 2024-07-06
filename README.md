# MultiCloud Resource Implementation with Terraform

This project demonstrates the implementation of resources in a MultiCloud environment using AWS and Google Cloud Platform (GCP) with Terraform. The steps involve creating necessary IAM users, setting up the environment, and provisioning infrastructure using Terraform.

## Prerequisites

- AWS account
- Google Cloud Platform account
- Terraform installed
- IAM permissions for creating and managing resources

## Steps in Amazon Web Services (AWS)

### 1. Creating the terraform-en-1 User using the IAM Service

1. Access the [AWS console](https://aws.amazon.com) and log in.
2. Navigate to IAM from the services menu.
3. Click on **Users** and then **Add users**.
4. Enter the user name `terraform-en-1` and select **Programmatic access**.
5. Attach the policy `AmazonS3FullAccess`.
6. Click **Next**, review, and create the user.

### 2. Creating the Access Key for the terraform-en-1 User

1. Select the `terraform-en-1` user in IAM.
2. Go to the **Security credentials** tab.
3. In the **Access keys** section, click **Create access key**.
4. Select **Command Line Interface (CLI)**.
5. Click **Next** and then **Create access key**.
6. Download the `.csv` file and rename it to `key.csv`.

## Steps in Google Cloud Platform (GCP)

### 1. Preparing the Environment

1. Access the [Google Cloud Console](https://console.cloud.google.com) and log in.
2. Open the Cloud Shell.
3. Download the `mission1.zip` file using the `wget` command:
   ```bash
   wget <https://tcb-public-events.s3.amazonaws.com/icp/mission1.zip>
   ```

### 2. Uploading the key.csv File

1. Upload `key.csv` to the Cloud Shell using the browser upload feature.
2. Verify the files using:
   ```bash
   ls
   ```
3. Prepare the environment:
   ```bash
   unzip mission1.zip
   mv key.csv mission1/en
   cd mission1/en
   chmod +x *.sh
   ```

### 3. Setting Up AWS and GCP Environment

1. Configure AWS credentials:
   ```bash
   mkdir -p ~/.aws/
   touch ~/.aws/credentials_multiclouddeploy
   ./aws_set_credentials.sh key.csv
   ```
2. Configure GCP project:
   ```bash
   GOOGLE_CLOUD_PROJECT_ID=$(gcloud config get-value project)
   gcloud config set project $GOOGLE_CLOUD_PROJECT_ID
   ./gcp_set_project.sh
   ```

### 4. Enabling Required APIs

Enable necessary APIs in GCP:
```bash
gcloud services enable containerregistry.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable sqladmin.googleapis.com
gcloud services enable cloudresourcemanager.googleapis.com
gcloud services enable serviceusage.googleapis.com
gcloud services enable compute.googleapis.com
gcloud services enable servicenetworking.googleapis.com --project=$GOOGLE_CLOUD_PROJECT_ID
```

## Running Terraform to Provision MultiCloud Infrastructure

1. Navigate to the Terraform directory:
   ```bash
   cd ~/mission1/en/terraform/
   ```
2. Initialize Terraform:
   ```bash
   terraform init
   ```
3. Plan the infrastructure:
   ```bash
   terraform plan
   ```
4. Apply the configuration:
   ```bash
   terraform apply
   ```

**Note:** The provisioning process can take 15 to 25 minutes. Keep the Cloud Shell open during this process. If disconnected, reconnect when the session expires.

## Appendix I - Destroying the Environment

To reset the environment, follow these steps:

1. Delete VPC Peering in Google Cloud.
2. Remove resources with Terraform:
   ```bash
   cd ~/mission1/en/terraform/
   terraform destroy
   ```
3. Clean up Cloud Shell in both AWS and Google Cloud:
   ```bash
   cd ~
   rm -rf mission*
   ```

