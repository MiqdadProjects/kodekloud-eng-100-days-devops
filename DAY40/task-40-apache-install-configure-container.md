**🌟 Task 40 - Install and Configure Apache2 on kkloud Container on App Server 2**

**📌 Task Description**

The **Nautilus DevOps team** needs to complete the pending setup on the **`kkloud`** container running on **App Server 2** in **Stratos Datacenter**. The tasks include:

a. Install **Apache2** inside the container using `apt`
b. Configure Apache to listen on **port 6000** (without restricting to a specific IP or hostname)
c. Ensure Apache service is running inside the container and keep the container running

👉 **Your task:** Configure Apache web server inside a running Docker container with custom port configuration.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
sudo su -
```

**Purpose**: Connect to Application Server 2 as steve user and switch to root for Docker operations.

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

---

## 🔹 Step 3: List running containers

```bash
docker ps
```

**Purpose**: Confirm the target container `kkloud` is running on the system.

**Expected Output Example**:
```
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
6c0584989b4e   ubuntu:18.04   "/bin/bash"   7 minutes ago   Up 7 minutes             kkloud
```

**Container Details**:
- **Name**: kkloud
- **Image**: ubuntu:18.04
- **Status**: Must be running (Up)

---

## 🔹 Step 4: Check container details

```bash
docker inspect kkloud
```

**Purpose**: View detailed container configuration and state information.

**Key Information**:
- Running state
- Network configuration
- Mounted volumes
- Environment variables

---

## 🔹 Step 5: Access container shell

```bash
docker exec -it kkloud /bin/bash
```

**Purpose**: Enter interactive bash shell inside the running kkloud container.

**Command Options**:
- `-i`: Interactive mode (keep STDIN open)
- `-t`: Allocate pseudo-TTY (terminal)
- `/bin/bash`: Shell to execute

**Expected Result**: Command prompt changes to show you're inside the container.

---

## 🔹 Step 6: Update package repository

```bash
apt update
```

**Purpose**: Update the package list to get information on newest versions of packages.

**Expected Output**:
```
Hit:1 http://archive.ubuntu.com/ubuntu bionic InRelease
Get:2 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
...
Reading package lists... Done
```

---

## 🔹 Step 7: Install Apache2 web server

```bash
apt install apache2 -y
```

**Purpose**: Install Apache HTTP server package inside the container.

**Installation Process**:
- Downloads Apache2 and dependencies
- Configures default Apache setup
- Creates necessary directories and files

**Expected Output**:
```
Reading package lists... Done
Building dependency tree
...
Setting up apache2 (2.4.x-ubuntu1)
```

---

## 🔹 Step 8: Install text editor (optional)

```bash
apt install vim -y
```

**Purpose**: Install vim text editor for editing configuration files.

**Alternative**:
```bash
apt install nano -y  # If you prefer nano editor
```

---

## 🔹 Step 9: Navigate to Apache configuration directory

```bash
cd /etc/apache2
ls -la
```

**Purpose**: Navigate to Apache configuration directory and view available config files.

**Expected Files**:
```
drwxr-xr-x  apache2.conf
drwxr-xr-x  ports.conf
drwxr-xr-x  sites-available/
drwxr-xr-x  sites-enabled/
...
```

---

## 🔹 Step 10: Edit Apache ports configuration

```bash
vim ports.conf
```

**Purpose**: Edit the ports configuration file to change Apache's listening port.

**Configuration Change**:

**Find this line:**
```apache
Listen 80
```

**Change to:**
```apache
Listen 6000
```

**Editor Commands** (in vim):
- Press `i` to enter insert mode
- Make the change
- Press `Esc` to exit insert mode
- Type `:wq` and press `Enter` to save and exit

---

## 🔹 Step 11: Verify ports configuration

```bash
cat ports.conf | grep Listen
```

**Purpose**: Confirm the port configuration was changed correctly.

**Expected Output**:
```
Listen 6000
```

---

## 🔹 Step 12: Start Apache service

```bash
service apache2 start
```

**Purpose**: Start the Apache HTTP server inside the container.

**Expected Output**:
```
 * Starting Apache httpd web server apache2
 * 
```

**Alternative command**:
```bash
/etc/init.d/apache2 start
```

---

## 🔹 Step 13: Check Apache service status

```bash
service apache2 status
```

**Purpose**: Verify Apache service is running successfully.

**Expected Output**:
```
 * apache2 is running
```

**Status Indicators**:
- ✅ "is running" - Service active
- ❌ "is not running" - Service stopped

---

## 🔹 Step 14: Verify Apache is listening on port 6000

```bash
netstat -tlnp | grep 6000
```

**Purpose**: Confirm Apache is listening on the correct port.

**Expected Output**:
```
tcp6       0      0 :::6000                 :::*                    LISTEN      123/apache2
```

**If netstat not available**:
```bash
apt install net-tools -y
netstat -tlnp | grep 6000
```

---

## 🔹 Step 15: Test Apache from inside container

```bash
curl http://localhost:6000/
```

**Purpose**: Test Apache web server functionality from within the container.

**Expected Output**: HTML content of Apache default page.

**Alternative test**:
```bash
wget -O - http://localhost:6000/
```

---

## 🔹 Step 16: Keep container running

**Important**: Do NOT exit the container completely. Apache needs to keep running.

**Option 1 - Keep current session**:
```bash
# Stay in the container shell - don't type exit
# Container will stay running
```

**Option 2 - Detach without stopping**:
```bash
# Press Ctrl+P, then Ctrl+Q to detach
# Container continues running in background
```

**Option 3 - Run Apache in foreground**:
```bash
apachectl -D FOREGROUND &
```

---

## 🔹 Step 17: Verify from host (App Server 2)

**From another terminal session on App Server 2**:

```bash
docker exec kkloud service apache2 status
```

**Purpose**: Verify Apache is still running from outside the container.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect and access container
ssh steve@stapp02
sudo su -
docker ps
docker exec -it kkloud /bin/bash

# Inside container - Install and configure Apache
apt update
apt install apache2 -y
apt install vim -y
cd /etc/apache2
vim ports.conf
# (Change Listen 80 to Listen 6000)

# Start and verify Apache
service apache2 start
service apache2 status
netstat -tlnp | grep 6000
curl http://localhost:6000/

# Keep container running (don't exit)
```

---

## 💡 Additional Tips

- **Container persistence**: Keep container running by not exiting the shell
- **Service management**: Use `service apache2 restart` after config changes
- **Log access**: Check logs with `tail -f /var/log/apache2/error.log`
- **Port testing**: Use `curl` or `wget` for quick HTTP tests
- **Alternative ports**: Apache can listen on multiple ports if needed

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Container stops when exiting shell**
**Symptoms**: Container status changes to "Exited" after leaving shell
**Solution**: Re-enter container or run Apache in foreground
```bash
docker start kkloud
docker exec -it kkloud /bin/bash
service apache2 start
```

### **Issue 2: Apache fails to start**
**Symptoms**: Service status shows "failed" or error messages
**Solution**: Check error logs and configuration syntax
```bash
apache2ctl configtest
cat /var/log/apache2/error.log
```

### **Issue 3: Port 6000 already in use**
**Symptoms**: Apache can't bind to port 6000
**Solution**: Check what's using the port and stop it
```bash
netstat -tlnp | grep 6000
kill <PID>
service apache2 restart
```

### **Issue 4: Package installation fails**
**Symptoms**: apt install errors or connection issues
**Solution**: Update package lists and check network
```bash
apt update
apt install apache2 -y --fix-missing
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **installing and configuring Apache web server inside a running Docker container** with custom port configuration while ensuring the container and service remain running after setup.

**💡 Solution Approach:**

1. **Container Access**: Used `docker exec` to access interactive shell inside running kkloud container
2. **Package Management**: Updated apt repositories and installed Apache2 using container's package manager
3. **Port Configuration**: Modified `/etc/apache2/ports.conf` to change listening port from default 80 to 6000
4. **Service Management**: Started Apache service using container's init system (service command)
5. **Verification Process**: Confirmed Apache running on correct port using netstat and curl testing
6. **Container Persistence**: Maintained container running state by keeping shell session active

**🎯 Key Success Factors:**
- **Interactive execution** using `docker exec -it` for shell access inside running container
- **Package installation** using apt within Ubuntu-based container environment
- **Configuration modification** editing ports.conf to specify custom port 6000
- **Service initialization** starting Apache within container using service commands
- **Port verification** confirming Apache listening on correct port with netstat
- **Container persistence** ensuring container stays running after configuration

**⚠️ Critical Configuration Details:**
- **Container state** must be running before exec command can access it
- **Port configuration** requires changing Listen directive in ports.conf
- **Service management** uses init.d/service commands within container
- **Container persistence** depends on keeping process running (shell or service)
- **Network access** Apache binds to all interfaces when no IP specified

**🔒 Container Service Management:**
- **Exec vs attach** exec creates new process, attach connects to existing
- **Interactive mode** required for shell access and editing
- **Service lifecycle** services don't auto-start in containers by default
- **Container persistence** requires foreground process or persistent shell

**⚠️ Important Container Concepts**:
- **Stateless nature** containers don't persist changes unless committed
- **Process management** container runs as long as PID 1 process runs
- **Service handling** traditional init systems work differently in containers
- **Network isolation** container has own network namespace by default

---

## ⚠️ Important Production Notes

🔧 **Container Services**: Running services in containers requires understanding of container lifecycle and process management

🔐 **Port Configuration**: Custom ports allow running multiple services without conflicts on host system

📊 **Container State**: Changes made inside running containers are temporary unless image is committed

🛡️ **Best Practice**: For production, use Dockerfile to bake Apache installation and configuration into image