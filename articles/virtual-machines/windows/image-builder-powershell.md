---
title: PowerShell kullanarak Azure Image Builder ile Windows VM oluşturma
description: Azure Image Builder PowerShell modülü ile bir Windows sanal makinesi oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693467"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Önizleme: PowerShell kullanarak Azure Image Builder ile Windows VM oluşturma

Bu makalede, Azure VM Image Builder PowerShell modülünü kullanarak özelleştirilmiş bir Windows görüntüsünü nasıl oluşturabileceğiniz gösterilmektedir.

> [!CAUTION]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır. Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır. Üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> **Az. ımagebuilder** ve **az. managedserviceıdentity** PowerShell modülleri önizlemede olduğunda, `Install-Module` cmdlet 'ini parametresiyle birlikte kullanarak bunları ayrı olarak yüklemelisiniz `AllowPrerelease` . Bu PowerShell modülleri genel kullanıma sunulduğunda, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Kayıt özellikleri

Önizleme sırasında Azure Image Builder 'ı ilk kez kullanıyorsanız, yeni **Virtualmachinetemplatepreview** özelliğini kaydedin.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Özellik kaydının durumunu denetleyin.

> [!NOTE]
> **Registrationstate** , üzerinde `Registering` değişiklik yapmadan önce birkaç dakika içinde olabilir `Registered` . Devam etmeden önce durum **kaydoluncaya** kadar bekleyin.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Henüz kayıtlı değilse, Azure aboneliğinizle kullanılmak üzere aşağıdaki kaynak sağlayıcılarını kaydedin.

- Microsoft.Compute
- Microsoft. Keykasası
- Microsoft.Storage
- Microsoft.Network
- Microsoft. Virtualmachineımages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Değişkenleri tanımlama

Birkaç bilgi parçasını sürekli olarak kullanacaksınız. Bilgileri depolamak için değişkenler oluşturun.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Azure abonelik KIMLIĞINIZ için bir değişken oluşturun. `subscriptionID`Değişkenin ABONELIK kimliğinizi içerdiğini doğrulamak için aşağıdaki örnekte ikinci satırı çalıştırabilirsiniz.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, değişkeninde belirtilen bölgedeki ada göre bir kaynak grubu oluşturur `$imageResourceGroup` `$location` . Bu kaynak grubu, görüntü yapılandırma şablonu yapıtı ve görüntüsünü depolamak için kullanılır.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Kullanıcı kimliği oluşturma ve rol izinlerini ayarlama

Aşağıdaki örneği kullanarak belirtilen kaynak grubunda görüntü oluşturmak için Azure Image Builder izinleri verin. Bu izin olmadan görüntü oluşturma işlemi başarıyla tamamlanmaz.

Rol tanımı ve kimlik adları için değişkenler oluşturun. Bu değerler benzersiz olmalıdır.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Kullanıcı Kimliği oluşturun.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Kimlik kaynağını ve asıl kimlikleri değişkenlerde depolayın.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Görüntü dağıtmak için kimlik izinleri atama

. JSON yapılandırma dosyasını indirin ve bu makalede tanımlanan ayarlara göre değiştirin.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Rol tanımını oluşturun.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Görüntü Oluşturucu hizmet sorumlusu için rol tanımına izin verin.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Şu hatayı alırsanız: "_New-AzRoleDefinition: rol tanımı sınırı aşıldı. Başka rol tanımı oluşturulamıyor._", bkz. [Azure RBAC sorunlarını giderme](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Paylaşılan Görüntü Galerisi Oluşturma

Galeri 'yi oluşturun.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Galeri tanımı oluşturun.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Görüntü oluşturma

Azure görüntü Oluşturucu kaynak nesnesi oluşturun. Geçerli parametre değerleri için [Azure PowerShell Azure Marketi 'Nde WINDOWS VM görüntülerini bulma](./cli-ps-findimage.md) bölümüne bakın.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Azure Image Builder dağıtıcı nesnesi oluşturun.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Azure görüntü Oluşturucu özelleştirme nesnesi oluşturun.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

İkinci bir Azure görüntü Oluşturucu özelleştirme nesnesi oluşturun.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Azure görüntü Oluşturucu şablonu oluşturun.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Tamamlandığında bir ileti döndürülür ve içinde bir görüntü Oluşturucu yapılandırma şablonu oluşturulur `$imageResourceGroup` .

Şablon oluşturma işleminin başarılı olup olmadığını anlamak için aşağıdaki örneği kullanabilirsiniz.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Arka planda, görüntü Oluşturucu aynı zamanda aboneliğinizde bir hazırlama kaynak grubu oluşturur. Bu kaynak grubu, görüntü derlemesi için kullanılır. Şu biçimdedir: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Hazırlama kaynak grubunu doğrudan silmeyin. Görüntü şablonu yapıtı silme, bu, hazırlama kaynak grubunun silinmesine neden olur.

Hizmet, görüntü yapılandırma şablonu gönderimi sırasında bir hata bildirirse:

- Bkz. [Azure VM görüntü oluşturma (AıB) hatalarında sorun giderme](../linux/image-builder-troubleshoot.md).
- Yeniden denemeden önce aşağıdaki örneği kullanarak şablonu silin.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Görüntü derlemesini Başlat

Görüntü yapılandırmasını VM görüntü Oluşturucu hizmetine gönderme.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Görüntü oluşturma işleminin tamamlanmasını bekleyin. Bu adım bir saate kadar sürebilir.

Hatalarla karşılaşırsanız, [Azure VM görüntü oluşturma (AıB) hatalarında sorun giderme](../linux/image-builder-troubleshoot.md)konusunu gözden geçirin.

## <a name="create-a-vm"></a>VM oluşturma

VM için oturum açma kimlik bilgilerini bir değişkende depolayın. Parolanın karmaşık olması gerekir.

```azurepowershell-interactive
$Cred = Get-Credential
```

Oluşturduğunuz görüntüyü kullanarak VM 'yi oluşturun.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Özelleştirmeleri doğrulama

VM 'yi oluştururken ayarladığınız Kullanıcı adını ve parolayı kullanarak VM 'ye bir Uzak Masaüstü bağlantısı oluşturun. VM içinde, PowerShell ' i açın ve `Get-Content` Aşağıdaki örnekte gösterildiği gibi çalıştırın:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Görüntü özelleştirme işlemi sırasında oluşturulan dosyanın içeriğine göre çıkış görmeniz gerekir.

```Output
Azure-Image-Builder-Was-Here
```

Aynı PowerShell oturumunda, `c:\buildArtifacts\index.html` Aşağıdaki örnekte gösterildiği gibi, dosyanın varlığını denetleyerek ikinci özelleştirmenin başarıyla tamamlandığını doğrulayın:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Sonuç, görüntü özelleştirme işlemi sırasında indirilen dosyayı gösteren bir dizin listelemesi olmalıdır.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklar gerekmiyorsa, aşağıdaki örnekleri çalıştırarak bunları silebilirsiniz.

### <a name="delete-the-image-builder-template"></a>Görüntü Oluşturucu şablonunu silme

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Görüntü kaynak grubunu sil

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan. json dosyasının bileşenleri hakkında daha fazla bilgi edinmek için bkz. [Görüntü Oluşturucu şablonu başvurusu](../linux/image-builder-json.md).
