---
title: Image Builder-Windows sanal masaüstü görüntüsü oluşturma
description: PowerShell 'de Azure Image Builder kullanarak Windows sanal masaüstü 'nün Azure VM görüntüsünü oluşturun.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 69718b219d239ac13e5d932b05a7dd29619adaa3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045595"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Azure VM Image Builder ve PowerShell kullanarak Windows sanal masaüstü görüntüsü oluşturma

Bu makalede, bu özelleştirmelerle bir Windows sanal masaüstü görüntüsünün nasıl oluşturulacağı gösterilmektedir:

* [Fslogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)yükleniyor.
* Topluluk deposundan bir [Windows sanal masaüstü iyileştirmesi betiği](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) çalıştırma.
* [Microsoft ekipleri](../../virtual-desktop/teams-on-wvd.md)'nı yükler.
* [Yeniden başlat](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* [Windows Update](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer) Çalıştır

Bunu Azure VM görüntü Oluşturucu kullanarak nasıl otomatikleştirebileceğinizi ve görüntüyü diğer bölgelere çoğaltabileceğiniz, ölçeği denetleyebildiğiniz ve kuruluşunuzun içindeki ve dışındaki görüntüyü paylaştığınız [paylaşılan bir görüntü galerisine](../shared-image-galleries.md)nasıl dağıtacağınızı göstereceğiz.


Bu örnek, bir görüntü Oluşturucu yapılandırmasını dağıtmak için, içinde iç içe geçmiş görüntü Oluşturucu şablonuyla bir Azure Resource Manager şablonu kullanır. Bu, değişkenler ve parametre girişleri gibi bazı diğer avantajlar sağlar. Ayrıca komut satırından parametreleri geçirebilirsiniz.

Bu makalenin bir kopyalama ve yapıştırma alıştırması olması amaçlanmıştır.

> [!NOTE]
> Uygulamaları yüklemek için betikler [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD)' da bulunur. Bunlar, üretim iş yükleri için değil yalnızca çizim ve test amaçlıdır. 

## <a name="tips-for-building-windows-images"></a>Windows görüntülerini oluşturmaya yönelik ipuçları 

- VM boyutu-varsayılan VM boyutu, `Standard_D1_v2` Windows için uygun olmayan bir ' dır. `Standard_D2_v2`Ya da daha fazlasını kullanın.
- Bu örnek, [PowerShell Özelleştirici betiklerini](../linux/image-builder-json.md)kullanır. Bu ayarları kullanmanız gerekir, aksi olarak derleme yanıt vermeyi durdurur.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Örnek:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Kodunuzda yorum yapın-ııB derleme günlüğü (özelleştirme. log) son derece ayrıntılıdır. Bu, betiklerinizi ' yazma-ana bilgisayar ' kullanarak açıklamadıysanız bunlar günlüklere gönderilir ve sorun giderme daha kolay olur.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Çıkış kodları-AıB, tüm betiklerin 0 çıkış kodu döndürmesini bekler, sıfır olmayan çıkış kodu, özelleştirmeye ve derlemeyi durdurmasına neden olur. Karmaşık betikleriniz varsa, izleme ekleme ve çıkış kodlarını yayma, bunlar özelleştirme. log dosyasında gösterilir.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test: Lütfen bağımsız bir VM 'de kodunuzu test edin ve test edin, hiçbir Kullanıcı istemi olmadığından emin olun, doğru ayrıcalığa vb. kullandığınızı unutmayın.

- Ağ- `Set-NetAdapterAdvancedProperty` . Bu en iyi duruma getirme betiği içinde ayarlanmakta, ancak ağın bağlantısını kestiğinden, AıB derlemesi başarısız olur. Araştırma aşamasındadır.

## <a name="prerequisites"></a>Önkoşullar

En son Azure PowerShell cmdlet 'Lerinin yüklü olması gerekir, Yükleme ayrıntıları için [buraya](/powershell/azure/overview) bakın.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Ortam ve değişkenleri ayarlama

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>İzinler, Kullanıcı kimliği ve rol 


 Kullanıcı Kimliği oluşturun.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Görüntüleri dağıtmak için kimliğe izinler atayın. Bu komut, daha önce belirtilen parametrelerle şablonu indirip güncelleştirecek.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Şu hatayı görürseniz: ' New-AzRoleDefinition: rol tanımı sınırı aşıldı. Başka rol tanımı oluşturulamıyor. ' çözümlemek için şu makaleye bakın: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Paylaşılan görüntü galerisini oluşturma 

Zaten paylaşılan bir görüntü galeriniz yoksa, bir tane oluşturmanız gerekir.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Görüntü şablonunu yapılandırma

Bu örnek için, şablonu indirip daha önce belirtilen parametrelerle güncelleştirecek olan bir şablonumuz, FsLogix, işletim sistemi iyileştirmeleri, Microsoft ekipleri ve son Windows Update çalıştırma işlemlerini yükleyecek.

Bu şablonu açarsanız, kullanılmakta olan görüntünün kaynak özelliğinde görebilirsiniz. Bu örnekte, bir Win 10 çoklu oturum görüntüsü kullanılmaktadır. 

### <a name="windows-10-images"></a>Windows 10 görüntüleri
Bilmeniz gereken iki anahtar türü: çok oturumlu ve tek oturum.

Çoklu oturum görüntüleri havuza alınmış kullanım için tasarlanmıştır. Azure 'daki görüntü ayrıntılarına bir örnek aşağıda verilmiştir:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Tek oturum görüntüleri, bireysel kullanım için tasarlanmıştır. Azure 'daki görüntü ayrıntılarına bir örnek aşağıda verilmiştir:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Ayrıca kullanılabilir win10 görüntülerini da değiştirebilirsiniz:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Şablonu indir ve Yapılandır

Şimdi, şablonu indirmeniz ve kullanım için yapılandırmanız gerekir.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

[Şablonu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)görüntülemeniz ücretsizdir, tüm kod görüntülenebilir.


## <a name="submit-the-template"></a>Şablonu gönderme

Şablonunuz hizmete gönderilmesi gerekir, bu, bağımlı yapıtları indirir (betikler gibi), doğrular, izinleri denetler ve bu kaynakları hazırlama kaynak grubunda, ön eki olan *IT_* depolar.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Görüntü oluşturma
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Komut, görüntü Oluşturucu hizmetinin görüntü derlemesini tamamlamasını beklemez, aşağıdaki durumu sorgulayabilirsiniz.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>VM oluşturma
Artık derleme tamamlanmıştır, görüntüden bir VM oluşturabilirsiniz, [buradan](/powershell/module/az.compute/new-azvm#examples)örnekleri kullanın.

## <a name="clean-up"></a>Temizleme

İlk olarak kaynak grubu şablonunu silin, yalnızca kaynak grubunun tamamını silmeyin, aksi takdirde AıB tarafından kullanılan hazırlama kaynak grubu (*IT_*) temizlenmeyecektir.

Görüntü şablonunu kaldırın.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Rol atamasını silin.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Kaynak grubunu silin.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

[GitHub 'da](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)daha fazla örnek deneyebilirsiniz.