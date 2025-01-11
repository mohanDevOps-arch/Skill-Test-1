# Task 8: PostgreSQL Database Volume Management

## Objective
Learn how to use Docker volumes to persist data for a PostgreSQL database, ensuring that the data remains intact even if the container is stopped or removed.

---

## Step 1: Set Up PostgreSQL Container

### 1.1: Run a PostgreSQL Container
Use the official PostgreSQL Docker image to create and run a container with environment variables for setting up a database:

```bash
docker run -e POSTGRES_USER=admin \
           -e POSTGRES_PASSWORD=admin123 \
           -e POSTGRES_DB=test_db \
           -d --name postgres-db postgres:latest
```

### Explanation:
- `POSTGRES_USER=admin`: Sets the PostgreSQL username.
- `POSTGRES_PASSWORD=admin123`: Sets the password for the PostgreSQL user.
- `POSTGRES_DB=test_db`: Creates a database named `test_db` upon initialization.
- `--name postgres-db`: Names the container `postgres-db`.
- `postgres:latest`: Specifies the latest version of the PostgreSQL image.

---

## Step 2: Create and Attach a Named Volume

### 2.1: Create a Named Volume
To persist PostgreSQL data, create a named Docker volume:

```bash
docker volume create postgres-data
```

### 2.2: Run the PostgreSQL Container with the Volume Attached
Start the PostgreSQL container with the named volume:

```bash
docker run -e POSTGRES_USER=admin \
           -e POSTGRES_PASSWORD=admin123 \
           -e POSTGRES_DB=test_db \
           -v postgres-data:/var/lib/postgresql/data \
           -d --name postgres-db postgres:latest
```

### Explanation:
- `-v postgres-data:/var/lib/postgresql/data`: Attaches the `postgres-data` volume to the `/var/lib/postgresql/data` directory in the container, where PostgreSQL stores its data.

---

## Step 3: Test Data Persistence

### 3.1: Access the PostgreSQL Database
1. Access the running container:
   ```bash
   docker exec -it postgres-db psql -U admin -d test_db
   ```

2. Inside the PostgreSQL CLI, create a sample table and insert some data:
   ```sql
   CREATE TABLE test_table (id SERIAL PRIMARY KEY, name VARCHAR(50));
   INSERT INTO test_table (name) VALUES ('Docker Volume Test');
   SELECT * FROM test_table;
   ```

### Expected Output:
```plaintext
 id |        name         
----+---------------------
  1 | Docker Volume Test
```

### 3.2: Stop and Remove the Container
Stop and remove the container to simulate data persistence testing:

```bash
docker stop postgres-db
docker rm postgres-db
```

### 3.3: Run the Container Again with the Same Volume Attached
Restart the PostgreSQL container with the same volume:

```bash
docker run -e POSTGRES_USER=admin \
           -e POSTGRES_PASSWORD=admin123 \
           -e POSTGRES_DB=test_db \
           -v postgres-data:/var/lib/postgresql/data \
           -d --name postgres-db postgres:latest
```

### 3.4: Verify Data Persistence
Access the database again:

```bash
docker exec -it postgres-db psql -U admin -d test_db
```

Run a query to verify the data:

```sql
SELECT * FROM test_table;
```

### Expected Output:
```plaintext
 id |        name         
----+---------------------
  1 | Docker Volume Test
```

---

## Key Commands Summary

| Command                                             | Description                                              |
|-----------------------------------------------------|----------------------------------------------------------|
| `docker volume create <volume_name>`                | Creates a named Docker volume.                           |
| `docker run -v <volume_name>:<container_path> ...`  | Attaches a named volume to a container.                  |
| `docker exec -it <container_name> <command>`        | Executes a command inside a running container.           |
| `docker stop <container_name>`                      | Stops a running container.                               |
| `docker rm <container_name>`                        | Removes a stopped container.                             |
| `SELECT * FROM <table_name>;`                       | Retrieves all rows from a table in the PostgreSQL database. |

---

## Benefits of Using Volumes with Databases

- **Data Persistence**: Ensures data remains intact across container restarts or removals.
- **Backup and Restore**: Named volumes can be easily backed up and restored.
- **Isolation**: Keeps database data separate from the container’s ephemeral filesystem.

---

## Summary

1. **PostgreSQL Container**: Set up a PostgreSQL container with environment variables for user, password, and database.
2. **Docker Volume**: Created a named volume (`postgres-data`) to persist PostgreSQL data.
3. **Testing**: Verified that data persists across container restarts.
