# Azure Blob Storage Hands-On Lab

In this exercise, learnt to:
- 🏗️ Create an Azure storage account
- 📦 Create a container (like a folder) for your files
- 📤 Upload a file to the cloud
- 🔓 Configure public access to view your file from anywhere

---

## Prerequisites

- An Azure account (free tier works fine)
- A web browser
- An image file on your computer (or you can download one)

---

## Task 1: Create a Storage Account

Think of a **storage account** as your main vault in Azure where all your data lives.

### Step-by-Step Instructions

#### Step 1: Open Azure Portal
1. Go to [https://portal.azure.com](https://portal.azure.com)
2. Sign in with your Azure account

#### Step 2: Start Creating a Resource
1. Click **"Create a resource"** (big + button in the top left)
2. Under **Categories**, select **"Infrastructure Services"**
3. Find **"Storage account"** and click **"Create"**

#### Step 3: Configure Basic Settings

You'll see a form with several tabs. Start with the **"Basics"** tab:

| Setting | What to Enter | Explanation |
|---------|--------------|-------------|
| **Subscription** | Select your subscription | Your Azure billing account |
| **Resource group** | Click **"Create new"** → Enter `IntroAzureRG` → Click **OK** | A folder to organize all resources for this lab |
| **Storage account name** | Create a unique name (e.g., `mystorage123demo`) | Must be unique across ALL of Azure, 3-24 chars, lowercase letters and numbers only |
| **Region** | Leave default | The data center location closest to you |
| **Performance** | **Standard** | Good balance of cost and speed |
| **Redundancy** | **Locally redundant storage (LRS)** | 3 copies in one data center (cheapest option) |

&gt; 💡 **Tip:** Your storage account name must be globally unique. If you get an error, try adding numbers or your initials.

#### Step 4: Configure Advanced Settings

1. Click the **"Advanced"** tab at the top
2. Find the setting: **"Allow enabling anonymous access on individual containers"**
3. **Check the box** ✓

&gt; 📝 **Why this matters:** This lets you make specific files publicly accessible later, which we'll need for this exercise.
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/1ebd66e9-cb63-4e28-a5d0-2dd535b10c8f" />

#### Step 5: Review and Create

1. Click **"Review + create"** at the bottom
2. Azure will check your settings (takes a few seconds)
3. Once you see **"Validation passed"**, click **"Create"**
4. Wait 1-2 minutes for deployment to complete

#### Step 6: Go to Your New Storage Account

1. You'll see a notification: **"Deployment is complete"**
2. Click **"Go to resource"** to open your storage account

---

## Task 2: Work with Blob Storage

Now you'll create a **container** (like a folder) and upload a file to it.

### What is a Container?
Think of a container as a **folder** inside your storage account. You can have multiple containers to organize your files (e.g., "images", "documents", "backups").

### Step-by-Step Instructions

#### Step 1: Navigate to Containers

1. In your storage account, look at the left menu
2. Under **"Data storage"**, click **"Containers"**

#### Step 2: Create a New Container

1. Click the **"+ Add container"** button at the top
2. Fill in the details:

<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/d1c10e3a-594a-42fa-80ee-5051be388d33" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/41303234-0f31-4637-bcd1-0d0ceed69982" />

| Setting | What to Enter | Explanation |
|---------|--------------|-------------|
| **Name** | Enter a name (e.g., `myimages` or `public-files`) | Lowercase letters, numbers, and hyphens only |
| **Anonymous access level** | **Private (no anonymous access)** | We'll change this later |

3. Click **"Create"**

#### Step 3: Prepare an Image to Upload

You have two options:

**Option A - Use your own image:**
- Find any image file on your computer (JPG, PNG, etc.)

**Option B - Download a sample image:**
1. Open a new browser tab
2. Go to [Bing Images](https://www.bing.com/images) or any image search
3. Search for something (e.g., "Azure logo")
4. Right-click an image → **"Save image as"** → Save to your desktop

#### Step 4: Upload Your Image

1. Back in the Azure portal, click on the container you just created
2. Click the **"Upload"** button at the top
3. Click the **folder icon** to browse your computer
4. Select your image file
5. Click **"Upload"**

&gt; 📝 **Note:** You can upload multiple files this way. Each file is called a "blob" in Azure.

#### Step 5: Try to Access Your File (It Should Fail)

1. Click on the file name you just uploaded
2. You'll see the **Properties** tab on the right
3. Find the **URL** field and copy the link (looks like: `https://youraccount.blob.core.windows.net/containername/filename.jpg`)
4. Open a new browser tab and paste the URL
5. **You should see an error message:**

```xml
&lt;Error&gt;
&lt;Code&gt;ResourceNotFound&lt;/Code&gt;
  &lt;Message&gt;The specified resource does not exist. RequestId:4a4bd3d9-101e-005a-1a3e-84bd42000000&lt;/Message&gt;
&lt;/Error&gt;
```
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/867b07a1-e34b-41f3-b39a-f71236423fc6" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/202c8fab-7ad4-4b06-bc62-b338fb7e09b4" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/a6a255ea-49fa-42c3-9a11-d1626792db56" />

&gt; ✅ **This is expected!** The file exists, but it's private. Next, we'll make it public.

---

## Task 3: Make Your Blob Publicly Accessible

Now you'll change the access level so anyone can view your image via the URL.

### Step-by-Step Instructions

#### Step 1: Change Access Level

1. Go back to the Azure portal tab
2. Click **"Change access level"** at the top
3. In the dropdown, select: **"Blob (anonymous read access for blobs only)"**

| Access Level | What It Means |
|-------------|---------------|
| **Private** | No one can access without authentication |
| **Blob** | Anyone can read individual files (what we want) |
| **Container** | Anyone can list all files AND read them |

4. Click **"OK"**

<img width="579" height="432" alt="image" src="https://github.com/user-attachments/assets/b090e141-3f3b-4b70-a153-fb6a7872e54c" />

#### Step 2: Verify Public Access

1. Go back to the browser tab where you saw the error
2. **Refresh the page** (press F5 or Ctrl+R)
3. **Your image should now display!** 🎉

&gt; 🎊 **Congratulations!** You've successfully:
&gt; - Created a storage account
&gt; - Created a container
&gt; - Uploaded a blob (file)
&gt; - Made it accessible from anywhere on the internet

---

## Clean Up (Important!)

To avoid charges, delete everything you created:

### Step 1: Delete the Resource Group

1. Go to [Azure Portal Home](https://portal.azure.com)
2. Under **"Azure services"**, click **"Resource groups"**
3. Find and click on **"IntroAzureRG"** (the resource group you created)
4. Click **"Delete resource group"** at the top
5. Type `IntroAzureRG` to confirm
6. Click **"Delete"**
7. On the confirmation popup, click **"Delete"** again

&gt; ⚠️ **This deletes everything:** The storage account, containers, and all files inside. This action cannot be undone!

---

## What You Learned

| Concept | Explanation |
|---------|-------------|
| **Storage Account** | Your main vault in Azure for all data |
| **Container** | A folder to organize your blobs |
| **Blob** | Any file stored in blob storage (images, videos, documents) |
| **Access Levels** | Control who can see your files (Private vs Public) |
| **URL Access** | Every blob gets a unique web address |

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Storage account name already exists" | Pick a more unique name (add numbers/initials) |
| "Upload failed" | Check file size (max 5TB per blob) and try again |
| "Still see error after changing access" | Wait 30 seconds for changes to propagate, then refresh |
| "Can't find Containers menu" | Make sure you're inside your storage account, not at the main portal |

---

## Next Steps

- [Learn more about Blob Storage tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
- [Explore Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) (desktop app for managing files)
- [Try the Azure CLI for automation](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli)
