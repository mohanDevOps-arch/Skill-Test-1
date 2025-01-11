# Task 1: Build and Run a Dockerized Flask Application

## Objective
In this task, you will create a simple Flask application, containerize it using Docker, and run it in a container. You will verify that the application is accessible through a web browser on your local machine.

### 1. Write the Flask Application

Create a new Python file named `app.py` in your project directory with the following code:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Welcome to the Dockerized Flask App!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### 2. Create a Dockerfile to Containerize the Application

Next, create a `Dockerfile` in the same directory where `app.py` is located. This file will define the steps to build the Docker image for your Flask application. Use the following code:

```Dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

### 3. Create the `requirements.txt` File

To install the necessary dependencies for the Flask application, you need a `requirements.txt` file. Create this file in the same directory with the following content:

```
Flask==2.0.1
```

### 4. Build the Docker Image

Open your terminal and navigate to the directory containing the `Dockerfile` and `app.py`. Run the following command to build the Docker image:

```bash
docker build -t flask-app .
```

This command will build the Docker image and tag it as `flask-app`.

### 5. Run the Docker Container

Once the image is built, you can run the container with the following command:

```bash
docker run -p 5000:5000 flask-app
```

This command will run the Docker container and map port 5000 inside the container to port 5000 on your local machine.

### 6. Test the Application

Open your web browser and navigate to:

```
http://localhost:5000
```

You should see the message: 

```
Welcome to the Dockerized Flask App!
```

This confirms that your Flask application is running successfully inside a Docker container.

---

## Summary

- **Flask application**: A simple app that returns a welcome message on the homepage.
- **Dockerization**: Used a `Dockerfile` to containerize the Flask application.
- **Commands**: 
  - Build the Docker image with `docker build -t flask-app .`
  - Run the Docker container with `docker run -p 5000:5000 flask-app`
- **Testing**: The application can be accessed on `http://localhost:5000`.

This completes the task of building and running a Dockerized Flask application.
