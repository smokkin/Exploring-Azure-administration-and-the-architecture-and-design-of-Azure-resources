# 🚀 Azure VM Web Hosting - Complete Implementation Guide

> A comprehensive guide to deploying, configuring, and securing Azure virtual machines as web servers, with multiple implementation scenarios and production-ready architectures.

---

## 📋 Table of Contents

1. [Overview & Core Concepts](#1-overview--core-concepts-)
2. [Basic Implementation (Original Lab)](#2-basic-implementation-original-lab-)
3. [Enhanced Single VM Deployment](#3-enhanced-single-vm-deployment-)
4. [Scalable Multi-VM Architecture](#4-scalable-multi-vm-architecture-)
5. [High Availability Deployment](#5-high-availability-deployment-)
6. [Container-Based Web Hosting](#6-container-based-web-hosting-)
7. [Security Hardening Guide](#7-security-hardening-guide-)
8. [Monitoring & Maintenance](#8-monitoring--maintenance-)
9. [Troubleshooting Guide](#9-troubleshooting-guide-)
10. [Cost Optimization](#10-cost-optimization-)

---

## 1. Overview & Core Concepts 🎯

### What You'll Learn

This guide transforms a basic Azure VM lab into **production-ready deployment patterns** used by enterprises worldwide. You'll master:

- ✅ Infrastructure as Code (IaC) deployment
- ✅ Network security configuration
- ✅ Automated software provisioning
- ✅ Load balancing and scaling
- ✅ Security hardening
- ✅ Monitoring and alerting

### 💡 Core Concepts Explained

| Concept | Simple Analogy | Technical Definition |
|---------|---------------|---------------------|
| **Resource Group** | A folder containing all project files | Logical container that holds related Azure resources |
| **Virtual Machine** | A computer running in the cloud | Emulated computer system with CPU, memory, storage |
| **Network Security Group (NSG)** | A building security guard | Filters network traffic based on rules (IP, port, protocol) |
| **Custom Script Extension** | Automatic installer that runs when computer starts | Azure feature that downloads and executes scripts on VMs |
| **Public IP** | Your home street address | Internet-routable address assigned to Azure resources |
| **Load Balancer** | A traffic director at a concert | Distributes incoming traffic across multiple servers |

### Architecture Patterns Overview

```
Pattern 1: Basic Single VM          Pattern 2: Scalable Multi-VM
┌──────────────┐                    ┌──────────────┐
│   Internet   │                    │   Internet   │
└──────┬───────┘                    └──────┬───────┘
       │                                   │
       ▼                                   ▼
┌──────────────┐                    ┌──────────────┐
│  Public IP   │                    │ Load Balancer│
└──────┬───────┘                    └──────┬───────┘
       │                                   │
       ▼                                   ▼
┌──────────────┐                    ┌──────────────┐
│  NSG (22,80) │                    │  VM Scale Set│
└──────┬───────┘                    │  (2+ VMs)    │
       │                           └──────────────┘
       ▼                                   │
┌──────────────┐                          │
│ Ubuntu +     │                    ┌─────┴─────┐
│ Nginx        │                    ▼           ▼
└──────────────┐              ┌────────┐  ┌────────┐
               │              │  VM 1  │  │  VM 2  │
               │              │ +Nginx │  │ +Nginx │
               │              └────────┘  └────────┘
               │
Pattern 3: High Availability
┌─────────────────────────────────────────┐
│              Azure Traffic Manager       │
│         (Global Load Balancing)          │
└───────────────────┬─────────────────────┘
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
┌──────────────┐        ┌──────────────┐
│  Region 1    │        │  Region 2    │
│ ┌──────────┐ │        │ ┌──────────┐ │
│ │Load Bal│ │        │ │Load Bal│ │
│ └────┬─────┘ │        │ └────┬─────┘ │
│      │       │        │      │       │
│  ┌───┴───┐   │        │  ┌───┴───┐   │
│  ▼       ▼   │        │  ▼       ▼   │
│ ┌────┐ ┌────┐│        │ ┌────┐ ┌────┐│
│ │VM1 │ │VM2 ││        │ │VM3 │ │VM4 ││
│ └────┘ └────┘│        │ └────┘ └────┘│
└──────────────┘        └──────────────┘
```

---

## 2. Basic Implementation (Original Lab) 🎓

### Scenario: Learning Environment
**Use Case:** First-time Azure user learning VM deployment and basic networking
**Duration:** 20 minutes
**Cost:** ~$0.10/hour (when running)

---

### Step-by-Step Implementation

#### Phase 1: Environment Setup (2 minutes)

**Prerequisites:**
- [ ] Azure subscription (free tier works)
- [ ] Access to Azure Cloud Shell or local Azure CLI
- [ ] Basic familiarity with command line

**Action Steps:**

1. **Access Azure Portal**
   - Navigate to [portal.azure.com](https://portal.azure.com)
   - Sign in with your Azure account
   - Verify subscription is active

2. **Launch Cloud Shell**
   - Click the Cloud Shell icon (top navigation bar)
   - Select **Bash** environment (not PowerShell)
   - Wait for storage account creation (first time only)

**Why Cloud Shell?**
- Pre-authenticated (no login commands needed)
- Pre-installed Azure CLI
- Persistent storage across sessions
- Access from anywhere

---

#### Phase 2: Resource Group Creation (1 minute)

**What is a Resource Group?**
Think of it as a **project folder** that contains all related Azure resources. It enables:
- **Lifecycle management:** Delete everything at once
- **Access control:** Apply permissions to entire project
- **Cost tracking:** View all costs for the project
- **Organization:** Group related resources logically

**Command:**
```bash
# Create resource group in Central US region
az group create --name IntroAzureRG --location centralus
```

**Expected Output:**
```json
{
  "id": "/subscriptions/xxxx/resourceGroups/IntroAzureRG",
  "location": "centralus",
  "managedBy": null,
  "name": "IntroAzureRG",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```

**Verification:**
```bash
# Confirm resource group exists
az group show --name IntroAzureRG --output table
```

**Region Selection Guide:**

| Region | Best For | Latency to |
|--------|----------|------------|
| `centralus` | US Central, general purpose | Chicago area |
| `eastus` | US East Coast | New York area |
| `westus2` | US West Coast | San Francisco area |
| `westeurope` | Europe | Amsterdam |
| `southeastasia` | Asia Pacific | Singapore |

---

#### Phase 3: Virtual Machine Deployment (5 minutes)

**VM Configuration Explained:**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `--resource-group` | IntroAzureRG | Places VM in our project folder |
| `--name` | my-vm | Unique identifier for the VM |
| `--size` | Standard_D2s_v5 | 2 vCPUs, 8GB RAM, premium SSD |
| `--public-ip-sku` | Standard | Static public IP (doesn't change) |
| `--image` | Ubuntu2204 | Operating system (Ubuntu 22.04 LTS) |
| `--admin-username` | azureuser | Login username for SSH |
| `--generate-ssh-keys` | Auto | Creates secure SSH key pair |

**Deployment Command:**
```bash
az vm create   --resource-group "IntroAzureRG"   --name my-vm   --size Standard_D2s_v5   --public-ip-sku Standard   --image Ubuntu2204   --admin-username azureuser   --generate-ssh-keys
```

**What Happens During Deployment:**

1. **Resource Provisioning (2-3 min):**
   - Azure allocates physical hardware
   - Creates virtual network interface
   - Assigns public IP address
   - Sets up OS disk (30GB default)

2. **OS Installation (1-2 min):**
   - Ubuntu 22.04 image copied to disk
   - Initial configuration applied
   - SSH keys injected for authentication

3. **Boot Process (30 sec):**
   - VM starts up
   - Network configuration applied
   - Extensions prepared for execution

**Expected Output:**
```json
{
  "fqdns": "",
  "id": "/subscriptions/xxxx/resourceGroups/IntroAzureRG/providers/Microsoft.Compute/virtualMachines/my-vm",
  "location": "centralus",
  "macAddress": "00-0D-3A-xx-xx-xx",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "20.231.78.145",
  "resourceGroup": "IntroAzureRG",
  "zones": ""
}
```

**Save the Public IP Address!** You'll need it later.

---

#### Phase 4: Web Server Installation (3 minutes)

**What is Custom Script Extension?**
It's an **automation agent** that runs on your VM after deployment. Think of it as:
- A chef that prepares your restaurant before opening
- A setup wizard that runs automatically
- A DevOps engineer executing your installation script

**Architecture:**
```
Azure Portal/CLI → Azure API → VM Agent → Downloads Script → Executes Script → Reports Status
```

**The Nginx Installation Script:**
```bash
#!/bin/bash
# configure-nginx.sh

# Step 1: Update package lists
apt-get update -y

# Step 2: Install Nginx web server
apt-get install -y nginx

# Step 3: Configure custom home page
HOSTNAME=$(hostname)
echo "<html><body><h2>Welcome to Azure! My name is ${HOSTNAME}.</h2></body></html>" > /var/www/html/index.html

# Step 4: Ensure Nginx starts on boot
systemctl enable nginx
systemctl restart nginx
```

**Deployment Command:**
```bash
az vm extension set   --resource-group "IntroAzureRG"   --vm-name my-vm   --name customScript   --publisher Microsoft.Azure.Extensions   --version 2.1   --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}'   --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
```

**Parameter Breakdown:**

| Parameter | Purpose |
|-----------|---------|
| `--settings` | Public configuration (script location) |
| `--protected-settings` | Encrypted configuration (execution command, credentials) |
| `fileUris` | URL where script is hosted |
| `commandToExecute` | Shell command to run after download |

**Monitoring Extension Execution:**
```bash
# Check extension status
az vm extension list   --resource-group IntroAzureRG   --vm-name my-vm   --output table

# View detailed status
az vm get-instance-view   --resource-group IntroAzureRG   --name my-vm   --query "instanceView.extensions"
```

**Verification Steps:**
```bash
# SSH into VM and verify Nginx is running
ssh azureuser@<PUBLIC_IP>
systemctl status nginx

# Check Nginx version
nginx -v

# Verify home page exists
cat /var/www/html/index.html
```

---

#### Phase 5: Network Security Configuration (5 minutes)

**Understanding the Problem:**

By default, Azure VMs are **locked down** for security. Only port 22 (SSH) is open:

```
Current State:
Internet → [NSG: Allow 22 only] → VM
                ↓
            HTTP Request (port 80)
                ↓
            ❌ DENIED (timeout)
```

**Why Port 22 is Open?**
- SSH (Secure Shell) is the management protocol for Linux
- Administrators need remote access to configure the system
- It's the "back door" for system administration

**Why Port 80 is Blocked?**
- Default security posture: "Deny all, allow specific"
- Prevents accidental exposure of services
- Requires explicit configuration to open

---

**Step 1: Retrieve VM IP Address**

```bash
# Store IP address in variable for reuse
IPADDRESS="$(az vm list-ip-addresses   --resource-group "IntroAzureRG"   --name my-vm   --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress"   --output tsv)"

# Display the IP
echo "VM Public IP: $IPADDRESS"
```

**Query Explanation:**
- `az vm list-ip-addresses`: Retrieves IP configuration
- `--query`: JMESPath query to extract specific fields
- `"[].virtualMachine.network.publicIpAddresses[*].ipAddress"`: Navigate JSON structure
- `--output tsv`: Tab-separated values (clean output)

---

**Step 2: Test Current Connectivity (Should Fail)**

```bash
# Attempt HTTP connection with 5-second timeout
curl --connect-timeout 5 http://$IPADDRESS
```

**Expected Result:**
```
curl: (28) Connection timed out after 5001 milliseconds
```

**What This Tells Us:**
- Network path exists (no "host not found" error)
- VM is running (responds to ARP)
- Port 80 is blocked at NSG level
- Connection times out waiting for response

---

**Step 3: Inspect Current NSG Rules**

```bash
# List all NSGs in resource group
az network nsg list   --resource-group "IntroAzureRG"   --query '[].name'   --output table
```

**Output:**
```
my-vmNSG
```

**Azure Naming Convention:**
Azure automatically creates an NSG named `{VM-name}NSG` when you create a VM with a public IP.

---

**Step 4: Examine Existing Rules**

```bash
# List rules in human-readable table format
az network nsg rule list   --resource-group "IntroAzureRG"   --nsg-name my-vmNSG   --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}'   --output table
```

**Output:**
```
Name              Priority    Port    Access
----------------  ----------  ------  --------
default-allow-ssh 1000        22      Allow
```

**Understanding NSG Rule Priority:**

| Priority Range | Usage |
|---------------|-------|
| 100-200 | High priority rules (overrides defaults) |
| 201-500 | Application-specific rules |
| 501-999 | Custom rules |
| 1000+ | Default Azure rules |
| 65000+ | System rules (cannot change) |

**Rule Processing Order:**
1. Check rules from lowest priority number to highest
2. First matching rule wins
3. If no match, default deny rule applies

---

**Step 5: Create HTTP Allow Rule**

```bash
# Create NSG rule to allow HTTP traffic
az network nsg rule create   --resource-group "IntroAzureRG"   --nsg-name my-vmNSG   --name allow-http   --protocol tcp   --priority 100   --destination-port-range 80   --access Allow   --description "Allow inbound HTTP traffic for web server"
```

**Parameter Details:**

| Parameter | Value | Explanation |
|-----------|-------|-------------|
| `--protocol` | tcp | HTTP uses TCP protocol |
| `--priority` | 100 | High priority (evaluated before default rules) |
| `--destination-port-range` | 80 | Standard HTTP port |
| `--access` | Allow | Permit the traffic |
| `--direction` | Inbound | (Default) Allow incoming traffic |
| `--source-address-prefix` | * | (Default) Allow from anywhere |

**Security Note:**
In production, replace `--source-address-prefix *` with specific IP ranges:
```bash
# Example: Allow only from your office IP
--source-address-prefix 203.0.113.0/24
```

---

**Step 6: Verify Rule Creation**

```bash
# Confirm both rules exist
az network nsg rule list   --resource-group "IntroAzureRG"   --nsg-name my-vmNSG   --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}'   --output table
```

**Expected Output:**
```
Name              Priority    Port    Access
----------------  ----------  ------  --------
allow-http        100         80      Allow
default-allow-ssh 1000        22      Allow
```

---

#### Phase 6: Verification & Testing (2 minutes)

**Test 1: Command Line Verification**

```bash
# Test HTTP connectivity
curl --connect-timeout 5 http://$IPADDRESS
```

**Expected Success Output:**
```html
<html><body><h2>Welcome to Azure! My name is my-vm.</h2></body></html>
```

**Test 2: Browser Verification**

1. Open web browser
2. Navigate to `http://<IPADDRESS>`
3. You should see:
   - Welcome message
- VM hostname displayed
- No SSL warning (using HTTP, not HTTPS)

**Test 3: Detailed Response Inspection**

```bash
# View full HTTP response headers
curl -I http://$IPADDRESS
```

**Expected Output:**
```
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 15 Jan 2024 10:30:00 GMT
Content-Type: text/html
Content-Length: 74
Last-Modified: Mon, 15 Jan 2024 10:25:00 GMT
Connection: keep-alive
ETag: "65a5..."
Accept-Ranges: bytes
```

---

#### Phase 7: Cleanup (2 minutes)

**Why Cleanup?**
- VMs incur charges while running (even if idle)
- Resource groups contain multiple billable resources
- Clean environment prevents confusion in future labs

**Cleanup Command:**
```bash
# Delete entire resource group and all resources
az group delete --name IntroAzureRG --yes --no-wait
```

**Parameters:**
- `--yes`: Skip confirmation prompt
- `--no-wait`: Return immediately (deletion happens in background)

**Alternative: Stop VM (Cost Saving Without Deletion)**
```bash
# Stop VM but keep resources (saves compute cost, keeps storage)
az vm stop --resource-group IntroAzureRG --name my-vm

# Deallocate (stop billing for compute)
az vm deallocate --resource-group IntroAzureRG --name my-vm

# Restart later
az vm start --resource-group IntroAzureRG --name my-vm
```

**Cost Comparison:**

| State | Compute Cost | Storage Cost | Total/Hour |
|-------|-------------|--------------|------------|
| Running | ~$0.10 | ~$0.005 | ~$0.105 |
| Stopped | ~$0.05 | ~$0.005 | ~$0.055 |
| Deallocated | $0 | ~$0.005 | ~$0.005 |
| Deleted | $0 | $0 | $0 |

---

## 3. Enhanced Single VM Deployment 🛡️

### Scenario: Production-Ready Single Web Server
**Use Case:** Small business website, development environment, or microservice hosting
**Improvements Over Basic Lab:**
- HTTPS support with SSL certificate
- Automated backups
- Monitoring integration
- Security hardening
- DNS configuration

---

### Architecture

```
Users
  │
  ▼
┌─────────────────────────────────────┐
│         Azure DNS (CNAME)           │
│    www.yourdomain.com → Public IP   │
└─────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────┐
│      Azure DDoS Protection          │
│         (Standard Tier)             │
└─────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────┐
│        Public IP (Static)           │
└─────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────┐
│    Network Security Group (NSG)     │
│  ┌─────────┐ ┌─────────┐           │
│  │Allow 443│ │Allow 80 │           │
│  │  HTTPS  │ │  HTTP   │           │
│  └─────────┘ └─────────┘           │
└─────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────┐
│   Ubuntu 22.04 LTS VM               │
│  ┌─────────────────────────────┐   │
│  │  Nginx (Reverse Proxy)      │   │
│  │  ┌─────────────────────┐   │   │
│  │  │  Let's Encrypt SSL  │   │   │
│  │  │  Auto-renewal         │   │   │
│  │  └─────────────────────┘   │   │
│  └─────────────────────────────┘   │
│  ┌─────────────────────────────┐   │
│  │  Azure Monitor Agent        │   │
│  │  - CPU/Memory metrics       │   │
│  │  - Nginx logs               │   │
│  │  - Disk usage               │   │
│  └─────────────────────────────┘   │
│  ┌─────────────────────────────┐   │
│  │  Backup Vault (Daily)       │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

---

### Implementation Guide

#### Step 1: Enhanced Resource Group with Tags

```bash
# Create resource group with metadata tags for cost tracking
az group create   --name ProductionWebRG   --location eastus   --tags     Environment=Production     Project=WebServer     Owner=DevOps     CostCenter=IT-Infrastructure
```

**Why Tags Matter:**
- **Cost Management:** Filter billing by tag values
- **Resource Organization:** Group resources logically
- **Automation:** Scripts can target resources by tags
- **Compliance:** Track ownership and purpose

---

#### Step 2: Virtual Network with Subnet Segmentation

```bash
# Create virtual network with proper segmentation
az network vnet create   --resource-group ProductionWebRG   --name WebVNet   --address-prefix 10.0.0.0/16   --subnet-name WebSubnet   --subnet-prefix 10.0.1.0/24

# Create dedicated subnet for management (jump box)
az network vnet subnet create   --resource-group ProductionWebRG   --vnet-name WebVNet   --name ManagementSubnet   --address-prefix 10.0.2.0/24
```

**Subnet Strategy:**

| Subnet | Purpose | CIDR | Hosts |
|--------|---------|------|-------|
| WebSubnet | Web servers | 10.0.1.0/24 | 251 |
| ManagementSubnet | Admin access | 10.0.2.0/24 | 251 |
| DatabaseSubnet | Future DB | 10.0.3.0/24 | 251 |

---

#### Step 3: Network Security Group with Least Privilege

```bash
# Create NSG for web tier
az network nsg create   --resource-group ProductionWebRG   --name WebTierNSG   --tags Environment=Production

# Allow HTTPS (443) - Primary access
az network nsg rule create   --resource-group ProductionWebRG   --nsg-name WebTierNSG   --name Allow-HTTPS   --priority 100   --source-address-prefixes Internet   --destination-port-ranges 443   --access Allow   --protocol Tcp   --description "Allow secure web traffic"

# Allow HTTP (80) - For redirect to HTTPS
az network nsg rule create   --resource-group ProductionWebRG   --nsg-name WebTierNSG   --name Allow-HTTP   --priority 110   --source-address-prefixes Internet   --destination-port-ranges 80   --access Allow   --protocol Tcp   --description "Allow web traffic for redirect"

# Allow SSH from specific IP only (replace with your IP)
az network nsg rule create   --resource-group ProductionWebRG   --nsg-name WebTierNSG   --name Allow-SSH-Admin   --priority 120   --source-address-prefixes $(curl -s ifconfig.me)/32   --destination-port-ranges 22   --access Allow   --protocol Tcp   --description "SSH access from admin IP only"

# Deny all other inbound traffic
az network nsg rule create   --resource-group ProductionWebRG   --nsg-name WebTierNSG   --name Deny-All-Inbound   --priority 4000   --source-address-prefixes *   --destination-port-ranges *   --access Deny   --protocol *   --direction Inbound   --description "Explicit deny all inbound"
```

**Security Improvement:**
- SSH restricted to single IP (your admin workstation)
- Explicit deny rule for defense in depth
- HTTPS prioritized over HTTP
- All rules documented with descriptions

---

#### Step 4: VM with Managed Identity and Boot Diagnostics

```bash
# Create VM with enhanced configuration
az vm create   --resource-group ProductionWebRG   --name web-server-01   --size Standard_B2s   --image Ubuntu2204   --vnet-name WebVNet   --subnet WebSubnet   --nsg WebTierNSG   --public-ip-sku Standard   --admin-username azureuser   --generate-ssh-keys   --storage-sku Premium_LRS   --os-disk-size-gb 64   --os-disk-delete-option Delete   --data-disk-sizes-gb 128   --data-disk-delete-option Delete   --enable-agent true   --enable-auto-update true   --patch-mode AutomaticByPlatform   --tags     Environment=Production     Role=WebServer     BackupPolicy=Daily
```

**Enhancement Details:**

| Feature | Benefit |
|---------|---------|
| `--storage-sku Premium_LRS` | SSD storage for better I/O |
| `--os-disk-size-gb 64` | Larger OS disk for logs/updates |
| `--data-disk-sizes-gb 128` | Separate data disk for web content |
| `--enable-auto-update` | Automatic security patches |
| `--patch-mode AutomaticByPlatform` | Azure manages reboots |
| `--tags` | Metadata for management |

---

#### Step 5: Advanced Custom Script for Complete Setup

Create file `advanced-setup.sh`:

```bash
#!/bin/bash
set -e  # Exit on error

LOG_FILE="/var/log/vm-setup.log"
exec > >(tee -a "$LOG_FILE")
exec 2>&1

echo "=== Starting VM Setup: $(date) ==="

# Update system
echo "Updating package lists..."
apt-get update -y
apt-get upgrade -y

# Install essential packages
echo "Installing packages..."
apt-get install -y     nginx     certbot     python3-certbot-nginx     fail2ban     ufw     htop     net-tools     unzip     curl     git

# Configure UFW firewall
echo "Configuring UFW..."
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 'Nginx Full'
ufw --force enable

# Configure Fail2ban for intrusion prevention
echo "Configuring Fail2ban..."
cat > /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
port = http,https
logpath = /var/log/nginx/error.log
EOF

systemctl restart fail2ban

# Configure Nginx with security headers
echo "Configuring Nginx..."
cat > /etc/nginx/sites-available/default << 'EOF'
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm;

    server_name _;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    location / {
        try_files $uri $uri/ =404;
    }

    # Hide Nginx version
    server_tokens off;

    # Limit request size
    client_max_body_size 1m;

    # Enable gzip compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript application/rss+xml application/atom+xml image/svg+xml;
}
EOF

# Create custom home page
echo "Creating home page..."
HOSTNAME=$(hostname)
PUBLIC_IP=$(curl -s ifconfig.me)
Uptime=$(uptime -p)

cat > /var/www/html/index.html << EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to Azure VM</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        .container {
            background: rgba(255, 255, 255, 0.1);
            padding: 30px;
            border-radius: 10px;
            backdrop-filter: blur(10px);
        }
        h1 { margin-top: 0; }
        .info-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 20px;
        }
        .info-item {
            background: rgba(255, 255, 255, 0.2);
            padding: 15px;
            border-radius: 5px;
        }
        .label { font-weight: bold; opacity: 0.8; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Welcome to Azure!</h1>
        <p>Your web server is running successfully with enhanced security configuration.</p>

        <div class="info-grid">
            <div class="info-item">
                <div class="label">Hostname</div>
                <div>$HOSTNAME</div>
            </div>
            <div class="info-item">
                <div class="label">Public IP</div>
                <div>$PUBLIC_IP</div>
            </div>
            <div class="info-item">
                <div class="label">Uptime</div>
                <div>$Uptime</div>
            </div>
            <div class="info-item">
                <div class="label">OS</div>
                <div>Ubuntu 22.04 LTS</div>
            </div>
        </div>

        <p style="margin-top: 30px; font-size: 0.9em; opacity: 0.8;">
            Secured with: UFW Firewall | Fail2ban | Security Headers | Auto-updates
        </p>
    </div>
</body>
</html>
EOF

# Set proper permissions
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html

# Restart Nginx
echo "Starting Nginx..."
systemctl restart nginx
systemctl enable nginx

# Setup log rotation for custom logs
cat > /etc/logrotate.d/custom-vm-logs << 'EOF'
/var/log/vm-setup.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 644 root root
}
EOF

echo "=== Setup Complete: $(date) ==="
echo "VM is ready for production use!"
```

**Deploy the script:**
```bash
# Upload script to Azure storage or GitHub, then:
az vm extension set   --resource-group ProductionWebRG   --vm-name web-server-01   --name customScript   --publisher Microsoft.Azure.Extensions   --version 2.1   --settings '{"fileUris":["https://your-storage.blob.core.windows.net/scripts/advanced-setup.sh"]}'   --protected-settings '{"commandToExecute": "chmod +x advanced-setup.sh && ./advanced-setup.sh"}'
```

---

#### Step 6: Configure SSL Certificate with Let's Encrypt

```bash
# SSH into VM
ssh azureuser@$(az vm list-ip-addresses -g ProductionWebRG -n web-server-01 --query '[0].virtualMachine.network.publicIpAddresses[0].ipAddress' -o tsv)

# Install SSL certificate (run on VM)
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com --non-interactive --agree-tos --email admin@yourdomain.com

# Setup auto-renewal (usually automatic, but verify)
sudo certbot renew --dry-run

# Add cron job for renewal check
(crontab -l 2>/dev/null; echo "0 3 * * * /usr/bin/certbot renew --quiet") | crontab -
```

---

#### Step 7: Configure Azure Monitor

```bash
# Enable Azure Monitor for VMs
az vm extension set   --resource-group ProductionWebRG   --vm-name web-server-01   --name AzureMonitorLinuxAgent   --publisher Microsoft.Azure.Monitor   --version 1.0

# Create Log Analytics workspace
az monitor log-analytics workspace create   --resource-group ProductionWebRG   --name WebServerWorkspace   --location eastus   --sku PerGB2018

# Enable VM insights
az vm monitor log-analytics workspace enable   --resource-group ProductionWebRG   --workspace WebServerWorkspace   --name web-server-01
```

---

## 4. Scalable Multi-VM Architecture 📈

### Scenario: Handling Traffic Growth
**Use Case:** E-commerce site, popular blog, or SaaS application expecting variable traffic
**Key Components:**
- Load balancer for traffic distribution
- VM Scale Set for auto-scaling
- Shared storage for content
- Database backend (Azure SQL or MySQL)

---

### Architecture

```
                                    Users
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Azure Traffic Manager                         │
│              (Global DNS Load Balancing)                         │
│         Routes users to nearest healthy region                   │
└─────────────────────────────────────────────────────────────────┘
                                      │
            ┌─────────────────────────┴─────────────────────────┐
            │                                                   │
            ▼                                                   ▼
┌─────────────────────────────┐                   ┌─────────────────────────────┐
│      Region: East US         │                   │      Region: West US         │
│                             │                   │                             │
│  ┌───────────────────────┐  │                   │  ┌───────────────────────┐  │
│  │   Azure Load Balancer  │  │                   │  │   Azure Load Balancer  │  │
│  │   (Layer 4/7)          │  │                   │  │   (Layer 4/7)          │  │
│  └───────────┬───────────┘  │                   │  └───────────┬───────────┘  │
│              │               │                   │              │               │
│  ┌───────────┴───────────┐  │                   │  ┌───────────┴───────────┐  │
│  │   VM Scale Set        │  │                   │  │   VM Scale Set        │  │
│  │   (2-10 instances)    │  │                   │  │   (2-10 instances)    │  │
│  │                       │  │                   │  │                       │  │
│  │  ┌─────┐ ┌─────┐     │  │                   │  │  ┌─────┐ ┌─────┐     │  │
│  │  │VM-1 │ │VM-2 │ ... │  │                   │  │  │VM-1 │ │VM-2 │ ... │  │
│  │  │+Nginx│ │+Nginx│    │  │                   │  │  │+Nginx│ │+Nginx│    │  │
│  │  └──┬──┘ └──┬──┘     │  │                   │  │  └──┬──┘ └──┬──┘     │  │
│  │     │       │        │  │                   │  │     │       │        │  │
│  │     └───────┴──┐     │  │                   │  │     └───────┴──┐     │  │
│  │                │     │  │                   │  │                │     │  │
│  │  ┌─────────────▼─────┐│  │                   │  │  ┌─────────────▼─────┐│  │
│  │  │  Azure Files       ││  │                   │  │  │  Azure Files       ││  │
│  │  │  (Shared Storage)  ││  │                   │  │  │  (Shared Storage)  ││  │
│  │  │  /var/www/html    ││  │                   │  │  │  /var/www/html    ││  │
│  │  └───────────────────┘│  │                   │  │  └───────────────────┘│  │
│  └─────────────────────────┘  │                   │  └─────────────────────────┘  │
│                               │                   │                               │
│  ┌─────────────────────────┐  │                   │  ┌─────────────────────────┐  │
│  │  Azure Database for      │  │                   │  │  Azure Database for      │  │
│  │  MySQL / PostgreSQL      │  │                   │  │  MySQL / PostgreSQL      │  │
│  │  (Data Persistence)       │  │                   │  │  (Data Persistence)       │  │
│  └─────────────────────────┘  │                   │  └─────────────────────────┘  │
└───────────────────────────────┘                   └───────────────────────────────┘
```

---

### Implementation Guide

#### Step 1: Create Shared Resources

```bash
# Resource group
az group create --name ScalableWebRG --location eastus

# Virtual Network
az network vnet create   --resource-group ScalableWebRG   --name ScaleVNet   --address-prefix 10.0.0.0/16   --subnet-name WebSubnet   --subnet-prefix 10.0.1.0/24

# Azure Files for shared content
az storage account create   --resource-group ScalableWebRG   --name webcontentstorage   --location eastus   --sku Standard_LRS   --enable-large-file-share

# Create file share
az storage share create   --account-name webcontentstorage   --name webcontent   --quota 100

# Get storage key
STORAGE_KEY=$(az storage account keys list   --resource-group ScalableWebRG   --account-name webcontentstorage   --query '[0].value' -o tsv)
```

---

#### Step 2: Create Load Balancer

```bash
# Public IP for load balancer
az network public-ip create   --resource-group ScalableWebRG   --name WebLBPublicIP   --sku Standard   --allocation-method Static

# Load balancer
az network lb create   --resource-group ScalableWebRG   --name WebLoadBalancer   --sku Standard   --public-ip-address WebLBPublicIP   --frontend-ip-name WebFrontEnd   --backend-pool-name WebBackEndPool

# Health probe
az network lb probe create   --resource-group ScalableWebRG   --lb-name WebLoadBalancer   --name HealthProbe   --protocol http   --port 80   --path /

# Load balancing rule
az network lb rule create   --resource-group ScalableWebRG   --lb-name WebLoadBalancer   --name HTTPRule   --protocol tcp   --frontend-port 80   --backend-port 80   --frontend-ip-name WebFrontEnd   --backend-pool-name WebBackEndPool   --probe-name HealthProbe
```

---

#### Step 3: Create VM Scale Set

```bash
# Cloud-init script for scale set instances
cat > cloud-init.txt << 'EOF'
#cloud-config
package_upgrade: true
packages:
  - nginx
  - cifs-utils

runcmd:
  # Mount Azure Files
  - mkdir -p /var/www/html
  - mount -t cifs //webcontentstorage.file.core.windows.net/webcontent /var/www/html -o vers=3.0,username=webcontentstorage,password=$STORAGE_KEY,dir_mode=0777,file_mode=0777,serverino

  # Add to fstab for persistence
  - echo "//webcontentstorage.file.core.windows.net/webcontent /var/www/html cifs nofail,vers=3.0,username=webcontentstorage,password=$STORAGE_KEY,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab

  # Configure Nginx
  - systemctl start nginx
  - systemctl enable nginx
EOF

# Create scale set
az vmss create   --resource-group ScalableWebRG   --name WebScaleSet   --image Ubuntu2204   --upgrade-policy-mode automatic   --admin-username azureuser   --generate-ssh-keys   --vnet-name ScaleVNet   --subnet WebSubnet   --lb WebLoadBalancer   --backend-pool-name WebBackEndPool   --instance-count 2   --vm-sku Standard_B2s   --custom-data cloud-init.txt
```

---

#### Step 4: Configure Auto-Scaling

```bash
# Auto-scale rule: Scale out when CPU > 70% for 5 minutes
az monitor autoscale create   --resource-group ScalableWebRG   --name WebAutoScale   --resource WebScaleSet   --resource-type Microsoft.Compute/virtualMachineScaleSets   --min-count 2   --max-count 10   --count 2

# Scale out rule
az monitor autoscale rule create   --resource-group ScalableWebRG   --autoscale-name WebAutoScale   --condition "Percentage CPU > 70 avg 5m"   --scale out 2

# Scale in rule
az monitor autoscale rule create   --resource-group ScalableWebRG   --autoscale-name WebAutoScale   --condition "Percentage CPU < 30 avg 10m"   --scale in 1
```

---

## 5. High Availability Deployment 🌐

### Scenario: Mission-Critical Application
**Use Case:** Financial services, healthcare systems, e-commerce during sales events
**Requirements:**
- 99.99% uptime SLA
- Automatic failover
- Data redundancy
- Global distribution

---

### Multi-Region Architecture

```bash
# Create primary region
az group create --name WebPrimaryRG --location eastus

# Create secondary region (paired region)
az group create --name WebSecondaryRG --location westus2

# Deploy identical infrastructure in both regions
# (Use ARM templates or Terraform for consistency)

# Configure Traffic Manager for global load balancing
az network traffic-manager profile create   --resource-group WebPrimaryRG   --name WebTrafficManager   --routing-method Performance   --unique-dns-name mywebapp-global

# Add endpoints for both regions
az network traffic-manager endpoint create   --resource-group WebPrimaryRG   --profile-name WebTrafficManager   --name PrimaryEndpoint   --type azureEndpoints   --target-resource-id $(az network public-ip show -g WebPrimaryRG -n WebLBPublicIP --query id -o tsv)   --endpoint-status Enabled

az network traffic-manager endpoint create   --resource-group WebPrimaryRG   --profile-name WebTrafficManager   --name SecondaryEndpoint   --type azureEndpoints   --target-resource-id $(az network public-ip show -g WebSecondaryRG -n WebLBPublicIP --query id -o tsv)   --endpoint-status Enabled
```

---

## 6. Container-Based Web Hosting 🐳

### Modern Alternative: Web Server in Containers

**Benefits Over VMs:**
- Faster startup (seconds vs minutes)
- Better resource utilization
- Easier updates (image-based)
- Consistent environments

**Implementation with Azure Container Instances:**

```bash
# Quick container deployment (good for dev/test)
az container create   --resource-group ContainerWebRG   --name web-container   --image nginx:latest   --cpu 1   --memory 1   --ports 80   --ip-address Public   --dns-name-label mywebcontainer   --os-type Linux

# For production: Use Azure Kubernetes Service (AKS)
az aks create   --resource-group ContainerWebRG   --name WebCluster   --node-count 3   --enable-addons http_application_routing   --generate-ssh-keys
```

---

## 7. Security Hardening Guide 🔒

### Production Security Checklist

| Layer | Control | Implementation |
|-------|---------|----------------|
| **Network** | DDoS Protection | Enable Azure DDoS Standard |
| **Network** | Firewall | Azure Firewall or NSG rules |
| **Network** | WAF | Application Gateway with WAF |
| **VM** | OS Hardening | CIS benchmarks, minimal packages |
| **VM** | Antimalware | Microsoft Antimalware extension |
| **VM** | Disk Encryption | Azure Disk Encryption (ADE) |
| **VM** | Patch Management | Automatic OS updates |
| **Application** | SSL/TLS | Let's Encrypt or Azure Key Vault |
| **Application** | Secrets Mgmt | Azure Key Vault integration |
| **Monitoring** | Threat Detection | Microsoft Defender for Cloud |
| **Monitoring** | Log Analysis | Azure Sentinel or Log Analytics |

---

## 8. Monitoring & Maintenance 📊

### Key Metrics to Monitor

```bash
# Setup Azure Monitor alerts
az monitor metrics alert create   --resource-group ProductionWebRG   --name HighCPUAlert   --scopes $(az vm show -g ProductionWebRG -n web-server-01 --query id -o tsv)   --condition "avg percentage cpu > 80"   --window-size 5m   --evaluation-frequency 1m   --action $(az monitor action-group create -g ProductionWebRG -n AdminGroup --short-name Admins --query id -o tsv)
```

### Maintenance Tasks

| Frequency | Task | Command/Method |
|-----------|------|----------------|
| **Daily** | Check VM health | Azure Monitor dashboard |
| **Weekly** | Review NSG logs | Azure Network Watcher |
| **Weekly** | Update SSL certificates | Certbot renew --dry-run |
| **Monthly** | OS updates | apt-get upgrade |
| **Monthly** | Security review | Microsoft Defender recommendations |
| **Quarterly** | Access review | Azure AD PIM |
| **Annually** | DR drill | Failover to secondary region |

---

## 9. Troubleshooting Guide 🔧

### Common Issues & Solutions

#### Issue 1: Cannot Connect via SSH
**Symptoms:** `ssh: connect to host port 22: Connection refused`

**Diagnostic Steps:**
```bash
# Check NSG rules
az network nsg rule list -g MyRG --nsg-name MyNSG -o table

# Verify VM is running
az vm get-instance-view -g MyRG -n MyVM --query instanceView.statuses

# Check serial console output
az vm boot-diagnostics get-boot-log -g MyRG -n MyVM

# Reset SSH config (if needed)
az vm user reset-ssh -g MyRG -n MyVM
az vm extension set -g MyRG --vm-name MyVM --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.4 --protected-settings '{"username":"azureuser", "password":"NewPassword123!"}'
```

---

#### Issue 2: Website Not Accessible
**Symptoms:** `curl: (7) Failed to connect`

**Diagnostic Steps:**
```bash
# 1. Verify NSG allows port 80/443
az network nsg rule list -g MyRG --nsg-name MyNSG --query "[?destinationPortRange=='80' || destinationPortRange=='443']"

# 2. Check if Nginx is running (SSH into VM)
systemctl status nginx
sudo netstat -tlnp | grep :80

# 3. Verify firewall on VM
sudo ufw status

# 4. Check application logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/vm-setup.log
```

---

#### Issue 3: High CPU/Memory Usage
**Symptoms:** Slow response, timeouts

**Diagnostic Steps:**
```bash
# SSH into VM and check processes
htop
sudo ps aux --sort=-%cpu | head -10

# Check disk space
df -h

# Check for DDoS attack
sudo netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n | tail
```

---

## 10. Cost Optimization 💰

### Cost-Saving Strategies

| Strategy | Savings | Implementation |
|----------|---------|----------------|
| **Reserved Instances** | Up to 72% | 1-year or 3-year commitment |
| **Spot VMs** | Up to 90% | For fault-tolerant workloads |
| **Auto-shutdown** | 50-70% | Dev/test VMs off-hours |
| **Right-sizing** | 20-40% | Match SKU to actual usage |
| **Managed Disks** | 30% | Standard SSD vs Premium |
| **Azure Hybrid Benefit** | 40% | Bring your own Windows license |

**Auto-shutdown Configuration:**
```bash
# Enable auto-shutdown for dev VMs
az vm auto-shutdown   --resource-group DevRG   --name DevVM   --time 1800   --email "admin@company.com"
```

---

## 📖 Additional Resources

- [Azure VM Documentation](https://docs.microsoft.com/azure/virtual-machines/)
- [Azure CLI Reference](https://docs.microsoft.com/cli/azure/)
- [Nginx Best Practices](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [CIS Ubuntu Hardening Guide](https://www.cisecurity.org/benchmark/ubuntu_linux/)

---

*This guide provides comprehensive coverage from basic labs to enterprise deployments. Start with the basic implementation, then progressively enhance using the advanced sections.*

**Contributing:** Found an issue? Want to add a scenario? Submit feedback!

**License:** Educational use permitted. Attribution appreciated.

