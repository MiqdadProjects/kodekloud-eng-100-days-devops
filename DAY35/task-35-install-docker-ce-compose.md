**🌟 Task 35 - Install Docker CE and Docker Compose on App Server 3**

**📌 Task Description**

The **Nautilus DevOps team** plans to containerize applications starting with **App Server 3**. The task is to install **Docker Community Edition (Docker CE)** and **Docker Compose plugin** on the server, then start and enable the Docker service for container operations.

👉 **Your task:** Install Docker CE, Docker Compose plugin, and configure Docker service for containerized application deployment.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
sudo su -
```

**Purpose**: Connect to App Server 3 as banner user and switch to root for administrative installation tasks.

---

## 🔹 Step 2: Check operating system version

```bash
cat /etc/os-release
```

**Purpose**: Verify the operating system version to ensure compatibility with Docker installation.

**Expected Output Example**:
```
NAME="CentOS Stream"
VERSION="9"
ID="centos"
ID_LIKE="rhel fedora"
```

**OS Compatibility**: Docker CE supports CentOS, RHEL, Fedora, and similar distributions.

---

## 🔹 Step 3: Update system packages

```bash
sudo dnf update -y
```

**Purpose**: Update existing packages to ensure system has latest security patches and dependencies.

**Best Practice**: Always update system before installing new packages to avoid dependency conflicts.

---

## 🔹 Step 4: Install required utilities

```bash
sudo dnf -y install dnf-plugins-core
```

**Purpose**: Install DNF plugins that enable repository management capabilities.

**Package Details**:
- **dnf-plugins-core**: Provides config-manager plugin for adding repositories

---

## 🔹 Step 5: Add Docker CE repository

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

**Purpose**: Add Docker's official repository to enable installation of Docker CE packages.

**Repository Details**:
- **Source**: Docker's official CentOS repository
- **Purpose**: Provides latest stable Docker CE releases
- **Updates**: Enables automatic updates through DNF

---

## 🔹 Step 6: Verify repository was added

```bash
dnf repolist | grep docker
```

**Purpose**: Confirm Docker repository was successfully added to system.

**Expected Output**:
```
docker-ce-stable        Docker CE Stable - x86_64
```

---

## 🔹 Step 7: Install Docker CE and related components

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Purpose**: Install complete Docker ecosystem including engine, CLI tools, and plugins.

**Packages Installed**:
- **docker-ce**: Docker Engine (Community Edition)
- **docker-ce-cli**: Docker command-line interface
- **containerd.io**: Container runtime
- **docker-buildx-plugin**: Extended build capabilities
- **docker-compose-plugin**: Docker Compose v2 as plugin

---

## 🔹 Step 8: Verify Docker installation

```bash
docker --version
```

**Purpose**: Confirm Docker CE was installed successfully and check version.

**Expected Output**:
```
Docker version 24.x.x, build xxxxxxx
```

---

## 🔹 Step 9: Verify Docker Compose plugin

```bash
docker compose version
```

**Purpose**: Confirm Docker Compose plugin was installed correctly.

**Expected Output**:
```
Docker Compose version v2.x.x
```

**Note**: Docker Compose v2 uses `docker compose` (without hyphen) instead of `docker-compose`.

---

## 🔹 Step 10: Start and enable Docker service

```bash
sudo systemctl enable --now docker
```

**Purpose**: Enable Docker service to start on boot and start it immediately.

**Command Benefits**:
- `--now` flag starts service immediately
- `enable` ensures automatic start on system boot

**Alternative separate commands**:
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 🔹 Step 11: Check Docker service status

```bash
systemctl status docker
```

**Purpose**: Verify Docker daemon is running successfully.

**Expected Output**:
```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: disabled)
   Active: active (running) since [timestamp]
   Main PID: 1234 (dockerd)
```

**Success Indicators**:
- **Loaded**: Service definition loaded
- **Active**: Running state
- **Enabled**: Will start on boot

---

## 🔹 Step 12: Verify Docker is running

```bash
docker info
```

**Purpose**: Display comprehensive Docker system information to confirm proper configuration.

**Key Information Displayed**:
- Server version
- Storage driver
- Container and image counts
- System resources

---

## 🔹 Step 13: Test Docker functionality

```bash
docker run hello-world
```

**Purpose**: Run a test container to verify Docker can pull images and run containers.

**Expected Output**:
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
...
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

## 🔹 Step 14: Verify Docker socket permissions

```bash
ls -la /var/run/docker.sock
```

**Purpose**: Check Docker socket file permissions for proper access control.

**Expected Output**:
```
srw-rw---- 1 root docker 0 [date] /var/run/docker.sock
```

---

## 🔹 Step 15: Add user to docker group (optional)

```bash
sudo usermod -aG docker banner
```

**Purpose**: Allow banner user to run Docker commands without sudo.

**Note**: User needs to log out and back in for group changes to take effect.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect and setup
ssh banner@stapp03
sudo su -

# System preparation
cat /etc/os-release
sudo dnf update -y
sudo dnf -y install dnf-plugins-core

# Add Docker repository
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Install Docker components
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start and enable Docker
sudo systemctl enable --now docker

# Verify installation
docker --version
docker compose version
systemctl status docker
docker run hello-world
```

---

## 💡 Additional Tips

- **Repository updates**: Docker repository provides automatic updates through DNF
- **Compose v2**: Use `docker compose` instead of deprecated `docker-compose`
- **Storage driver**: Docker automatically selects optimal storage driver for system
- **Logging**: Check `/var/log/messages` for Docker-related system logs
- **Firewall**: Ensure firewall rules allow Docker networking if needed

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Repository add fails**
**Symptoms**: Cannot add Docker repository
**Solution**: Check network connectivity and repository URL
```bash
curl -I https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf clean all
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### **Issue 2: Package conflicts**
**Symptoms**: DNF reports package conflicts during installation
**Solution**: Remove old Docker packages first
```bash
sudo dnf remove docker docker-client docker-common docker-latest
sudo dnf install docker-ce docker-ce-cli containerd.io
```

### **Issue 3: Docker service fails to start**
**Symptoms**: systemctl status shows failed state
**Solution**: Check logs and verify dependencies
```bash
sudo journalctl -xeu docker.service
sudo systemctl restart docker
```

### **Issue 4: Permission denied when running Docker**
**Symptoms**: Regular user cannot run Docker commands
**Solution**: Add user to docker group and re-login
```bash
sudo usermod -aG docker $USER
# Log out and log back in
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **installing Docker CE and Docker Compose plugin from official repositories** while ensuring proper service configuration and verification of container functionality on the target server.

**💡 Solution Approach:**

1. **System Preparation**: Updated system packages and installed DNF plugin utilities for repository management
2. **Repository Configuration**: Added Docker's official CentOS repository for accessing latest stable Docker CE packages
3. **Component Installation**: Installed complete Docker ecosystem including engine, CLI, containerd, buildx, and compose plugin
4. **Service Management**: Enabled and started Docker service using systemctl for immediate and persistent operation
5. **Installation Verification**: Confirmed successful installation through version checks and test container execution
6. **Functionality Testing**: Ran hello-world container to validate complete Docker installation and container operations

**🎯 Key Success Factors:**
- **Official repository usage** ensuring access to latest stable Docker CE releases
- **Complete component installation** including all necessary Docker ecosystem packages
- **Plugin-based Compose** using Docker Compose v2 as plugin instead of standalone tool
- **Service persistence** enabling Docker service for automatic startup on system boot
- **Comprehensive verification** testing both installation and functionality
- **Proper service management** using systemctl for reliable service control

**⚠️ Critical Configuration Details:**
- **Repository source** must use official Docker repository for CentOS/RHEL systems
- **Package selection** includes docker-ce, containerd.io, buildx, and compose plugins
- **Service enablement** requires both enable and start operations for complete setup
- **Compose v2 syntax** uses `docker compose` command instead of `docker-compose`
- **Docker socket** provides communication interface between client and daemon

**🔒 Docker Installation Benefits:**
- **Containerization platform** enables isolated, portable application deployment
- **Resource efficiency** provides lightweight virtualization compared to VMs
- **Consistent environments** ensures applications run same way across development and production
- **Microservices support** facilitates modern application architectures

---

## ⚠️ Important Production Notes

🔧 **Container Platform**: Complete Docker CE installation with Compose plugin ready for application containerization

🔐 **Service Configuration**: Docker daemon enabled for automatic startup and configured for system integration

📊 **Version Management**: Docker CE from official repository enables easy updates and security patches

🛡️ **Best Practices**: Installation follows Docker's recommended approach using official repositories and plugin-based architecture