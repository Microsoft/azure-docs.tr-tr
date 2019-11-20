---
title: Azure disk şifrelemesi ve Azure sanal makine ölçek kümeleri uzantı sıralaması
description: Bu makale, Linux IaaS VM 'Leri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: 351c3603ddc069647577b5465ea9fa839810cbc1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530894"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Sanal makine ölçek kümesi uzantısı sıralaması ile Azure disk şifrelemesini kullanma

Azure Disk Şifrelemesi gibi uzantılar, belirli bir sırada bir Azure sanal makineler ölçek kümesine eklenebilir. Bunu yapmak için [uzantı sıralaması](virtual-machine-scale-sets-extension-sequencing.md)kullanın. 

Genel olarak, bir diske şifreleme uygulanmalıdır:

- Ya da diskleri veya birimleri hazırlayan özel komut dosyalarından sonra.
- Şifrelenmiş disklerdeki veya birimlerdeki verilere erişen veya bu verileri kullanan özel betiklerden önce.

Her iki durumda da `provisionAfterExtensions` özelliği, hangi uzantının daha sonra sırada ekleneceğini belirler.

## <a name="sample-azure-templates"></a>Örnek Azure şablonları

Azure disk şifrelemesi 'nin başka bir uzantıdan sonra uygulanmasını istiyorsanız, `provisionAfterExtensions` özelliğini AzureDiskEncryption Extension bloğuna koyun. 

Aşağıda, bir Windows diskini başlatan ve biçimlendiren "AzureDiskEncryption" tarafından izlenen bir PowerShell betiği olan "CustomScriptExtension" kullanılarak bir örnek verilmiştir:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Azure disk şifrelemesi 'nin başka bir uzantıdan önce uygulanmasını istiyorsanız, `provisionAfterExtensions` özelliğini izlenecek uzantının bloğuna koyun.

Aşağıda, Windows tabanlı bir Azure VM 'de izleme ve tanılama özellikleri sağlayan bir uzantı olan "AzureDiskEncryption" ve arkasından "VMDiagnosticsSettings" kullanılarak sunulan bir örnek verilmiştir:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Daha ayrıntılı bir şablon için bkz.:
* Diski biçimlendiren özel bir kabuk betikten sonra Azure disk şifrelemesi uzantısını uygulama (Linux): [Deploy-extseq-Linux-Ade-After-CustomScript. JSON](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Sonraki adımlar
- Uzantı sıralaması hakkında daha fazla bilgi edinin: [sanal makine ölçek kümelerinde dizi uzantısı sağlama](virtual-machine-scale-sets-extension-sequencing.md).
- @No__t_0 özelliği hakkında daha fazla bilgi edinin: [Microsoft. COMPUTE virtualMachineScaleSets/Extensions şablon başvurusu](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesi](disk-encryption-overview.md)
- [Azure CLı kullanarak sanal makine ölçek kümelerini şifreleme](disk-encryption-cli.md)
- [Azure PowerShell kullanarak bir sanal makine ölçek kümelerini şifreleme](disk-encryption-powershell.md)
- [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md)
