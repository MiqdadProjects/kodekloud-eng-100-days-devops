**🌟 Task 45 - Fix Dockerfile Build Issues on App Server 2**

**📌 Task Description**

A member of the **Nautilus DevOps team** encountered errors while building a Docker image on **App Server 2**. The Dockerfile is located under **`/opt/docker`**. The objective is to **fix the issues** without changing the base image or modifying any data files such as `index.html`, then successfully build the image.

👉 **Your task:** Troubleshoot and fix Dockerfile errors to enable successful image building.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
sudo su -
```

**Purpose**: Connect to Application Server 2 as steve user and switch to root for Docker operations.

---

## 🔹 Step 2: Navigate to Dockerfile directory

```bash
cd /opt/docker
```

**Purpose**: Navigate to the directory containing the problematic Dockerfile.

**Verify location**:
```bash
pwd
ls -la
```

---

## 🔹 Step 3: Examine the original Dockerfile

```bash
cat Dockerfile
```

**Purpose**: Review the existing Dockerfile to identify potential issues.

**Common Dockerfile Issues**:
- Incorrect instruction syntax
- Missing line continuations (\)
- Incorrect file paths in COPY commands
- Syntax errors in RUN commands
- Missing dependencies or packages

---

## 🔹 Step 4: Check directory structure

```bash
tree /opt/docker
# or
ls -R /opt/docker
```

**Purpose**: Verify all referenced files and directories exist.

**Expected Structure**:
```
/opt/docker/
├── Dockerfile
├── certs/
│   ├── server.crt
│   └── server.key
└── html/
    └── index.html
```

---

## 🔹 Step 5: Verify certificate and HTML files exist

```bash
ls -la certs/
ls -la html/
```

**Purpose**: Confirm all files referenced in COPY commands are present.

**Expected Files**:
```
certs/server.crt
certs/server.key
html/index.html
```

---

## 🔹 Step 6: Backup original Dockerfile

```bash
cp Dockerfile Dockerfile.backup
```

**Purpose**: Create backup before making changes for safety.

---

## 🔹 Step 7: Create corrected Dockerfile

```bash
vi Dockerfile
```

**Purpose**: Edit Dockerfile with corrected syntax and configuration.

---

## 🔹 Step 8: Add corrected Dockerfile content

**Corrected Dockerfile:**

```dockerfile
FROM httpd:2.4.43

# Configure Apache to listen on port 8080
RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

# Enable SSL module
RUN sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

# Enable socache_shmcb module (required for SSL)
RUN sed -i '/LoadModule socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

# Include SSL configuration
RUN sed -i '/Include conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

# Copy SSL certificates
COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key

# Copy website content
COPY html/index.html /usr/local/apache2/htdocs/
```

**Key Fixes**:
- **Escaped forward slashes** in sed commands (`\/` instead of `/`)
- **Proper sed syntax** for uncommenting lines
- **Correct COPY paths** matching actual directory structure
- **Clear comments** documenting each step

**Save and exit**: Press `Esc`, type `:wq`, press `Enter`

---

## 🔹 Step 9: Verify Dockerfile syntax

```bash
cat Dockerfile
```

**Purpose**: Review the corrected Dockerfile to ensure all changes are proper.

**Validation Checklist**:
- ✅ FROM instruction present and correct
- ✅ Escaped characters in sed commands
- ✅ COPY paths match directory structure
- ✅ No syntax errors in RUN commands

---

## 🔹 Step 10: Attempt Docker build

```bash
docker build -t httpd-ssl:latest .
```

**Purpose**: Build Docker image using corrected Dockerfile.

**Expected Build Process**:
```
[+] Building 25.3s (10/10) FINISHED
 => [internal] load build definition from Dockerfile
 => [internal] load .dockerignore
 => [internal] load metadata for docker.io/library/httpd:2.4.43
 => [1/6] FROM docker.io/library/httpd:2.4.43
 => [internal] load build context
 => [2/6] RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
 => [3/6] RUN sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
 => [4/6] RUN sed -i '/LoadModule socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
 => [5/6] RUN sed -i '/Include conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf
 => [6/6] COPY certs/server.crt /usr/local/apache2/conf/server.crt
 => [7/6] COPY certs/server.key /usr/local/apache2/conf/server.key
 => [8/6] COPY html/index.html /usr/local/apache2/htdocs/
 => exporting to image
 => => naming to docker.io/library/httpd-ssl:latest
```

---

## 🔹 Step 11: Verify successful build

```bash
docker images httpd-ssl
```

**Purpose**: Confirm the image was created successfully.

**Expected Output**:
```
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
httpd-ssl    latest    abc123def456   30 seconds ago   145MB
```

**Success Indicators**:
- ✅ Image appears in list
- ✅ Tag is "latest"
- ✅ Recent creation timestamp
- ✅ Reasonable size (~145MB)

---

## 🔹 Step 12: Inspect the built image

```bash
docker inspect httpd-ssl:latest
```

**Purpose**: View detailed image metadata and layer information.

**Key Information**:
- Build date
- Layer count
- Configuration
- Environment variables

---

## 🔹 Step 13: Test run the container (optional)

```bash
docker run -d --name test-httpd -p 8080:8080 httpd-ssl:latest
```

**Purpose**: Test that the built image runs successfully.

**Verification**:
```bash
docker ps
curl http://localhost:8080
```

---

## 🔹 Step 14: Check container logs (if testing)

```bash
docker logs test-httpd
```

**Purpose**: Verify Apache started correctly with SSL configuration.

**Expected Logs**:
```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name...
[date] [ssl:warn] [pid 1] AH01909: server certificate does NOT include an ID which matches the server name
[date] [mpm_event:notice] [pid 1] AH00489: Apache/2.4.43 (Unix) OpenSSL/x.x.x configured
```

---

## 🔹 Step 15: Clean up test container (if created)

```bash
docker stop test-httpd
docker rm test-httpd
```

**Purpose**: Remove test container after verification.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect and navigate
ssh steve@stapp02
sudo su -
cd /opt/docker

# Backup and create corrected Dockerfile
cp Dockerfile Dockerfile.backup
cat > Dockerfile << 'EOF'
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
RUN sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
RUN sed -i '/LoadModule socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
RUN sed -i '/Include conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key
COPY html/index.html /usr/local/apache2/htdocs/
EOF

# Build and verify
docker build -t httpd-ssl:latest .
docker images httpd-ssl
```

---

## 💡 Common Dockerfile Issues & Fixes

### **Issue 1: Unescaped special characters in sed**
**Problem**: Forward slashes in sed patterns need escaping
**Fix**: Use `\/` instead of `/` in file paths
```dockerfile
# Wrong
RUN sed -i '/LoadModule ssl_module modules/mod_ssl.so/s/^#//g'

# Correct
RUN sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g'
```

### **Issue 2: Missing COPY source files**
**Problem**: Files referenced in COPY don't exist
**Fix**: Verify files exist in build context
```bash
ls -la certs/server.crt
ls -la html/index.html
```

### **Issue 3: Incorrect base image version**
**Problem**: Base image tag doesn't exist
**Fix**: Verify image exists on Docker Hub
```bash
docker pull httpd:2.4.43
```

### **Issue 4: Line continuation errors**
**Problem**: Missing backslash for multi-line commands
**Fix**: Add `\` at end of lines or use separate RUN commands

---

## 🔧 Troubleshooting Build Failures

### **Build Error: COPY failed**
**Symptoms**: "COPY failed: stat ... no such file or directory"
**Solution**: Verify source file paths relative to build context
```bash
ls -la certs/
ls -la html/
```

### **Build Error: sed command failed**
**Symptoms**: "sed: can't read ... No such file or directory"
**Solution**: Check file paths in sed commands are correct
```bash
docker run --rm httpd:2.4.43 ls /usr/local/apache2/conf/
```

### **Build Error: Syntax error**
**Symptoms**: "Dockerfile parse error" or "unknown instruction"
**Solution**: Check Dockerfile syntax, proper instruction format
```bash
docker build --no-cache -t httpd-ssl:latest .
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **identifying and fixing Dockerfile syntax errors** that prevented successful image building, particularly related to sed command escaping and proper instruction syntax.

**💡 Solution Approach:**

1. **Problem Analysis**: Examined original Dockerfile to identify syntax errors and issues
2. **Directory Structure Verification**: Confirmed all referenced files exist in correct locations
3. **Sed Command Fixes**: Corrected sed commands by properly escaping forward slashes in file paths
4. **COPY Path Validation**: Verified COPY source paths match actual directory structure
5. **Build Context Understanding**: Ensured all relative paths correct from build context perspective
6. **Syntax Validation**: Reviewed Docker build output to identify and fix remaining issues
7. **Successful Build**: Achieved clean build with corrected Dockerfile

**🎯 Key Success Factors:**
- **Character escaping** properly escaping `/` as `\/` in sed patterns
- **Path accuracy** verifying COPY source paths exist in build context
- **Base image integrity** not modifying specified httpd:2.4.43 base image
- **Data preservation** not altering index.html or certificate files
- **Build validation** confirming successful image creation through docker images
- **Systematic troubleshooting** identifying and fixing issues methodically

**⚠️ Critical Dockerfile Fixes**:
- **Sed escape sequences** forward slashes in paths must be escaped: `modules\/mod_ssl.so`
- **Pattern matching** sed patterns for uncommenting lines: `'/pattern/s/^#//g'`
- **COPY paths** must be relative to build context (current directory)
- **Multi-layer approach** separate RUN commands for clear layer separation
- **Apache configuration** proper SSL module enabling and configuration inclusion

**🔒 Dockerfile Best Practices Applied:**
- **Clear comments** documenting purpose of each instruction
- **Layer optimization** grouping related commands when appropriate
- **Build context awareness** understanding relative path resolution
- **Error handling** proper syntax to avoid build failures

**⚠️ Important Troubleshooting Concepts**:
- **Build context** directory sent to Docker daemon for build
- **Layer caching** Docker caches layers to speed up builds
- **Syntax validation** Docker validates Dockerfile before building
- **Error messages** build errors provide specific line numbers and issues

---

## ⚠️ Important Production Notes

🔧 **Dockerfile Debugging**: Systematic approach to identifying and fixing build errors is essential

🔐 **Syntax Accuracy**: Special characters in shell commands within Dockerfiles require proper escaping

📊 **Build Context**: Understanding relative paths and file availability in build context prevents COPY failures

🛡️ **Testing Strategy**: Building with `--no-cache` flag helps identify persistent issues vs cached results.