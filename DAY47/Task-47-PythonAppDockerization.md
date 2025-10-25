# 🌟 Task 47 - Dockerize and Deploy Python App on App Server 3

**📌 Task Description**

The **Nautilus Application Development team** needs to Dockerize a Python application and deploy it on **App Server 3** in the Stratos Datacenter. A `requirements.txt` file with the app’s dependencies has been copied to `/python_app/src/` on the server. The task involves creating a Dockerfile, building an image, and running a container to serve the application.

**👉 Task Requirements**:
- Create a Dockerfile under `/python_app`:
  - Use any Python base image.
  - Install dependencies from `/python_app/src/requirements.txt`.
  - Expose port 8082.
  - Run `server.py` using `CMD`.
- Build an image named `nautilus/python-app`.
- Deploy a container named `pythonapp_nautilus`:
  - Map container port 8082 to host port 8095.
- Test the application using `curl http://localhost:8095/`.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
sudo su -
```

**Purpose**: Connect to App Server 3 as the `banner` user and switch to `root` for Docker operations.

---

## 🔹 Step 2: Navigate to the Python App Directory

```bash
cd /python_app
```

**Purpose**: Navigate to the directory where the Dockerfile will be created.

**Verify Location**:
```bash
pwd
ls -la
ls -la src/
```

**Expected Structure**:
```
/python_app/
├── src/
│   ├── requirements.txt
│   └── server.py
```

---

## 🔹 Step 3: Create the Dockerfile

```bash
vi Dockerfile
```

**Purpose**: Create and edit the Dockerfile with the required configuration.

---

## 🔹 Step 4: Add Dockerfile Content

**Dockerfile**:

```dockerfile
# Use official Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependencies file
COPY src/requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application files
COPY src/ .

# Expose the application port
EXPOSE 8082

# Run the Python server
CMD ["python", "server.py"]
```

**Key Configurations**:
- **FROM**: Uses `python:3.9-slim` for a lightweight Python base image.
- **WORKDIR**: Sets `/app` as the working directory for file operations.
- **COPY**: Copies `requirements.txt` from `src/` to install dependencies.
- **RUN**: Installs dependencies with `pip`, using `--no-cache-dir` to reduce image size.
- **COPY**: Copies all `src/` files (including `server.py`) to the working directory.
- **EXPOSE**: Exposes port 8082 for the application.
- **CMD**: Runs `server.py` with the Python interpreter.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 5: Verify Dockerfile Syntax

```bash
cat Dockerfile
```

**Purpose**: Confirm the Dockerfile contains the correct configuration.

**Validation Checklist**:
- ✅ `FROM python:3.9-slim` as base image
- ✅ Correct `COPY` paths for `requirements.txt` and `src/`
- ✅ `EXPOSE 8082` for the application port
- ✅ `CMD ["python", "server.py"]` for running the app
- ✅ No syntax errors

---

## 🔹 Step 6: Build the Docker Image

```bash
docker build -t nautilus/python-app .
```

**Purpose**: Build the Docker image using the Dockerfile.

**Expected Build Process**:
```
[+] Building 10.2s (8/8) FINISHED
 => [internal] load build definition from Dockerfile
 => [internal] load .dockerignore
 => [internal] load metadata for docker.io/library/python:3.9-slim
 => [1/5] FROM docker.io/library/python:3.9-slim
 => [2/5] WORKDIR /app
 => [3/5] COPY src/requirements.txt .
 => [4/5] RUN pip install --no-cache-dir -r requirements.txt
 => [5/5] COPY src/ .
 => exporting to image
 => => naming to docker.io/nautilus/python-app
```

---

## 🔹 Step 7: Verify Image Creation

```bash
docker images nautilus/python-app
```

**Purpose**: Confirm the image was created successfully.

**Expected Output**:
```
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
nautilus/python-app   latest    abc123def456   10 seconds ago   150MB
```

**Success Indicators**:
- ✅ Image appears in the list
- ✅ Tag is `latest`
- ✅ Recent creation timestamp
- ✅ Reasonable size (~150MB, depending on dependencies)

---

## 🔹 Step 8: Deploy the Container

```bash
docker run -d --name pythonapp_nautilus -p 8095:8082 nautilus/python-app
```

**Purpose**: Run the container in detached mode with the specified name and port mapping.

**Expected Output**:
```
xyz789abc123
```

---

## 🔹 Step 9: Verify Running Container

```bash
docker ps
```

**Purpose**: Confirm the `pythonapp_nautilus` container is running.

**Expected Output**:
```
CONTAINER ID   IMAGE                 COMMAND            CREATED         STATUS         PORTS                    NAMES
xyz789abc123   nautilus/python-app   "python server.py" 10 seconds ago  Up 10 seconds  0.0.0.0:8095->8082/tcp   pythonapp_nautilus
```

---

## 🔹 Step 10: Test Application Accessibility

```bash
curl http://localhost:8095/
```

**Purpose**: Verify the Python application is accessible on port 8095.

**Expected Output**:
```
Welcome to xFusionCorp Industries!
```

---

## 🔹 Step 11: Check Container Logs

```bash
docker logs pythonapp_nautilus
```

**Purpose**: Ensure the application started without errors.

**Expected Logs**: Depends on `server.py` output; typically shows server startup messages (e.g., `Running on http://0.0.0.0:8082`).

---

## 🔹 Step 12: Clean Up (Optional)

```bash
docker stop pythonapp_nautilus
docker rm pythonapp_nautilus
```

**Purpose**: Stop and remove the container if testing is complete.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect and navigate
ssh banner@stapp03
sudo su -
cd /python_app

# Create Dockerfile
cat > Dockerfile << 'EOF'
# Use official Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependencies file
COPY src/requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application files
COPY src/ .

# Expose the application port
EXPOSE 8082

# Run the Python server
CMD ["python", "server.py"]
EOF

# Build and deploy
docker build -t nautilus/python-app .
docker images nautilus/python-app
docker run -d --name pythonapp_nautilus -p 8095:8082 nautilus/python-app
docker ps
curl http://localhost:8095/
```

---

## 💡 Common Dockerfile Issues & Fixes

### **Issue 1: Missing requirements.txt**
**Problem**: `COPY src/requirements.txt` fails due to missing file.
**Fix**: Verify file exists in `/python_app/src/`.
```bash
ls -la src/requirements.txt
```

### **Issue 2: Dependency Installation Failure**
**Problem**: `pip install` fails due to missing packages or network issues.
**Fix**: Check `requirements.txt` and network connectivity.
```bash
docker pull python:3.9-slim
cat src/requirements.txt
```

### **Issue 3: Port Already in Use**
**Problem**: Host port 8095 is occupied.
**Fix**: Check and free the port.
```bash
netstat -tuln | grep 8095
kill -9 <pid>
```

### **Issue 4: server.py Not Found**
**Problem**: `CMD ["python", "server.py"]` fails because `server.py` is missing.
**Fix**: Ensure `server.py` is in `/python_app/src/`.
```bash
ls -la src/server.py
```

---

## 🔧 Troubleshooting Build and Deployment Failures

### **Build Error: COPY Failed**
**Symptoms**: "COPY failed: stat src/requirements.txt: no such file or directory".
**Solution**: Verify file paths relative to the build context.
```bash
ls -la src/
```

### **Build Error: pip Install Failed**
**Symptoms**: "ERROR: Could not find a version that satisfies the requirement...".
**Solution**: Validate `requirements.txt` and retry with `--no-cache`.
```bash
docker build --no-cache -t nautilus/python-app .
```

### **Container Error: Failed to Start**
**Symptoms**: `docker ps -a` shows `Exited` status.
**Solution**: Check logs for errors (e.g., missing `server.py`, dependency issues).
```bash
docker logs pythonapp_nautilus
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was Dockerizing a Python application by creating a Dockerfile that correctly installs dependencies, exposes the required port, and runs the application, followed by deploying a container with specific port mappings.

**💡 Solution Approach**:
1. **Directory Setup**: Navigated to `/python_app` and verified `src/requirements.txt` and `src/server.py`.
2. **Dockerfile Creation**: Defined a lean Dockerfile using `python:3.9-slim`, installing dependencies and setting up the app.
3. **Image Build**: Built the `nautilus/python-app` image with correct file paths.
4. **Container Deployment**: Ran the `pythonapp_nautilus` container with port mapping `8095:8082`.
5. **Verification**: Tested the app with `curl http://localhost:8095/` and checked container status.

**🎯 Key Success Factors**:
- **Correct Base Image**: Used `python:3.9-slim` for efficiency.
- **Dependency Management**: Installed dependencies from `requirements.txt` without caching.
- **File Path Accuracy**: Ensured `COPY src/` included all necessary files.
- **Port Mapping**: Correctly mapped `8095:8082` for external access.
- **Testing**: Validated output with `curl` and confirmed `Welcome to xFusionCorp Industries!`.

**⚠️ Critical Dockerfile Fixes**:
- Use `WORKDIR /app` for consistent file operations.
- Separate `COPY` for `requirements.txt` to leverage layer caching.
- Use `--no-cache-dir` in `pip install` to reduce image size.
- Validate `server.py` existence before running.

**🔒 Dockerfile Best Practices Applied**:
- **Minimal Base Image**: Used `python:3.9-slim` for smaller footprint.
- **Layer Optimization**: Separated dependency installation and file copying.
- **Clear Commands**: Used explicit `CMD ["python", "server.py"]` for clarity.
- **Error Prevention**: Validated file paths and dependencies.

**⚠️ Important Troubleshooting Concepts**:
- **Build Context**: Ensure all files are in the correct directory relative to the Dockerfile.
- **Layer Caching**: Use `--no-cache` for troubleshooting persistent build issues.
- **Log Analysis**: Check container logs for runtime errors.
- **Port Conflicts**: Verify host ports are free before deployment.

---

## ⚠️ Important Production Notes

🔧 **Dockerfile Optimization**: Use slim base images and minimize layers for production efficiency.
🔐 **Security**: Avoid hardcoding sensitive data in `server.py`; use environment variables or secrets.
📊 **Dependency Management**: Regularly update `requirements.txt` to patch vulnerabilities.
🛡️ **Port Security**: Restrict access to port 8095 with firewall rules in production.

*Last Updated: October 25, 2025*