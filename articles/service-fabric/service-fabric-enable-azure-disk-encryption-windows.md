---
title: Azure Service Fabric Windows kümeleri için disk şifrelemeyi etkinleştirme | Microsoft Docs
description: Bu makalede, Azure Resource Manager Azure Key Vault kullanılarak Azure Service Fabric küme düğümleri için disk şifrelemenin nasıl etkinleştirileceği açıklanır.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: atsenthi
ms.openlocfilehash: 64abc48d57196fe20466032652c4b9bfb2e6c71f
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599535"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Windows 'da Azure Service Fabric küme düğümleri için disk şifrelemeyi etkinleştirme 
> [!div class="op_single_selector"]
> * [Windows için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Bu öğreticide, Windows 'da Service Fabric küme düğümlerinde disk şifrelemeyi nasıl etkinleştireceğinizi öğreneceksiniz. Düğüm türlerinin ve sanal makine ölçek kümelerinin her biri için bu adımları izlemeniz gerekir. Düğümleri şifrelemek için, sanal makine ölçek kümelerinde Azure Disk Şifrelemesi özelliğini kullanacağız.

Kılavuzda aşağıdaki konular ele alınmaktadır:

* Windows 'daki Service Fabric küme sanal makine ölçek kümelerinde disk şifrelemeyi etkinleştirirken dikkat etmeniz için önemli kavramlar.
* Windows 'daki Service Fabric küme düğümlerinde disk Şifrelemeyi etkinleştirmeden önce izlenen adımlar.
* Windows 'daki Service Fabric küme düğümlerinde disk şifrelemeyi etkinleştirmek için izlenecek adımlar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

**Kendi kendine kayıt** 

Sanal makine ölçek kümesi için disk şifreleme önizlemesi, kendi kendine kayıt gerektirir. Aşağıdaki adımları kullanın: 

1. İlk olarak, aşağıdaki komutu çalıştırın:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Durum, *kayıtlı*olana kadar yaklaşık 10 dakika bekleyin. Şu komutu çalıştırarak durumu kontrol edebilirsiniz: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Anahtar Kasası.** 

1. Ölçek kümesiyle aynı abonelikte ve bölgede bir Anahtar Kasası oluşturun, ardından PowerShell cmdlet 'ini kullanarak anahtar kasasında **Enabledfordiskencryption** erişim ilkesini seçin. İlkeyi aşağıdaki komutla Azure portal Key Vault Kullanıcı arabirimini kullanarak da ayarlayabilirsiniz:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Yeni şifreleme komutlarına sahip olan [Azure CLI](/cli/azure/install-azure-cli)'nin en son sürümünü yükler.
3. [Azure PowerShell sürümden Azure SDK 'sının](https://github.com/Azure/azure-powershell/releases) en son sürümünü yükler. Aşağıda, sanal makine ölçek kümesi Azure disk şifrelemesi cmdlet 'leri tarafından etkinleştirilir ([ayarlanır](/powershell/module/az.compute/set-azvmssdiskencryptionextension)), şifreleme durumunu alabilir[](/powershell/module/az.compute/get-azvmssvmdiskencryption)ve ölçek kümesi örneğindeki şifrelemeyi kaldırır ([devre dışı](/powershell/module/az.compute/disable-azvmssdiskencryption)).

| Komut | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 veya üzeri | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 veya üzeri | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 veya üzeri | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 veya üzeri | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 veya üzeri | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 veya üzeri | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Disk şifrelemesi için desteklenen senaryolar
* Sanal Makine Ölçek Kümeleri için şifreleme yalnızca yönetilen disklerle oluşturulan ölçek kümeleri için desteklenir. Yerel (veya yönetilmeyen) disk ölçek kümeleri için desteklenmez.
* Şifreleme, Windows 'daki sanal makine ölçek kümelerinde işletim sistemi ve veri birimleri için desteklenir. Windows 'daki sanal makine ölçek kümeleri için işletim sistemi ve veri birimlerinde şifrelemeyi devre dışı bırak işlemi de desteklenir.
* Sanal Makine Ölçek Kümeleri için sanal makine yeniden görüntüsü ve yükseltme işlemleri geçerli önizlemede desteklenmiyor.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Yeni bir küme oluşturun ve disk şifrelemeyi etkinleştirin

Bir küme oluşturmak ve bir Azure Resource Manager şablonu ve kendinden imzalı bir sertifika kullanarak disk şifrelemeyi etkinleştirmek için aşağıdaki komutları kullanın.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma 
Aşağıdaki komutlarla oturum açın:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Zaten sahip olduğunuz özel şablonu kullanın 

Gereksinimlerinize uyacak özel bir şablon yazmak gerekirse, [Azure Service Fabric kümesi oluşturma şablonu örnekleri](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) sayfasında bulunan şablonlardan biriyle başlamanız önemle tavsiye ederiz. [Küme şablonu bölümünü özelleştirmek][customize-your-cluster-template] için aşağıdaki kılavuza bakın.

Zaten özel bir şablonunuz varsa, şablondaki ve parametre dosyasının sertifikayla ilgili üç parametrenin tümünün aşağıdaki gibi adlandırıldığını ve değerlerin null olduğunu aşağıdaki gibi denetleyin:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Windows 'da bir Service Fabric kümesine uygulama dağıtma
Kümenize bir uygulama dağıtmak için [PowerShell kullanarak uygulamaları dağıtma ve kaldırma](service-fabric-deploy-remove-applications.md)bölümündeki adımları ve yönergeleri izleyin.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Daha önce oluşturulan sanal makine ölçek kümeleri için disk şifrelemeyi etkinleştir

Önceki adımlar aracılığıyla oluşturduğunuz sanal makine ölçek kümeleri için disk şifrelemeyi etkinleştirmek üzere aşağıdaki komutları çalıştırın:
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Windows 'da bir sanal makine ölçek kümesi için disk şifrelemenin etkinleştirilip etkinleştirilmediğini doğrulama
Aşağıdaki komutları çalıştırarak bir sanal makine ölçek kümesinin tamamının veya bir ölçek kümesindeki herhangi bir örneğinin durumunu alın.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Ayrıca, sanal makine ölçek kümesinde oturum açabilir ve sürücülerin şifrelendiğinden emin olabilirsiniz.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric kümesinde bir sanal makine ölçek kümesi için disk şifrelemeyi devre dışı bırakma 
Aşağıdaki komutları çalıştırarak bir sanal makine ölçek kümesi için disk şifrelemeyi devre dışı bırakın. Disk şifrelemesini devre dışı bırakmak, tek bir örnek değil, tüm sanal makine ölçek kümesi için geçerli olduğunu unutmayın.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Sonraki adımlar
Bu noktada, güvenli bir kümeniz olmalıdır ve Service Fabric küme düğümleri ve sanal makine ölçek kümeleri için disk şifrelemeyi etkinleştirmeyi ve devre dışı bırakmayı bilmeniz gerekir. Linux 'ta Service Fabric küme düğümleri hakkında benzer yönergeler için bkz. [Linux Için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
