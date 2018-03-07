$rgname = "aya"

$KeyVaultName = "kkk"

$keyEncryptionKeyName = "lsckek"

$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid

$vmname = "rdfewin2012r2"

$KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;

$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;

$KeyVaultResourceId = $KeyVault.ResourceId;

$vaultsecret = Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {($_.Tags.MachineName -eq $vmName)} 

$secrtname = $vaultsecret.Name

$sec = Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secrtname

$outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rgname -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

   "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }




$nic = New-AzureRmNetworkInterface -Name ANMDBServerBK-nic -ResourceGroupName anm-prod -Location centralindia -SubnetId "/subscriptions/0593017f-f4c6-4220-973f-0d4de3849e39/resourceGroups/network-mn-prod/providers/Microsoft.Network/virtualNetworks/shared-ic-prod2-vnet/subnets/gdc_apps_subnet"
#Create the basic configuration for the replacement VM
$newVM = New-AzureRmVMConfig -VMName ANMDBServerBK -VMSize Standard_DS13_v2 
$newvm = Add-AzureRmVMNetworkInterface -VM $newvm -Id $nic.Id

Set-AzureRmVMOSDisk -VM $newVM -VhdUri "https://gsdprodstorage.blob.core.windows.net/vhd500294a664724cc184ca028930424298/bbab51c82d1c241efb708b2fa3c075867.vhd" -Name ANMDBServerBK-os -CreateOption Attach -Linux -DiskEncryptionKeyUrl $sec.id -DiskEncryptionKeyVaultId $keyvault.resourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
Add-AzureRmVMDataDisk -VM $newVM -VhdUri "https://gsdprodstorage.blob.core.windows.net/vhd500294a664724cc184ca028930424298/b8d092bcb2df94d919d0c30fb3f6115cd000.vhd"
#Create the VM
New-AzureRmVM -ResourceGroupName $rgname -Location centralindia -VM $NewVM -DisableBginfoExtension 


