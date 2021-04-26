---
title: Konak Azure portal yönetilen disklerde şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirme
description: Azure yönetilen disklerinizde (Azure portal uçtan uca şifrelemeyi etkinleştirmek için konaktaki şifrelemeyi kullanın.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721877"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Konakta şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirmek için Azure portal kullanma

Konakta şifrelemeyi etkinleştirdiğinizde, VM konağında depolanan veriler, REST ve depolama hizmetine şifrelenen akışlara şifrelenir. Konakta şifreleme ve diğer yönetilen disk şifreleme türleri hakkında kavramsal bilgiler için bkz.:

* Linux: [VM verileriniz için ana bilgisayardan uca şifreleme](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [VM verileriniz için ana bilgisayardan uca şifreleme Ile şifreleme](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Desteklenen VM boyutları

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Önkoşullar

VM/VMSS 'niz için EncryptionAtHost özelliğini kullanmadan önce aboneliğiniz için özelliği etkinleştirmeniz gerekir. Aboneliğiniz için özelliği etkinleştirmek üzere lütfen aşağıdaki adımları izleyin:

1. **Azure Portal**: [Azure Portal](https://portal.azure.com)Cloud Shell simgesini seçin:

    ![Azure portal Cloud Shell başlatılacak simge](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  Aboneliğinizi için özelliği kaydetmek üzere aşağıdaki komutu yürütün

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  Özelliği denemeden önce aşağıdaki komutu kullanarak kayıt durumunun kayıtlı olduğundan (birkaç dakika sürer) emin olun.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Azure portal, [belirtilen bağlantıyı](https://aka.ms/diskencryptionupdates)kullanarak oturum açın.

> [!IMPORTANT]
> Azure portal erişmek için, [sağlanmış bağlantıyı](https://aka.ms/diskencryptionupdates) kullanmanız gerekir. Ana bilgisayardaki şifreleme, bağlantıyı kullanmadan Genel Azure portal Şu anda görünür değil.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault ve disk şifreleme kümesi oluşturma

Özellik etkinleştirildikten sonra, henüz yapmadıysanız bir Azure Key Vault ve bir disk şifreleme kümesi ayarlamanız gerekir.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM'yi dağıtma

Konakta şifrelemeyi etkinleştirmek için yeni bir VM dağıtmanız gerekir, bu, mevcut VM 'lerde etkinleştirilemez.

1. **Sanal makineleri** arayın ve **+ Ekle** ' yi seçerek bir VM oluşturun.
1. Yeni bir sanal makine oluşturun, uygun bir bölge ve desteklenen bir VM boyutu seçin.
1. **Temel** dikey penceredeki diğer değerleri istediğiniz gibi doldurup **diskler** dikey penceresine geçin.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Sanal makine oluşturma temelleri dikey penceresinin ekran görüntüsü, bölge ve V d boyutu vurgulanır.":::

1. **Diskler** dikey penceresinde, **konakta şifreleme** için **Evet** ' i seçin.
1. Diğer seçimleri istediğiniz gibi yapın.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Sanal makine oluşturma diskleri dikey penceresinin ekran görüntüsü, konaktaki şifreleme vurgulanır.":::

1. VM dağıtım işlemini tamamlayın ortamınıza uyan seçimler yapın.

Konakta şifreleme özelliği etkinleştirilmiş bir VM 'yi dağıttıysanız, ana bilgisayar üzerinde şifreleme kullanılarak tüm ilişkili disklerin şifrelenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Resource Manager şablonu örnekleri](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
