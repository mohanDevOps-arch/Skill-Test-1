# **Task 8: Deploy a Dockerized Node.js Application Using Amazon ECR and ECS**

This guide walks through deploying a simple Node.js application on AWS using Amazon Elastic Container Registry (ECR) and Elastic Container Service (ECS) with Fargate. The application will be exposed via an Application Load Balancer (ALB).

---

## **Step 1: Prepare the Node.js Application**

1. **Node.js Application Code:**
   Create a simple Node.js application (e.g., `app.js`) with the following code:

   ```javascript
   const express = require('express');
   const app = express();

   app.get('/', (req, res) => {
       res.send('Welcome to the Travel Memory App!');
   });

   app.listen(8080, () => {
       console.log('Server running on port 8080');
   });
   ```

2. **Dockerfile:**
   Create a `Dockerfile` to containerize the application:

   ```dockerfile
   FROM node:14
   WORKDIR /app
   COPY . .
   RUN npm install
   EXPOSE 8080
   CMD [ "node", "app.js" ]
   ```

3. **Build the Docker Image:**
   Open a terminal in the directory containing the `Dockerfile` and application code, and run the following commands:

   ```bash
   docker build -t travel-memory-app .
   ```

---

## **Step 2: Push the Docker Image to Amazon ECR**

1. **Create an ECR Repository:**

   - Go to the **ECR Console** in AWS.
   - Click **Create Repository**.
   - Provide a name for the repository, e.g., `travel-memory-app`.
   - Click **Create Repository**.

2. **Authenticate Docker to ECR:**

   - Run the following command to authenticate Docker with your ECR registry:

   ```bash
   aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<your-region>.amazonaws.com
   ```

3. **Tag the Docker Image:**

   - Tag your local image for ECR:

   ```bash
   docker tag travel-memory-app:latest <account-id>.dkr.ecr.<your-region>.amazonaws.com/travel-memory-app:latest
   ```

4. **Push the Docker Image to ECR:**

   - Push the image to your ECR repository:

   ```bash
   docker push <account-id>.dkr.ecr.<your-region>.amazonaws.com/travel-memory-app:latest
   ```

---

## **Step 3: Create an ECS Cluster**

1. **Navigate to ECS Console:**

   - Go to the **ECS Console** in AWS.
   - Click **Create Cluster**.

2. **Configure the Cluster:**

   - Choose the **Networking only (Fargate)** cluster template.
   - Provide a name for the cluster, e.g., `TravelMemoryCluster`.
   - Click **Create Cluster**.

---

## **Step 4: Create a Task Definition**

1. **Go to Task Definitions:**

   - In the ECS Console, click **Task Definitions** > **Create new Task Definition**.

2. **Choose Fargate Launch Type:**

   - Select **Fargate** as the launch type.

3. **Define the Container Settings:**

   - Add a container:
     - **Container Name:** `travel-memory-app`
     - **Image:** `<account-id>.dkr.ecr.<your-region>.amazonaws.com/travel-memory-app:latest`
     - **Port Mappings:** 8080 (the port your app listens on).
   - Specify **CPU and Memory Limits:**
     - Example: 0.5 vCPU and 1 GB Memory.

4. **Save the Task Definition:**

   - Click **Create**.

---

## **Step 5: Create a Load Balancer**

1. **Navigate to EC2 Console:**

   - In the **AWS EC2 Console**, go to **Load Balancers** > **Create Load Balancer**.

2. **Configure the Load Balancer:**

   - **Type:** Application Load Balancer.
   - **Scheme:** Internet-facing.
   - **Subnets:** Select two subnets in different Availability Zones.
   - **Security Group:** Create or use an existing Security Group with:
     - **HTTP (Port 80):** Allow traffic from anywhere (0.0.0.0/0).

3. **Create a Target Group:**

   - Go to the **Target Groups** section.
   - Create a new target group:
     - **Target Type:** IP Address.
     - **Protocol and Port:** HTTP, Port 80.
     - **Health Check Path:** `/`.

4. **Attach Target Group to ALB:**

   - Set up a listener for the ALB to forward traffic to the target group.

---

## **Step 6: Deploy the Application Using ECS Fargate**

1. **Create a New Service:**

   - In the ECS Console, go to your cluster and click **Create Service**.
   - **Launch Type:** Fargate.
   - **Task Definition:** Select the task definition created earlier.
   - **Service Name:** Provide a name for the service, e.g., `TravelMemoryService`.
   - **Number of Tasks:** Define the desired number of tasks (e.g., 2).

2. **Configure the Load Balancer:**

   - Choose **Application Load Balancer**.
   - Select the ALB and the target group created earlier.

3. **Deploy the Service:**

   - Click **Create Service** to deploy your application.

---

## **Step 7: Access the Application**

1. **Get the Load Balancer DNS Name:**

   - Go to the **Load Balancers** section in the EC2 Console.
   - Copy the **DNS Name** of the ALB.

2. **Open the Application in a Browser:**

   - Paste the ALB DNS Name in your browser:
     ```
     http://<ALB-DNS-Name>
     ```
   - You should see the message: **"Welcome to the Travel Memory App!"**.

---

## **Summary**

By following these steps, you have:

- Containerized a Node.js application.
- Pushed the Docker image to Amazon ECR.
- Deployed the application on ECS Fargate.
- Configured an ALB to distribute traffic to your ECS tasks.

This setup ensures scalability, high availability, and simplified management of your application. Let me know if you have questions or need further assistance!

