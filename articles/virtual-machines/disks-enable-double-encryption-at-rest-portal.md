---
title: Rest Azure portal yönetilen disklerde çift şifrelemeyi etkinleştir
description: Azure portal kullanarak, yönetilen disk verileriniz için REST 'de çift şifrelemeyi etkinleştirin.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b5f58b549fc9d879f6c2e5515c679d82c3ff44e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560370"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Yönetilen diskler için REST 'te çift şifrelemeyi etkinleştirmek üzere Azure portal kullanın

Azure Disk Depolama, yönetilen diskler için REST 'de çift şifrelemeyi destekler. Rest 'de Çift şifreleme ve diğer yönetilen disk şifreleme türleri hakkında kavramsal bilgiler için, disk şifreleme makaleimizin [rest bölümünde Çift şifreleme](disk-encryption.md#double-encryption-at-rest) bölümüne bakın.

## <a name="getting-started"></a>Kullanmaya başlama

1. [Azure portalında](https://aka.ms/diskencryptionupdates) oturum açın.

    > [!IMPORTANT]
    > Azure portal erişmek için, [sağlanmış bağlantıyı](https://aka.ms/diskencryptionupdates) kullanmanız gerekir. Rest 'te Çift şifreleme, bağlantıyı kullanmadan Genel Azure portal Şu anda görünür değil.

1. **Disk şifreleme kümelerini** arayın ve seçin.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Ana Azure portal ekran görüntüsü, disk şifreleme kümeleri arama çubuğunda vurgulanır.":::

1. **+ Ekle**'yi seçin.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Disk şifreleme kümesi dikey penceresinin ekran görüntüsü, + Ekle vurgulanır.":::

1. Desteklenen bölgelerden birini seçin.
1. **Şifreleme türü** için, **platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla çift şifrelemeyi** seçin.

    > [!NOTE]
    > Belirli bir şifreleme türüyle bir disk şifreleme kümesi oluşturduktan sonra, bu değişiklik değiştirilemez. Farklı bir şifreleme türü kullanmak istiyorsanız, yeni bir disk şifreleme kümesi oluşturmanız gerekir.

1. Kalan bilgileri girin.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla disk şifreleme kümesi oluşturma dikey penceresinin, bölgelerin ve çift şifrelemenin ekran görüntüsü vurgulanır.":::

1. Bir Azure Key Vault ve anahtar seçin ya da gerekirse yeni bir tane oluşturun.

    > [!NOTE]
    > Key Vault bir örnek oluşturursanız, geçici silme ve Temizleme korumasını etkinleştirmeniz gerekir. Bu ayarlar, yönetilen diskleri şifrelemek için bir Key Vault kullanılırken zorunludur ve yanlışlıkla silme nedeniyle veri kaybını korumanıza karşı koruma sağlar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Key Vault oluşturma dikey penceresinin ekran görüntüsü.":::

1. **Oluştur**’u seçin.
1. Oluşturduğunuz disk şifreleme kümesine gidin ve görüntülenen hatayı seçin. Bu işlem, disk şifreleme kümesini çalışacak şekilde yapılandırır.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Disk şifreleme kümesi görüntülenirken hata, hata metni: bir disk, resim veya anlık görüntüyü bu disk şifrelemesi kümesiyle ilişkilendirmek Için, Anahtar Kasası 'na izin vermeniz gerekir.":::

    Bir bildirim açılır ve başarılı olmalıdır. Bunun yapılması, anahtar kasanızın bulunduğu disk şifreleme kümesini kullanmanıza olanak sağlar.
    
    ![Anahtar kasanızın başarılı izninin ve rol atamasının ekran görüntüsü.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Diskinize gidin.
1. **Şifrelemeyi** seçin.
1. **Şifreleme türü** için, **platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla çift şifrelemeyi** seçin.
1. Disk şifreleme kümesini seçin.
1. **Kaydet**' i seçin.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Yönetilen diskinizin şifreleme dikey penceresinin ekran görüntüsü, belirtilen şifreleme türü vurgulanır.":::

Artık yönetilen diskinizde bekleyen ' de çift şifrelemeyi etkinleştirdiniz.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell-sunucu tarafı şifreleme ile yönetilen disklerle müşteri tarafından yönetilen anahtarları etkinleştirin](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager şablonu örnekleri](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Sunucu tarafı şifreleme ile müşteri tarafından yönetilen anahtarları etkinleştirme-örnekler](./linux/disks-enable-customer-managed-keys-cli.md#examples)