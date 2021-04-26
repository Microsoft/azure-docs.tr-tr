---
title: Azure PowerShell-VM konağında uçtan uca şifrelemeyi etkinleştirin
description: Konakta şifreleme kullanarak Azure VM 'leriniz için uçtan uca şifrelemeyi etkinleştirme.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f82169c084fc65fd483119bb84f29198ed288019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580326"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Konakta şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirmek için Azure PowerShell modülünü kullanın

Konakta şifrelemeyi etkinleştirdiğinizde, VM konağında depolanan veriler, REST ve depolama hizmetine şifrelenen akışlara şifrelenir. Konakta şifreleme ve diğer yönetilen disk şifreleme türleri hakkında kavramsal bilgiler için bkz. [VM verileriniz için konak-uçtan uca şifreleme](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Desteklenen VM boyutları

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Ayrıca, VM boyutlarını programlı bir şekilde bulabilirsiniz. Programlı olarak nasıl alınacağını öğrenmek için [desteklenen VM boyutlarını bulma](#finding-supported-vm-sizes) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

VM/VMSS 'niz için EncryptionAtHost özelliğini kullanmadan önce aboneliğiniz için özelliği etkinleştirmeniz gerekir. Aboneliğiniz için özelliği etkinleştirmek üzere lütfen aşağıdaki adımları izleyin:

1.  Aboneliğinizi için özelliği kaydetmek üzere aşağıdaki komutu yürütün

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

2.  Özelliği denemeden önce aşağıdaki komutu kullanarak kayıt durumunun kayıtlı olduğundan (birkaç dakika sürer) emin olun.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault ve DiskEncryptionSet oluşturma

Özellik etkinleştirildikten sonra, henüz yapmadıysanız bir Azure Key Vault ve DiskEncryptionSet ayarlamanız gerekir.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>VM ve sanal makine ölçek kümelerine bağlı diskler için konakta şifrelemeyi etkinleştir

Konakta şifrelemeyi etkinleştirerek VM 'Ler için securityProfile veya API sürüm **2020-06-01** ve üstünü kullanarak sanal makine ölçek kümeleri altında yeni bir EncryptionAtHost özelliği ayarlayabilirsiniz.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="examples"></a>Örnekler

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir VM oluşturun. 

Daha önce oluşturulan DiskEncryptionSet 'in Kaynak URI 'sini kullanarak yönetilen disklere sahip bir VM oluşturun ve müşteri tarafından yönetilen anahtarlarla işletim sistemi ve veri diskleri önbelleğini şifreleyin. Geçici diskler, platform tarafından yönetilen anahtarlarla şifrelenir. 

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

# Enable encryption at host by specifying EncryptionAtHost parameter

$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize -EncryptionAtHost
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption with a customer managed key for OS disk by setting DiskEncryptionSetId property 

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

# Add a data disk encrypted with a customer managed key by setting DiskEncryptionSetId property 

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Platform tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir VM oluşturun. 

İşletim sisteminde şifrelemeye sahip bir sanal makine oluşturun ve işletim sistemi/veri disklerinin ve platformlar arası anahtarlarla geçici disklerin önbelleğini şifreleyin. 

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

# Enable encryption at host by specifying EncryptionAtHost parameter

$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize -EncryptionAtHost

$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Konakta şifrelemeyi etkinleştirmek için bir VM 'yi güncelleştirin. 

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMName = "yourVMName"

$VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName

Stop-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName -Force

Update-AzVM -VM $VM -ResourceGroupName $ResourceGroupName -EncryptionAtHost $true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>VM için konaktaki şifreleme durumunu denetleme

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMName = "yourVMName"

$VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName

$VM.SecurityProfile.EncryptionAtHost
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Ana bilgisayarda, müşteri tarafından yönetilen anahtarlarla etkinleştirilmiş bir sanal makine ölçek kümesi oluşturun. 

Daha önce oluşturulan DiskEncryptionSet Kaynak URI 'sini kullanarak yönetilen disklere sahip bir sanal makine ölçek kümesi oluşturun ve müşteri tarafından yönetilen anahtarlarla işletim sistemi ve veri diskleri önbelleğini şifreleyin. Geçici diskler, platform tarafından yönetilen anahtarlarla şifrelenir. 

```powershell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

# Enable encryption at host by specifying EncryptionAtHost parameter

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic' -EncryptionAtHost

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption with a customer managed key for the OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted with a customer managed key by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Platform tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir sanal makine ölçek kümesi oluşturun. 

İşletim sisteminde şifrelemeye sahip bir sanal makine ölçek kümesi oluşturun ve işletim sistemi/veri disklerinin önbelleğini ve platform tarafından yönetilen anahtarlarla geçici diskleri şifreler. 

```powershell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

# Enable encryption at host by specifying EncryptionAtHost parameter

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic' -EncryptionAtHost

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig
 
$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS 

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Konakta şifrelemeyi etkinleştirmek için bir sanal makine ölçek kümesini güncelleştirin. 

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMScaleSetName = "yourVMSSName"

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -Name $VMScaleSetName

Update-AzVmss -VirtualMachineScaleSet $VMSS -Name $VMScaleSetName -ResourceGroupName $ResourceGroupName -EncryptionAtHost $true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi için konaktaki şifreleme durumunu denetle

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMScaleSetName = "yourVMSSName"

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -Name $VMScaleSetName

$VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
```

## <a name="finding-supported-vm-sizes"></a>Desteklenen VM boyutlarını bulma

Eski VM boyutları desteklenmez. Desteklenen VM boyutlarının listesini şunlardan biriyle bulabilirsiniz:

[Kaynak SKU 'larını](/rest/api/compute/resourceskus/list) çağırma ve `EncryptionAtHostSupported` özelliğin **true** olarak ayarlandığını denetleme.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Veya [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell cmdlet 'ini çağırarak.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kaynakları oluşturup yapılandırdığınıza göre, bunları yönetilen disklerinizin güvenliğini sağlamak için kullanabilirsiniz. Aşağıdaki bağlantı, yönetilen disklerinizin güvenliğini sağlamak için kullanabileceğiniz, her biri ilgili senaryoya sahip örnek betikler içerir.

[Azure Resource Manager şablonu örnekleri](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
