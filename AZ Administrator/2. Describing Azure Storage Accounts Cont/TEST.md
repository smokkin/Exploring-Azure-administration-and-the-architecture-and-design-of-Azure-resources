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
