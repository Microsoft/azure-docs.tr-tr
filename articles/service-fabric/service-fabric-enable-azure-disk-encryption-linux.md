---
title: Azure Service Fabric Linux kümeleri için disk şifrelemeyi etkinleştirme | Microsoft Docs
description: Bu makalede, Azure Resource Manager ve Azure Key Vault kullanarak Linux 'ta Azure Service Fabric küme düğümleri için disk şifrelemenin nasıl etkinleştirileceği açıklanır.
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
ms.openlocfilehash: 5bcfad63df69010851dde66b0c8935e63a509455
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599600"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Linux 'ta Azure Service Fabric küme düğümleri için disk şifrelemeyi etkinleştirme 
> [!div class="op_single_selector"]
> * [Linux için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Bu öğreticide, Linux 'ta Azure Service Fabric küme düğümlerinde disk şifrelemeyi nasıl etkinleştireceğinizi öğreneceksiniz. Düğüm türlerinin ve sanal makine ölçek kümelerinin her biri için bu adımları izlemeniz gerekir. Düğümleri şifrelemek için, sanal makine ölçek kümelerinde Azure Disk Şifrelemesi özelliğini kullanacağız.

Kılavuzda aşağıdaki konular ele alınmaktadır:

* Linux 'ta Service Fabric kümesi sanal makine ölçek kümelerinde disk şifrelemeyi etkinleştirirken dikkat etmeniz için önemli kavramlar.
* Linux 'ta Service Fabric küme düğümlerinde disk Şifrelemeyi etkinleştirmeden önce izlenen adımlar.
* Linux 'ta Service Fabric küme düğümlerinde disk şifrelemeyi etkinleştirmek için izlenecek adımlar.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

 **Kendi kendine kayıt**

Sanal makine ölçek kümesi için disk şifreleme önizlemesi, kendi kendine kayıt gerektirir. Aşağıdaki adımları kullanın:

1. Şu komutu çalıştırın: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Durum, *kayıtlı*olana kadar yaklaşık 10 dakika bekleyin. Şu komutu çalıştırarak durumu kontrol edebilirsiniz:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Anahtar Kasası.**

1. Ölçek kümesiyle aynı abonelikte ve bölgede bir Anahtar Kasası oluşturun. Ardından, PowerShell cmdlet 'ini kullanarak anahtar kasasında **Enabledfordiskencryption** erişim ilkesini seçin. İlkeyi aşağıdaki komutla Azure portal Key Vault Kullanıcı arabirimini kullanarak da ayarlayabilirsiniz:
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
* Hem şifreleme hem de devre dışı şifreleme, Linux 'ta sanal makine ölçek kümelerinde işletim sistemi ve veri birimlerinde desteklenir. 
* Sanal Makine Ölçek Kümeleri için sanal makine (VM) yeniden görüntüsü ve yükseltme işlemleri geçerli önizlemede desteklenmiyor.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Yeni bir küme oluşturun ve disk şifrelemeyi etkinleştirin

Bir küme oluşturmak ve bir Azure Resource Manager şablonu ve kendinden imzalı bir sertifika kullanarak disk şifrelemeyi etkinleştirmek için aşağıdaki komutları kullanın.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma  

Aşağıdaki komutlarla oturum açın:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Zaten sahip olduğunuz özel şablonu kullanın 

Özel bir şablon yazmak gerekirse, [Azure Service Fabric kümesi oluşturma şablonu örnekleri](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) sayfasındaki şablonlardan birini kullanmanızı önemle tavsiye ederiz. 

Zaten özel bir şablonunuz varsa, şablondaki ve parametre dosyasının sertifikayla ilgili üç parametrenin tümünün aşağıdaki gibi adlandırıldığını iki kez kontrol edin. Ayrıca değerlerinin null olduğundan emin olun:

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

Linux 'ta sanal makine ölçek kümeleri için yalnızca veri diski şifrelemesi desteklendiğinden, bir Kaynak Yöneticisi şablonu kullanarak bir veri diski eklemeniz gerekir. Veri diski sağlama şablonunuzu aşağıdaki gibi güncelleştirin:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

Eşdeğer CLı komutu aşağıda verilmiştir. Declare deyimlerinin değerlerini uygun değerlerle değiştirin. CLı, önceki PowerShell komutunun desteklediği tüm diğer parametreleri destekler.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Bir Linux örneğine veri diski bağlama
Bir sanal makine ölçek kümesi üzerinde şifrelemeye devam etmeden önce, eklenen veri diskinin doğru bağlandığından emin olun. Linux kümesi VM 'de oturum açın ve **Lsblk** komutunu çalıştırın. Çıktı, **bağlama noktası** sütununda eklenen veri diskini göstermelidir.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Linux 'ta bir Service Fabric kümesine uygulama dağıtma
Kümenize bir uygulama dağıtmak için hızlı başlangıç aşamasında [adımları ve yönergeleri izleyin: Service Fabric](service-fabric-quickstart-containers-linux.md)için Linux kapsayıcıları dağıtın.


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Linux 'ta bir sanal makine ölçek kümesi için disk şifrelemenin etkinleştirilip etkinleştirilmediğini doğrulayın
Bir sanal makine ölçek kümesinin tamamının veya bir ölçek kümesindeki herhangi bir örneğinin durumunu almak için aşağıdaki komutları çalıştırın.
Ayrıca, Linux kümesi VM 'de oturum açabilir ve **Lsblk** komutunu çalıştırabilirsiniz. Çıktı, eklenen veri diskini **bağlama noktası** sütununda göstermelidir ve **tür** sütunu *Crypt*' i okumalı.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric kümesinde bir sanal makine ölçek kümesi için disk şifrelemeyi devre dışı bırakma
Aşağıdaki komutları çalıştırarak bir sanal makine ölçek kümesi için disk şifrelemeyi devre dışı bırakın. Disk şifrelemesini devre dışı bırakmak, tek bir örnek değil, tüm sanal makine ölçek kümesi için geçerli olduğunu unutmayın.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Sonraki adımlar
Bu noktada, güvenli bir kümeniz olmalıdır ve Service Fabric küme düğümleri ve sanal makine ölçek kümeleri için disk şifrelemeyi etkinleştirmeyi ve devre dışı bırakmayı bilmeniz gerekir. Linux 'ta Service Fabric küme düğümleri hakkında benzer yönergeler için bkz. [Windows Için disk şifrelemesi](service-fabric-enable-azure-disk-encryption-windows.md). 
