## Static webhosting from terraform/AWS

🛠️ Step 1: Set Up Your Environment
1. Install Terraform and AWS CLI.
2. Run aws configure to set up your AWS credentials. 
#
📁 Step 2: Prepare Your Website Files
Place your static files (index.html, error.html) in the same folder as your Terraform files.

Example:

<img width="856" height="329" alt="Image" src="https://github.com/user-attachments/assets/86fdddcd-0174-4608-8574-1ae0d5e5f8e2" />

### 📦 Step 3: Define Your Terraform Files

🔹 provider.tf
```
provider "aws" {
  region = "ap-south-1"
}
```
🔹 resource.tf
```
resource "aws_s3_bucket" "bucket1" {
  bucket = "web-bucket-boban"
}

resource "aws_s3_bucket_public_access_block" "bucket1" {
  bucket = aws_s3_bucket.bucket1.id
  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

resource "aws_s3_object" "index" {
  bucket = "web-bucket-boban"
  key    = "index.html"
  source = "index.html"
  content_type = "text/html"
}

resource "aws_s3_object" "error" {
  bucket = "web-bucket-boban"
  key    = "error.html"
  source = "error.html"
  content_type = "text/html"
}

resource "aws_s3_bucket_website_configuration" "bucket1" {
  bucket = aws_s3_bucket.bucket1.id
  index_document {
    suffix = "index.html"
  }
  error_document {
    key = "error.html"
  }
}

resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.bucket1.id
  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [ "s3:GetObject" ],
      "Resource": [
        "${aws_s3_bucket.bucket1.arn}",
        "${aws_s3_bucket.bucket1.arn}/*"
      ]
    }
  ]
}
EOF
}
```
🔹 output.tf
```
output "websiteendpoint" {
  value = aws_s3_bucket.bucket1.website_endpoint
}
```
### 💻Step 4: Run Terraform Commands in VS Code Terminal

Initialize Terraform:
```
terraform init
```
 Apply the configuration:
```
terraform apply -auto-approve
```
### 🌐 Step 5: Get Your Website Link
After applying, Terraform will show the website endpoint:
```
web-bucket-boban.s3-website.ap-south-1.amazonaws.com
```
<img width="1564" height="974" alt="Image" src="https://github.com/user-attachments/assets/a08cd797-4244-4c65-97c4-db159a9eb930" />
<img width="1919" height="1003" alt="Image" src="https://github.com/user-attachments/assets/4541416d-2e1c-4b9e-91ba-44d004a7e510" />
