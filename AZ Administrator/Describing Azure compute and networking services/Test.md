Create an virtual machine and configure as a web host
In this exercise, you create an Azure virtual machine (VM), install a web server, and upate the network configuration to allow access from the internet.

This exercise should take approximately 20 minutes to complete.

You could use the Azure portal, the Azure CLI, or an Azure Resource Manager (ARM) template to complete the steps in this exercise.

In this instance, you’re going to use the Azure CLI.

Task 1: Create a resource group
The very first task has you create a resource group in the West US region. Everything else created during this lab will be created within that resource group.

Log into the Azure portal.
Select the Azure Cloud Shell icon to bring up Cloud Shell.
From the Azure CLI, create a resource group named IntroAzureRG.
code
 az group create --name IntroAzureRG --location centralus
Task 2: Create a Linux virtual machine
Use the following Azure CLI command to create a Linux VM.

From Cloud Shell, run the following az vm create command to create a Linux VM:

code
az vm create \
  --resource-group "IntroAzureRG" \
  --name my-vm \
  --size Standard_D2s_v5 \
  --public-ip-sku Standard \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys    
Your VM may take a few moments to provision. You named the VM my-vm, and will refer to the VM later based on that name.

Task 3: Install Nginx
After your VM is created, you’ll use a Custom Script Extension to install Nginx. The Custom Script Extension is an easy way to download and run scripts on your Azure VMs. It’s just one of the many ways you can configure the system after your VM is up and running.

Run the following az vm extension set command to configure Nginx on your VM:

code
 az vm extension set \
   --resource-group "IntroAzureRG" \
   --vm-name my-vm \
   --name customScript \
   --publisher Microsoft.Azure.Extensions \
   --version 2.1 \
   --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
   --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'    
This command uses the Custom Script Extension to run a Bash script on your VM. The script is stored on GitHub. While the command runs, you can choose to examine the Bash script from a separate browser tab. To summarize, the script:

Runs apt-get update to download the latest package information from the internet. This step helps ensure that the next command can locate the latest version of the Nginx package.
Installs Nginx.
Sets the home page, /var/www/html/index.html, to print a welcome message that includes your VM’s host name.
Right now, the VM you created and installed Nginx on during the previous exercise isn’t accessible from the internet. In the next few steps, you’ll create a network security group that changes that by allowing inbound HTTP access on port 80.

[!NOTE] It’s important that you’re in the BASH version of Cloud Shell for some of the commands in this exercise. You can use the Switch to… button if you’re currently in PowerShell mode.

Task 4: Access your web server
In this procedure, you get the IP address for your VM and attempt to access your web server’s home page.

Run the following az vm list-ip-addresses command to get your VM’s IP address and store the result as a Bash variable:

code
IPADDRESS="$(az vm list-ip-addresses \
  --resource-group "IntroAzureRG" \
  --name my-vm \
  --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" \
  --output tsv)"    
Run the following curl command to download the home page:

code
curl --connect-timeout 5 http://$IPADDRESS
The --connect-timeout argument specifies to allow up to five seconds for the connection to occur. After five seconds, you see an error message that states that the connection timed out:

code
curl: (28) Connection timed out after 5001 milliseconds
This message means that the VM wasn’t accessible within the timeout period.

As an optional step, try to access the web server from a browser:

Run the following to print your VM’s IP address to the console:

code
echo $IPADDRESS       
You see an IP address, for example, 23.102.42.235.

Copy the IP address that you see to the clipboard.
Open a new browser tab and go to your web server. After a few moments, you see that the connection isn’t happening. If you wait for the browser to time out, you see something like this:

Screenshot of a web browser showing an error message that says the connection timed out.

Keep this browser tab open for later.
Task 5: List the current network security group rules
Your web server wasn’t accessible. To find out why, let’s examine your current NSG rules.

Run the following az network nsg list command to list the network security groups that are associated with your VM:

code
az network nsg list \
  --resource-group "IntroAzureRG" \
  --query '[].name' \
  --output tsv    
You see this output:

code
my-vmNSG
Every VM on Azure is associated with at least one network security group. In this case, Azure created an NSG for you called my-vmNSG.

Run the following az network nsg rule list command to list the rules associated with the NSG named my-vmNSG:

code
az network nsg rule list \
  --resource-group "IntroAzureRG" \
  --nsg-name my-vmNSG    
You see a large block of text in JSON format in the output. In the next step, you’ll run a similar command that makes this output easier to read.

Run the az network nsg rule list command a second time. This time, use the --query argument to retrieve only the name, priority, affected ports, and access (Allow or Deny) for each rule. The --output argument formats the output as a table so that it’s easy to read.

code
az network nsg rule list \
  --resource-group "IntroAzureRG" \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table    
You see this output:

code
Name              Priority    Port    Access
-----------------  ----------  ------  --------
default-allow-ssh  1000        22      Allow
You see the default rule, default-allow-ssh. This rule allows inbound connections over port 22 (SSH). SSH (Secure Shell) is a protocol that’s used on Linux to allow administrators to access the system remotely. The priority of this rule is 1000. Rules are processed in priority order, with lower numbers processed before higher numbers.

By default, a Linux VM’s NSG allows network access only on port 22. This port enables administrators to access the system. You need to also allow inbound connections on port 80, which allows access over HTTP.

Task 6: Create the network security rule
Here, you create a network security rule that allows inbound access on port 80 (HTTP).

Run the following az network nsg rule create command to create a rule called allow-http that allows inbound access on port 80:

code
az network nsg rule create \
  --resource-group "IntroAzureRG" \
  --nsg-name my-vmNSG \
  --name allow-http \
  --protocol tcp \
  --priority 100 \
  --destination-port-range 80 \
  --access Allow    
For learning purposes, here you set the priority to 100. In this case, the priority doesn’t matter. You would need to consider the priority if you had overlapping port ranges.

To verify the configuration, run az network nsg rule list to see the updated list of rules:

code
az network nsg rule list \
  --resource-group "IntroAzureRG" \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table    
You see both the default-allow-ssh rule and your new rule, allow-http:

code
Name              Priority    Port    Access
-----------------  ----------  ------  --------
default-allow-ssh  1000        22      Allow
allow-http          100        80      Allow    
Task 7: Access your web server again
Now that you configured network access to port 80, let’s try to access the web server a second time.

[!NOTE] After you update the NSG, it may take a few moments before the updated rules propagate. Retry the next step, with pauses between attempts, until you get the desired results.

Run the same curl command that you ran earlier:

code
curl --connect-timeout 5 http://$IPADDRESS
You see this response:

code
<html><body><h2>Welcome to Azure! My name is my-vm.</h2></body></html>
As an optional step, refresh your browser tab that points to your web server. You see the home page:

A screenshot of a web browser showing the home page from the web server. The home page displays a welcome message.

Nice work. In practice, you can create a standalone network security group that includes the inbound and outbound network access rules you need. If you have multiple VMs that serve the same purpose, you can assign that NSG to each VM at the time you create it. This technique enables you to control network access to multiple VMs under a single, central set of rules.

You’ve completed this exercise and all of the exercises for this module. To clean up your Azure environment and avoid leaving VMs running when not in use, delete the IntroAzureRG resource group.

Clean up
From the Azure home page, under Azure services, select Resource groups.
Select the IntroAzureRG resource group.
Select Delete resource group.
Ensure the Apply force delete for selected Virtal machines and Virtual machine scale sets box is checked.
Enter IntroAzureRG to confirm deletion of the resource group
On the confirmation window, select Delete.
