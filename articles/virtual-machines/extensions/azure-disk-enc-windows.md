---
title: Windows için Azure disk şifrelemesi
description: Bir sanal makine uzantısı kullanarak bir Windows sanal makinesine Azure disk şifrelemesi dağıtır.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073218"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows için Azure disk şifrelemesi (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Genel Bakış

Azure disk şifrelemesi, Windows çalıştıran Azure sanal makinelerinde tam disk şifrelemesi sağlamak için BitLocker 'ı kullanır.  Bu çözüm, Anahtar Kasası aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri yönetmek için Azure Key Vault ile tümleşiktir. 

## <a name="prerequisites"></a>Önkoşullar

Önkoşulların tam listesi için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](../linux/disk-encryption-overview.md), özellikle aşağıdaki bölümler:

- [Linux sanal makineleri için Azure disk şifrelemesi](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ağ gereksinimleri](../windows/disk-encryption-overview.md#networking-requirements)
- [grup ilkesi gereksinimleri](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Uzantı şemaların serileştirilmesi

Windows AzureDiskEncryption uzantısı için iki şemaların serileştirilmesi vardır: v 2.2, daha yeni, önerilen bir şema Azure Active Directory (AAD) özelliklerini kullanmayan bir şema ve AAD özellikleri gerektiren eski bir şema. Kullanmakta olduğunuz uzantıya karşılık gelen şema sürümünü kullanmanız gerekir: AzureDiskEncryption uzantısı sürüm 2,2 için şema v 2.2, AzureDiskEncryption uzantısı sürüm 1,1 için şema v 1.1.

### <a name="schema-v22-no-aad-recommended"></a>Şema v 2.2: AAD yok (önerilen)

V 2.2 şeması tüm yeni VM 'Lerde önerilir ve Azure Active Directory özellik gerektirmez.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>Şema v 1.1: AAD ile 

1,1 şeması `aadClientID` ve `aadClientSecret` ya da `AADClientCertificate` gerektirir ve yeni VM 'Ler için önerilmez.

`aadClientSecret`kullanma:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

`AADClientCertificate`kullanma:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1,1, 2,2 | string |
| (1,1 şeması) Aadclientıd | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1,1 şeması) AADClientSecret | password | string |
| (1,1 şeması) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path": "", "ad": "", "file_system": ""} | JSON sözlüğü |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | string |
| KeyEncryptionKeyURL 'Si | url | string |
| KeyVaultURL | url | string |
| seçim Deyimi | password | string | 
| SequenceVersion | uniqueidentifier | string |
| Birimtürü | İşletim sistemi, veri, tümü | string |

## <a name="template-deployment"></a>Şablon dağıtımı
Şablon dağıtımı örneği için, bkz. [Galeri görüntüsünden yeni bir şifrelenmiş WINDOWS VM oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Azure CLI dağıtım

Yönergeleri en son [Azure CLI belgelerinde](/cli/azure/vm/encryption?view=azure-cli-latest)bulabilirsiniz. 

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

[Azure disk şifrelemesi sorun giderme kılavuzu](../../security/azure-security-disk-encryption-tsg.md)'na bakın.

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/community/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Sonraki adımlar
Uzantılar hakkında daha fazla bilgi için bkz. [Windows Için sanal makine uzantıları ve özellikleri](features-windows.md).
