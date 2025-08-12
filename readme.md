# AltSchool CloudLaunch Project – Semester 3 Month 1 Assessment

## Project Overview

CloudLaunch is a lightweight platform designed to showcase a basic company website and store internal private documents. The goal of this project is to deploy CloudLaunch using core AWS services, demonstrating fundamental AWS skills in S3 bucket hosting, IAM user permissions, and VPC network design.

---

## Task 1: Static Website Hosting Using S3 + IAM User with Limited Permissions

### Steps Taken

1. **S3 Buckets Created:**

   - `cloudlaunch-site-bucket-5416`  
     - Configured for static website hosting with an `index.html` file uploaded.  
     - Public access enabled for read-only anonymous users through a bucket policy.  
     - Bucket policy configured to allow public `s3:GetObject` access to website files only.

   - `cloudlaunch-private-bucket-5416`  
     - Private bucket with no public access.  
     - Access restricted exclusively to the IAM user for `GetObject` and `PutObject` permissions only.  
     - No delete permissions granted.

   - `cloudlaunch-visible-only-bucket-5416`  
     - Private bucket with no public access.  
     - IAM user can list the bucket contents (`ListBucket`) but cannot access (read/write) the objects inside.

2. **IAM User Created:**

   - User named `cloudlaunch-user` was created.  
   - Custom IAM policy attached to this user that restricts actions to the three S3 buckets only:  
     - `ListBucket` on all three buckets.  
     - `GetObject` on `cloudlaunch-site-bucket-5416`.  
     - `GetObject` and `PutObject` on `cloudlaunch-private-bucket-5416`.  
     - No delete permissions anywhere.  
     - No object access on `cloudlaunch-visible-only-bucket-5416` except bucket listing.  
   - Password policy enforced to require password change on first login.

### Static Website Link

- S3 static website endpoint URL: `[INSERT YOUR S3 WEBSITE URL HERE]`

---

## Task 2: VPC Design for CloudLaunch Environment

### Steps Taken

1. **Created a VPC:**

   - VPC named `cloudlaunch-vpc` with CIDR block `10.0.0.0/16`.

2. **Created Subnets:**

   - Public Subnet: `10.0.1.0/24` (named `cloudlaunch-vpc-subnet-public1`)  
     - Intended for load balancers or public-facing services.  
     - Auto-assign public IPv4 addresses enabled.

   - Application Subnet: `10.0.2.0/24` (named `cloudlaunch-app-rt`)  
     - Intended for private application servers.

   - Database Subnet: `10.0.3.0/28` (named `cloudlaunch-db-rt`)  
     - Intended for private database services.

3. **Internet Gateway (IGW):**

   - Created and attached `cloudlaunch-igw` to the VPC.

4. **Route Tables:**

   - Created route table `cloudlaunch-public-rt` associated with the public subnet.  
     - Configured route `0.0.0.0/0` → Internet Gateway.

   - Created route tables `cloudlaunch-app-rt` and `cloudlaunch-db-rt` for private subnets.  
     - No routes to internet gateway or NAT; fully private.

5. **Security Groups:**

   - `cloudlaunch-app-sg`  
     - Allows HTTP (port 80) access within the VPC only (inbound).  

   - `cloudlaunch-db-sg`  
     - Allows MySQL (port 3306) access from the application subnet only (inbound).  

6. **IAM Permissions for VPC:**

   - Updated `cloudlaunch-user` to have read-only access to VPC components (subnets, route tables, security groups) via a custom IAM policy granting `ec2:Describe*` actions.

---

## IAM Policy JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "ListSpecificBuckets",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": [
        "arn:aws:s3:::cloudlaunch-site-bucket-5416",
        "arn:aws:s3:::cloudlaunch-private-bucket-5416",
        "arn:aws:s3:::cloudlaunch-visible-only-bucket-5416"
      ]
    },
    {
      "Sid": "GetFromSiteBucket",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::cloudlaunch-site-bucket-5416/*"
    },
    {
      "Sid": "GetPutPrivateBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::cloudlaunch-private-bucket-5416/*"
    },
    {
      "Sid": "VPCReadOnly",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVpcs",
        "ec2:DescribeSubnets",
        "ec2:DescribeRouteTables",
        "ec2:DescribeSecurityGroups"
      ],
      "Resource": "*"
    }
  ]
}

## Additional Information

* **AWS Account ID:** `[562169195760]`
* **AWS Console URL:** `https://562169195760.signin.aws.amazon.com/console`
* **IAM User Credentials:**
   * Username: `cloudlaunch-user`
  * Password:
  BY{D8|7{                                                                                             /}

## Notes and Observations

* CloudFront integration was considered a bonus and was **not implemented** to stay within the free tier and assignment scope.
* All resources were created using AWS Free Tier eligible services. No EC2 instances, NAT gateways, or RDS instances were provisioned.
* IAM policies were designed with the principle of least privilege, ensuring strict access controls.
* The VPC layout follows a logical separation for future scaling with public, application, and database subnets.

## How to Test

* Access the public website via the S3 website endpoint URL.
* Log in to AWS Console as `cloudlaunch-user` to verify:
   * Ability to list and read permitted S3 buckets.
   * No delete permissions on buckets.
   * View-only access to VPC and its components.
* Confirm that private buckets cannot be accessed anonymously or by unauthorized users.

## Contact / Support

For any issues or further information, please contact `[Joyce Ebea]` at `[ebeajoyce.work@gmail.com]`