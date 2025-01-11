# Task 9: Shared Volume Configuration

## Objective

Learn how to use Docker volumes for sharing data between multiple containers, enabling seamless data exchange.

---

## Step 1: Create a Shared Volume

Create a named Docker volume to be shared between multiple containers:

```bash
docker volume create shared-volume
```

### Explanation:

- `shared-volume`: The name of the volume that will be used by multiple containers.
- Named volumes allow persistent and shared access to data between containers.

---

## Step 2: Run Containers Using the Shared Volume

### 2.1: Run the First Container (e.g., Flask App)

Start a Flask container and mount the shared volume:

```bash
docker run -v shared-volume:/data -d --name flask-app flask-app
```

### Explanation:

- `-v shared-volume:/data`: Mounts the `shared-volume` volume to the `/data` directory inside the container.
- `--name flask-app`: Names the container `flask-app`.
- `flask-app`: The image for the Flask application.

### 2.2: Run the Second Container (e.g., Redis)

Start another container (e.g., Redis) and mount the same shared volume:

```bash
docker run -v shared-volume:/data -d --name redis redis
```

### Explanation:

- Both containers now have access to the `/data` directory, backed by the `shared-volume` Docker volume.
- The `redis` container uses the official Redis image for this example.

---

## Step 3: Test Data Sharing

### 3.1: Add Data in the First Container

1. Access the first container:

   ```bash
   docker exec -it flask-app bash
   ```

2. Create a file in the `/data` directory:

   ```bash
   echo "Hello from Flask App" > /data/shared-file.txt
   ```

3. Verify the file exists:

   ```bash
   cat /data/shared-file.txt
   ```

### 3.2: Access the Second Container to Verify the Shared Data

1. Access the second container:

   ```bash
   docker exec -it redis bash
   ```

2. Verify the file created by the Flask app is accessible:

   ```bash
   cat /data/shared-file.txt
   ```

### Expected Output:

```plaintext
Hello from Flask App
```

---

## Key Commands Summary

| Command                                            | Description                                    |
| -------------------------------------------------- | ---------------------------------------------- |
| `docker volume create <volume_name>`               | Creates a named Docker volume.                 |
| `docker run -v <volume_name>:<container_path> ...` | Attaches a named volume to a container.        |
| `docker exec -it <container_name> <command>`       | Executes a command inside a running container. |
| `echo "<text>" > <file>`                           | Writes text to a file in the container.        |
| `cat <file>`                                       | Displays the contents of a file.               |

---

## Benefits of Shared Volumes

1. **Data Sharing**: Enables multiple containers to access and modify the same data.
2. **Persistence**: Ensures data remains intact even after container restarts or removals.
3. **Collaboration**: Allows containers to work together on shared resources, improving inter-service communication.

---

## Summary

1. Created a shared volume using `docker volume create`.
2. Mounted the shared volume in two containers (`flask-app` and `redis`).
3. Verified data sharing by creating and accessing a file between the containers.
