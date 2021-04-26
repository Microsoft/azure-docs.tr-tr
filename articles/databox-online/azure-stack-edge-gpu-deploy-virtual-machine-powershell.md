---
title: Azure PowerShell aracılığıyla Azure Stack Edge cihazınızda VM 'Leri dağıtma
description: Azure PowerShell kullanarak Azure Stack Edge cihazında sanal makinelerin nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 90925f30e84ec3c06e715669ebd982c823dfaf5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568333"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Azure PowerShell aracılığıyla Azure Stack Edge cihazınızda VM 'Leri dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure PowerShell kullanarak Azure Stack Edge cihazınızda bir sanal makinenin (VM) nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. Bilgiler, GPU (grafik işleme birimi), Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazlarında Azure Stack Edge Pro için geçerlidir.

## <a name="vm-deployment-workflow"></a>VM dağıtımı iş akışı

Dağıtım iş akışı aşağıdaki diyagramda görüntülenir:

![VM dağıtımı iş akışının diyagramı.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Cihazdaki yerleşik bir aboneliği sorgulama

Azure Resource Manager için, yalnızca Kullanıcı tarafından görülebilen tek bir sabit abonelik desteklenir. Bu abonelik cihaz başına benzersizdir ve abonelik adı ve abonelik KIMLIĞI değiştirilemez.

Abonelik, VM oluşturma için gerekli tüm kaynakları içerir. 

> [!IMPORTANT]
> Abonelik, Azure portal VM 'Leri etkinleştirdiğinizde oluşturulur ve yerel olarak cihazınızda bulunur.

Abonelik, VM 'Leri dağıtmak için kullanılır.

1.  Aboneliği listelemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    İşte örnek bir çıktı:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Cihazda çalışan kayıtlı kaynak sağlayıcılarının bir listesini alın. Listede normalde işlem, ağ ve depolama alanı bulunur.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Kaynak sağlayıcıları önceden kaydedilir ve değiştirilemez veya değiştirilemez.
    
    İşte örnek bir çıktı:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) ile yeni bir Azure kaynak grubu oluşturun. Kaynak grubu, depolama hesabı, disk ve yönetilen disk gibi Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

> [!IMPORTANT]
> Tüm kaynaklar cihazdaki aynı konumda oluşturulur ve konum **Dbelocal** olarak ayarlanır.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

İşte örnek bir çıktı:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Önceki adımda oluşturduğunuz kaynak grubunu kullanarak yeni bir depolama hesabı oluşturun. Bu, sanal makıne için sanal disk görüntüsünü karşıya yüklemek için kullandığınız yerel bir depolama hesabıdır.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager kullanarak, yerel olarak yedekli depolama (Standart veya Premium) gibi yalnızca yerel depolama hesapları oluşturabilirsiniz. Katmanlı depolama hesapları oluşturmak için bkz. [öğretici: Azure Stack Edge Pro ile depolama hesapları aracılığıyla veri aktarma GPU 'su](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

İşte örnek bir çıktı:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Depolama hesabı anahtarını almak için `Get-AzureRmStorageAccountKey` komutunu çalıştırın. İşte örnek bir çıktı:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Blob URI 'sini ana bilgisayar dosyasına ekleyin

Blob URI 'sini, Azure Blob depolama alanına bağlanmak için kullandığınız istemcinin Hosts dosyasına, [Azure PowerShell aracılığıyla Azure Stack Edge cihazınızda VM 'Leri dağıtma](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)' nın "Adım 5: uç nokta adı çözümlemesi için konak dosyasını değiştirme" bölümünde zaten eklemiş olursunuz. Bu giriş, blob URI 'sini eklemek için kullanıldı:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\>\<dnsdomain\>

## <a name="install-certificates"></a>Sertifikaları yükleme

HTTPS kullanıyorsanız, cihazınıza uygun sertifikaları yüklemeniz gerekir. Burada, blob uç noktası sertifikasını yüklersiniz. Daha fazla bilgi için bkz. [Azure Stack Edge Pro ile SERTIFIKALARı GPU cihazlarıyla kullanma](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>VHD’yi karşıya yükleme

Daha önce oluşturduğunuz yerel depolama hesabındaki sayfa Bloblarında kullanılacak tüm disk görüntülerini kopyalayın. Sanal sabit diski (VHD) depolama hesabına yüklemek için [AzCopy](../storage/common/storage-use-azcopy-v10.md) gibi bir araç kullanabilirsiniz. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
AzCopy 10 ile aşağıdaki komutları kullanın:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

İşte örnek bir çıktı: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>VHD 'den yönetilen disk oluşturma

Karşıya yüklenen VHD 'den yönetilen bir disk oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
İşte örnek bir çıktı: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

İşte örnek bir çıktı. Bu cmdlet hakkında daha fazla bilgi için, bkz. [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Görüntü yönetilen diskinden bir VM görüntüsü oluşturma

Yönetilen diskten bir VM görüntüsü oluşturmak için aşağıdaki komutu çalıştırın. *\<Disk name>*, *\<OS type>* Ve *\<Disk size>* değerlerini gerçek değerlerle değiştirin.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

İşte örnek bir çıktı. Bu cmdlet hakkında daha fazla bilgi için, bkz. [New-Azurermımage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>VM 'nizi önceden oluşturulmuş kaynaklarla oluşturma

VM 'yi oluşturup dağıtmadan önce bir sanal ağ oluşturmanız ve bir sanal ağ arabirimini onunla ilişkilendirmeniz gerekir.

> [!IMPORTANT]
> Aşağıdaki kurallar geçerlidir:
> - Kaynak grupları arasında bile yalnızca bir sanal ağ oluşturabilirsiniz. Sanal ağın tam olarak mantıksal ağla aynı adres alanı olmalıdır.
> - Sanal ağ yalnızca bir alt ağa sahip olabilir. Alt ağ, sanal ağ ile tam olarak aynı adres alanına sahip olmalıdır.
> - Sanal ağ arabirim kartını oluştururken, yalnızca statik ayırma yöntemini kullanabilirsiniz. Kullanıcının özel bir IP adresi sağlaması gerekir.

### <a name="query-the-automatically-created-virtual-network"></a>Otomatik olarak oluşturulan sanal ağı sorgula

Cihazınızın yerel kullanıcı arabiriminden işlem etkinleştirdiğinizde, adlı bir sanal ağ `ASEVNET` , kaynak grubu altında otomatik olarak oluşturulur `ASERG` . 

Var olan sanal ağı sorgulamak için aşağıdaki komutu kullanın:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Sanal ağ arabirimi kartı oluşturma

Sanal ağ alt ağ KIMLIĞINI kullanarak bir sanal ağ arabirim kartı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

İşte örnek bir çıktı:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

İsteğe bağlı olarak, bir VM için bir sanal ağ arabirim kartı oluştururken genel IP 'yi geçirebilirsiniz. Bu örnekte, genel IP özel IP 'yi döndürür. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>VM oluşturma

Artık VM görüntüsünü kullanarak bir VM oluşturabilir ve daha önce oluşturduğunuz sanal ağa iliştirebilirsiniz.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

VM 'yi oluşturup etkinleştirdikten sonra, oturum açmak için aşağıdaki kullanıcı adını ve parolayı kullanacaksınız.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

Bir Windows VM veya Linux sanal makinesi oluşturup oluşturdığınıza bağlı olarak, bağlantı yönergeleri farklı olabilir.

### <a name="connect-to-a-linux-vm"></a>Linux VM'ye bağlanma

Bir Linux sanal makinesine bağlanmak için aşağıdakileri yapın:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Windows VM'ye bağlanma

Bir Windows sanal makinesine bağlanmak için aşağıdakileri yapın:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>VM 'yi yönetme

Aşağıdaki bölümlerde Azure Stack Edge Pro cihazınızda oluşturabileceğiniz bazı yaygın işlemler açıklanır.

### <a name="list-vms-that-are-running-on-the-device"></a>Cihazda çalışan VM 'Leri listeleyin

Azure Stack Edge cihazınızda çalışan tüm VM 'lerin bir listesini döndürmek için şu komutu çalıştırın:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>VM 'yi aç

Cihazınızda çalışan bir sanal makineyi açmak için aşağıdaki cmdlet 'i çalıştırın:

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Bu cmdlet hakkında daha fazla bilgi için bkz. [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>VM 'yi askıya alma veya kapatma

Cihazınızda çalışan bir sanal makineyi durdurmak veya kapatmak için aşağıdaki cmdlet 'i çalıştırın:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Bu cmdlet hakkında daha fazla bilgi için bkz. [stop-AzureRmVM cmdlet 'i](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Veri diski ekleme

VM 'nizin iş yükü gereksinimleri artdıkça bir veri diski eklemeniz gerekebilir. Bunun için aşağıdaki komutu çalıştırın:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>VM’yi silin

Bir sanal makineyi cihazınızdan kaldırmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Bu cmdlet hakkında daha fazla bilgi için bkz. [Remove-AzureRmVm cmdlet 'i](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Resource Manager cmdlet 'leri](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)