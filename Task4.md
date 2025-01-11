# Task 4: Set Up a Static Website Using Amazon S3 to Display Your Portfolio

## Objective:
Learn how to create a static website using Amazon S3 to showcase your portfolio. This task involves setting up an S3 bucket, enabling static website hosting, uploading your files, and configuring permissions to make your website accessible to the public.

---

## Step 1: Create an S3 Bucket

1. **Log in to the AWS Management Console**:
   - Navigate to the [S3 Console](https://console.aws.amazon.com/s3/).

2. **Click "Create Bucket"**:
   - On the S3 dashboard, click the **Create bucket** button.

3. **Enter Bucket Details**:
   - **Bucket Name**: Enter a globally unique name for your bucket (e.g., `my-portfolio-site`). Ensure the name is meaningful and easy to remember.
   - **Region**: Select the region closest to your target audience for better performance.

4. **Keep Default Settings**:
   - Leave all other settings (e.g., Object Ownership, Block Public Access) as they are. You’ll adjust the permissions later.

5. **Create the Bucket**:
   - Scroll to the bottom and click the **Create bucket** button.

---

## Step 2: Enable Static Website Hosting

1. **Navigate to the Bucket Properties**:
   - Click on the name of your bucket to open it.
   - Go to the **Properties** tab.

2. **Enable Static Website Hosting**:
   - Scroll down to the **Static website hosting** section and click **Edit**.
   - Select **Enable** and choose **Use this bucket to host a website**.

3. **Configure Website Hosting Settings**:
   - Set the **Index document** to `index.html` (or the name of your homepage file).
   - Optionally, set an **Error document** (e.g., `error.html`) to display a custom error page when a user accesses a broken link.

4. **Save Changes**:
   - Click the **Save changes** button to apply the settings.

---

## Step 3: Upload Your Portfolio HTML Files

1. **Navigate to the Bucket Overview**:
   - Go to the **Objects** tab of your bucket.

2. **Click "Upload"**:
   - Click the **Upload** button and select your website files (e.g., `index.html`, CSS files, images, etc.).

3. **Add Files**:
   - Click **Add files** or **Add folder** to upload all required files for your portfolio.

4. **Upload Files**:
   - Click **Upload** to begin the process. Once the upload is complete, you’ll see your files listed in the bucket.

---

## Step 4: Configure Bucket Permissions for Public Access

1. **Allow Public Access to the Bucket**:
   - Go to the **Permissions** tab of your bucket.
   - Under **Block public access (bucket settings)**, click **Edit**.
   - Disable the option to **Block all public access** by unchecking the box.
   - Confirm the changes by typing **confirm** in the prompt.

2. **Apply a Bucket Policy for Public Access**:
   - Scroll down to the **Bucket policy** section and click **Edit**.
   - Copy and paste the following bucket policy (replace `my-portfolio-site` with your bucket name):
     ```json
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Principal": "*",
                 "Action": "s3:GetObject",
                 "Resource": "arn:aws:s3:::my-portfolio-site/*"
             }
         ]
     }
     ```
   - Click **Save changes**.

3. **Verify Object Permissions**:
   - Ensure the uploaded files are publicly accessible. You can check the permissions of individual objects in the **Objects** tab.

---

## Step 5: Test Your Static Website

1. **Find the S3 Website Endpoint**:
   - Go to the **Properties** tab of your bucket.
   - Scroll to the **Static website hosting** section.
   - Copy the **Bucket website endpoint URL** (e.g., `http://my-portfolio-site.s3-website-us-east-1.amazonaws.com`).

2. **Access Your Website**:
   - Open a web browser and paste the URL into the address bar.
   - Your portfolio website should load, displaying the content of your `index.html` file.

---

## Troubleshooting Tips:

1. **Website Not Loading?**:
   - Check if the files are uploaded correctly and are publicly accessible.
   - Verify the bucket policy and permissions.

2. **Broken Links or Missing Files?**:
   - Ensure all supporting files (CSS, images, JavaScript) are uploaded and referenced correctly in the HTML file.

3. **Custom Domain (Optional)**:
   - If you have a custom domain, you can configure it to point to your S3 website endpoint using Route 53.
