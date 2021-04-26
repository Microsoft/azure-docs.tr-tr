---
title: Service Fabric yönetilen küme (Önizleme) düğümleri için disk şifrelemeyi etkinleştir
description: ARM şablonunu kullanarak Windows 'da Azure Service Fabric yönetilen küme düğümleri için disk şifrelemeyi etkinleştirmeyi öğrenin.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100642585"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Service Fabric yönetilen küme (Önizleme) düğümleri için disk şifrelemeyi etkinleştir

Bu kılavuzda, [Sanal Makine Ölçek Kümeleri](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) için Azure Resource Manager (ARM) şablonları aracılığıyla [Azure disk şifrelemesi](../virtual-machines/windows/disk-encryption-overview.md) özelliğini kullanarak Windows 'daki Service Fabric yönetilen küme düğümlerinde disk şifrelemeyi nasıl etkinleştireceğinizi öğreneceksiniz.

> [!IMPORTANT]
> Sanal makine ölçek kümesi disk şifrelemesi önizlemesi, görüntü yükseltmesini veya yeniden görüntüsünü desteklememektedir. İşletim sistemi görüntünüzü yükseltmeniz gerekiyorsa kullanmayın.

## <a name="register-for-azure-disk-encryption"></a>Azure disk şifrelemesi için kaydolun

Sanal makine ölçek kümesi için disk şifreleme önizlemesi, kendi kendine kayıt gerektirir. Şu komutu çalıştırın:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Şunu çalıştırarak kaydın durumunu denetleyin:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Durum *kayıtlı* olarak değiştiğinde devam etmeye hazırsınız demektir.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Disk şifrelemesi için Key Vault sağlama

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için bir Azure Key Vault gerektirir. Key Vault ve Service Fabric yönetilen kümeniz aynı Azure bölgesinde ve abonelikte bulunmalıdır. Bu gereksinimler karşılandığında, disk şifrelemesi için etkinleştirerek yeni veya mevcut bir Key Vault kullanabilirsiniz.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Disk şifrelemesi etkinken Key Vault oluşturma

Disk şifrelemesi için yeni bir Key Vault oluşturmak üzere aşağıdaki komutları çalıştırın. Key Vault bölgesinin [Service Fabric yönetilen kümeler için desteklendiğinden](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) ve kümenizle aynı bölgede bulunduğundan emin olun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Disk şifrelemeyi etkinleştirmek için mevcut Key Vault güncelleştirin

Mevcut bir Key Vault disk şifrelemeyi etkinleştirmek için aşağıdaki komutları çalıştırın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Disk şifrelemesi için şablon ve parametre dosyalarını güncelleştirme

Aşağıdaki adım, [var olan bir yönetilen kümede](tutorial-managed-cluster-deploy.md)disk şifrelemeyi etkinleştirmek için gerekli şablon değişiklikleri boyunca size yol gösterir. Alternatif olarak, bu şablonla etkinleştirilmiş yeni Service Fabric yönetilen bir kümeyi, disk şifrelemesi etkin bir şekilde dağıtabilirsiniz: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Aşağıdaki parametreleri, şablonuna, kendi aboneliğinizi, kaynak grubu adını ve kasaadınızı şu koşullarda ekleyin `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Sonra, `AzureDiskEncryption` şablondaki yönetilen küme düğümü türlerine VM uzantısını ekleyin:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Son olarak, anahtar *Vaultresourceıd* içindeki kendi aboneliğinizi, kaynak grubunuzu ve Anahtar Kasası adını değiştirerek parametreler dosyasını güncelleştirin:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Değişiklikleri dağıtın ve doğrulayın

Hazırladıktan sonra, yönetilen kümenizde disk şifrelemeyi etkinleştirmek için değişiklikleri dağıtın.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Komut kullanarak, düğüm türünün temel alınan ölçek kümesinde disk şifreleme durumunu kontrol edebilirsiniz `Get-AzVmssDiskEncryption` . İlk olarak, yönetilen kümenizin destekleyici kaynak grubunun adını (temeldeki sanal ağı, yük dengeleyiciyi, genel IP, NSG, ölçek kümesi ve depolama hesaplarını içeren) bulmanız gerekir. `VmssName`Denetlemek istediğiniz herhangi bir küme düğümü türü adını (dağıtım şablonunuzda belirtildiği gibi) değiştirdiğinizden emin olun.

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

Çıktı şuna benzer görünmelidir:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Sonraki adımlar

[Örnek: Standart SKU Service Fabric yönetilen küme, disk şifrelemesi etkin olan 1 düğüm türü](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Windows VM 'Leri için Azure disk şifrelemesi](../virtual-machines/windows/disk-encryption-overview.md)

[Sanal makine ölçek kümelerini Azure Resource Manager ile şifreleyin](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
