# Deployment
	Install-Modue Az
	Import-Module Az
	Connect -AzAccount
New-AzVm `
>>     -ResourceGroupName 'FWAUERG' `
>>     -Name 'FWAUEVM1' `
>>     -Location 'Australia East' `
>>     -VirtualNetworkName 'FWAUEVNET1' `
>>     -SubnetName 'FW-Subnet1' `
>>     -Image 'Win2019Datacenter' `
>>     -Size 'Standard_DS1_v2' `
>>     -SecurityGroupName 'FWAUE-NSG' `
>>     -PublicIpAddressName 'FW-PIP' `
>>     -OpenPorts 443,3389
>>     #Register Recovery Services
>>      Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
>>     #Creates Backup Vault 
>>      New-AzRecoveryServicesVault `
>>     -ResourceGroupName "FWAUERG" `
>>     -Name "FWRecoveryServicesVault" `
>> -Location "Australia East"
>>  #Set Context
>>  Get-AzRecoveryServicesVault `
>>     -Name "FWRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
>>     # Assign the FW-BackupPolicy to the variable
# Have Created a new Backup Policy to have Daily, Weekly and monthly backups called "FW-BackupPolicy"
PS C:\Windows\system32> $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "FW-BackupPolicy"
$policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "FW-BackupPolicy"
#Enables Backup
PS C:\Windows\system32> Enable-AzRecoveryServicesBackupProtection `
>>     -ResourceGroupName "FWAUERG" `
>>     -Name "FWAUEVM1" `
>>     -Policy $policy
>>     
>>     #Triggers Backup
>>     $backupcontainer = Get-AzRecoveryServicesBackupContainer `
>>     -ContainerType "AzureVM" `
>>     -FriendlyName "FWAUEVM1"
PS C:\Windows\system32> $item = Get-AzRecoveryServicesBackupItem `
>>     -Container $backupcontainer `
>>     -WorkloadType "AzureVM"

# Monitors the Backup Job
>>      Get-AzRecoveryservicesBackupJob
