# Task 4: Custom Bridge Network Creation

## Objective
In this task, you will learn how to create a custom bridge network in Docker and use it to allow multiple containers (a Flask app and a Redis service) to communicate with each other. This ensures that containers can interact without exposing them to the host machine's network.

---

## Step 1: Create a Custom Bridge Network

A custom bridge network isolates your containers and provides them with a private network for communication.

### Command to Create the Network:
```bash
docker network create --driver bridge custom-network
```

### Explanation:
- `docker network create`: Creates a new Docker network.
- `--driver bridge`: Specifies that the network should use the bridge driver, which is the default for custom networks.
- `custom-network`: The name of the custom network.

### Verify the Network Creation:
Run the following command to list all networks and confirm that `custom-network` is created:
```bash
docker network ls
```

You should see an entry for `custom-network` in the output.

---

## Step 2: Deploy Containers in the Custom Network

Now, deploy the containers (a Flask app and a Redis service) and attach them to the `custom-network`.

### Step 2.1: Run the Redis Container
Run the Redis container first:
```bash
docker run --network custom-network -d --name redis redis
```

### Explanation:
- `--network custom-network`: Attaches the container to the `custom-network`.
- `-d`: Runs the container in detached mode.
- `--name redis`: Assigns the name `redis` to the container for easier identification.
- `redis`: Uses the official Redis image.

### Step 2.2: Run the Flask Container
Run the Flask app container:
```bash
docker run --network custom-network -d --name flask-app flask-app
```

### Explanation:
- `--network custom-network`: Attaches the container to the `custom-network`.
- `-d`: Runs the container in detached mode.
- `--name flask-app`: Assigns the name `flask-app` to the container.
- `flask-app`: The name of the Docker image for the Flask application.

---

## Step 3: Configure Communication Between Containers

For the Flask app to interact with the Redis container:
1. Ensure that the Flask app code is configured to connect to Redis using its **container name** (`redis`), which is resolvable as a hostname within the `custom-network`.

### Sample Flask Code (`app.py`):
Here’s an example of how the Flask app should be written:

```python
from flask import Flask
import redis

app = Flask(__name__)

# Connect to Redis using the container name as the hostname
redis_client = redis.StrictRedis(host='redis', port=6379, decode_responses=True)

@app.route('/')
def index():
    # Increment a counter in Redis
    count = redis_client.incr('counter')
    return f"Welcome! You have visited this page {count} times."

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Key Points:
- `host='redis'`: The Flask app connects to Redis using the container name `redis`, which is automatically resolvable in the custom network.
- `port=6379`: Redis's default port.

---

## Step 4: Test Network Communication

### Step 4.1: Check if Containers are Running
Use the following command to ensure both containers are running:
```bash
docker ps
```

You should see both `flask-app` and `redis` containers listed.

---

### Step 4.2: Access the Flask Application
Open your web browser or use a tool like `curl` to access the Flask app:

```bash
curl http://localhost:5000
```

You should see a response like:
```
Welcome! You have visited this page 1 times.
```

### Step 4.3: Verify Communication
Each time you refresh the page or make a new request to `http://localhost:5000`, the visit counter should increment. This confirms that the Flask app is successfully interacting with the Redis service over the custom network.

---

## Step 5: Troubleshooting

If the Flask app cannot connect to Redis:
1. Ensure both containers are running in the same network:
   ```bash
   docker network inspect custom-network
   ```
   Check that both `flask-app` and `redis` are listed under `Containers`.

2. Verify the Flask app's code:
   - Ensure the `host='redis'` is correctly configured in the Redis client initialization.

3. Check container logs for errors:
   ```bash
   docker logs flask-app
   ```

---

## Key Commands Summary

| Command                                          | Description                                      |
|--------------------------------------------------|--------------------------------------------------|
| `docker network create --driver bridge custom-network` | Creates a custom bridge network.               |
| `docker run --network custom-network ...`        | Attaches a container to the custom network.     |
| `docker network inspect custom-network`          | Lists details about the custom network.         |
| `docker ps`                                      | Lists all running containers.                   |
| `docker logs <container_name>`                   | Displays logs of a container for debugging.     |

---

## Summary

### What You Achieved:
1. **Created a custom bridge network**:
   - Ensured isolated communication between containers.
2. **Deployed two containers (Flask and Redis)**:
   - Attached both to the custom network.
3. **Configured inter-container communication**:
   - The Flask app successfully connected to Redis using its container name.
4. **Tested communication**:
   - Verified that the Flask app interacted with Redis as expected.

### Benefits of Custom Networks:
- **Container Isolation**: Containers communicate only with others in the same network.
- **Simplified Communication**: Hostnames (container names) resolve automatically within the network.
- **Better Security**: Containers are not exposed directly to the host network.
