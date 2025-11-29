### Scenario

---

Yasira is eager to start building a website for the café. She has some Python development skills and she's learning more about how to develop solutions on the cloud. Sabali is a secondary school student who also works at the café. He has some skills in graphic design and a strong interest in learning about cloud computing.

The café has a single location in a large city. It mostly gains new customers when someone walks by, notices the café, and decides to try it. The café has a reputation for high-quality desserts and coffees, but their reputation is limited to people who have visited, or who have heard about them from their customers.

Yasira suggests to Maria and Yusif (her parents and the owners of the café) that they should expand community awareness of what the café has to offer. The café doesn’t have a web presence, and it doesn’t currently use any cloud computing services. However, that situation is about to change.

---

## ⚙️ Objectives

Create a static website by using Amazon S3.

Apply a bucket policy on an S3 bucket to configure customized data protection.

Upload objects to an S3 bucket by using the AWS SDK for Python (Boto3).

Configure the website that is hosted on Amazon S3 to be accessible only from a specific IP address range, and test the configuration.

#### Before starting an EC2 instance were already created for you in the AWS account which was running

### A business request for the café: Create a static website

Yasira mentions to Sabali that she would like to build a proof-of-concept website for Maria and Yusif that will showcase the café's offerings visually. It would also provide business details, such as the location of the store, business hours, and telephone number. Sabali is eager to see how she will build the website, and he agrees to create the graphics that she will use.

For this first challenge, I took the role of Yasira. I used the AWS Command Line Interface (AWS CLI) and the SDK for Python to configure Amazon S3 so that it hosts a basic website for the café.

## 🧭 Task 1: Connected to VS Code IDE and configuring the environment

### Steps:

1. Connected to the VS Code IDE using LabIDEURL in the browser "https://d16yt188v6n54u.cloudfront.net/?folder=/home/ec2-user/environment"
   to open the VS Code IDE
2. On the prompt window Welcome to code-server, entered the value for LabIDEPassword Submitted to open the VS Code IDE.
3. Ran the following commands in the terminal "sudo pip3 install boto3" to install AWS SDK for Python
4. Download and extract the files that is needed for this task by running the following command:
   "wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCDEV-2-91558/02-lab-s3/code.zip -P /home/ec2-user/environment"
5. Extracted the code.zip file "unzip code.zip"
6. Verify the AWS CLI version 2 "aws --version"

## 🧭 Task 2: Created an S3 bucket by using the AWS CLI to host the static site

### Steps:

1. Created a bucket using this command "aws s3api create-bucket --bucket YM-2025-28-11 --region us-east-1"

2. Used the AWS Management Console to confirm that the bucket was created, and observed the current permissions settings on the bucket.

---

By default Block all public access is turned on.

I edited it by de-select Block all public access.

Then Selected Block public access to buckets and objects granted through new access control lists (ACLs).

Selected Block public access to buckets and objects granted through any access control lists (ACLs).

Selected Block public and cross-account access to buckets and objects through any public bucket or access point policies.

and Saved changes.

Now the bucket currently doesn't have a bucket policy attached to it.

---

## 🧭 Task 3: Setting a bucket policy on the bucket by using the SDK for Python

After Yasira has created the bucket, she wants to set permissions on the bucket. She wants Yusif, Maria, and Sabali to be able to access the first version of the website that she's building so that they can see the progress she's making. However, she doesn't want to expose the site to the outside public(world|customers) yet. She accomplished this objective by configuring a bucket policy on the bucket. This bucket policy will enforce a condition.

### Steps:

1. Created the policy document (a json file named website*security_policy.json)
   website_security_policy.json file,
   {
   "Version": "2008-10-17",
   "Statement": [
   {
   "Effect": "Allow",
   "Principal": "*",
   "Action": "s3:GetObject",
   "Resource": [
   "arn:aws:s3:::YM-2025-28-11/_",
   "arn:aws:s3:::YM-2025-28-11"
   ],
   "Condition": {
   "IpAddress": {
   "aws:SourceIp": [
   "41.66.202.239/32"
   ]
   }
   }
   },
   {
   "Sid": "DenyOneObjectIfRequestNotSigned",
   "Effect": "Deny",
   "Principal": "\*",
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

NB: the IP address stimulated was to be address being used to connect your computer to the internet over the same ISP.
With this site One wont be able able to view the website if they use 0.0.0.0 as the allowed aws:SourceIp, because Amazon S3 is smart enough to recognize that means public access, and since I set the bucket permissions to Block all public access, S3 will block access. Therefore for a successfully connection ensure I use the specific IPv4 address.

Analysis: This policy document consists of two statements. The first statement allows GetObject requests from your IP address. The second statement denies access to an object in the bucket that's named report.html, unless a specific condition is met. The report.html file doesn't exist in the bucket yet.

2. Applied the bucket policy to the bucket created by using the SDK for Python.
   In the permissions.py file (which contains starter code).
   Edited the file by replacing YM-2025-28-11 with the name of the bucket.
3. In the terminal, navigated to the python_3 directory and run the following code:
   cd python_3
   python3 permissions.py

4. Return to the browser tab where the Amazon S3 console was opened, and observed that the bucket policy has been applied to the bucket.

## 🧭 Task 4: Uploaded objects to the bucket to create the website

Now that Sofía has configured permissions on the bucket, she needs to upload the website objects to the bucket. Fortunately, Sofía created the website code some time ago, and it's in the resources directory in the VS Code IDE. Nikhil created the graphics for the website, and he's excited to see what the website will look like. Sofía is now ready to upload the files. To complete this task, she will use the VS Code IDE Bash terminal to issue a simple recursive file upload command and disable caching (since the website is still being developed).

1. Uploaded files to the S3 bucket aws s3 cp ../resources/website s3://YM-2025-28-22/ --recursive --cache-control "max-age=0"

## 🧭 Task 4:Testing access to the website

Now that Sofía has uploaded the website files to the S3 bucket, she must test the site and verify that it loads when a user accesses it through a virtual secure endpoint.

IN the Amazon S3 console.

Choose your bucket name, and then choose Objects.

If the files you just uploaded do not display, choose the refresh icon to view them.

Choose the index.html file.

Copied the Object URL. It will be in the following format.
https://<bucket-name>.s3.amazonaws.com/index.html

Note: In this lab scenario, the S3 bucket you created is intentionally not configured for static website hosting (a feature available in the bucket properties). Instead, you will access the website using the Object URL of the index.html file.

Verify that your website displays by pasting that full URI into your browser. Ensuring you are on the same network (as it will block anything other than the IPv4 you specified earlier)

Try to access the same URL from a location outside of your network.

Ways that you can test outside access:

If you have mobile phone with a cellular network connection, try loading the same URL in a browser on the mobile phone.

Another way to test is by running the following command in the VS Code bash terminal (where <bucket-name> is the actual bucket name):

curl https://<bucket-name>.s3.amazonaws.com/index.html
Analysis: Regardless whether you load the page from another location or use the curl command from the IDE, an attempt is made to retrieve the page that you specified. It should return an AccessDenied error because your mobile device or the VS Code IDE (whichever one you chose to use) connects to the internet by using a different IP address than your computer.

The essential point is that you should only be able to access the website if the device uses the IPv4 address that's specified in the S3 bucket policy. In the café story, this IPv4 address is the café's network IP address.

Test that the website is loading.

Back in the browser tab where the website loads correctly, choose Login on the top right of the header.

An alert will display saying No API to call This behavior is also expected at this point in the course.

Analysis: In the next lab, you will begin to create an application programming interface (API) that the JavaScript code in this website will interact with. For now, however, that API doesn't exist yet.

Task 6: Analyzing the website code
Nikhil watched as Sofía configured the website. Now that the website is hosted and working, he asks Sofía to explain the code behind the site.

In the VS Code IDE, from the navigation pane, browse the resources > website directory and open the index.html file.

Notice that this HTML page references a number of CSS files in the head section

Towards the end of the body section, the code references a few JavaScript (.js) files. Some of these JavaScript files define the essential features of the site. One of these files is named config.js another one of them is named pastries.js.

Open the config.js file.

It contains an object literal that looks like this:

window.COFFEE_CONFIG = {
API_GW_BASE_URL_STR: null,
COGNITO_LOGIN_BASE_URL_STR: null
};
As you continue through the labs in this course, at the appropriate time (for example, when you add Amazon API Gateway and Amazon Cognito to the website implementation) you will set values for these keys and overwrite the contents of this file on S3 with the changes. Then the behavior of the website will adapt to the enhancements accordingly.

Open the pastries.js file.

Notice the logic defined in the loadAllItems function, that if the API_GW_BASE_URL_STR has a value of null (which it currently does, as you saw in config.js), then this function will print the items contained in the file named all_products.json.

Observe the printItems function details that are defined further down in the code.

Open the all_products.json file.

Notice that this JSON file contains information about all the products that display on the website.

For now, this product information is stored in this file hosted on Amazon S3, however, in a later lab, you will migrate this product information into a database.

Optionally, browse the other files that make up the website, to get a sense of how the website code works. The files have been left unobfuscated so you can read them if you wish. However you do not need to understand this application code in order to complete this course.

Update from the café
Sofie

Sofía is pleased that she now has a basic website that's hosted on Amazon S3. Because it's still a work in progress, she made sure to configure the site so that it's only accessible from the café's network. Frank and Martha are both pleased with the results so far.

Sofía takes a minute to relax, but she's also already looking forward to the next task. She's planning to start developing the components that will make the site run dynamically by using REST API calls to a database backend.
