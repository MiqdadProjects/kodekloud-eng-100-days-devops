**🌟 Task 41 - Create a Custom Docker Image with Ubuntu 24.04 and Apache2 on Port 8085**

**📌 Task Description**

The **Nautilus application development team** requires a custom Docker image built on **`ubuntu:24.04`** that installs **Apache2** configured to listen on **port 8085**. The Dockerfile should be located at **`/opt/docker/Dockerfile`** (with a capital D).

👉 **Your task:** Create a Dockerfile that builds a custom Apache web server image with specific port configuration.

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

**Purpose**: Ensure Docker daemon is active before performing build operations.

**Expected Output**:
```
● docker.service - Docker Application Container Engine
   Active: active (running)
```

---

## 🔹 Step 3: Create Dockerfile directory if needed

```bash
mkdir -p /opt/docker
cd /opt/docker
```

**Purpose**: Ensure the required directory exists and navigate to it for Dockerfile creation.

**Verify location**:
```bash
pwd
```

**Expected Output**: `/opt/docker`

---

## 🔹 Step 4: Create the Dockerfile

```bash
vi Dockerfile
```

**Purpose**: Create and edit the Dockerfile with custom image specifications.

**Important**: Filename must be exactly `Dockerfile` with capital 'D'.

---

## 🔹 Step 5: Add Dockerfile content

**Dockerfile Content:**

```dockerfile
# Use Ubuntu 24.04 as base image
FROM ubuntu:24.04

# Update package lists and install Apache2
RUN apt-get update && apt-get install apache2 -y

# Configure Apache to listen on port 8085
RUN sed -i 's/Listen 80/Listen 8085/g' /etc/apache2/ports.conf

# Expose port 8085 for external access
EXPOSE 8085

# Start Apache in foreground mode
CMD ["apache2ctl", "-D", "FOREGROUND"]
```

**Dockerfile Breakdown**:
- **FROM**: Specifies base image (ubuntu:24.04)
- **RUN**: Executes commands during build
- **sed**: Modifies Apache port configuration
- **EXPOSE**: Documents port usage (metadata)
- **CMD**: Default command when container starts

**Save and exit**: Press `Esc`, type `:wq`, press `Enter`

---

## 🔹 Step 6: Verify Dockerfile syntax

```bash
cat Dockerfile
```

**Purpose**: Display Dockerfile contents to verify correct syntax and configuration.

**Expected Content**: Should match the Dockerfile from Step 5.

---

## 🔹 Step 7: Build the Docker image

```bash
docker build -t custom-apache:test .
```

**Purpose**: Build Docker image from Dockerfile with specified tag.

**Build Process Output**:
```
[+] Building 45.3s (8/8) FINISHED
 => [internal] load build definition from Dockerfile
 => [1/4] FROM docker.io/library/ubuntu:24.04
 => [2/4] RUN apt-get update && apt-get install apache2 -y
 => [3/4] RUN sed -i 's/Listen 80/Listen 8085/g' /etc/apache2/ports.conf
 => exporting to image
 => => naming to docker.io/library/custom-apache:test
```

**Build Options**:
- `-t`: Tag the image (repository:tag format)
- `.`: Build context (current directory)

---

## 🔹 Step 8: Verify image was created

```bash
docker images custom-apache
```

**Purpose**: Confirm the custom image appears in local Docker registry.

**Expected Output**:
```
REPOSITORY       TAG     IMAGE ID       CREATED          SIZE
custom-apache    test    abc123def456   30 seconds ago   250MB
```

**Image Verification**:
- ✅ Repository: custom-apache
- ✅ Tag: test
- ✅ Recent creation time
- ✅ Reasonable size (includes Ubuntu + Apache)

---

## 🔹 Step 9: Inspect the built image

```bash
docker inspect custom-apache:test
```

**Purpose**: View detailed image metadata and configuration.

**Key Information**:
- Exposed ports
- CMD configuration
- Environment variables
- Layer information

---

## 🔹 Step 10: Run container from custom image

```bash
docker run -d -p 8085:8085 --name apache-test custom-apache:test
```

**Purpose**: Start a container from the custom image with port mapping.

**Command Options**:
- `-d`: Detached mode (run in background)
- `-p 8085:8085`: Map host port to container port
- `--name`: Container name for easy reference

**Expected Output**: Container ID (64-character hash)

---

## 🔹 Step 11: Verify container is running

```bash
docker ps
```

**Purpose**: Confirm the apache-test container is running successfully.

**Expected Output**:
```
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                    NAMES
abc123def456   custom-apache:test    "apache2ctl -D FORE…"   10 seconds ago  Up 9 seconds   0.0.0.0:8085->8085/tcp   apache-test
```

---

## 🔹 Step 12: Check container logs

```bash
docker logs apache-test
```

**Purpose**: View Apache startup logs to ensure service started correctly.

**Expected Output**:
```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name...
[date] [mpm_event:notice] [pid 1] AH00489: Apache/2.4.x (Ubuntu) configured -- resuming normal operations
```

---

## 🔹 Step 13: Test Apache from host

```bash
curl http://localhost:8085
```

**Purpose**: Test Apache web server functionality from the host machine.

**Expected Output**: HTML content of Apache default page.

**Success Indicators**:
- ✅ HTTP 200 OK response
- ✅ HTML content returned
- ✅ Apache version in response headers

---

## 🔹 Step 14: Test with detailed output

```bash
curl -I http://localhost:8085
```

**Purpose**: View HTTP response headers to confirm Apache is serving correctly.

**Expected Output**:
```
HTTP/1.1 200 OK
Date: [timestamp]
Server: Apache/2.4.x (Ubuntu)
Content-Type: text/html
```

---

## 🔹 Step 15: View image build history

```bash
docker history custom-apache:test
```

**Purpose**: Display layer history showing each Dockerfile instruction.

**Expected Output**:
```
IMAGE          CREATED          CREATED BY                                      SIZE
abc123def456   2 minutes ago    CMD ["apache2ctl" "-D" "FOREGROUND"]           0B
def456abc789   2 minutes ago    EXPOSE 8085                                     0B
...
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect and setup
ssh banner@stapp03
sudo su -

# Create Dockerfile
mkdir -p /opt/docker
cd /opt/docker
cat > Dockerfile << 'EOF'
FROM ubuntu:24.04
RUN apt-get update && apt-get install apache2 -y
RUN sed -i 's/Listen 80/Listen 8085/g' /etc/apache2/ports.conf
EXPOSE 8085
CMD ["apache2ctl", "-D", "FOREGROUND"]
EOF

# Build and run
docker build -t custom-apache:test .
docker run -d -p 8085:8085 --name apache-test custom-apache:test

# Verify
docker images custom-apache
docker ps
curl http://localhost:8085
```

---

## 💡 Additional Tips

- **Layer caching**: Docker caches layers to speed up subsequent builds
- **Multi-stage builds**: Use for optimizing image size
- **.dockerignore**: Exclude unnecessary files from build context
- **Build arguments**: Use ARG for parameterized builds
- **HEALTHCHECK**: Add health checks for container monitoring

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Build fails at apt-get update**
**Symptoms**: Network or repository errors during package installation
**Solution**: Check network connectivity and retry
```bash
docker build --no-cache -t custom-apache:test .
```

### **Issue 2: Port already in use**
**Symptoms**: Cannot start container, port 8085 in use
**Solution**: Stop conflicting service or use different port
```bash
netstat -tlnp | grep 8085
docker run -d -p 8086:8085 --name apache-test custom-apache:test
```

### **Issue 3: Container exits immediately**
**Symptoms**: Container status shows "Exited"
**Solution**: Check logs and verify CMD configuration
```bash
docker logs apache-test
docker run -it custom-apache:test /bin/bash
```

### **Issue 4: sed command doesn't work**
**Symptoms**: Apache still listens on port 80
**Solution**: Verify sed syntax and file path
```bash
docker run -it custom-apache:test cat /etc/apache2/ports.conf
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **creating a Dockerfile that builds a custom image** with specific base image, application installation, port configuration, and service startup requirements.

**💡 Solution Approach:**

1. **Base Image Selection**: Used Ubuntu 24.04 as specified for base operating system
2. **Package Installation**: Combined apt-get update and Apache2 installation in single RUN command for layer efficiency
3. **Configuration Modification**: Used sed command to modify Apache ports.conf file during build
4. **Port Documentation**: Used EXPOSE directive to document container's listening port
5. **Service Management**: Configured apache2ctl with FOREGROUND flag to run as container's main process
6. **Image Building**: Used docker build with custom tag to create reusable image
7. **Testing Validation**: Ran container from image and verified Apache serving on correct port

**🎯 Key Success Factors:**
- **Dockerfile location** placing file at exact path `/opt/docker/Dockerfile` as required
- **Base image specification** using ubuntu:24.04 as foundation
- **Layer optimization** combining commands to reduce image layers
- **Port configuration** using sed for automated configuration modification
- **Foreground execution** ensuring Apache runs as PID 1 to keep container alive
- **Build verification** testing image functionality after build completion

**⚠️ Critical Dockerfile Details:**
- **FROM instruction** must be first non-comment instruction
- **RUN commands** execute during build time, not container runtime
- **CMD instruction** defines default command when container starts
- **EXPOSE** is documentation only, doesn't publish ports automatically
- **Foreground mode** required for service to keep container running

**🔒 Dockerfile Best Practices Applied:**
- **Layer efficiency** combining update and install reduces layers
- **Configuration automation** sed command eliminates manual configuration
- **Service persistence** apache2ctl foreground keeps container alive
- **Port documentation** EXPOSE documents intended port usage

**⚠️ Important Docker Build Concepts**:
- **Build context** directory contents sent to Docker daemon
- **Layer caching** unchanged layers reused in subsequent builds
- **Image tagging** provides versioning and identification
- **Container vs image** image is template, container is running instance

---

## ⚠️ Important Production Notes

🔧 **Dockerfile Method**: Building images from Dockerfile ensures reproducibility and version control

🔐 **Image Optimization**: Combine RUN commands and clean up in same layer to reduce image size

📊 **Port Management**: EXPOSE documents ports but docker run -p actually publishes them

🛡️ **Service Architecture**: Running services in foreground is essential for container lifecycle management