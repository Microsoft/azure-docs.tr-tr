---
title: Linux için Azure disk şifrelemesi
description: Linux için Azure disk şifrelemesini, sanal makine uzantısı kullanarak bir sanal makineye dağıtır.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073802"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux için Azure disk şifrelemesi (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Genel Bakış

Azure disk şifrelemesi, [Select Azure Linux dağıtımları](https://aka.ms/adelinux)üzerinde tam disk şifrelemesi sağlamak için Linux 'daki dm-crypt alt sisteminden yararlanır.  Bu çözüm, disk şifreleme anahtarlarını ve gizli dizileri yönetmek için Azure Key Vault ile tümleşiktir.

## <a name="prerequisites"></a>Önkoşullar

Önkoşulların tam listesi için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](../linux/disk-encryption-overview.md), özellikle aşağıdaki bölümler:

- [Linux sanal makineleri için Azure disk şifrelemesi](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ek VM gereksinimleri](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Ağ gereksinimleri](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Uzantı şemaların serileştirilmesi

Azure disk şifrelemesi için iki şemaların serileştirilmesi vardır: v 1.1, daha yeni ve Azure Active Directory (AAD) özellikleri kullanmayan, önerilen bir şema ve AAD özellikleri gerektiren eski bir şema. Kullanmakta olduğunuz uzantıya karşılık gelen şema sürümünü kullanmanız gerekir: AzureDiskEncryptionForLinux uzantısı sürüm 1,1 için şema v 1.1, AzureDiskEncryptionForLinux uzantısı sürümü 0,1 için şema v 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Şema v 1.1: AAD yok (önerilir)

V 1.1 şeması önerilir ve Azure Active Directory özellik gerektirmez.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Şema v 0.1: AAD ile 

0,1 şeması `aadClientID` ve `aadClientSecret` ya da `AADClientCertificate`gerektirir.

`aadClientSecret`kullanma:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
| typeHandlerVersion | 0.1, 1.1 | int |
| (0.1 Şeması) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 şeması) AADClientSecret | password | string |
| (0,1 şeması) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path": "", "ad": "", "file_system": ""} | JSON sözlüğü |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | string |
| KeyEncryptionKeyURL 'Si | url | string |
| seçim KeyVaultURL | url | string |
| Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| Birimtürü | İşletim sistemi, veri, tümü | string |

## <a name="template-deployment"></a>Şablon dağıtımı

Şablon dağıtımı örneği için bkz. [çalışan bir LINUX VM 'de şifrelemeyi etkinleştirme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Azure CLI dağıtım

Yönergeleri en son [Azure CLI belgelerinde](/cli/azure/vm/encryption?view=azure-cli-latest)bulabilirsiniz. 

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Sorun giderme için, [Azure disk şifrelemesi sorun giderme kılavuzu](../../security/azure-security-disk-encryption-tsg.md)'na bakın.

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/community/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Sonraki adımlar

VM uzantıları hakkında daha fazla bilgi için bkz. [Linux Için sanal makine uzantıları ve özellikleri](features-linux.md).