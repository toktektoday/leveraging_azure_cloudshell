![Illustration to use for new users](https://azurecomcdn.azureedge.net/cvt-779fa2985e70b1ef1c34d319b505f7b4417add09948df4c5b81db2a9bad966e5/images/page/services/devops/hero-images/index-hero.jpg)
# **Remote Admin Access** with **Azure Cloud Shell**
This project describes how to leverage **Azure Cloud Shell** for **Remote Admin Access**.
Azure Cloud Shell is a browser-based and multi-platform command-line tool, that allows you to connect to Azure and manage your resources from virtually anywhere. Azure Cloud Shell is assigned per unique user account and automatically authenticated with each session for instant access to your resources through the Azure CLI or Azure PowerShell cmdlets. 

Cloud Shell machines are temporary. An Azure Cloud Shell session is run on a temporary host provided on a per-session, per-user basis, however, it persists your files and data through a mounted Azure Files share. These attributes make Azure Cloud Shell very suitable for managing your daily operations. 

A common need for someone in operations, and not only, is to gain remote access to Azure Virtual Machines. Throughout this article we'll explore how to leverage Azure Cloud Shell's capabilities to manage Azure-hosted Virtual Machines at speed and scale.

Cloud Shell is managed by Microsoft so it comes equipped with popular command-line tools and language support, including Linux shell interpreters, PowerShell modules and Azure tools. Below, are examples of how to make use of these pre-packed tools to remotely connect to a single Windows or Linux machine and, of how to remotely invoke commands on multiple machines at once, being them Windows or Linux machines.
## For **Remote Admin Access** on **Windows machines**

### For opening a **remote session** on a single Windows machine
This procedure will open a remote session on a single Windows machine
```powershell
# Provide administator details
$vmName = "Your VM's Name"
 
# Get the VM object
$vm = Get-AzVm -Name $vmName
 
# Temporarily enable remote sessions on the machine (Opens WinRm on Port 5986)
Enable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Protocol https -OsType Windows

# Open a remote session
Enter-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Credential (get-credential)
     
# Locally execute the cmds you wish to
iex hostname
iex pwd

#After you are done
Disable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```
<a href="https://shell.azure.com/powershell" target="_blank">
  <img src="[https://aka.ms/deploytoazurebutton](https://dev.azure.com/toktektoday/a67192fa-d63d-4d01-bf70-8898d78b6d6c/_apis/git/repositories/e05f0b85-327a-4f2d-949e-3ae6e8b6b8d0/items?scopePath=/src/images/run_with_cloudshell.png&branchName=main&api-version=6.0 =180x)"/>
</a>

### For executing **remote commands** on multiple Windows machines
This procedure will execute remote commands on multiple Windows machines. The -ScriptBlock {} and the VM objects querying will vary according to your needs.
```powershell
# Get the VM objects : Query retrieving multiple Windows machine objects
$vms = Get-AzVM -status | Where-Object {$_.PowerState -eq "VM running" -and $_.StorageProfile.OsDisk.OsType -eq "Windows"}

# Run the commands against the target VMs in parallel
$vms | ForEach-Object -Parallel {
    # Temporarily enable remote sessions on the machine (Opens WinRm on Port 5986)
    Enable-AzVMPSRemoting -Name $_.Name -ResourceGroupName $_.ResourceGroupName -Protocol https -OsType Windows
    
    # Execute a remote command or scriptblock
    Invoke-AzVMRunCommand -VM $_ -CommandId 'RunPowerShellScript' -ScriptString 'get-service win*'
    
    #After you are done
    Disable-AzVMPSRemoting -Name $_.Name -ResourceGroupName $_.ResourceGroupName
}
```
[![Run with Cloudshell](https://dev.azure.com/toktektoday/a67192fa-d63d-4d01-bf70-8898d78b6d6c/_apis/git/repositories/e05f0b85-327a-4f2d-949e-3ae6e8b6b8d0/items?scopePath=/src/images/run_with_cloudshell.png&branchName=main&api-version=6.0 =180x)](https://shell.azure.com/powershell)

## For **Remote Admin Access** on **Linux machines**

### For opening a **remote session** on a single Linux machine
This procedure will open a remote session on a single Linux machine
```powershell
# Provide administator details
$vmName = "Your VM's Name"
 
# Get the VM object
$vm = Get-AzVm -Name $vmName
 
# Temporarily enable remote sessions on the machine (Opens SSH on Port 22)
Enable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Protocol ssh -OsType Linux

# Open a remote session
Enter-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Credential (get-credential)
     
# Locally execute the cmds you wish to
uname -a

#After you are done
Disable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```
[![Run with Cloudshell](https://dev.azure.com/toktektoday/a67192fa-d63d-4d01-bf70-8898d78b6d6c/_apis/git/repositories/e05f0b85-327a-4f2d-949e-3ae6e8b6b8d0/items?scopePath=/src/images/run_with_cloudshell.png&branchName=main&api-version=6.0 =180x)](https://shell.azure.com/powershell)

### For executing **remote commands** on multiple Linux machines
This procedure will execute remote commands multiple Linux machines. The -ScriptBlock {} and the VM objects querying will vary according to your needs.
```powershell
# Get the VM objects : Query retrieving multiple Linux machine objects
$vms = Get-AzVM -status | Where-Object {$_.PowerState -eq "VM running" -and $_.StorageProfile.OsDisk.OsType -eq "Linux"}

# Run the commands against the target VMs in parallel
$vms | ForEach-Object -Parallel {  
    # Temporarily enable remote sessions on the machine (Opens SSH on Port 22)
    Enable-AzVMPSRemoting -Name $_.Name -ResourceGroupName $_.ResourceGroupName -Protocol ssh -OsType Linux

    # Execute a remote command or scriptblock 
    Invoke-AzVMRunCommand -VM $_ -CommandId 'RunShellScript' -ScriptString 'uname -a'

    #After you are done
    Disable-AzVMPSRemoting -Name $_.Name -ResourceGroupName $_.ResourceGroupName
}
```
[![Run with Cloudshell](https://dev.azure.com/toktektoday/a67192fa-d63d-4d01-bf70-8898d78b6d6c/_apis/git/repositories/e05f0b85-327a-4f2d-949e-3ae6e8b6b8d0/items?scopePath=/src/images/run_with_cloudshell.png&branchName=main&api-version=6.0 =180x)](https://shell.azure.com/powershell)

#References
https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart-powershell
https://azure.microsoft.com/en-us/get-started/azure-portal/cloud-shell/?OCID=AIDcmm68ejnsa0_SEM_e92f803cf6e1114e615ce2d15d089e39:G:s&ef_id=e92f803cf6e1114e615ce2d15d089e39:G:s&msclkid=e92f803cf6e1114e615ce2d15d089e39#overview
https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows
https://docs.microsoft.com/en-us/azure/virtual-machines/linux/run-command?WT.mc_id=itopstalk-blog-thmaure
https://docs.microsoft.com/en-us/azure/virtual-machines/windows/run-command
https://docs.microsoft.com/en-us/azure/virtual-machines/windows/run-command?WT.mc_id=itopstalk-blog-thmaure
https://techcommunity.microsoft.com/t5/itops-talk-blog/how-to-run-scripts-in-your-azure-vm-by-using-run-command/ba-p/1362360
https://docs.microsoft.com/en-us/powershell/module/az.compute/invoke-azvmruncommand?view=azps-8.2.0
https://www.thomasmaurer.ch/2021/03/how-to-run-scripts-against-multiple-azure-vms-by-using-run-command/
https://www.thomasmaurer.ch/2019/01/azure-cloud-shell/#:~:text=You%20can%20run%20the%20Enable-AzVMPSRemoting%20cmdlet%20to%20enable,and%20configure%20the%20remoting%20and%20NSGs%20in%20Azure.


### For opening a **remote session** on a single Linux machine (SSH)
This procedure will open a remote session on a single Linux machine
```powershell

# Provide administator details

$vmName = "Your VM's Name"

# Get the VM object
$vm = Get-AzVm -Name $vmName
$vmLocalAdminUser = $vm.OSProfile.AdminUsername
$networkProfile = $vm.NetworkProfile.NetworkInterfaces.id.Split("/")|Select -Last 1
$vmIpAddress = (Get-AzNetworkInterface -Name $networkProfile).IpConfigurations.PublicIpAddress
$userHost = $vmLocalAdminUser + "@" + $vmIpAddress

# Generate your SSH Key
git init
ssh-keygen -f ~/.ssh/id_rsa -t rsa -b 4096
$sshPublicKey = cat ~/.ssh/id_rsa.pub
$path = "/home/" + $vmLocalAdminUser + "/.ssh/authorized_keys"

# Temporarily enable remote sessions on the machine (Opens SSH on Port 22)
Enable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Protocol ssh -OsType Linux
 
# Add your SSH Key to the virtual machine
Add-AzVMSshPublicKey -VM $vm -KeyData $sshPublicKey -Path $path

# Open a remote session
ssh $userHost 

# Locally execute the cmds you wish to
iex hostname
iex pwd

#After you are done
Disable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


### For opening a **remote session** on a single Windows machine (SSH) from linux or windows
This procedure will open a remote session on a single Linux machine
```powershell

# Provide administator details

$vmName = "Your VM's Name"

# Get the VM object
$vm = Get-AzVm -Name $vmName
$vmLocalAdminUser = $vm.OSProfile.AdminUsername
$networkProfile = $vm.NetworkProfile.NetworkInterfaces.id.Split("/")|Select -Last 1
$vmIpAddressName = (Get-AzNetworkInterface -Name $networkProfile).IpConfigurations.PublicIpAddress.Id.Split("/")|Select -Last 1
$vmIpAddress = (Get-AzPublicIpAddress -Name $vmIpAddressName -ResourceGroupName $vm.ResourceGroupName).IpAddress
$userHost = $vmLocalAdminUser + "@" + $vmIpAddress

# Temporarily enable remote sessions on the machine (Opens SSH on Port 22)
Enable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Protocol ssh -OsType Linux

# Open a remote session
Enter-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Credential (get-credential)

# Locally execute the cmds to enable OpenSSH on the remote Windows machine 
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0 
Get-Service ssh-agent | Set-Service -StartupType Automatic  
Start-Service ssh-agent
Get-Service sshd | Set-Service -StartupType Automatic  
Start-Service sshd

# BACK TO CLOUDSHELL

# Generate your SSH Key
ssh-keygen -f ~/.ssh/id_rsa -t rsa -b 4096
$sshPublicKey = cat ~/.ssh/id_rsa.pub
$path = "C:\Users\$vmLocalAdminUser\.ssh"

# Get the public key file generated previously on your client
$authorizedKey = Get-Content -Path .ssh\id_rsa.pub

# Generate the PowerShell to be run remote that will copy the public key file generated previously on your client to the authorized_keys file on your server
$remotePowershell = "powershell New-Item -Force -ItemType Directory -Path $path; Add-Content -Force -Path $path\authorized_keys -Value '$authorizedKey'"

# Connect to your server and run the PowerShell using the $remotePowerShell variable
ssh $userHost $remotePowershell

# Open a remote session
ssh $userHost 

# Locally execute the cmds you wish to
hostname
exit 

#After you are done
Disable-AzVMPSRemoting -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement
