# Deploy a Simple Web Page on an EC2 Instance

This task will guide you through deploying a simple web page on an Amazon EC2 instance using the Apache HTTP server. Follow these steps carefully to understand the basics of working with AWS EC2.

---

## Step 1: Launch an EC2 Instance

1. **Log in to the AWS Management Console**

   - Navigate to the EC2 Dashboard.

2. **Launch an Instance**

   - Click on the "Launch Instances" button.
   - Choose an Amazon Machine Image (AMI):
     - Select "Amazon Linux 2" or "Ubuntu Server 22.04".
   - Choose an Instance Type:
     - Select `t2.micro` (Free Tier eligible).
   - Configure Instance Details:
     - Leave most settings as default.
     - Ensure the instance is launched in a public subnet of your VPC.
     - Assign a public IP address to the instance.

3. **Add Storage**

   - Leave the default storage settings.

4. **Configure Security Group**

   - Create a new security group or modify an existing one to allow the following:
     - **SSH (port 22):** Allow traffic from your IP address.
     - **HTTP (port 80):** Allow traffic from anywhere (`0.0.0.0/0`).

5. **Launch the Instance**

   - Review the settings and click "Launch".
   - Choose or create a key pair to connect to the instance.

---

## Step 2: Configure the EC2 Instance

Once your instance is running:

1. **Connect to the Instance**

   - Open a terminal or use an SSH client to connect to the instance using the key pair.
   - Example command:
     ```bash
     ssh -i "your-key.pem" ec2-user@<public-ip>
     ```

2. **Update the Instance**

   - Run the following commands to update the package manager:
     ```bash
     sudo apt update -y       # For Ubuntu
     sudo yum update -y       # For Amazon Linux
     ```

3. **Install Apache HTTP Server**

   - Install the Apache web server:
     ```bash
     sudo apt install -y apache2       # For Ubuntu
     sudo yum install -y httpd         # For Amazon Linux
     ```
   - Start the Apache service:
     ```bash
     sudo systemctl start apache2      # For Ubuntu
     sudo systemctl start httpd        # For Amazon Linux
     ```
   - Enable the Apache service to start on boot:
     ```bash
     sudo systemctl enable apache2     # For Ubuntu
     sudo systemctl enable httpd       # For Amazon Linux
     ```

---

## Step 3: Serve a Simple Web Page

1. **Create the Web Page**

   - Use the following command to create a simple HTML file:
     ```bash
     echo "<html><h1>Hello, World! Welcome to my EC2 Instance</h1></html>" | sudo tee /var/www/html/index.html
     ```

2. **Verify the Web Page**

   - Open a browser and navigate to `http://<public-ip>`, replacing `<public-ip>` with the public IP address of your EC2 instance.
   - You should see the message: **Hello, World! Welcome to my EC2 Instance**.

---

## Step 4: Test and Troubleshoot

1. **Check Apache Status**

   - If the page does not load, verify that the Apache service is running:
     ```bash
     sudo systemctl status apache2      # For Ubuntu
     sudo systemctl status httpd        # For Amazon Linux
     ```

2. **Verify Security Group Settings**

   - Ensure that your security group allows inbound HTTP traffic on port 80.

3. **Check Public IP**

   - Confirm that you are using the correct public IP address assigned to your instance.

---

## Step 5: Clean Up Resources

1. **Terminate the Instance**
   - Once you are done, go back to the EC2 dashboard and terminate the instance to avoid unnecessary charges.

---

### Key Takeaways

- You learned how to launch an EC2 instance and configure it to host a web page.
- You used a user data script to automate web server setup.
- You verified web server functionality using a browser.
