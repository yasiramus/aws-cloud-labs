# Creating a Static Website Using Amazon S3

<!-- ![s3-static-site](../assets/diagram/s3-static-website.diagram.png) -->

**Module:** AWS Object Storage

## 🎯 Objectives

- Created a static website using Amazon S3.
- Applied a restrictive bucket policy to allow website access only from a specific IP range.
- Uploaded site objects using the AWS SDK for Python (Boto3).
- Tested and validated restricted access to ensure proper data protection.

---

## 🛠️ Steps Taken

### 1. Environment Setup & Connection

- Connected to the provided **VS Code IDE (LabIDE)** through a CloudFront-distributed URL.

- Authenticated using the LabIDE password and verified the environment.

- Installed **Boto3**, the AWS SDK for Python:

  ```bash
  sudo pip3 install boto3
  ```

- Downloaded the project files:

  ```bash
  wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCDEV-2-91558/02-lab-s3/code.zip -P /home/ec2-user/environment
  unzip code.zip
  ```

- Verified AWS CLI installation:

  ```bash
  aws --version
  ```

---

### 2. Created an S3 Bucket for Hosting the Website

- Created a unique S3 bucket:

  ```bash
  aws s3api create-bucket --bucket YM-2025-28-11 --region us-east-1
  ```

- Verified the bucket in the AWS Console.

- Updated public access settings:

  - Disabled “Block all public access”
  - Kept ACL-related public access blocks enabled
  - Ensured no existing bucket policies were attached

**Outcome:** Bucket successfully created and prepared for policy configuration.

---

### 3. Applied a Restricted Bucket Policy Using Boto3

The objective was to make the website accessible **only from a specific IPv4 address** so that the café owners could preview it privately.

- Created a **custom bucket policy** (`website_security_policy.json`) restricting access to an approved IP:

  ```json
  {
    "Version": "2008-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": [
          "arn:aws:s3:::YM-2025-28-11/*",
          "arn:aws:s3:::YM-2025-28-11"
        ],
        "Condition": {
          "IpAddress": {
            "aws:SourceIp": ["41.66.202.239/32"]
          }
        }
      },
      {
        "Sid": "DenyOneObjectIfRequestNotSigned",
        "Effect": "Deny",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::YM-2025-28-11/report.html",
        "Condition": {
          "StringNotEquals": {
            "s3:authtype": "REST-QUERY-STRING"
          }
        }
      }
    ]
  }
  ```

- Updated the existing Python starter script (`permissions.py`) to apply the policy.

- Executed the script:

  ```bash
  cd python_3
  python3 permissions.py
  ```

- Confirmed via console that the policy attached successfully.

**Outcome:** Bucket restricted to a specific IP address for controlled preview access.

---

### 4. Uploaded Website Assets to S3

- Uploaded the website contents recursively:

  ```bash
  aws s3 cp ../resources/website s3://YM-2025-28-11/ --recursive --cache-control "max-age=0"
  ```

- Verified that all objects appeared under **Objects** in the S3 console.

**Outcome:** Static site files successfully deployed to Amazon S3.

---

### 5. Tested Website Accessibility

- Retrieved and opened the Object URL for `index.html`:

  ```
  https://YM-2025-28-11.s3.amazonaws.com/index.html
  ```

- Confirmed the website **loaded successfully only when accessed from the allowed IPv4 address**.

- Simulated access from other networks:

  - Used a mobile device on cellular data
  - Used `curl` from VS Code terminal:

    ```bash
    curl https://<bucket-name>.s3.amazonaws.com/index.html
    ```

- Both external attempts returned **AccessDenied**, confirming the bucket policy functioned correctly.

- Tested additional UI behavior:

  - Clicking **Login** triggered the expected “No API to call” alert (API not deployed yet).

  - ![cafe website](<../assets//s3/Screenshot%20(1444).png>)

**Outcome:** Access restrictions worked exactly as configured.

---

### 6. Analyzed the Website Codebase

- Reviewed HTML, CSS, and JavaScript files in the `resources/website` directory.

- Examined core configuration logic in `config.js`:

  ```js
  window.COFFEE_CONFIG = {
    API_GW_BASE_URL_STR: null,
    COGNITO_LOGIN_BASE_URL_STR: null,
  };
  ```

- Reviewed application behavior in `pastries.js`, including logic that displayed products from `all_products.json` when API endpoints were not yet configured.

- Explored the JSON product data structure supporting the UI.

**Outcome:** Gained clear insight into how the S3-hosted static site loads data and will integrate with future APIs.

---

## 🔑 Key Learnings

- Amazon S3 can host static websites securely without enabling full static website hosting mode.
- Bucket policies can effectively restrict access to specific IP ranges for controlled testing.
- Boto3 provides flexible, programmable control over S3 permissions.
- Proper caching control (`max-age=0`) ensures updated changes appear immediately.
- Object URLs can be used to serve site content when website hosting is intentionally disabled.

---

## 🚧 Challenges

- Ensuring bucket names were unique globally.
- Carefully structuring the IP-restriction policy to avoid accidental access denial.
- Understanding interaction between **public access block settings** and **bucket policies**.

---

## ✅ Result: Lab Completed Successfully

![cafe website](<../assets//s3/Screenshot%20(1445).png>)

![cafe website](<../assets//s3/Screenshot%20(1446).png>)

![cafe website](<../assets//s3/Screenshot%20(1448).png>)
![cafe website](<../assets//s3/Screenshot%20(1447).png>)

I successfully:

- Created a static website hosted on Amazon S3.
- Restricted access to the café’s internal IP for private previewing.
- Applied a custom bucket policy using the AWS SDK for Python.
- Uploaded the website assets and confirmed correct access behavior.
- Analyzed the website’s code and architecture in preparation for future backend integration.

**This project demonstrates hands-on experience with AWS S3, S3 policies, Boto3, and secure static website deployment.**

---
