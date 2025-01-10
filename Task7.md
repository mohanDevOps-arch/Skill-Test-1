# Task 7: Multi-Stage Builds for Python Flask Application  

## Objective  
In this task, you will learn how to use **multi-stage builds** in Docker to optimize a Python Flask application. Multi-stage builds help reduce the size of the final Docker image by separating build-time and runtime dependencies.  

---

## Step 1: Create the Flask Application  

### 1.1: Write the Flask Application  
Create a file named `app.py` with the following content:  

```python  
from flask import Flask  

app = Flask(__name__)  

@app.route('/')  
def home():  
    return "Hello, Docker!"  

if __name__ == '__main__':  
    app.run(host='0.0.0.0', port=5000)  
```  

This is a simple Flask application that serves "Hello, Docker!" on the home route (`/`).  

### 1.2: Create a `requirements.txt` File  
In the same directory as `app.py`, create a `requirements.txt` file to list the necessary dependencies:  

```text  
flask==2.3.3  
```  

---

## Step 2: Create a Multi-Stage Dockerfile  

### Write the `Dockerfile`  

```dockerfile  
# Stage 1: Build Stage  
FROM python:3.9-slim AS build-stage  
WORKDIR /app  
COPY requirements.txt .  
RUN pip install --no-cache-dir -r requirements.txt  
COPY . .  

# Stage 2: Production Stage  
FROM python:3.9-slim AS production-stage  
WORKDIR /app  
COPY --from=build-stage /app /app  
EXPOSE 5000  
CMD ["python", "app.py"]  
```  

### Explanation of Multi-Stage Dockerfile:  
1. **Build Stage (`build-stage`)**:  
   - Uses the lightweight `python:3.9-slim` image for building.  
   - Installs dependencies listed in `requirements.txt` and copies the application files.  

2. **Production Stage (`production-stage`)**:  
   - Uses another lightweight `python:3.9-slim` image for the runtime environment.  
   - Copies the built application and its dependencies from the build stage.  
   - Exposes port `5000` and starts the application with `python app.py`.  

---

## Step 3: Build and Run the Docker Image  

### 3.1: Build the Docker Image  
Run the following command in the directory containing the `Dockerfile` and application files:  

```bash  
docker build -t flask-multi-stage-app .  
```  

### 3.2: Run the Docker Container  
Start a container using the built image:  

```bash  
docker run -d -p 5000:5000 --name flask-app flask-multi-stage-app  
```  

### Explanation:  
- `-d`: Runs the container in detached mode.  
- `-p 5000:5000`: Maps port 5000 of the host to port 5000 of the container.  
- `--name flask-app`: Names the container `flask-app`.  

---

## Step 4: Test the Application  

1. Open a browser and go to:  
   ```  
   http://localhost:5000  
   ```  

2. You should see the response:  
   ```
   Hello, Docker!
   ```  

3. Alternatively, test using `curl` in the terminal:  
   ```bash  
   curl http://localhost:5000  
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

- **Reduced Image Size**: Only the necessary components are included in the final image.  
- **Separation of Concerns**: Build-time dependencies are not carried over to the production image.  
- **Improved Security**: Smaller production images reduce potential vulnerabilities.  

---

## Sample Output  

### Terminal Output on Running the Container:  
```bash  
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)  
```  

### Browser Output:  
```
Hello, Docker!
```  

---

## Summary  

1. **Flask Application**: Created a basic Flask application that returns "Hello, Docker!" on the home route.  
2. **Multi-Stage Dockerfile**: Used multi-stage builds to optimize the image size by separating build-time and runtime dependencies.  
3. **Testing**: Verified the application by accessing it through a web browser or `curl`.  
