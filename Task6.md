# Task 6: Multi-Stage Builds for a Node.js Application  

## Objective  
In this task, you will learn how to use **multi-stage builds** in Docker to build and deploy a lightweight Node.js application. This approach is beneficial for optimizing Docker images by reducing their size while separating build and runtime environments.  

---

## Step 1: Create the Node.js Application  

### 1.1: Write the Node.js Application  
Create a file named `server.js` with the following code:  

```javascript  
const http = require('http');  

const hostname = '0.0.0.0';  
const port = 3000;  

const server = http.createServer((req, res) => {  
  res.statusCode = 200;  
  res.setHeader('Content-Type', 'text/plain');  
  res.end('Hello, World!\n');  
});  

server.listen(port, hostname, () => {  
  console.log(`Server running at http://${hostname}:${port}/`);  
});  
```  

This script creates a simple HTTP server that listens on port 3000 and responds with "Hello, World!" for all requests.  

---

## Step 2: Create a Multi-Stage Dockerfile  

### Write a `Dockerfile` for Multi-Stage Builds:  

```dockerfile  
# Stage 1: Build Stage  
FROM node:16 AS build-stage  
WORKDIR /app  
COPY package*.json ./  
RUN npm install  
COPY . .  

# Stage 2: Production Stage  
FROM node:16-slim AS production-stage  
WORKDIR /app  
COPY --from=build-stage /app .  
EXPOSE 3000  
CMD ["node", "server.js"]  
```  

### Explanation of Multi-Stage Dockerfile:  
1. **Build Stage (`build-stage`)**:  
   - Uses the full Node.js image to install dependencies and build the application.  
   - This stage copies `package*.json` for dependency management and runs `npm install`.  
   - Copies all application files into the `/app` directory.  

2. **Production Stage (`production-stage`)**:  
   - Uses a lightweight `node:16-slim` image to minimize the size of the production container.  
   - Copies only the required files (`/app`) from the build stage.  
   - Exposes port `3000` and starts the application with `node server.js`.  

---

## Step 3: Build and Run the Docker Image  

### 3.1: Build the Docker Image  
Run the following command in the directory containing the `Dockerfile` and `server.js`:  

```bash  
docker build -t nodejs-multi-stage-app .  
```  

### 3.2: Run the Docker Container  
Start a container from the built image:  

```bash  
docker run -d -p 3000:3000 --name nodejs-app nodejs-multi-stage-app  
```  

### Explanation:  
- `-d`: Runs the container in detached mode.  
- `-p 3000:3000`: Maps port 3000 of the host machine to port 3000 of the container.  
- `--name nodejs-app`: Assigns a name to the container for easier reference.  

---

## Step 4: Test the Application  

1. Open your browser and navigate to:  
   ```  
   http://localhost:3000  
   ```  

2. You should see the message:  
   ```
   Hello, World!
   ```  

3. Alternatively, test using `curl` in the terminal:  
   ```bash  
   curl http://localhost:3000  
   ```  

---

## Key Commands Summary  

| Command                                            | Description                                          |  
|----------------------------------------------------|------------------------------------------------------|  
| `docker build -t <image_name> .`                   | Builds the Docker image using the specified `Dockerfile`. |  
| `docker run -d -p <host_port>:<container_port> ...`| Runs the container and maps host to container ports. |  
| `curl http://<host>:<port>`                        | Tests the application by sending HTTP requests.      |  

---

## Benefits of Multi-Stage Builds  

- **Reduced Image Size**: Only the necessary files are included in the final production image.  
- **Separation of Concerns**: Build dependencies and runtime dependencies are isolated.  
- **Improved Security**: Smaller production images reduce the attack surface.  

---

## Sample Output  

### Terminal Output on Running the Container:  
```bash  
Server running at http://0.0.0.0:3000/  
```  

### Browser Output:  
```
Hello, World!
```  

---

## Summary  

1. **Node.js Application**: Built a simple HTTP server to respond with "Hello, World!".
2. **Multi-Stage Dockerfile**: Used multi-stage builds to optimize image size by separating the build and runtime environments.  
3. **Testing**: Verified the application by accessing it through a web browser or `curl`.  
