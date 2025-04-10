
# Deploy a Self-hosted n8n Instance on Google Cloud Free Tier

## Overview

This guide explains how to deploy an instance of **n8n**, an open-source workflow automation tool, on a **Google Cloud Platform (GCP)** VM using the Free Tier. The setup includes Docker, Docker Compose, basic authentication, persistent storage, and optional automation via script.



## Step 1: Create a GCP VM Instance

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Navigate to **Compute Engine > VM instances**.
3. Click **"Create Instance"**.
4. Configure the following:
   - **Name**: `n8n-vm`
   - **Region/Zone**: Choose your nearest region
   - **Machine type**: `e2-micro` (Series: E2)
   - **Boot disk**: Ubuntu 22.04 LTS
   - **Firewall**:
     - [x] Allow HTTP (Port 80)
     - [x] Allow HTTPS (Port 443)
   - Add your **SSH key** if required.
5. Click **Create**.



## Step 2: Install Docker and Docker Compose

1. SSH into your VM from the GCP console.
2. Run the following commands to install Docker:

   ```bash
   sudo apt update
   sudo apt install -y docker.io
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
 

3. Install Docker Compose:

   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   ```


## Step 3: Configure n8n Using Docker Compose

1. Create a working directory:

   ```bash
   mkdir ~/n8n && cd ~/n8n
   ```

2. Create a `.env` file:

   ```bash
   nano .env
   ```

   Add the following content (replace `<YOUR_PUBLIC_IP>` with your actual VM IP):

   ```env
   N8N_BASIC_AUTH_ACTIVE=true
   N8N_BASIC_AUTH_USER=admin
   N8N_BASIC_AUTH_PASSWORD=yourpassword
   N8N_HOST=<YOUR_PUBLIC_IP>
   N8N_PORT=5678
   N8N_PROTOCOL=http
   ```

3. Create a `docker-compose.yml` file:

   ```bash
   nano docker-compose.yml
   ```

   Paste the following content:

   ```yaml
   version: "3"

   services:
     n8n:
       image: n8nio/n8n
       restart: always
       ports:
         - "5678:5678"
       environment:
         - N8N_BASIC_AUTH_ACTIVE=true
         - N8N_BASIC_AUTH_USER=admin
         - N8N_BASIC_AUTH_PASSWORD=yourpassword
         - N8N_HOST=${N8N_HOST}
         - N8N_PORT=5678
         - N8N_PROTOCOL=http
       volumes:
         - n8n_data:/home/node/.n8n

   volumes:
     n8n_data:
   ```


## Step 4: Enable Basic Authentication with NGINX

1. Install NGINX and Apache utilities:

   ```bash
   sudo apt update
   sudo apt install nginx apache2-utils -y
   ```

2. Create a password file:

   ```bash
   sudo htpasswd -c /etc/nginx/.htpasswd yourusername
   ```
   ![image](https://github.com/user-attachments/assets/f36ae3e1-5e2f-4b5e-a337-fb23309880b5)


3. Edit the default NGINX site configuration:

   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

   Add the following inside the `location /` block:

   ```nginx
   auth_basic "Restricted Content";
   auth_basic_user_file /etc/nginx/.htpasswd;
   ```
   ![image](https://github.com/user-attachments/assets/c399515f-2d08-4541-ad8c-059d75d58d12)


4. Restart NGINX:

   ```bash
   sudo systemctl restart nginx
   ```

   ![image](https://github.com/user-attachments/assets/2479c15a-141e-48eb-b25a-0006055f0d7b)


## Step 5: Enable Persistent Storage

### Create and Attach Persistent Disk

1. Create a persistent disk (run in Cloud Shell or terminal):

   ```bash
   gcloud compute disks create my-persistent-disk \
     --size=10GB \
     --zone=us-central1-a
   ```

   ![image](https://github.com/user-attachments/assets/837758e4-0c85-4924-a5ff-3cdf775cac15)


2. Attach the disk to your VM:

   ```bash
   gcloud compute instances attach-disk n8n-vm \
     --disk=my-persistent-disk \
     --zone=us-central1-a
   ```

### Format and Mount the Disk

1. List block devices:

   ```bash
   lsblk
   ```

   ![image](https://github.com/user-attachments/assets/1886a910-72a6-4789-b2c7-b1a876ca808e)


2. Format the new disk (e.g., `/dev/sdb`):

   ```bash
   sudo mkfs.ext4 -m 0 -F -E lazy_itable_init=0,lazy_journal_init=0 /dev/sdb
   ```

3. Mount the disk:

   ```bash
   sudo mkdir -p /mnt/mydisk
   sudo mount -o discard,defaults /dev/sdb /mnt/mydisk
   ```

4. Get the UUID:

   ```bash
   sudo blkid /dev/sdb
   ```

   ![image](https://github.com/user-attachments/assets/bf54549f-e138-4285-a3df-652d24856783)


5. Edit `/etc/fstab`:

   ```bash
   sudo nano /etc/fstab
   ```

   Add the following line:

   ```fstab
   UUID=your-uuid-here /mnt/mydisk ext4 discard,defaults,nofail 0 2
   ```
   ![image](https://github.com/user-attachments/assets/da968c3b-c864-46a6-9afa-1c8328d9c027)



## Step 6: Start n8n

Run the following command to start n8n:

```bash
sudo docker-compose up -d
```

Check if it's running:

```bash
docker ps
```
![image](https://github.com/user-attachments/assets/ed9caddb-677b-415f-9198-c833528664f0)



## Step 7: Access n8n in the Browser

Open your browser and go to:

```
http://<YOUR_PUBLIC_IP>:5678
```

Use the credentials from the `.env` file to log in.

![image](https://github.com/user-attachments/assets/fb60e29d-35bd-46a2-b360-bd5c18b55d33)


## Step 8: Add Temporary Firewall Rules (For Testing)

> Only enable for testing purposes. Disable once setup is complete.

1. Go to **VPC Network > Firewall Rules**.
2. Create a rule that allows all traffic temporarily from `0.0.0.0/0`.

## Summary
- n8n deployed on GCP Free Tier using Docker

- Basic authentication secured via NGINX

- Persistent disk attached and mounted

- Access n8n via public IP on port 5678

## Author
### Rajpreet Jahagirdhar
