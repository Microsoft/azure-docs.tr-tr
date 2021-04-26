---
title: Linux için Azure disk şifrelemesi
description: Linux için Azure disk şifrelemesini, sanal makine uzantısı kullanarak bir sanal makineye dağıtır.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792391"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux için Azure disk şifrelemesi (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Genel Bakış

Azure disk şifrelemesi, [Select Azure Linux dağıtımları](../linux/disk-encryption-overview.md)üzerinde tam disk şifrelemesi sağlamak için Linux 'daki dm-crypt alt sisteminden yararlanır.  Bu çözüm, disk şifreleme anahtarlarını ve gizli dizileri yönetmek için Azure Key Vault ile tümleşiktir.

## <a name="prerequisites"></a>Önkoşullar

Önkoşulların tam listesi için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](../linux/disk-encryption-overview.md), özellikle aşağıdaki bölümler:

- [Desteklenen VM 'Ler ve işletim sistemleri](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ek VM gereksinimleri](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Ağ gereksinimleri](../linux/disk-encryption-overview.md#networking-requirements)
- [Şifreleme anahtarı depolama gereksinimleri](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Uzantı şeması

Azure disk şifrelemesi (ADE) için uzantı şemasının iki sürümü vardır:
- v 1.1-Azure Active Directory (AAD) özelliklerini kullanmayan daha yeni bir önerilen şema.
- v 0.1-Azure Active Directory (AAD) özellikleri gerektiren eski bir şema. 

Hedef şemayı seçmek için, `typeHandlerVersion` özelliği kullanmak istediğiniz şemanın sürümüne eşit olarak ayarlanmalıdır.

### <a name="schema-v11-no-aad-recommended"></a>Şema v 1.1: AAD yok (önerilir)

V 1.1 şeması önerilir ve Azure Active Directory (AAD) özellikleri gerektirmez.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Şema v 0.1: AAD ile 

0,1 şeması `AADClientID` ve ya da gerektirir `AADClientSecret` `AADClientCertificate` .

Şunu kullanarak `AADClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Şunu kullanarak `AADClientCertificate` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Özellik değerleri

| Name | Değer/örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| yayımcı | Microsoft. Azure. Security | string |
| tür | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1,1, 0,1 | int |
| (0,1 şeması) Aadclientıd | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 şeması) AADClientSecret | password | string |
| (0,1 şeması) AADClientCertificate | #c0 | string |
| seçim (0,1 şeması) Deyimi | password | string |
| DiskFormatQuery | {"dev_path": "", "ad": "", "file_system": ""} | JSON sözlüğü |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (isteğe bağlı-varsayılan RSA-OAEP) KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | string |
| KeyVaultURL | url | string |
| Keyvaultresourceıd | url | string |
| seçim KeyEncryptionKeyURL 'Si | url | string |
| seçim KekVaultResourceId | url | string |
| seçim SequenceVersion | uniqueidentifier | string |
| Birimtürü | İşletim sistemi, veri, tümü | string |

## <a name="template-deployment"></a>Şablon dağıtımı

Şema v 1.1 tabanlı şablon dağıtımı örneği için bkz. Azure hızlı başlangıç şablonu [201-Encrypt-Running-Linux-VM-AAD olmadan](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Şema v 0.1 ' i temel alan şablon dağıtımı örneği için bkz. Azure hızlı başlangıç şablonu [201-Encrypt-Running-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir.
> - Linux işletim sistemi birimlerini şifrelerken, sanal makinenin kullanılamaz kabul edilmesi gerekir. Şifreleme işlemi sırasında erişilmesi gereken açık dosyaları engelleyen sorunlardan kaçınmak için şifreleme sürerken SSH oturum açma işlemlerini kesinlikle öneririz. İlerlemeyi denetlemek için [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 'ini veya [VM şifrelemesi Show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI komutunu kullanın. Bu işlemin, bir 30 GB işletim sistemi birimi için birkaç saat, ayrıca veri birimlerini şifrelemek için de ek süre gelmesi beklenmelidir. Şifreleme biçimi ALL seçeneği kullanılmamışsa, veri hacmi şifreleme süresi veri birimlerinin boyutuyla ve miktarıyla orantılıdır. 
> - Linux VM 'lerinde şifrelemeyi devre dışı bırakmak yalnızca veri birimlerinde desteklenir. İşletim sistemi birimi şifrelendiyse veri veya işletim sistemi birimlerinde desteklenmez. 

>[!NOTE]
> Ayrıca `VolumeType` , parametresi Tümü olarak ayarlandıysa, veri diskleri yalnızca düzgün şekilde bağlanmışsa şifrelenir.

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Sorun giderme için, [Azure disk şifrelemesi sorun giderme kılavuzu](../linux/disk-encryption-troubleshooting.md)'na bakın.

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. 

Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure desteği](https://azure.microsoft.com/support/options/) ' ne gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar

* VM uzantıları hakkında daha fazla bilgi için bkz. [Linux Için sanal makine uzantıları ve özellikleri](features-linux.md).
* Linux için Azure disk şifrelemesi hakkında daha fazla bilgi için bkz. [Linux sanal makineleri](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).