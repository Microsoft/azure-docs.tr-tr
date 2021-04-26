---
title: Windows VM 'Leri için bir görüntü Galerisi ile Azure Image Builder kullanma (Önizleme)
description: Azure Image Builder ve Azure PowerShell kullanarak Azure Paylaşılan Galeri görüntüsü sürümlerini oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: e8caf9f742217161c60ce90351989999f18adabb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694096"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Önizleme: bir Windows görüntüsü oluşturun ve paylaşılan bir görüntü galerisine dağıtın 

Bu makale, Azure görüntü Oluşturucu 'Yu nasıl kullanabileceğinizi ve Azure PowerShell [paylaşılan bir görüntü galerisinde](../shared-image-galleries.md)görüntü sürümü oluşturmayı ve sonra görüntüyü küresel olarak dağıtmayı gösterir. Bunu [Azure CLI](../linux/image-builder-gallery.md)kullanarak da yapabilirsiniz.

Görüntüyü yapılandırmak için bir. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şurada: [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Şablonun yerel bir sürümünü indirip düzenlemenizi sağlayacak ve bu makalede yerel PowerShell oturumu kullanılarak yazılmıştır.

Görüntüyü paylaşılan bir görüntü galerisine dağıtmak için şablon, şablon bölümünün [değeri olarak parçalama](../linux/image-builder-json.md#distribute-sharedimage) `distribute` .

Azure görüntü Oluşturucu görüntüyü genelleştirmek için otomatik olarak Sysprep çalıştırır, bu, gerekirse [geçersiz kılabileceğiniz](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) genel bir Sysprep komutu. 

Özelleştirmelerin kaç kez özelleştirmeler olduğunu unutmayın. Sysprep komutunu tek bir Windows görüntüsünde en fazla 8 kez çalıştırabilirsiniz. Sysprep 8 kez çalıştırıldıktan sonra, Windows görüntünüzü yeniden oluşturmanız gerekir. Daha fazla bilgi için bkz. [Sysprep 'i kaç kez çalıştıracağınızı gösteren sınırlamalar](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Özellikleri kaydetme
Önizleme sırasında Azure Image Builder 'ı kullanmak için yeni özelliği kaydetmeniz gerekir.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Özellik kaydının durumunu denetleyin.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

`RegistrationState` `Registered` Sonraki adıma geçmeden önce bekleyin.

Sağlayıcı kayıtlarınızı denetleyin. Her birinin döndürüldüğünden emin olun `Registered` .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Döndürmezse `Registered` , sağlayıcıları kaydetmek için aşağıdakileri kullanın:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

PowerShell modüllerini yükler:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Değişken oluşturma

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız. Ve gibi değişkenlerin değerlerini `username` `vmpassword` kendi bilgileriniz ile değiştirin.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Kullanıcı tarafından atanan bir kimlik oluşturma ve kaynak grubunda izinleri ayarlama
Image Builder, görüntüyü Azure Paylaşılan görüntü galerisine (SıG) eklemek için belirtilen [Kullanıcı kimliğini](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) kullanır. Bu örnekte, görüntüyü SıG 'a dağıtmayı gerçekleştirmeye yönelik ayrıntılı eylemlere sahip bir Azure rol tanımı oluşturacaksınız. Rol tanımı daha sonra kullanıcı kimliğine atanır.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Görüntü dağıtmak için kimlik izinleri atama

Bu komut bir Azure rol tanımı şablonunu indirir ve şablonu daha önce belirtilen parametrelerle güncelleştirir.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Paylaşılan görüntü galerisini oluşturma

Görüntü Oluşturucuyu paylaşılan bir görüntü Galerisi ile birlikte kullanmak için, var olan bir görüntü Galerisi ve görüntü tanımınız olması gerekir. Görüntü Oluşturucu, sizin için görüntü Galerisi ve görüntü tanımı oluşturmaz.

Kullanmak üzere bir galeri ve görüntü tanımınız yoksa, bunları oluşturarak başlayın. İlk olarak, bir görüntü galerisi oluşturun.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Şablonu indirme ve yapılandırma

. JSON şablonunu indirin ve değişkenleriniz ile yapılandırın.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

Şablonunuz hizmete gönderilmesi gerekir, bu komut dosyaları gibi bağımlı yapıtları indirir ve bunları hazırlama kaynak grubunda depolar, *IT_* ön ekine sahip olur.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Şablonu oluşturmak için şablonda ' Run ' komutunu çağırmanız gerekir.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

Görüntünün oluşturulması ve her iki bölgeye çoğaltılmasının biraz zaman alabilir. VM oluşturma işlemine geçmeden önce Bu bölüm bitene kadar bekleyin.

Görüntü oluşturma durumunun otomatikleştirilmesine yönelik seçenekler hakkında bilgi için bkz. [Benioku]
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Azure Image Builder tarafından oluşturulan görüntü sürümünden bir VM oluşturun.

Oluşturduğunuz görüntü sürümünü alın.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Görüntünün çoğaltılan ikinci bölgede sanal makineyi oluşturun.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Özelleştirmeyi doğrulama
VM 'yi oluştururken ayarladığınız Kullanıcı adını ve parolayı kullanarak VM 'ye bir Uzak Masaüstü bağlantısı oluşturun. VM 'nin içinde bir komut istemi açın ve şunu yazın:

```console
dir c:\
```

Görüntü özelleştirmesi sırasında oluşturulmuş adlı bir dizin görmeniz gerekir `buildActions` .


## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık aynı görüntünün yeni bir sürümünü oluşturmak için görüntü sürümünü yeniden özelleştirmeyi denemek istiyorsanız, **Bu adımı atlayın** ve [başka bir görüntü sürümü oluşturmak Için Azure Image Builder 'ı kullanma](image-builder-gallery-update-image-version.md)sayfasına gidin.


Bu işlem, oluşturulan görüntüyü ve diğer tüm kaynak dosyalarını siler. Kaynakları silmeden önce bu dağıtımla bitdiğinizden emin olun.

İlk olarak kaynak grubu şablonunu silin, aksi takdirde AıB tarafından kullanılan hazırlama kaynak grubu (*IT_*) temizlenmeyecektir.

Görüntü şablonunun RESOURCEID 'sini alın. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Görüntü şablonunu silin.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Rol atamasını Sil

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

tanımları kaldır

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Kimliği Sil

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

kaynak grubunu silin.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki Adımlar

Oluşturduğunuz görüntü sürümünü güncelleştirme hakkında bilgi edinmek için bkz. [Azure Image Builder 'ı kullanarak başka bir görüntü sürümü oluşturma](image-builder-gallery-update-image-version.md).
