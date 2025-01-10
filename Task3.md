# Task 3: Persistent Data Storage with Docker Volumes

## Objective
In this task, you will learn how to persist data in Docker using volumes. You will run a MySQL container, add some data to the database, and verify that the data persists even after the container is removed and restarted.

---

## Step 1: Create a MySQL Container

Run a MySQL container using the official MySQL Docker image with the necessary environment variables:

```bash
docker run -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test_db -d --name mysql-container mysql:latest
```

### Explanation:
- `-e MYSQL_ROOT_PASSWORD=root`: Sets the root password for the MySQL server.
- `-e MYSQL_DATABASE=test_db`: Automatically creates a new database named `test_db`.
- `-d`: Runs the container in detached mode.
- `--name mysql-container`: Assigns a name (`mysql-container`) to the container for easier reference.
- `mysql:latest`: Specifies the latest version of the MySQL image.

---

## Step 2: Create a Docker Volume

Create a named Docker volume to store MySQL data persistently:

```bash
docker volume create mysql-data
```

### Explanation:
- `docker volume create mysql-data`: Creates a Docker volume named `mysql-data`. This volume will store MySQL data and ensure persistence beyond the lifecycle of a single container.

---

## Step 3: Attach the Volume to the MySQL Container

Run the MySQL container again, this time attaching the `mysql-data` volume to persist data in the `/var/lib/mysql` directory (MySQL's default data storage path):

```bash
docker run -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test_db -v mysql-data:/var/lib/mysql -d --name mysql-container mysql:latest
```

### Explanation:
- `-v mysql-data:/var/lib/mysql`: Maps the `mysql-data` volume to `/var/lib/mysql`, which is where MySQL stores its data files.
- `--name mysql-container`: Names the container for easy identification.

---

## Step 4: Add Data to the MySQL Database

### Step 4.1: Access the MySQL Container

1. Use the `docker exec` command to access the MySQL container:
   ```bash
   docker exec -it mysql-container mysql -u root -p
   ```
   - Replace `mysql-container` with the name of your running container.
   - Enter the root password (`root`) when prompted.

2. Once inside the MySQL command-line interface, switch to the `test_db` database:
   ```sql
   USE test_db;
   ```

---

### Step 4.2: Create a Table and Insert Data

1. Create a new table called `users`:
   ```sql
   CREATE TABLE users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(255)
   );
   ```

2. Insert sample data into the `users` table:
   ```sql
   INSERT INTO users (name) VALUES ('Alice'), ('Bob'), ('Charlie');
   ```

3. Verify that the data has been added:
   ```sql
   SELECT * FROM users;
   ```
   You should see output like this:
   ```
   +----+---------+
   | id | name    |
   +----+---------+
   |  1 | Alice   |
   |  2 | Bob     |
   |  3 | Charlie |
   +----+---------+
   ```

4. Exit the MySQL shell:
   ```sql
   exit;
   ```

---

## Step 5: Remove the MySQL Container

Stop and remove the running MySQL container:

```bash
docker stop mysql-container
docker rm mysql-container
```

### Explanation:
- `docker stop mysql-container`: Stops the container.
- `docker rm mysql-container`: Removes the container.

---

## Step 6: Run a New MySQL Container with the Same Volume

Start a new MySQL container, reattaching the `mysql-data` volume to the `/var/lib/mysql` directory:

```bash
docker run -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test_db -v mysql-data:/var/lib/mysql -d --name new-mysql-container mysql:latest
```

---

## Step 7: Verify Data Persistence

### Step 7.1: Access the New MySQL Container

1. Use the `docker exec` command to connect to the new container:
   ```bash
   docker exec -it new-mysql-container mysql -u root -p
   ```
   Enter the root password (`root`) when prompted.

2. Switch to the `test_db` database:
   ```sql
   USE test_db;
   ```

---

### Step 7.2: Check the Data

1. Retrieve the data from the `users` table:
   ```sql
   SELECT * FROM users;
   ```
   You should see the same data (`Alice`, `Bob`, and `Charlie`) you added earlier, confirming that the data has persisted.

---

## Key Commands Summary

| Command                                          | Description                                      |
|--------------------------------------------------|--------------------------------------------------|
| `docker volume create mysql-data`                | Creates a named Docker volume for persistent storage. |
| `docker run -v mysql-data:/var/lib/mysql`        | Attaches the `mysql-data` volume to the container. |
| `docker exec -it <container_name> mysql -u root -p` | Accesses the MySQL shell inside the container.   |
| `docker stop <container_name>`                   | Stops the running container.                    |
| `docker rm <container_name>`                     | Removes the stopped container.                  |

---

## Summary

### What You Achieved:
1. Created and ran a MySQL container with a test database.
2. Persisted the MySQL data using a Docker volume.
3. Verified data persistence by stopping and removing the container, then reattaching the volume to a new container.

### Benefits of Docker Volumes:
- **Data Persistence**: Ensures database data is not lost when containers are stopped or removed.
- **Portability**: Volumes can be used with multiple containers.
- **Separation of Concerns**: Keeps data independent of container instances.
