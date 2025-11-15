**🌟 Task 68 - Install and Configure Jenkins Server with Admin User**

**📌 Task Description**

The **DevOps team at xFusionCorp Industries** is initiating the setup of **CI/CD pipelines** and has decided to utilize **Jenkins** as their server. Execute the task according to the provided requirements:

1. Install **Jenkins** on the jenkins server using the **yum utility only**, and start its service
2. Jenkins admin user should be configured with:
   - **Username**: `theadmin`
   - **Password**: `Adm!n321`
   - **Full name**: `Javed`
   - **Email**: `javed@jenkins.stratos.xfusioncorp.com`

👉 **Your task:** Install Jenkins server, configure initial setup, and create admin user with specified credentials.

💡 **Note:** To access the jenkins server, connect from the jump host using the root user with the password `S3curePass`.

---

## 🔹 Step 1: Connect to Jenkins Server

```bash
ssh root@jenkins
```

**Password**: `S3curePass`

**Purpose**: Connect to the dedicated Jenkins server from jump host.

---

## 🔹 Step 2: Check system OS version

```bash
cat /etc/os-release
```

**Purpose**: Verify the operating system version for compatibility.

**Expected**: CentOS, RHEL, or compatible Linux distribution.

---

## 🔹 Step 3: Install required dependencies

```bash
yum install wget -y
yum install fontconfig java-21-openjdk -y
```

**Purpose**: Install Java runtime and utilities required for Jenkins.

**Dependencies**:
- **wget**: Download utility for fetching Jenkins repository file
- **fontconfig**: Font configuration library
- **java-21-openjdk**: Java 21 OpenJDK runtime environment

---

## 🔹 Step 4: Verify Java installation

```bash
java -version
```

**Purpose**: Confirm Java is properly installed.

**Expected Output**:
```
openjdk version "21.x.x" ...
OpenJDK Runtime Environment ...
```

---

## 🔹 Step 5: Add Jenkins repository

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

**Purpose**: Download Jenkins repository configuration for stable releases.

**Repository Location**: `/etc/yum.repos.d/jenkins.repo`

---

## 🔹 Step 6: Import Jenkins GPG key

```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

**Purpose**: Import GPG key to verify package authenticity and integrity.

---

## 🔹 Step 7: Upgrade system packages

```bash
sudo yum upgrade -y
```

**Purpose**: Update all system packages before Jenkins installation.

**Best Practice**: Ensures latest security patches and dependencies.

---

## 🔹 Step 8: Install Jenkins

```bash
sudo yum install jenkins -y
```

**Purpose**: Install Jenkins package using yum package manager.

**Installation Details**:
- Creates jenkins user and group
- Installs to `/var/lib/jenkins`
- Creates systemd service unit

---

## 🔹 Step 9: Reload systemd daemon

```bash
sudo systemctl daemon-reload
```

**Purpose**: Reload systemd manager configuration to recognize Jenkins service.

---

## 🔹 Step 10: Enable Jenkins service

```bash
sudo systemctl enable jenkins
```

**Purpose**: Enable Jenkins to start automatically on system boot.

---

## 🔹 Step 11: Start Jenkins service

```bash
sudo systemctl start jenkins
```

**Purpose**: Start the Jenkins service.

**Startup Time**: May take 30-60 seconds for initial startup.

---

## 🔹 Step 12: Check Jenkins service status

```bash
sudo systemctl status jenkins
```

**Purpose**: Verify Jenkins service is running successfully.

**Expected Output**:
```
● jenkins.service - Jenkins Continuous Integration Server
   Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled)
   Active: active (running) since [timestamp]
   Main PID: 1234 (java)
```

**Success Indicators**:
- ✅ Active: **active (running)**
- ✅ Loaded: **enabled**

---

## 🔹 Step 13: Verify Jenkins port

```bash
ss -tlnp | grep 8080
```

**Purpose**: Confirm Jenkins is listening on default port 8080.

**Expected Output**:
```
LISTEN    0    50    *:8080    *:*    users:(("java",pid=1234,fd=123))
```

---

## 🔹 Step 14: Retrieve initial admin password

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Purpose**: Get the auto-generated initial administrator password.

**Expected Output**: A long alphanumeric string (password)

**Important**: Copy this password - you'll need it for web UI setup.

---

## 🔹 Step 15: Access Jenkins Web UI

**Action**: Click the **"Jenkins"** button on the top bar of the lab interface.

**URL**: Typically `http://jenkins:8080` or `http://<jenkins-ip>:8080`

**Purpose**: Access Jenkins web interface to complete setup wizard.

---

## 🔹 Step 16: Unlock Jenkins

**On the Jenkins web UI:**

1. **Getting Started page** appears
2. **Paste the initial admin password** from Step 14
3. Click **"Continue"** button

**Purpose**: Authenticate initial access to Jenkins.

---

## 🔹 Step 17: Install suggested plugins

**On the Customize Jenkins page:**

1. Click **"Install suggested plugins"**
2. Wait for plugin installation to complete
3. Monitor installation progress

**Plugins Installed**:
- Git plugin
- Pipeline plugins
- Credentials plugin
- SSH plugins
- And many more essential plugins

**Installation Time**: 5-10 minutes depending on network speed.

---

## 🔹 Step 18: Create admin user

**On the Create First Admin User page:**

**Fill in the following details:**
- **Username**: `theadmin`
- **Password**: `Adm!n321`
- **Confirm password**: `Adm!n321`
- **Full name**: `Javed`
- **E-mail address**: `javed@jenkins.stratos.xfusioncorp.com`

**Action**: Click **"Save and Continue"** button

**Purpose**: Create the Jenkins administrator account with specified credentials.

---

## 🔹 Step 19: Configure Jenkins URL

**On the Instance Configuration page:**

- **Jenkins URL**: Keep the default or adjust if needed
- Typically shows: `http://jenkins:8080/` or `http://<server-ip>:8080/`

**Action**: Click **"Save and Finish"**

**Purpose**: Set the base URL for Jenkins instance.

---

## 🔹 Step 20: Complete setup

**On the Jenkins is ready page:**

**Action**: Click **"Start using Jenkins"**

**Purpose**: Finalize setup and access Jenkins dashboard.

**Success**: You'll be redirected to Jenkins main dashboard.

---

## 🔹 Step 21: Verify admin user login

**Test the admin credentials:**

1. **Logout** from Jenkins (if logged in)
2. **Login** with credentials:
   - Username: `theadmin`
   - Password: `Adm!n321`

**Purpose**: Confirm admin user is properly configured and functional.

---

## 📋 Quick Command Reference

**Jenkins Installation Commands:**

```bash
# Connect to Jenkins server
ssh root@jenkins
# Password: S3curePass

# Install dependencies
yum install wget -y
yum install fontconfig java-21-openjdk -y

# Add Jenkins repository
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Install Jenkins
yum upgrade -y
yum install jenkins -y

# Start Jenkins service
systemctl daemon-reload
systemctl enable jenkins
systemctl start jenkins
systemctl status jenkins

# Get initial admin password
cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Web UI Configuration:**
1. Access Jenkins UI via browser
2. Paste initial admin password
3. Install suggested plugins
4. Create admin user with specified credentials
5. Complete instance configuration

---

## 💡 Additional Tips

- **Firewall**: Ensure port 8080 is open if firewall is active
- **Memory**: Jenkins requires minimum 2GB RAM for optimal performance
- **Backup**: Regularly backup `/var/lib/jenkins` directory
- **Plugins**: Keep plugins updated for security and features
- **Security**: Change admin password periodically

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Jenkins service fails to start**
**Symptoms**: Service status shows "failed"
**Solution**: Check logs and verify Java installation
```bash
journalctl -xeu jenkins.service
java -version
systemctl restart jenkins
```

### **Issue 2: Port 8080 already in use**
**Symptoms**: Jenkins cannot bind to port 8080
**Solution**: Change Jenkins port or stop conflicting service
```bash
ss -tlnp | grep :8080
# Edit /etc/sysconfig/jenkins or /usr/lib/systemd/system/jenkins.service
```

### **Issue 3: Cannot access web UI**
**Symptoms**: Browser cannot connect to Jenkins
**Solution**: Check firewall and service status
```bash
systemctl status jenkins
firewall-cmd --list-ports
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload
```

### **Issue 4: Initial password file not found**
**Symptoms**: Cannot find initialAdminPassword
**Solution**: Wait for Jenkins to fully start, check file permissions
```bash
systemctl status jenkins
ls -la /var/lib/jenkins/secrets/
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **complete Jenkins installation and configuration** including system dependencies, repository setup, service management, and web UI-based admin user creation with specific credentials.

**💡 Solution Approach:**

1. **Dependency Management**: Installed Java 21 and required utilities before Jenkins installation
2. **Repository Configuration**: Added Jenkins stable repository and imported GPG key for package verification
3. **Package Installation**: Used yum to install Jenkins following official documentation
4. **Service Management**: Properly configured systemd service with enable and start commands
5. **Initial Password Retrieval**: Located and copied initial admin password from secrets directory
6. **Web UI Setup**: Completed setup wizard through browser interface
7. **Plugin Installation**: Selected suggested plugins for standard Jenkins functionality
8. **Admin User Creation**: Configured admin user with exact specified credentials

**🎯 Key Success Factors:**
- **Java installation** ensuring Java 21 OpenJDK installed before Jenkins
- **Repository setup** proper GPG key import for package authenticity
- **Service configuration** using systemctl for proper service management
- **Password retrieval** accessing initialAdminPassword at correct location
- **Credential accuracy** using exact username, password, name, and email as specified
- **Plugin selection** installing suggested plugins for complete functionality

**⚠️ Critical Configuration Details:**
- **Java requirement** Jenkins requires Java runtime (Java 11 or 21)
- **Repository URL** must use stable Jenkins repository for production
- **Service enablement** ensures Jenkins starts on system boot
- **Initial password** located at `/var/lib/jenkins/secrets/initialAdminPassword`
- **Credential requirements** must match exact specifications for admin user

**🔒 Jenkins Installation Benefits:**
- **CI/CD platform** enables automated build, test, and deployment pipelines
- **Plugin ecosystem** extensive plugins for various tools and integrations
- **User management** role-based access control for team collaboration
- **Pipeline as code** Jenkinsfile support for version-controlled pipelines

**⚠️ Important Jenkins Concepts**:
- **Master/Agent architecture** distributed build system capability
- **Job configuration** various job types for different automation needs
- **Security realm** authentication and authorization configuration
- **Plugin management** extending Jenkins functionality through plugins

---

## ⚠️ Important Production Notes

🔧 **CI/CD Foundation**: Jenkins installation provides complete continuous integration and delivery platform

🔐 **Security Configuration**: Admin user properly configured with strong password and accurate contact information

📊 **Service Management**: Jenkins service enabled for automatic startup and managed through systemd

🛡️ **Initial Setup**: Suggested plugins provide comprehensive baseline functionality for most use cases