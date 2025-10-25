**🌟 Task 42 - Create Docker macvlan Network on App Server 2**

**📌 Task Description**

The **Nautilus DevOps team** needs a custom Docker network setup for **application server 2** in **Stratos DC**. The requirements are:

a. Create a Docker network named **`official`**
b. Use the **`macvlan`** driver for the network
c. Configure the network with **`subnet`** and **`ip-range`** of **`192.168.30.0/24`**
d. Set a gateway IP **`192.168.30.1`**

👉 **Your task:** Create a custom Docker network using macvlan driver with specific network configuration.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Connect to Application Server 2 as steve user for Docker network operations.

---

## 🔹 Step 2: Verify Docker service is running

```bash
sudo systemctl status docker
```

**Purpose**: Ensure Docker daemon is active before performing network operations.

**Expected Output**:
```
● docker.service - Docker Application Container Engine
   Active: active (running)
```

---

## 🔹 Step 3: Check existing Docker networks

```bash
sudo docker network ls
```

**Purpose**: View currently configured Docker networks before creating new one.

**Expected Output Example**:
```
NETWORK ID     NAME      DRIVER    SCOPE
abc123def456   bridge    bridge    local
def456abc789   host      host      local
789abc123def   none      null      local
```

**Default Networks**:
- **bridge**: Default network for containers
- **host**: Uses host's network stack
- **none**: No network connectivity

---

## 🔹 Step 4: Understand macvlan network driver

**Macvlan Driver Characteristics**:
- **Direct Layer 2 access**: Containers get MAC addresses
- **Network visibility**: Containers appear as physical devices on network
- **IP allocation**: Containers receive IPs from subnet range
- **Performance**: Lower latency than bridge networks
- **Use cases**: Legacy applications, network monitoring

---

## 🔹 Step 5: Create the macvlan network

```bash
sudo docker network create -d macvlan \
  --subnet=192.168.30.0/24 \
  --ip-range=192.168.30.0/24 \
  --gateway=192.168.30.1 \
  official
```

**Purpose**: Create Docker network with macvlan driver and specified network configuration.

**Command Options**:
- `-d macvlan`: Use macvlan driver
- `--subnet`: Network address range
- `--ip-range`: Available IP allocation range
- `--gateway`: Default gateway for network
- `official`: Network name

**Expected Output**:
```
abc123def456789012345678901234567890abcdef123456789012345678901234
```

**Output Explanation**: 64-character hash is the network ID.

---

## 🔹 Step 6: Verify network creation

```bash
sudo docker network ls
```

**Purpose**: Confirm the 'official' network appears in Docker network list.

**Expected Output**:
```
NETWORK ID     NAME       DRIVER    SCOPE
abc123def456   bridge     bridge    local
def456abc789   host       host      local
789abc123def   none       null      local
012abc345def   official   macvlan   local
```

**Verification Points**:
- ✅ Network name: **official**
- ✅ Driver: **macvlan**
- ✅ Scope: **local**

---

## 🔹 Step 7: Inspect network details

```bash
sudo docker network inspect official
```

**Purpose**: View detailed configuration of the newly created macvlan network.

**Expected Output**:
```json
[
    {
        "Name": "official",
        "Id": "abc123def456...",
        "Created": "2024-03-15T10:30:00.123456789Z",
        "Scope": "local",
        "Driver": "macvlan",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.30.0/24",
                    "IPRange": "192.168.30.0/24",
                    "Gateway": "192.168.30.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

**Key Configuration Points**:
- **Subnet**: 192.168.30.0/24
- **IPRange**: 192.168.30.0/24
- **Gateway**: 192.168.30.1
- **Driver**: macvlan

---

## 🔹 Step 8: Verify IPAM configuration

```bash
sudo docker network inspect official --format='{{json .IPAM.Config}}'
```

**Purpose**: Extract and display IP Address Management (IPAM) configuration specifically.

**Expected Output**:
```json
[{"Subnet":"192.168.30.0/24","IPRange":"192.168.30.0/24","Gateway":"192.168.30.1"}]
```

---

## 🔹 Step 9: Test network with container (optional)

```bash
sudo docker run --rm --network=official --ip=192.168.30.10 alpine ip addr show
```

**Purpose**: Test the macvlan network by running a container connected to it.

**Command Breakdown**:
- `--network=official`: Connect to macvlan network
- `--ip=192.168.30.10`: Assign specific IP from range
- `alpine`: Lightweight test image
- `ip addr show`: Display container network configuration

---

## 🔹 Step 10: Check network connectivity (optional)

```bash
sudo docker run --rm --network=official --ip=192.168.30.11 alpine ping -c 3 192.168.30.1
```

**Purpose**: Test connectivity to the gateway from a container on the macvlan network.

**Expected Behavior**: Successful ping responses if gateway is reachable.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to server
ssh steve@stapp02

# Verify Docker is running
sudo systemctl status docker

# Create macvlan network
sudo docker network create -d macvlan \
  --subnet=192.168.30.0/24 \
  --ip-range=192.168.30.0/24 \
  --gateway=192.168.30.1 \
  official

# Verify network creation
sudo docker network ls
sudo docker network inspect official
```

---

## 💡 Additional Tips

- **Parent interface**: Add `--parent=eth0` to specify host network interface
- **VLAN tagging**: Use `--parent=eth0.100` for VLAN-tagged interfaces
- **IPv6 support**: Add `--ipv6` flag for dual-stack configuration
- **Custom IPAM**: Use `--aux-address` for reserving specific IPs
- **Network removal**: Use `docker network rm official` to delete network

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Network already exists**
**Symptoms**: Error "network with name official already exists"
**Solution**: Remove existing network or use different name
```bash
sudo docker network ls
sudo docker network rm official
sudo docker network create -d macvlan --subnet=192.168.30.0/24 --gateway=192.168.30.1 official
```

### **Issue 2: Parent interface not specified**
**Symptoms**: Warning about missing parent interface
**Solution**: Specify parent network interface explicitly
```bash
sudo docker network create -d macvlan \
  --subnet=192.168.30.0/24 \
  --gateway=192.168.30.1 \
  --parent=eth0 \
  official
```

### **Issue 3: IP range conflicts**
**Symptoms**: Cannot allocate IPs or network conflicts
**Solution**: Verify subnet doesn't conflict with existing networks
```bash
ip addr show
sudo docker network inspect bridge
```

### **Issue 4: Container cannot reach gateway**
**Symptoms**: No connectivity from macvlan containers
**Solution**: Check host firewall and network configuration
```bash
sudo iptables -L
sudo ip route show
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **creating a Docker macvlan network with specific network parameters** including subnet, IP range, and gateway configuration for advanced container networking scenarios.

**💡 Solution Approach:**

1. **Network Driver Selection**: Used macvlan driver to provide containers with direct Layer 2 network access
2. **Subnet Configuration**: Specified 192.168.30.0/24 subnet for network address space
3. **IP Range Allocation**: Defined same IP range for container IP assignment
4. **Gateway Configuration**: Set 192.168.30.1 as default gateway for network traffic
5. **Network Naming**: Created network with name "official" for easy identification
6. **Verification Process**: Confirmed network creation and inspected detailed configuration

**🎯 Key Success Factors:**
- **Driver specification** using `-d macvlan` for advanced networking capabilities
- **Network parameters** properly configuring subnet, IP range, and gateway
- **CIDR notation** using /24 notation for subnet mask (255.255.255.0)
- **Gateway IP** setting appropriate gateway within subnet range
- **Network inspection** validating configuration through docker network inspect
- **IPAM verification** confirming IP Address Management settings

**⚠️ Critical Configuration Details:**
- **Subnet specification** must use CIDR notation (192.168.30.0/24)
- **IP range** typically matches subnet but can be subset for IP allocation control
- **Gateway IP** must be within subnet range and typically host's interface IP
- **Macvlan driver** provides Layer 2 access but requires compatible network infrastructure
- **Parent interface** optional but recommended for explicit interface binding

**🔒 Macvlan Network Benefits:**
- **Direct network access** containers appear as physical devices on network
- **Performance** lower latency compared to bridge networking
- **Legacy compatibility** supports applications requiring Layer 2 access
- **IP allocation** containers receive real IPs from network subnet

**⚠️ Important Macvlan Considerations**:
- **Host communication** macvlan containers cannot communicate with host by default
- **Network requirements** requires promiscuous mode on parent interface
- **Subnet planning** must not conflict with existing network ranges
- **IP management** careful planning needed to avoid IP conflicts

---

## ⚠️ Important Production Notes

🔧 **Advanced Networking**: Macvlan provides Layer 2 network access for containers requiring direct network visibility

🔐 **Network Isolation**: Containers on macvlan network appear as separate devices on physical network

📊 **IP Management**: Careful subnet and IP range planning required to avoid conflicts with existing networks

🛡️ **Use Cases**: Ideal for legacy applications, network monitoring tools, and scenarios requiring direct Layer 2 access