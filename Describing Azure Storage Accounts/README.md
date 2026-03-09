# Azure Storage Account — Simple Guide

> A storage account provides a unique namespace for your Azure Storage data that's accessible from anywhere in the world over HTTP or HTTPS.
> Data in this account is secure, highly available, durable, and massively scalable.

## What is an Azure Storage Account?

Think of an Azure Storage Account as your **personal vault in the cloud** where you can store different types of data (files, images, databases, etc.). 

**Key benefits:**
- 🌍 **Accessible worldwide** - Access your data from anywhere via web (HTTP/HTTPS)
- 🔒 **Secure** - Built-in security protections
- ⚡ **Highly available** - Your data is always accessible
- 🛡️ **Durable** - Your data is protected against loss
- 📈 **Massively scalable** - Grows with your needs

## Overview
An Azure Storage account is a unique namespace in Azure for storing blobs, files, queues, and tables. It is accessible over HTTP/HTTPS and provides built-in security, availability, and scalability.

## Redundancy options
- **LRS** — Locally redundant storage (copies within one region).
- **GRS** — Geo-redundant storage (replicates to a secondary region).
- **RA-GRS** — Read-access GRS (read from secondary region).
- **ZRS** — Zone-redundant storage (replicates across availability zones).
- **GZRS** — Geo-zone-redundant storage (zone + geo replication).
- **RA-GZRS** — Read-access GZRS (read from secondary region).

## Step 1: Choose Your Storage Account Type

When creating a storage account, you first pick a **type**. This choice determines:
- What services you can use
- How your data is protected (redundancy)
- What scenarios it works best for

### Storage Account Types Explained

| Type | What You Can Store | Data Protection Options | Best For |
|------|-------------------|------------------------|----------|
| **Standard general-purpose v2** | Blobs, Files, Queues, Tables | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS | **Most scenarios** - This is the go-to choice for general use |
| **Premium block blobs** | Blobs only | LRS, ZRS | High-speed applications, small files, low latency needs |
| **Premium file shares** | Files only | LRS, ZRS | High-performance file sharing, supports both Windows (SMB) and Linux (NFS) protocols |
| **Premium page blobs** | Page blobs only | LRS | Specialized virtual machine storage |

> 💡 **Tip**: Start with **Standard general-purpose v2** unless you have specific high-performance needs!

---

## Understanding Data Protection (Redundancy)

These options determine how many copies of your data exist and where they're located:

| Option | What It Means | Use Case |
|--------|--------------|----------|
| **LRS** (Locally Redundant Storage) | 3 copies in one data center | Budget-conscious, acceptable risk |
| **GRS** (Geo-Redundant Storage) | 3 copies in primary region + 3 copies in secondary region hundreds of miles away | Protection against regional disasters |
| **RA-GRS** (Read-Access Geo-Redundant) | Same as GRS, but you can read from the backup location | Need to access data even if primary fails |
| **ZRS** (Zone-Redundant Storage) | Copies spread across 3 availability zones in one region | Protection against data center failures |
| **GZRS** (Geo-Zone-Redundant) | ZRS in primary region + LRS in secondary region | Maximum protection within region + disaster recovery |
| **RA-GZRS** (Read-Access Geo-Zone-Redundant) | Same as GZRS, with read access to secondary | Ultimate protection with backup access |

---

## Step 2: Name Your Storage Account

Your storage account name creates a **unique web address** for your data.

### Naming Rules:
- ✅ **Length**: 3-24 characters
- ✅ **Characters**: Lowercase letters and numbers only (no spaces, no uppercase)
- ✅ **Uniqueness**: Must be unique across ALL of Azure (like a username)

### How Your Data is Accessed

Once created, your data gets specific web addresses:

| Service | Web Address Format |
|---------|-------------------|
| Blob Storage (files, images) | `https://youraccountname.blob.core.windows.net` |
| Data Lake Storage (big data) | `https://youraccountname.dfs.core.windows.net` |
| Azure Files (shared folders) | `https://youraccountname.file.core.windows.net` |
| Queue Storage (message queues) | `https://youraccountname.queue.core.windows.net` |
| Table Storage (NoSQL data) | `https://youraccountname.table.core.windows.net` |

---

## Quick Start Checklist

1. **Choose account type** → Start with "Standard general-purpose v2" for most cases
2. **Pick redundancy** → LRS for testing, GRS/ZRS for production, GZRS for critical data
3. **Create unique name** → 3-24 chars, lowercase letters and numbers only
4. **Access your data** → Use the endpoint URLs to connect your applications

---

## Example

If you name your account `mystorage123`:

- Your blob files will be at: `https://mystorage123.blob.core.windows.net`
- Your file shares at: `https://mystorage123.file.core.windows.net`

---

# Azure Storage Redundancy — Simple Explanation

> Azure Storage keeps multiple copies of your data so it stays safe and available even when things go wrong.
> Choose a redundancy option by balancing cost against how much protection and availability you need.

## What is Redundancy? (The "Backup" Concept)

**Redundancy = Making multiple copies of your data** so you never lose it.

Think of it like this:
- 📸 You take a photo on your phone
- ☁️ It automatically backs up to iCloud/Google Photos
- 💻 You also save it to your laptop
- 🖼️ You print a physical copy

If your phone breaks, you still have 3 other copies. **That's redundancy!**

Azure does this automatically for your cloud storage to protect against:
- 🔧 Hardware failures (hard drives dying)
- ⚡ Power outages
- 🌐 Network problems
- 🌪️ Natural disasters

---

## The Big Picture: Two Levels of Protection

Azure protects your data at **two geographic levels**:

┌─────────────────────────────────────────┐
│     PRIMARY REGION (Your Main Area)     │
│  ┌─────────────────────────────────┐    │
│  │  Multiple copies of your data  │    │
│  │  (Protects against local failures)│   │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
↓
┌─────────────────────────────────────────┐
│   SECONDARY REGION (Far Away Backup)    │
│  ┌─────────────────────────────────┐    │
│  │  Additional copy hundreds of    │    │
│  │  miles away (Disaster protection)│   │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘





































Describe Azure storage redundancy
Completed
100 XP
6 minutes
Azure Storage always stores multiple copies of your data so that it's protected from planned and unplanned events such as transient hardware failures, network or power outages, and natural disasters. Redundancy ensures that your storage account meets its availability and durability targets even in the face of failures.

When deciding which redundancy option is best for your scenario, consider the tradeoffs between lower costs and higher availability. The factors that help determine which redundancy option you should choose include:

How your data is replicated in the primary region.
Whether your data is replicated to a second region that is geographically distant to the primary region, to protect against regional disasters.
Whether your application requires read access to the replicated data in the secondary region if the primary region becomes unavailable.
Redundancy in the primary region
Data in an Azure Storage account is always replicated three times in the primary region. Azure Storage offers two options for how your data is replicated in the primary region, locally redundant storage (LRS) and zone-redundant storage (ZRS).

Locally redundant storage
Locally redundant storage (LRS) replicates your data three times within a single data center in the primary region. LRS provides at least 11 nines of durability (99.999999999%) of objects over a given year.
<img width="273" height="285" alt="image" src="https://github.com/user-attachments/assets/0043966e-4bad-4224-8e6a-64f7611306a8" />

Diagram showing the structure used for locally redundant storage.

LRS is the lowest-cost redundancy option and offers the least durability compared to other options. LRS protects your data against server rack and drive failures. However, if a disaster such as fire or flooding occurs within the data center, all replicas of a storage account using LRS may be lost or unrecoverable. To mitigate this risk, Microsoft recommends using zone-redundant storage (ZRS), geo-redundant storage (GRS), or geo-zone-redundant storage (GZRS).

Zone-redundant storage
For Availability Zone-enabled Regions, zone-redundant storage (ZRS) replicates your Azure Storage data synchronously across three Azure availability zones in the primary region. ZRS offers durability for Azure Storage data objects of at least 12 nines (99.9999999999%) over a given year.
<img width="503" height="501" alt="image" src="https://github.com/user-attachments/assets/856b46d7-de76-4056-a01d-78bb8092b9ab" />

Diagram showing ZRS, with a copy of data stored in each of three availability zones.

With ZRS, your data is still accessible for both read and write operations even if a zone becomes unavailable. No remounting of Azure file shares from the connected clients is required. If a zone becomes unavailable, Azure undertakes networking updates, such as DNS repointing. These updates may affect your application if you access data before the updates have completed.

Microsoft recommends using ZRS in the primary region for scenarios that require high availability. ZRS is also recommended for restricting replication of data within a country or region to meet data governance requirements.

Redundancy in a secondary region
For applications requiring high durability, you can choose to additionally copy the data in your storage account to a secondary region that is hundreds of miles away from the primary region. If the data in your storage account is copied to a secondary region, then your data is durable even in the event of a catastrophic failure that prevents the data in the primary region from being recovered.

When you create a storage account, you select the primary region for the account. The paired secondary region is based on Azure Region Pairs, and can't be changed.

Azure Storage offers two options for copying your data to a secondary region: geo-redundant storage (GRS) and geo-zone-redundant storage (GZRS). GRS is similar to running LRS in two regions, and GZRS is similar to running ZRS in the primary region and LRS in the secondary region.

By default, data in the secondary region isn't available for read or write access unless there's a failover to the secondary region. If the primary region becomes unavailable, you can choose to fail over to the secondary region. After the failover has completed, the secondary region becomes the primary region, and you can again read and write data.

 Important

Because data is replicated to the secondary region asynchronously, a failure that affects the primary region may result in data loss if the primary region can't be recovered. The interval between the most recent writes to the primary region and the last write to the secondary region is known as the recovery point objective (RPO). The RPO indicates the point in time to which data can be recovered. Azure Storage typically has an RPO of less than 15 minutes, although there's currently no SLA on how long it takes to replicate data to the secondary region.

Geo-redundant storage
GRS copies your data synchronously three times within a single physical location in the primary region using LRS. It then copies your data asynchronously to a single physical location in the secondary region (the region pair) using LRS. GRS offers durability for Azure Storage data objects of at least 16 nines (99.99999999999999%) over a given year.
<img width="731" height="371" alt="image" src="https://github.com/user-attachments/assets/a4a603f4-c9c2-4b98-8487-e84a85dcb279" />

Diagram showing GRS, with primary region LRS replicating data to LRS in a second region.

Geo-zone-redundant storage
GZRS combines the high availability provided by redundancy across availability zones with protection from regional outages provided by geo-replication. Data in a GZRS storage account is copied across three Azure availability zones in the primary region (similar to ZRS) and is also replicated to a secondary geographic region, using LRS, for protection from regional disasters. Microsoft recommends using GZRS for applications requiring maximum consistency, durability, and availability, excellent performance, and resilience for disaster recovery.
<img width="960" height="615" alt="image" src="https://github.com/user-attachments/assets/3dde8a71-bcdc-469a-9af2-4d8517e36c14" />

Diagram showing GZRS, with primary region ZRS replicating data to LRS in a second region.

GZRS is designed to provide at least 16 nines (99.99999999999999%) of durability of objects over a given year.

Read access to data in the secondary region
Geo-redundant storage (with GRS or GZRS) replicates your data to another physical location in the secondary region to protect against regional outages. However, that data is available to be read only if the customer or Microsoft initiates a failover from the primary to secondary region. However, if you enable read access to the secondary region, your data is always available, even when the primary region is running optimally. For read access to the secondary region, enable read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS).

 Important

Remember that the data in your secondary region may not be up-to-date due to RPO.
