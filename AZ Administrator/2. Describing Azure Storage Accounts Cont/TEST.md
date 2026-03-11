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





























Create a storage blob
In this exercise, you create an Azure storage container, upload a file to blob storage, and then configure blob storage to allow access to the file.

This exercise should take approximately 15 minutes to complete.

Task 1: Create a storage account
In this task, you’ll create a new storage account.

Sign in to the Azure portal at https://portal.azure.com
Select Create a resource.
Under Categories, select Infrastructure Services.
Under Storage account, select Create.
On the Basics tab of the Create a storage account blade, fill in the following information. Leave the defaults for everything else.

Setting	Value
Subscription	Select the subscription you want to use for the exercise.
Resource group	Select Create new and enter IntroAzureRG and select OK
Storage account name	Create a unique storage account name
Region	Leave default
Performance	Standard
Redundancy	Locally redundant storage (LRS)
On the Advanced tab of the Create a storage account blade, fill in the following information. Leave the defaults for everything else.

Setting	Value
Allow enabling anonymous access on individual containers	Checked
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/1ebd66e9-cb63-4e28-a5d0-2dd535b10c8f" />


Select Review + create to review your storage account settings and allow Azure to validate the configuration.
Once validated, select Create. Wait for the notification that the account was successfully created.
Select Go to resource.
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/867b07a1-e34b-41f3-b39a-f71236423fc6" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/202c8fab-7ad4-4b06-bc62-b338fb7e09b4" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/a6a255ea-49fa-42c3-9a11-d1626792db56" />

Task 2: Work with blob storage
In this section, you’ll create a Blob container and upload a picture.

Under Data storage, select Containers.

<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/d1c10e3a-594a-42fa-80ee-5051be388d33" />
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/41303234-0f31-4637-bcd1-0d0ceed69982" />

Select + Add container and complete the information.

Setting	Value
Name	Enter a name for the container
Anonymous access level	Private (no anonymous access)
Select Create.

[!NOTE] Step 4 will need an image. If you want to upload an image you already have on your computer, continue to Step 4. Otherwise, open a new browser window and search Bing for an image and save the image to your computer.

Back in the Azure portal, select the container you created, then select Upload.
<img width="752" height="425" alt="image" src="https://github.com/user-attachments/assets/da7b0975-e4b9-4200-9caf-a3d094381886" />

Browse for the image file you want to upload. Select it and then select upload.

[!NOTE] You can upload as many blobs as you like in this way. New blobs will be listed within the container.

Select the Blob (file) you just uploaded. You should be on the properties tab.
Copy the URL from the URL field and paste it into a new tab. You should receive an error message similar to the following.

code
<Error>
<Code>ResourceNotFound</Code>
  <Message>The specified resource does not exist. RequestId:4a4bd3d9-101e-005a-1a3e-84bd42000000</Message>
</Error> 

Task 3: Change the access level of your blob
Go back to the Azure portal.
Select Change access level.
Set the Anonymous access level to Blob (anonymous read access for blobs only).
<img width="579" height="432" alt="image" src="https://github.com/user-attachments/assets/b090e141-3f3b-4b70-a153-fb6a7872e54c" />


Select OK.
Refresh the tab where you attempted to access the file earlier.
Congratulations - you’ve completed this exercise. You created a storage account, added a container to the storage account, and then uploaded blobs (files) to your container. Then you changed the access level so you could access your file from the internet.

Clean up
From the Azure home page, under Azure services, select Resource groups.
Select the IntroAzureRG resource group.
Select Delete resource group.
Enter IntroAzureRG to confirm deletion of the resource group
Select Delete.
On the confirmation window, select Delete.
