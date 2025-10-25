**🌟 Task 43 - Set Up NGINX Container on Application Server 3**

**📌 Task Description**

The **Nautilus DevOps team** needs to deploy an **NGINX container** on **Application Server 3** in **Stratos Datacenter** using the **`nginx:alpine`** image. The container should be named **`official`**, map host port **3000** to container port **80**, and remain in a running state.

👉 **Your task:** Deploy an NGINX web server container with specific naming and port mapping requirements.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
sudo su -
```

**Purpose**: Connect to Application Server 3 as banner user and switch to root for Docker operations.

---

## 🔹 Step 2: Verify Docker service is running

```bash
systemctl status docker
```

**Purpose**: Ensure Docker daemon is active before performing container operations.

**Expected Output**:
```
● docker.service - Docker Application Container Engine
   Active: active (running)
```

**If not running**:
```bash
systemctl start docker
```

---

## 🔹 Step 3: Check for existing containers

```bash
docker ps -a
```

**Purpose**: View all containers (running and stopped) to check if 'official' name is already in use.

**Expected Behavior**: Should not show any container named 'official' if this is first deployment.

---

## 🔹 Step 4: Pull the nginx:alpine image

```bash
docker pull nginx:alpine
```

**Purpose**: Download the NGINX Alpine image from Docker Hub registry.

**Expected Output**:
```
alpine: Pulling from library/nginx
619be1103602: Pull complete
1dd1e7d8e6d9: Pull complete
...
Digest: sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine
```

**Image Details**:
- **Base**: Alpine Linux (minimal footprint)
- **Size**: ~25MB (much smaller than standard nginx)
- **Benefits**: Faster pulls, smaller disk usage

---

## 🔹 Step 5: Verify image download

```bash
docker images nginx
```

**Purpose**: Confirm the nginx:alpine image is available locally.

**Expected Output**:
```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        alpine    a64a6e03b055   2 weeks ago    43.3MB
```

---

## 🔹 Step 6: Check if port 3000 is available

```bash
ss -tlnp | grep :3000
```

**Purpose**: Verify host port 3000 is not already in use by another service.

**Expected Result**: No output means port is available.

**If port is in use**:
```bash
# Find what's using the port
lsof -i :3000
# Stop the conflicting service if needed
```

---

## 🔹 Step 7: Run the NGINX container

```bash
docker run -d --name official -p 3000:80 nginx:alpine
```

**Purpose**: Start NGINX container with specified name and port mapping in detached mode.

**Command Options**:
- `-d`: Detached mode (run in background)
- `--name official`: Container name
- `-p 3000:80`: Port mapping (host:container)
- `nginx:alpine`: Image to use

**Expected Output**: Container ID (64-character hash)

---

## 🔹 Step 8: Verify container is running

```bash
docker ps
```

**Purpose**: Confirm the 'official' container is in running state.

**Expected Output**:
```
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
abc123def456   nginx:alpine   "/docker-entrypoint.…"   10 seconds ago  Up 9 seconds   0.0.0.0:3000->80/tcp   official
```

**Verification Points**:
- ✅ Container name: **official**
- ✅ Status: **Up**
- ✅ Port mapping: **0.0.0.0:3000->80/tcp**

---

## 🔹 Step 9: Check container logs

```bash
docker logs official
```

**Purpose**: View NGINX startup logs to ensure service started correctly.

**Expected Output**:
```
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
...
/docker-entrypoint.sh: Configuration complete; ready for start up
```

---

## 🔹 Step 10: Test NGINX from host

```bash
curl http://localhost:3000
```

**Purpose**: Test NGINX web server is serving content on mapped port.

**Expected Output**: HTML content of NGINX default welcome page.

**Success Indicators**:
- ✅ HTTP 200 OK response
- ✅ HTML content displayed
- ✅ "Welcome to nginx!" message

---

## 🔹 Step 11: Test with HTTP headers

```bash
curl -I http://localhost:3000
```

**Purpose**: View HTTP response headers to confirm NGINX is serving requests.

**Expected Output**:
```
HTTP/1.1 200 OK
Server: nginx/1.25.x
Date: [timestamp]
Content-Type: text/html
...
```

---

## 🔹 Step 12: Inspect container details

```bash
docker inspect official
```

**Purpose**: View detailed container configuration and state information.

**Key Information**:
- Network settings
- Port mappings
- Environment variables
- Mount points
- Container state

---

## 🔹 Step 13: Check container resource usage

```bash
docker stats official --no-stream
```

**Purpose**: Display current resource usage (CPU, memory, network) of the container.

**Expected Output**:
```
CONTAINER ID   NAME       CPU %   MEM USAGE / LIMIT   MEM %   NET I/O     BLOCK I/O
abc123def456   official   0.00%   3.5MiB / 7.6GiB     0.05%   1.2kB / 0B  0B / 0B
```

---

## 🔹 Step 14: Test from external host (optional)

**From Jump Host or another server**:

```bash
curl http://stapp03:3000
```

**Purpose**: Verify NGINX is accessible from external hosts.

**Note**: Requires firewall rules to allow port 3000 if firewall is active.

---

## 🔹 Step 15: Verify container auto-restart policy

```bash
docker inspect official --format='{{.HostConfig.RestartPolicy.Name}}'
```

**Purpose**: Check if container will restart automatically.

**Current State**: "no" (default, no auto-restart)

**To add restart policy** (optional):
```bash
docker update --restart=unless-stopped official
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to server
ssh banner@stapp03
sudo su -

# Pull and verify image
docker pull nginx:alpine
docker images nginx

# Run container
docker run -d --name official -p 3000:80 nginx:alpine

# Verify and test
docker ps
docker logs official
curl http://localhost:3000
```

---

## 💡 Additional Tips

- **Alpine vs standard**: Alpine images are ~10x smaller than standard images
- **Container persistence**: Use volumes for persistent data storage
- **Environment variables**: Pass config using `-e` flag
- **Custom nginx.conf**: Mount custom configuration with `-v`
- **HTTPS support**: Add port 443 mapping and SSL certificates

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Port 3000 already in use**
**Symptoms**: Error "port is already allocated"
**Solution**: Stop conflicting service or use different port
```bash
ss -tlnp | grep :3000
docker run -d --name official -p 3001:80 nginx:alpine
```

### **Issue 2: Container name already exists**
**Symptoms**: Error "name is already in use"
**Solution**: Remove existing container or use different name
```bash
docker rm -f official
docker run -d --name official -p 3000:80 nginx:alpine
```

### **Issue 3: Container exits immediately**
**Symptoms**: Container status shows "Exited"
**Solution**: Check logs for errors
```bash
docker logs official
docker ps -a  # Show all containers including stopped
```

### **Issue 4: Cannot access from external hosts**
**Symptoms**: Curl works locally but not remotely
**Solution**: Check firewall rules and network configuration
```bash
firewall-cmd --list-ports
firewall-cmd --permanent --add-port=3000/tcp
firewall-cmd --reload
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **deploying an NGINX web server container with specific naming and port mapping requirements** while ensuring the container remains in running state for service availability.

**💡 Solution Approach:**

1. **Image Selection**: Used nginx:alpine for lightweight, efficient deployment
2. **Image Acquisition**: Pulled image from Docker Hub to ensure latest stable version
3. **Port Mapping**: Configured host port 3000 to map to container's default HTTP port 80
4. **Container Naming**: Used specific name "official" for easy identification and management
5. **Detached Mode**: Ran container in background to keep it running persistently
6. **Service Verification**: Tested NGINX functionality through HTTP requests to mapped port

**🎯 Key Success Factors:**
- **Alpine image usage** providing minimal footprint while maintaining full NGINX functionality
- **Port mapping syntax** using `-p 3000:80` to expose container port 80 on host port 3000
- **Detached mode** using `-d` flag to run container in background
- **Container naming** using `--name official` for consistent identification
- **Service testing** validating NGINX serves content correctly on mapped port
- **State verification** confirming container remains in "Up" status

**⚠️ Critical Configuration Details:**
- **Port mapping format** uses `host_port:container_port` notation
- **Detached mode** essential for keeping container running in background
- **Container naming** must be unique across all containers on host
- **Image tag** nginx:alpine specifies both repository and tag
- **Default behavior** NGINX listens on port 80 inside container by default

**🔒 Container Deployment Benefits:**
- **Rapid deployment** container starts in seconds vs traditional installation
- **Consistency** same image runs identically across environments
- **Isolation** NGINX runs in isolated namespace with minimal dependencies
- **Resource efficiency** Alpine base provides minimal overhead

**⚠️ Important Container Concepts**:
- **Port publishing** `-p` makes container port accessible from host
- **Detached vs attached** detached runs in background, attached shows logs
- **Container lifecycle** container runs as long as PID 1 process runs
- **Image layers** nginx:alpine built on Alpine Linux base layers

---

## ⚠️ Important Production Notes

🔧 **Container Management**: NGINX container provides quick, isolated web server deployment

🔐 **Port Configuration**: Port mapping enables external access while maintaining container isolation

📊 **Resource Efficiency**: Alpine-based images provide full functionality with minimal footprint

🛡️ **Service Availability**: Detached mode ensures container continues running independently.