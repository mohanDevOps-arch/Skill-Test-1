# Task 2: Deploy a Flask Application on an EC2 Instance

## Objective:
Deploy a Python Flask application on an EC2 instance and make it accessible over the internet.

---

## Step 1: Launch an EC2 Instance

1. **Log in to the AWS Management Console**:
   - Navigate to the EC2 Dashboard.

2. **Launch an EC2 Instance**:
   - Click on **Launch Instance**.
   - Choose an Amazon Machine Image (AMI): Select **Amazon Linux 2** or **Ubuntu** (latest version recommended).
   - Choose an Instance Type: Select `t3.micro` .
   - Configure Network:
     - Choose a **custom VPC** or the default VPC.
     - Assign the instance to a **public subnet**.
   - Enable **Auto-Assign Public IP**: Ensure the instance has a public IP for external access.

3. **Add a Security Group**:
   - Create a new Security Group with the following inbound rules:
     - **SSH (port 22)**: Allow traffic from **My IP** to connect to the instance securely.
     - **HTTP (port 80)**: Allow traffic from **Anywhere (0.0.0.0/0)** to serve the Flask application.

4. **Add Key Pair**:
   - Select an existing key pair or create a new one. Download the key pair (`.pem` file) for SSH access.

5. **Launch Instance**:
   - Click **Launch** and wait for the instance to initialize.

---

## Step 2: Connect to the EC2 Instance

1. **Open a Terminal** (or use a tool like PuTTY for Windows).
2. Use the following SSH command to connect to the instance:
   ```bash
    ssh -i <path-to-your-key>.pem ubuntu@<Public-IP>   # For Ubuntu
    ssh -i <path-to-your-key>.pem ec2-user@<Public-IP> # For Amazon Linux 2
   ```
   Replace `<path-to-your-key>` with the location of your `.pem` file and `<Public-IP>` with the public IP address of the instance.

---

## Step 3: Install Required Software on the EC2 Instance

1. **Update the Package Manager**:
   ```bash
   sudo apt update -y   # For Ubuntu
   sudo yum update -y   # For Amazon Linux 2
   ```

2. **Install Python3 and pip**:
   ```bash
   sudo apt install -y python3 python3-pip   # For Ubuntu
   sudo yum install -y python3 python3-pip   # For Amazon Linux 2
   ```

3. **Install Flask**:
   ```bash
   pip3 install flask
   ```

---

## Step 4: Write the Flask Application

1. **Create a Directory for Your Application**:
   ```bash
   mkdir ~/flask-app
   cd ~/flask-app
   ```

2. **Write the Flask App**:
   - Create a file named `app.py`:
     ```bash
     nano app.py
     ```
   - Add the following code:
     ```python
     from flask import Flask
     app = Flask(__name__)

     @app.route('/')
     def home():
         return "Hello, Your Name!"

     if __name__ == "__main__":
         app.run(host="0.0.0.0", port=80)
     ```
   - Save the file by pressing `CTRL+O`, `Enter`, and `CTRL+X`.

---

## Step 5: Run the Flask Application

1. **Run the Application**:
   - Start the Flask app:
     ```bash
     sudo python3 app.py
     ```

2. **(Optional) Use `tmux` to Keep the Application Running**:
   - Install `tmux`:
     ```bash
     sudo apt install -y tmux   # For Ubuntu
     sudo yum install -y tmux   # For Amazon Linux 2
     ```
   - Start a `tmux` session:
     ```bash
     tmux
     ```
   - Run the Flask app inside the session:
     ```bash
     sudo python3 app.py
     ```
   - Detach the session by pressing `CTRL+B`, then `D`.

---

## Step 6: Verify the Deployment

1. **Open a Web Browser**:
   - Navigate to `http://<Public-IP>`, where `<Public-IP>` is the public IP address of your EC2 instance.

2. **Verify the Output**:
   - You should see the message:  
     **"Hello, Your Name!"**

3. **Troubleshooting**:
   - If the page doesn’t load:
     - Check if the Flask app is running using `ps -aux | grep python`.
     - Verify that port 80 is open in your Security Group rules.

---

## Notes:

1. **Running Flask Without Root Privileges**:
   - Running Flask directly on port 80 requires root privileges. Alternatively, use port 5000 and forward it using a reverse proxy like **Nginx** or **Apache**.

2. **Optional Enhancements**:
   - Use **Gunicorn** to serve the Flask app in a production environment:
     ```bash
     pip3 install gunicorn
     gunicorn -b 0.0.0.0:80 app:app
     ```

3. **Scaling**:
   - You can integrate this deployment with **Auto Scaling Groups** and **Load Balancers** for high availability.

---
