---
title: Azure portal-SSE ile yönetilen disklerle müşteri tarafından yönetilen anahtarları etkinleştirin
description: Yönetilen disklerinizde Azure portal aracılığıyla müşteri tarafından yönetilen anahtarları etkinleştirin.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 7fbcf37620f9d6edfb0f312c2eb09ef2d7994232
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731658"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Yönetilen diskler için müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi etkinleştirmek için Azure portal kullanın

Azure Disk Depolama, ' yi seçerseniz yönetilen diskler için sunucu tarafı şifreleme (SSE) kullanırken kendi anahtarlarınızı yönetmenizi sağlar. Müşteri tarafından yönetilen anahtarlar ve diğer yönetilen disk şifreleme türleri ile SSE hakkında kavramsal bilgiler için, disk şifreleme makalemize ait **müşteri tarafından yönetilen anahtarlar** bölümüne bakın:

- Linux için: [müşteri tarafından yönetilen anahtarlar](./disk-encryption.md#customer-managed-keys)
- Windows için: [müşteri tarafından yönetilen anahtarlar](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Kısıtlamalar

Şimdilik, müşteri tarafından yönetilen anahtarlar aşağıdaki kısıtlamalara sahiptir:

- Diskiniz için bu özellik etkinleştirilirse, devre dışı bırakılamaz.
    Bu sorunu geçici olarak çözmek için, tüm verileri, müşteri tarafından yönetilen anahtarları kullanmayan tamamen farklı bir yönetilen diske kopyalamanız gerekir:

    - Linux için: [yönetilen bir disk kopyalama](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows için: [yönetilen bir disk kopyalama](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

Aşağıdaki bölümlerde yönetilen diskler için müşteri tarafından yönetilen anahtarların nasıl etkinleştirileceği ve kullanılacağı ele alınmaktadır:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM'yi dağıtma

Anahtar Kasanızı ve disk şifreleme kümesini oluşturup ayarladığınıza göre, şifrelemeyi kullanarak bir VM dağıtabilirsiniz.
VM dağıtım işlemi standart dağıtım işlemine benzer, tek fark, VM 'yi diğer kaynaklarınızla aynı bölgeye dağıtmanız ve müşteri tarafından yönetilen anahtar kullanmayı tercih etmeniz gerekir.

1. **Sanal makineleri** arayın ve **+ Ekle** ' yi seçerek bir VM oluşturun.
1. **Temel** dikey pencerede, disk şifreleme kümesi ve Azure Key Vault aynı bölgeyi seçin.
1. **Temel** dikey penceredeki diğer değerleri istediğiniz şekilde girin.

    ![Bölge değeri vurgulanmış şekilde VM oluşturma deneyiminin ekran görüntüsü.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **Diskler** dikey penceresinde, **müşteri tarafından yönetilen bir anahtarla bekleyen şifreleme**' yi seçin.
1. Disk **şifreleme kümesi** açılır penceresinde disk şifreleme kümesini seçin.
1. Diğer seçimleri istediğiniz gibi yapın.

    ![VM oluşturma deneyiminin, diskler dikey penceresinin ekran görüntüsü. Disk şifreleme kümesi açılır ve vurgulanmış olarak.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Mevcut bir diskte etkinleştir

> [!CAUTION]
> Bir VM 'ye bağlı herhangi bir diskte disk şifrelemeyi etkinleştirmek, VM 'yi durdurmanız gerekir.
    
1. Disk şifreleme kümelerinizin biriyle aynı bölgede bulunan bir VM 'ye gidin.
1. VM 'yi açın ve **Durdur**' u seçin.

    ![Örnek sanal makinenizin ana kaplamanın, durdur düğmesi vurgulanmış şekilde ekran görüntüsü.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM 'yi durdurmayı tamamladıktan sonra, **diskler** ' i seçin ve ardından şifrelemek istediğiniz diski seçin.

    ![Örnek sanal makinenizin, diskler dikey penceresi açık olan ekran görüntüsü. İşletim sistemi diski, seçmeniz için örnek bir disk olarak vurgulanır.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **Şifreleme** ' yi seçin ve ardından **müşteri tarafından yönetilen bir anahtarla geri kalan şifrelemeyi** seçin ve ardından açılan listede disk şifreleme kümesini seçin.
1. **Kaydet**’i seçin.

    ![Örnek işletim sistemi diskinizin ekran görüntüsü. Şifreleme dikey penceresi açıktır, müşteri tarafından yönetilen bir anahtarla bekleyen şifreleme seçilidir ve ayrıca Azure Key Vault. Bu seçimleri yaptıktan sonra Kaydet düğmesi seçilidir.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Şifrelemek istediğiniz sanal makineye bağlı diğer diskler için bu işlemi tekrarlayın.
1. Disklerinizde, müşteri tarafından yönetilen anahtarlara geçiş tamamlandığında, şifrelemek istediğiniz başka bir bağlı disk yoksa VM 'nizi başlatabilirsiniz.

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapsamakta olan kaynaklara otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya yönetilen diski bir Azure AD dizininden diğerine taşırsanız, yönetilen disklerle ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. [Azure AD dizinleri arasında abonelik aktarma](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Sonraki adımlar

- [Müşteri tarafından yönetilen anahtarlarla şifrelenmiş diskler oluşturmak için Azure Resource Manager şablonlarını keşfet](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault nedir?](../key-vault/general/overview.md)
- [Makineleri, müşteri tarafından yönetilen anahtarlar etkinleştirilmiş disklerle Çoğalt](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell ile VMware VM'lerinin Azure'a olağanüstü durum kurtarmayı ayarlama](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell ve Azure Resource Manager kullanarak Hyper-V VM'leri için Azure'a olağanüstü durum kurtarmayı ayarlama](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)