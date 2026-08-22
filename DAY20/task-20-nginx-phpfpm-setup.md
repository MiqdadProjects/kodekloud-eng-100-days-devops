**🌟 Task 20 - Configure Nginx + PHP-FPM 8.3 with Unix Socket**

**📌 Task Description**

The **Nautilus application development team** is planning to launch a new **PHP-based application**, which they want to deploy on **Nautilus infra** in **Stratos DC**. The development team had a meeting to discuss the application deployment architecture. They decided to use **Nginx web server** with **PHP-FPM** to host the application. The Nginx server runs on port **8099** and uses **Unix socket** to communicate with PHP-FPM.

**Requirements:**
a. Install **nginx** on **app server 3**, configure it to use **port 8099** and its document root should be **`/var/www/html`**
b. Install **php-fpm version 8.3** on **app server 3**, it must use the unix socket **`/var/run/php-fpm/default.sock`** (create the parent directories if don't exist)
c. Configure **php-fpm and nginx** to work together
d. Once configured correctly, you can test the website using **`curl http://stapp03:8099/index.php`** command from jump host

👉 **Your task:** Set up a complete Nginx + PHP-FPM web server stack with custom port and Unix socket configuration.

💡 **Note:** We have copied two files, `index.php` and `info.php`, under `/var/www/html` as part of the PHP-based application setup. Please do not modify these files.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
sudo su -
```

**Purpose**: Connect to App Server 3 as the banner user and switch to root for administrative tasks.

---

## 🔹 Step 2: Install Nginx web server

```bash
yum install nginx -y
```

**Purpose**: Install Nginx web server package on App Server 3.

**Package Installation**: This installs Nginx with default configuration that we'll customize.

---

## 🔹 Step 3: Configure Nginx for port 8099 with proper routing

```bash
vi /etc/nginx/nginx.conf
```

**Purpose**: Edit the main Nginx configuration file to set custom port, document root, and proper PHP routing.

**Configuration Changes Required:**

**Find the server block and replace it with this complete configuration:**

```nginx
server {
    listen       8099;
    listen       [::]:8099;
    server_name  _;
    root         /var/www/html;
    index        index.php index.html;

    # Root location with try_files for proper routing
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # PHP file processing with FastCGI
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }
}
```

**⚠️ CRITICAL FIXES APPLIED:**

1. **Added `location /` block with `try_files`**: This ensures all requests are properly routed to existing files before falling back to `/index.php`. This solves the 404 issue reported in the bug report.
2. **Removed any `include /etc/nginx/default.d/*.conf;`**: This can cause conflicting location directives.
3. **Proper PHP location block**: Handles PHP file execution with correct FastCGI parameters.

**Key Configuration Points:**
- `try_files $uri $uri/ /index.php?$query_string;` - Attempts to serve file, then directory, then index.php
- `fastcgi_pass unix:/var/run/php-fpm/default.sock;` - Routes PHP requests to socket
- `$document_root$fastcgi_script_name` - Proper path resolution for PHP files
- `include fastcgi_params;` - Includes standard FastCGI parameters

**Save and exit**: Press `Esc`, type `:wq`, press `Enter`

---

## 🔹 Step 4: Start and verify Nginx service

```bash
systemctl start nginx
systemctl enable nginx
systemctl status nginx
```

**Purpose**: Start Nginx service and verify it's running on the correct port.

**Verify port binding**:
```bash
ss -tulnp | grep :8099
```

**Expected Output**:
```
tcp    LISTEN  0    128    *:8099    *:*    users:(("nginx",pid=1234,fd=6))
```

---

## 🔹 Step 5: Check OS version for PHP installation

```bash
cat /etc/os-release
```

**Purpose**: Verify the operating system version to determine the correct PHP installation method.

**Expected**: CentOS Stream 9 or similar RHEL-based distribution.

---

## 🔹 Step 6: Update system and install repositories

```bash
sudo dnf update -y
sudo dnf install -y epel-release
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
```

**Purpose**: Update the system and install EPEL and Remi repositories for PHP 8.3 installation.

**Repository Details**:
- **EPEL**: Extra packages for Enterprise Linux
- **Remi**: Repository containing newer PHP versions

---

## 🔹 Step 7: Configure PHP module for version 8.3

```bash
sudo dnf module reset php -y
sudo dnf module enable php:remi-8.3 -y
```

**Purpose**: Reset any existing PHP module configuration and enable PHP 8.3 from the Remi repository.

**Module Management**: This ensures we get PHP 8.3 specifically, not the default version.

---

## 🔹 Step 8: Install PHP-FPM and extensions

```bash
sudo dnf install -y php-fpm php-cli php-mysqlnd php-zip php-devel php-gd php-mbstring php-curl php-xml php-bcmath php-json
```

**Purpose**: Install PHP-FPM and commonly required PHP extensions.

**Extensions Installed**:
- **php-fpm**: FastCGI Process Manager
- **php-cli**: Command line interface
- **php-mysqlnd**: MySQL database support
- **php-gd, php-mbstring, etc.**: Common web development extensions

---

## 🔹 Step 9: Verify PHP installation

```bash
php -v
```

**Purpose**: Confirm PHP 8.3 is installed correctly.

**Expected Output**:
```
PHP 8.3.x (cli) (built: ...)
Copyright (c) The PHP Group
Zend Engine v4.3.x
```

---

## 🔹 Step 10: Configure PHP-FPM pool

```bash
vi /etc/php-fpm.d/www.conf
```

**Purpose**: Configure PHP-FPM to use Unix socket and run under nginx user.

**Configuration Changes Required:**

**Find and modify these lines:**

```ini
; Change user and group to nginx
user = nginx
group = nginx

; Change socket path to the required location
listen = /var/run/php-fpm/default.sock

; Set socket ownership and permissions
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

**Critical Settings**:
- **user/group**: Must be `nginx` for proper permissions
- **listen**: Unix socket path as specified in requirements
- **Socket permissions**: Ensure nginx can read/write to socket

---

## 🔹 Step 11: Create socket directory if needed

```bash
mkdir -p /var/run/php-fpm
chown nginx:nginx /var/run/php-fpm
chmod 755 /var/run/php-fpm
```

**Purpose**: Ensure the directory for the Unix socket exists with proper permissions.

---

## 🔹 Step 12: Validate Nginx configuration

```bash
nginx -t
```

**Purpose**: Test Nginx configuration syntax before restarting services.

**Expected Output**:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

**If errors found**: Review and fix configuration syntax before proceeding.

---

## 🔹 Step 13: Start PHP-FPM service

```bash
systemctl start php-fpm
systemctl enable php-fpm
systemctl status php-fpm
```

**Purpose**: Start PHP-FPM service and verify it's running correctly.

**Expected Status**: Active (running) with no errors.

---

## 🔹 Step 14: Restart Nginx with new configuration

```bash
systemctl restart nginx
systemctl status nginx
```

**Purpose**: Apply the new Nginx configuration and verify the service is running.

---

## 🔹 Step 15: Verify Unix socket creation

```bash
ls -l /var/run/php-fpm/
```

**Purpose**: Confirm that the Unix socket was created with proper permissions.

**Expected Output**:
```
srw-rw---- 1 nginx nginx 0 Mar 15 10:30 default.sock
```

**Socket Properties**:
- **Type**: Socket file (starts with 's')
- **Owner**: nginx:nginx
- **Permissions**: rw-rw---- (660)

---

## 🔹 Step 16: Set proper socket permissions (if needed)

```bash
chown nginx:nginx /var/run/php-fpm/default.sock
chmod 660 /var/run/php-fpm/default.sock
```

**Purpose**: Ensure the socket has correct ownership and permissions for nginx access.

---

## 🔹 Step 17: Test from Jump Host

**From Jump Host:**

```bash
curl http://stapp03:8099/index.php
```

**Purpose**: Test the PHP application through the web server to verify complete functionality.

**Expected Output**:
```
Welcome to xFusionCorp Industries!
```

**Success Indicator**: This confirms that Nginx and PHP-FPM are working together correctly.

---

## 🔹 Step 18: Additional verification tests

```bash
# Test info.php file
curl http://stapp03:8099/info.php

# Test direct index.html
curl http://stapp03:8099/index.html

# Test Nginx status
systemctl status nginx

# Test PHP-FPM status  
systemctl status php-fpm

# Check socket file details
ls -la /var/run/php-fpm/default.sock

# Check Nginx error logs if there are issues
tail -f /var/log/nginx/error.log
```

**Purpose**: Perform comprehensive testing to ensure all components are functioning properly.

---

## 🔹 Step 19: Final configuration verification

```bash
# Verify Nginx syntax one more time
nginx -t

# Check listening ports
ss -tulnp | grep :8099

# Verify PHP-FPM socket
ss -xl | grep default.sock

# Check SELinux context (if applicable)
ls -laZ /var/run/php-fpm/default.sock
```

**Purpose**: Final verification before task completion to ensure everything is properly configured.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Install and configure Nginx
sudo su -
yum install nginx -y
vi /etc/nginx/nginx.conf
# (Configure server block with try_files routing and PHP location)
systemctl start nginx && systemctl enable nginx

# Install PHP 8.3 from Remi repository
dnf update -y
dnf install -y epel-release
dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
dnf module reset php -y
dnf module enable php:remi-8.3 -y
dnf install -y php-fpm php-cli php-mysqlnd php-zip php-devel php-gd php-mbstring php-curl php-xml php-bcmath php-json

# Configure PHP-FPM
mkdir -p /var/run/php-fpm
chown nginx:nginx /var/run/php-fpm
vi /etc/php-fpm.d/www.conf
# (Set user=nginx, group=nginx, listen=/var/run/php-fpm/default.sock, listen.owner=nginx, listen.group=nginx, listen.mode=0660)

# Start services and test
systemctl start php-fpm && systemctl enable php-fpm
nginx -t
systemctl restart nginx

# Test from jump host
curl http://stapp03:8099/index.php
```

---

## 💡 Additional Tips

- **Log monitoring**: Check `/var/log/nginx/error.log` and `/var/log/php-fpm/www-error.log` for issues
- **Socket permissions**: Ensure nginx user can read/write to the Unix socket
- **Firewall**: Open port 8099 if firewall is active (`firewall-cmd --permanent --add-port=8099/tcp`)
- **SELinux**: May need to set appropriate contexts for the socket file: `semanage fcontext -a -t httpd_t "/var/run/php-fpm/default.sock"`
- **Performance**: Adjust PHP-FPM pool settings for production workloads
- **Request routing**: The `try_files` directive ensures requests are properly routed

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: 404 Not Found error (MAIN BUG)**
**Symptoms**: Nginx returns 404 error when accessing existing PHP files
**Root Cause**: Missing `location /` block with `try_files` directive causing requests not to reach PHP files
**Solution**: Add proper routing with `try_files` in location / block
```bash
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```
This is the critical fix from the bug report!

### **Issue 2: 502 Bad Gateway error**
**Symptoms**: Nginx returns 502 error when accessing PHP files
**Solution**: Check PHP-FPM service status and socket permissions
```bash
systemctl status php-fpm
ls -la /var/run/php-fpm/default.sock
chown nginx:nginx /var/run/php-fpm/default.sock
chmod 660 /var/run/php-fpm/default.sock
```

### **Issue 3: Socket file not created**
**Symptoms**: Unix socket doesn't exist after starting PHP-FPM
**Solution**: Check PHP-FPM configuration and create directory
```bash
mkdir -p /var/run/php-fpm
chown nginx:nginx /var/run/php-fpm
chmod 755 /var/run/php-fpm
systemctl restart php-fpm
ls -l /var/run/php-fpm/
```

### **Issue 4: Permission denied errors**
**Symptoms**: Nginx can't connect to PHP-FPM socket
**Solution**: Fix socket ownership and permissions
```bash
chown nginx:nginx /var/run/php-fpm/default.sock
chmod 660 /var/run/php-fpm/default.sock
```

### **Issue 5: PHP files download instead of executing**
**Symptoms**: PHP files are downloaded rather than executed
**Solution**: Check Nginx location block for PHP processing and verify it comes after root location block
```bash
nginx -t
# Verify location ~ \.php$ block exists and is correct
# Verify location / block has try_files directive
```

### **Issue 6: Conflicting configuration files**
**Symptoms**: Nginx serving wrong content or 404 errors
**Solution**: Remove or comment out conflicting include directives
```bash
vi /etc/nginx/nginx.conf
# Comment out or remove: include /etc/nginx/default.d/*.conf;
nginx -t
systemctl restart nginx
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **configuring Nginx and PHP-FPM to communicate via Unix socket** while ensuring proper request routing to prevent 404 errors. Additionally, conflicting configuration includes could introduce location directive conflicts.

**Root Cause of 404 Errors (Issue #4):**
1. Missing `location /` block with `try_files` directive
2. Requests not being routed to existing files before attempting PHP fallback
3. Possible conflicting `location /` directives from included configuration files
4. PHP-FPM requests not reaching the PHP location block properly

**💡 Solution Approach:**

1. **Added Proper Request Routing**: Implemented `location /` block with `try_files $uri $uri/ /index.php?$query_string;` to properly route all requests to existing files or fallback to index.php
2. **Removed Conflicting Configuration**: Ensured no conflicting `include /etc/nginx/default.d/*.conf;` directives that could introduce competing location blocks
3. **Custom Port Configuration**: Modified Nginx to listen on port 8099 instead of default port 80 with document root set to `/var/www/html`
4. **PHP 8.3 Installation**: Used Remi repository to install specific PHP-FPM 8.3 version on CentOS Stream 9 system
5. **Unix Socket Configuration**: Configured PHP-FPM to use Unix socket `/var/run/php-fpm/default.sock` instead of TCP connection
6. **User Context Alignment**: Set both Nginx and PHP-FPM to run under `nginx` user for proper permission handling
7. **Service Integration**: Configured Nginx FastCGI to communicate with PHP-FPM through the Unix socket
8. **Permission Management**: Ensured proper socket file ownership and permissions for seamless communication

**🎯 Key Success Factors:**
- ✅ **Proper routing with try_files** - Ensures all requests are handled correctly
- ✅ **Repository management** using dnf module system to get PHP 8.3 from Remi repository
- ✅ **Socket path configuration** creating the required directory structure `/var/run/php-fpm/`
- ✅ **User alignment** ensuring both services run under the same user (nginx) for permission consistency
- ✅ **FastCGI configuration** proper Nginx location block for PHP file processing
- ✅ **Service coordination** starting services in correct order and verifying socket creation
- ✅ **End-to-end testing** using curl from jump host to validate complete functionality

**⚠️ Critical Configuration Details:**
- ✅ **Location / block with try_files** - CRITICAL FIX for 404 issue
- **PHP-FPM pool configuration** must specify `listen = /var/run/php-fpm/default.sock`
- **Nginx FastCGI pass** must point to `unix:/var/run/php-fpm/default.sock`
- **Socket permissions** require nginx:nginx ownership with 660 permissions
- **Service user alignment** both nginx and php-fpm must run under nginx user
- **Directory creation** `/var/run/php-fpm/` directory must exist before socket creation

**🔒 Performance Benefits:**
- **Unix socket communication** provides faster inter-process communication than TCP
- **Reduced network overhead** eliminates TCP/IP stack for local communication
- **Better security** socket files provide better access control than network ports
- **Resource efficiency** lower memory and CPU usage compared to TCP connections

---

## ⚠️ Important Production Notes

🔧 **Web Server Stack**: Complete Nginx + PHP-FPM 8.3 configuration with Unix socket communication

🔧 **Request Routing**: Proper `try_files` configuration ensures all requests are handled correctly and prevents 404 errors

🔐 **Security Considerations**: Proper user contexts and socket permissions for secure operation

📊 **Performance Monitoring**: Monitor PHP-FPM pool processes and Nginx connection handling

🛡️ **Service Management**: Both services configured for automatic startup and proper dependency handling

---

## ✅ Task Completion Checklist

- [ ] Connected to App Server 3
- [ ] Installed Nginx web server
- [ ] **Configured Nginx with location / block and try_files directive** (CRITICAL)
- [ ] Configured Nginx to listen on port 8099
- [ ] Set document root to `/var/www/html`
- [ ] Verified Nginx is running and listening on port 8099
- [ ] Checked OS version for PHP installation
- [ ] Installed EPEL and Remi repositories
- [ ] Enabled PHP 8.3 module
- [ ] Installed PHP-FPM 8.3 and extensions
- [ ] Verified PHP 8.3 installation
- [ ] Created `/var/run/php-fpm` directory
- [ ] Configured PHP-FPM pool with Unix socket
- [ ] Set socket ownership and permissions (nginx:nginx, mode 660)
- [ ] Verified Nginx configuration syntax
- [ ] Started PHP-FPM service
- [ ] Restarted Nginx with new configuration
- [ ] Verified Unix socket creation
- [ ] Tested from jump host: `curl http://stapp03:8099/index.php`
- [ ] Verified output: "Welcome to xFusionCorp Industries!"
- [ ] Tested info.php: `curl http://stapp03:8099/info.php`
- [ ] Checked both Nginx and PHP-FPM service status
- [ ] Verified socket file permissions and ownership

**🎉 Success Criteria Met When:**
- Nginx listens on port 8099
- PHP files are executed (not downloaded or return 404)
- Unix socket `/var/run/php-fpm/default.sock` exists with proper permissions
- curl from jump host returns expected PHP output
- Both Nginx and PHP-FPM services are running
- No 404 errors when accessing index.php or info.php
- Proper routing with try_files ensures all requests are handled correctly
