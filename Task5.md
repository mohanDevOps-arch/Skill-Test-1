# Task 5: Network Isolation Implementation

## Objective  
In this task, you will learn how to implement network isolation in Docker by creating multiple bridge networks and deploying Nginx containers in separate networks. This ensures that containers in different networks cannot communicate with each other.  

---

## Step 1: Create Multiple Bridge Networks  

To isolate containers, we first create two separate bridge networks.  

### Commands to Create Networks:  

1. Create the first network:  
   ```bash  
   docker network create --driver bridge network-1  
   ```  

2. Create the second network:  
   ```bash  
   docker network create --driver bridge network-2  
   ```  

### Explanation:  
- `docker network create`: Creates a new Docker network.  
- `--driver bridge`: Specifies the bridge driver, which is the default network type.  
- `network-1` and `network-2`: Names of the two networks.  

### Verify the Networks:  
Run the following command to confirm the networks were created:  
```bash  
docker network ls  
```  
You should see `network-1` and `network-2` listed.

---

## Step 2: Deploy Nginx Containers in Different Networks  

Now, deploy Nginx containers and assign them to the respective networks.  

### Step 2.1: Run an Nginx Container in `network-1`:  
```bash  
docker run --network network-1 -d --name nginx-container-1 nginx  
```  

### Step 2.2: Run an Nginx Container in `network-2`:  
```bash  
docker run --network network-2 -d --name nginx-container-2 nginx  
```  

### Explanation:  
- `--network network-1`: Attaches the container to `network-1`.  
- `--network network-2`: Attaches the container to `network-2`.  
- `-d`: Runs the containers in detached mode.  
- `--name`: Assigns a name to the containers for easier identification.  
- `nginx`: Specifies the official Nginx image.  

---

## Step 3: Test Network Isolation  

### Step 3.1: Access `nginx-container-1`  
Open a terminal and enter the first container:  
```bash  
docker exec -it nginx-container-1 /bin/sh  
```  

### Step 3.2: Attempt to Ping `nginx-container-2`  
Inside the terminal for `nginx-container-1`, try to ping `nginx-container-2`:  
```bash  
ping nginx-container-2  
```  

### Expected Result:  
The ping should fail, confirming that `nginx-container-1` cannot communicate with `nginx-container-2` because they are on separate networks.  

---

## Step 4: Verify Network Isolation  

You can also inspect the networks to ensure proper isolation.  

### Inspect `network-1`:  
```bash  
docker network inspect network-1  
```  

Check the `Containers` section in the output to confirm that only `nginx-container-1` is part of this network.  

### Inspect `network-2`:  
```bash  
docker network inspect network-2  
```  

Similarly, verify that only `nginx-container-2` is part of `network-2`.  

---

## Step 5: Test Nginx Service Accessibility  

### Access Nginx on Each Container:
1. Expose the Nginx service of `nginx-container-1` and `nginx-container-2` to the host by specifying a port mapping:  
   ```bash  
   docker run --network network-1 -d -p 8081:80 --name nginx-container-1 nginx  
   docker run --network network-2 -d -p 8082:80 --name nginx-container-2 nginx  
   ```  

2. Open a browser or use `curl` to test the Nginx default pages:  
   - For `nginx-container-1`:  
     ```bash  
     curl http://localhost:8081  
     ```  
   - For `nginx-container-2`:  
     ```bash  
     curl http://localhost:8082  
     ```  

Both should display the default Nginx welcome page.  

---

## Key Commands Summary  

| Command                                          | Description                                      |  
|--------------------------------------------------|--------------------------------------------------|  
| `docker network create --driver bridge <name>`   | Creates a new bridge network.                   |  
| `docker run --network <network_name> ...`        | Attaches a container to a specific network.     |  
| `docker network ls`                              | Lists all available networks.                   |  
| `docker exec -it <container_name> /bin/sh`       | Opens a shell inside a running container.       |  
| `docker network inspect <network_name>`          | Shows details about a specific network.         |  
| `docker run -p <host_port>:<container_port> ...` | Exposes a container port to the host machine.   |  

---

## Summary  

### What You Achieved:  
1. **Created multiple bridge networks**:  
   - Isolated `nginx-container-1` and `nginx-container-2` by assigning them to `network-1` and `network-2`.  

2. **Deployed Nginx containers**:  
   - Used the official Nginx image to deploy containers in isolated networks.  

3. **Verified isolation**:  
   - Confirmed that containers in different networks cannot communicate by attempting to ping one from the other.  

4. **Tested Nginx services**:  
   - Accessed the Nginx default pages to ensure the containers were functioning properly.  

### Benefits of Network Isolation:  
- **Security**: Prevents unwanted communication between unrelated services.  
- **Logical Segmentation**: Groups containers by purpose while keeping them isolated.  
- **Flexibility**: Allows for different network configurations for different container groups.  
