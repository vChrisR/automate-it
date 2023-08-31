---
title: 'Automating SRM with PowerCLI'
date: Mon, 16 Jun 2014 10:15:07 +0000
draft: false
tags: ['PowerCLI', 'PowerCLI', 'Site REcovery Manager', 'SRM', 'vRealize Automation (vCAC)', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)']
---

_Update (11-sept-2014): VMware released a vCO plugin for SRM. So if you are looking to automate SRM with vCO don't use the Powercli script below but use the [plugin](https://my.vmware.com/web/vmware/info/slug/infrastructure_operations_management/vmware_vcenter_site_recovery_manager/5_8#drivers_tools) instead._ Recently I was working on automating the deployment of VMs on a twin datacenter vSphere design. The failover is handled by Site Recovery Manager and the deployment of the VMs is taken care of by vCAC. Luckily vCAC has SRM integration so no prolem there..... But apparently when VMware says "integration" they mean: "It's ok if you failover VMs deployed by vCAC". It doesn't mean that all VMs deployed by vCAC are automatically protected by SRM. Since we are using vCAC the logic choice would be to automated this using a vCenter Orchestrator workflow that enables protection for the deployed VMs. However, there is no vCO plug-in for SRM and SRM does not have a REST or SOAP API. At least not one that is compatible with the vCO SOAP plugin. This, to me, is surprising because both SRM and vCO have been around for quite a while. Luckily the latest version of VMware PowerCLI comes with SRM cmdlets. Or rather: one cmdlet. Now, vCO can call Powershell scripts so the obvious solution is to create a powershell script that configures SRM protection for a virtual machine and then call that from vCO. The first thing I tried was copy pasting the [example from the documentation](http://pubs.vmware.com/vsphere-55/index.jsp#com.vmware.powercli.ug.doc/GUID-333DA3CA-3BEC-4DBE-AB02-15A793763EF7.html). Which didn't work. It kept failing with the error:

"_The operation is not supported on the object"_

The same error was reported [here](https://communities.vmware.com/message/2389530) but no solution available. The error was caused by the line containing: "a_ssociateVms()_". It turns out I should have just read the developers guide because that states this method is only needed when you're using SRM Replication. If you are using array based replication there is no need to associate the VM with a protection group. However, removing this line only resulted in a new error. This time the error appears in the vSphere client:

_Not authenticated into SRM server <servername>_

The account I was using has administrator permissions on both vCenter servers and both SRM machines so it's not a permission issue. The solution is to pass credentials with Connect-srmserver command for both the local and the remote SRM server. Below is the script I created. It takes the vCenter server ,a username, the VM to protect and the protection group it is in as parameters.```
param (
    \[string\]$vcenter,
    \[string\]$username,
    \[string\]$vmname,
    \[string\]$protgroup
)
 
Add-PSSnapin VMware.VimAutomation.Core
 
# Retrieve password for service account from file and create credential object. this is needed for connect-srm
$password = cat $("C:\\orchestrator\\powershell\\"+ $username +".pw") | ConvertTo-SecureString
$credential = new-object -typename System.Management.Automation.PSCredential -ArgumentList $username,$password
 
#connect to vCenter server and get VM object
connect-viserver -Server $vcenter -Credential $credential
$vm = get-vm $vmname
 
#connect to SRM server. Remote AND local credentials are required.
$srm = connect-srmserver -RemoteCredential $credential -Credential $credential
$srmApi = $srm.ExtensionData
 
#Get Protection group and create protectionSpec
$protectionGroups= $srmApi.Protection.ListProtectionGroups()
$targetProtectionGroup = $protectionGroups | where {$\_.GetInfo().Name -eq $protgroup }
$protectionSpec = New-Object VMware.Vimautomation.Srm.Views.SrmProtectionGroupVmProtectionSpec
$protectionSpec.Vm = $vm.ExtensionData.MoRef
 
#protect the VM
$protectTask = $targetProtectionGroup.ProtectVms($protectionSpec)
while (-not $protectTask.IsComplete()) { sleep -Seconds 1 }
 
#Disconnect from vCenter and SRM
Disconnect-SrmServer -Confirm:$false
Disconnect-VIServer -Confirm:$false
```Please notice the "$password =" line in the script above. This reads the encrypted password for the user account from a password file in c:\\orchestrator\\powershell. Passing the password as plain text was not an option. Typing it obviously is impossible if you want to run the script from a vCO workflow. To solve this I store the password encrypted in a file. The password file is created using the script below:```
param (
    \[string\]$username
)
read-host -assecurestring | convertfrom-securestring | out-file $("C:\\orchestrator\\powershell\\" + $username + ".pw")
```This script takes a username as an input. You want to use the UPN here (user@domain). The script doesn't generate any output on the screen. Just start the script, type the password for the user and you're done. Now we want to kick this off from vCO. That's where it gets a little tricky because the script will probably not run under the account you use to develop and test the script. The thing is, the password file that is generated by the script  above is encrypted with a key specific to the use running the script. In other words: we need to run the script under the same account that will be used to run the srm protect script (in my case the vCO service account). Here is how to do it from a windows cmd prompt:```
runas /user:vcoserviceaccount powershell.exe
Powershell> generate-pw-file.ps1 -username vcoserviceaccount@domain

```Now you're ready to protect VMs automatically. I'll explain how to run it from a vCO workflow in another post. For now I'll leave you with an additional script which you'll need to unprotect the VM. Could come in handy when you want to automate the deletion of VMs.```
param (
    \[string\]$vcenter,
    \[string\]$username,
    \[string\]$vmname,
    \[string\]$protgroup
)
 
Add-PSSnapinVMware.VimAutomation.Core
 
# Retrieve password for service account from file and create credential object. this is needed for connect-srm
$password=cat $("C:\\orchestrator\\powershell\\"+$username+".pw") |ConvertTo-SecureString
$credential=new-object-typenameSystem.Management.Automation.PSCredential-ArgumentList$username,$password
 
#connect to vCenter server and get VM object
connect-viserver-Server$vcenter-Credential$credential
$vm=get-vm$vmname
 
#connect to SRM server. Remote AND local credentials are required.
$srm=connect-srmserver-RemoteCredential$credential-Credential$credential
$srmApi=$srm.ExtensionData
 
#Get Protection group
$protectionGroups=$srmApi.Protection.ListProtectionGroups()
$targetProtectionGroup=$protectionGroups|where {$\_.GetInfo().Name -eq$protgroup }
 
#Unprotect the VM
$protectTask=$targetProtectionGroup.UnprotectVms($vm.ExtensionData.MoRef)
while (-not$protectTask.IsComplete()) { sleep-Seconds1 }
 
#Disconnect from vCenter and SRM
Disconnect-SrmServer-Confirm:$false
Disconnect-VIServer-Confirm:$false
```