# Working with Amazon S3

## 🌤️ Introduction

Today’s lab was a practical dive into **Amazon Simple Storage Service (S3)** an AWS’s object storage solution for storing and managing data at scale.
This exercise simulated a **real-world scenario** where a café collaborates with an external media company to upload and manage product images securely through a shared S3 bucket.

I used this lab to strengthen my understanding of:

- S3 bucket management via AWS CLI,
- Permission-based access using IAM,
- Event-driven notifications through Amazon SNS.

It was an insightful experience seeing how seamlessly AWS services integrate to build secure, automated, and collaborative workflows.

---

## 🎯 Lab Overview

In this lab, I:

- Created and configured an **Amazon S3 bucket** for sharing image files.
- Granted permissions to an **external IAM user** (`mediacouser`) from a media company to upload and manage these files.
- Configured **Amazon SNS** to send **email notifications** whenever the S3 bucket contents were modified.

This setup mirrors a real-world use case where **businesses collaborate securely** with external stakeholders through AWS-managed resources.

---

## 🧩 Architecture Summary

The architecture involved:

- An **IAM user (`mediacouser`)** with controlled S3 access.
- An **S3 bucket** for storing product images (`cafe3985`).
- An **SNS Topic (`s3NotificationTopic`)** that triggers an **email notification** to the admin each time files are added, deleted, or changed in the S3 bucket.

---

## ⚙️ Objectives

By the end of the lab, I learned how to:

- Use `aws s3api` and `aws s3` CLI commands to **create and manage an S3 bucket**.
- Configure **IAM permissions** for controlled data sharing.
- **Trigger SNS notifications** for real-time event updates.

---

## 🧭 Task 1: Connecting to CLI Host and Configuring AWS CLI

### Steps:

1. Connected to the **EC2 CLI Host** instance using **EC2 Instance Connect**.
2. Configured the AWS CLI with the provided credentials:

   ```bash
   aws configure
   ```

   - Access key: `AHHFFJKDKVDVDJ`
   - Secret key: `jeiurgrgfgfjkfjssgsjdgjfj`
   - Region: `us-west-2`
   - Output format: `json`

✅ **Result:** CLI ready for executing AWS commands.
todo: add image here

---

## Task 2: Creating and Initializing the S3 Bucket

### Steps:

1. Created a unique S3 bucket:

   ```bash
   aws s3 mb s3://cafe3985 --region 'us-west-2'
   ```

2. Synced initial images into the bucket:

   ```bash
   aws s3 sync ~/initial-images/ s3://cafe3985/images
   ```

3. Verified uploaded images:

   ```bash
   aws s3 ls s3://cafe3985/images/ --human-readable --summarize
   ```

✅ **Result:** The images successfully uploaded and were accessible via the AWS Console.
todo: add image

---

## 🧍‍♀️ Task 3: Reviewing IAM Group and User Permissions

### IAM Group: `mediaco`

- Granted `s3:GetObject`, `s3:PutObject`, `s3:DeleteObject` actions on the `/images/` path.
- Ensured users can list buckets but not alter permissions.

### IAM User: `mediacouser`

- Inherited group policies.
- Created new **CLI access keys** for testing.
- Tested uploading, viewing, and deleting images via console access.

✅ **Result:**

- `mediacouser` could view, upload, and delete images.
- Unauthorized attempts (like changing bucket permissions) were correctly **denied**.

---

## 🔔 Task 4: Configuring Event Notifications

### Step 1: Created an SNS Topic

```bash
s3NotificationTopic
```

- Updated its access policy to allow `s3.amazonaws.com` to publish notifications.
- Subscribed my email to receive updates and **confirmed the subscription**.

### Step 2: Created S3 Event Configuration

Created a JSON file:

```json
{
  "TopicConfigurations": [
    {
      "TopicArn": "arn:aws:sns:us-west-2:xxxx:s3NotificationTopic",
      "Events": ["s3:ObjectCreated:*", "s3:ObjectRemoved:*"]
    }
  ]
}
```

Applied the configuration:

```bash
aws s3api put-bucket-notification-configuration \
  --bucket cafe3985 \
  --notification-configuration file://s3EventNotification.json
```

✅ **Result:**
Whenever an image was added, deleted, or modified, I received an **SNS email notification** instantly — confirming a successful end-to-end event workflow.

---

## 🧠 Reflection

This lab gave me a hands-on understanding of how **S3**, **IAM**, and **SNS** integrate to automate secure data sharing workflows.
I particularly appreciated how AWS abstracts complex configurations into clean CLI commands.
For me, the key takeaways were:

- Importance of **granular IAM permissions** in multi-user environments.
- The elegance of **event-driven notifications** using SNS.
- Using the **AWS CLI** boosts both control and productivity.

---

## 🏁 Conclusion

By the end of this lab, I had built a **fully functional S3 sharing solution** that:

- Ensures **secure collaboration** with external users,
- Implements **automated notifications** for transparency,
- And reflects **best practices** in IAM and S3 configuration.

This was another milestone in my AWS Re/Start journey a meaningful step toward mastering **cloud-based automation and infrastructure management**.
