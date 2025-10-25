**🌟 Task 38 - Pull and Retag Busybox Image on App Server 3**

**📌 Task Description**

The **Nautilus project developers** want to test containerized application features. As part of this, they need to pull the **`busybox:musl`** image on **Application Server 3** in **Stratos DC** and create a new tag **`busybox:media`** for the same image.

👉 **Your task:** Pull a specific Docker image from Docker Hub and create an alternative tag for local reference.

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

**Purpose**: Ensure Docker daemon is running before performing image operations.

**Expected Output**:
```
● docker.service - Docker Application Container Engine
   Active: active (running)
```

**If not running**:
```bash
sudo systemctl start docker
```

---

## 🔹 Step 3: Check existing Docker images (optional)

```bash
docker images
```

**Purpose**: View currently available Docker images on the system before pulling new ones.

**Initial State**: May be empty or contain previously downloaded images.

---

## 🔹 Step 4: Pull the busybox:musl image

```bash
docker pull busybox:musl
```

**Purpose**: Download the busybox image with musl tag from Docker Hub registry.

**Expected Output**:
```
musl: Pulling from library/busybox
5cc84ad355aa: Pull complete
Digest: sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Status: Downloaded newer image for busybox:musl
docker.io/library/busybox:musl
```

**Pull Process**:
- Connects to Docker Hub
- Downloads image layers
- Verifies layer integrity
- Stores image locally

---

## 🔹 Step 5: Verify image was pulled successfully

```bash
docker images busybox
```

**Purpose**: Confirm the busybox:musl image is now available locally.

**Expected Output**:
```
REPOSITORY   TAG    IMAGE ID       CREATED         SIZE
busybox      musl   44f1048931f5   12 months ago   1.46MB
```

**Image Details**:
- **Repository**: busybox
- **Tag**: musl
- **Image ID**: Unique identifier
- **Size**: Approximately 1.46MB

---

## 🔹 Step 6: Create new tag for the image

```bash
docker tag busybox:musl busybox:media
```

**Purpose**: Create an additional tag pointing to the same image for local reference purposes.

**Tag Operation**:
- Creates new reference to existing image
- Does not duplicate image data
- Same Image ID for both tags
- No network operation required

---

## 🔹 Step 7: Verify both tags exist

```bash
docker images busybox
```

**Purpose**: Confirm both tags (musl and media) are now available for the same image.

**Expected Output**:
```
REPOSITORY   TAG     IMAGE ID       CREATED         SIZE
busybox      media   44f1048931f5   12 months ago   1.46MB
busybox      musl    44f1048931f5   12 months ago   1.46MB
```

**Key Observation**: 
- ✅ Both tags share the **same Image ID** (44f1048931f5)
- ✅ No additional disk space used
- ✅ Both tags point to identical image data

---

## 🔹 Step 8: Inspect the image details

```bash
docker inspect busybox:media
```

**Purpose**: View detailed information about the tagged image configuration and layers.

**Useful Information**:
- Container configuration
- Environment variables
- Entry point and command
- Layer information
- Creation timestamp

---

## 🔹 Step 9: Test the image functionality

```bash
docker run --rm busybox:media echo "Testing media tag"
```

**Purpose**: Verify the newly tagged image works correctly by running a test container.

**Expected Output**:
```
Testing media tag
```

**Test Explanation**:
- `--rm`: Automatically remove container after exit
- `echo`: Simple command to verify functionality
- Container exits after executing command

---

## 🔹 Step 10: Compare both tags

```bash
docker run --rm busybox:musl echo "Testing musl tag"
docker run --rm busybox:media echo "Testing media tag"
```

**Purpose**: Demonstrate that both tags reference the same functional image.

**Expected Behavior**: Both commands produce identical results as they use the same image.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to server
ssh banner@stapp03
sudo su -

# Verify Docker and pull image
systemctl status docker
docker pull busybox:musl

# Create new tag
docker tag busybox:musl busybox:media

# Verify both tags
docker images busybox

# Test functionality (optional)
docker run --rm busybox:media echo "Test successful"
```

---

## 💡 Additional Tips

- **Image tags**: Tags are just labels pointing to image IDs
- **No duplication**: Retagging doesn't consume additional storage
- **Multiple tags**: Single image can have unlimited tags
- **Local operation**: Tagging is local and doesn't affect Docker Hub
- **Naming convention**: Use descriptive tags for different purposes

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Docker pull fails**
**Symptoms**: Cannot connect to Docker registry or timeout errors
**Solution**: Check network connectivity and Docker daemon status
```bash
systemctl status docker
docker info
ping registry-1.docker.io
```

### **Issue 2: Permission denied errors**
**Symptoms**: Regular user cannot run Docker commands
**Solution**: Use sudo or add user to docker group
```bash
sudo docker pull busybox:musl
# or
sudo usermod -aG docker banner
```

### **Issue 3: Disk space issues**
**Symptoms**: No space left on device during pull
**Solution**: Clean up unused images and containers
```bash
docker system df
docker image prune
docker system prune
```

### **Issue 4: Tag already exists**
**Symptoms**: Warning about overwriting existing tag
**Solution**: This is normal behavior; tag will point to new image
```bash
# Force tag creation
docker tag -f busybox:musl busybox:media
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **pulling a specific Docker image variant and creating an alternative local tag** for team reference purposes while understanding Docker's image tagging system and layer sharing.

**💡 Solution Approach:**

1. **Service Verification**: Confirmed Docker daemon was running and ready for image operations
2. **Image Pull Operation**: Downloaded busybox:musl image from Docker Hub registry using docker pull command
3. **Tag Creation**: Used docker tag command to create alternative busybox:media reference pointing to same image
4. **Verification Process**: Confirmed both tags exist and share identical Image ID
5. **Functionality Testing**: Validated that newly tagged image works correctly by running test containers
6. **Image Inspection**: Verified image details to understand configuration and layer structure

**🎯 Key Success Factors:**
- **Specific variant selection** pulling exact busybox:musl variant as specified
- **Tagging understanding** recognizing tags as references, not duplicates
- **Image ID awareness** understanding both tags point to same underlying image
- **No storage overhead** confirming retagging doesn't consume additional disk space
- **Local operation** recognizing tagging is local and doesn't affect remote registry
- **Testing procedures** validating functionality with both original and new tags

**⚠️ Critical Configuration Details:**
- **Image specification** must include both repository and tag (busybox:musl)
- **Tag syntax** uses format `docker tag source:tag destination:tag`
- **Image ID consistency** both tags must show identical Image ID after tagging
- **Registry behavior** pull operation downloads from Docker Hub by default
- **Local scope** tags are local references and not pushed to registry automatically

**🔒 Docker Image Management Benefits:**
- **Flexible naming** allows different tags for same image for different purposes
- **Storage efficiency** multiple tags share same image layers
- **Version management** enables semantic versioning and release naming
- **Team collaboration** provides descriptive references for shared images

---

## ⚠️ Important Production Notes

🔧 **Image Management**: Multiple tags enable flexible image organization without storage duplication

🔐 **Local References**: Tags provide meaningful names for team collaboration and deployment workflows

📊 **Storage Efficiency**: Image tagging doesn't consume additional disk space as tags share underlying layers

🛡️ **Registry Operations**: Pull operations download from Docker Hub while tags remain local until explicitly pushed