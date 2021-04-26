---
title: Azure yönetilen disklerinin sunucu tarafı şifrelemesi
description: Azure depolama, verilerinizi depolama kümelerine kalıcı yapmadan önce Rest durumunda şifreleyerek korur. Kendi anahtarlarınız ile şifrelemeyi yönetmek için müşteri tarafından yönetilen anahtarları kullanabilir veya yönetilen disklerinizin şifrelenmesi için Microsoft tarafından yönetilen anahtarlara güvenebilirsiniz.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531527"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Depolama sunucu tarafı şifrelemesi

Azure yönetilen disklerin çoğu, verilerinizi korumak ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olmak için sunucu tarafı şifreleme (SSE) kullanan Azure depolama şifrelemesi ile şifrelenir. Azure depolama şifrelemesi, Azure yönetilen disklerinde (işletim sistemi ve veri diskleri) depolanan verilerinizi otomatik olarak buluta kalıcı hale getirerek varsayılan olarak şifreler. Ancak, ana bilgisayar etkinleştirilmiş olarak Şifrelemeli Diskler, Azure depolama aracılığıyla şifrelenmez. Konakta şifreleme olan diskler için, sanal makineyi barındıran sunucu verilerinize yönelik şifrelemeyi sağlar ve şifrelenmiş veriler Azure depolama alanına akar.

Azure yönetilen disklerindeki veriler, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak saydam bir şekilde şifrelenir, en güçlü blok şifrelemeleri KULLANILABILIR ve FIPS 140-2 uyumludur. Azure yönetilen diskleri temel alan şifreleme modülleri hakkında daha fazla bilgi için bkz [. şifreleme API 'si: yeni nesil](/windows/desktop/seccng/cng-portal)

Azure depolama şifrelemesi, yönetilen disklerin performansını etkilemez ve ek bir maliyet yoktur. Azure depolama şifrelemesi hakkında daha fazla bilgi için bkz. [Azure depolama şifrelemesi](/azure/storage/common/storage-service-encryption).

> [!NOTE]
> Geçici diskler yönetilen diskler değildir ve konakta şifrelemeyi etkinleştirmediğiniz müddetçe SSE tarafından şifrelenmez.

## <a name="about-encryption-key-management"></a>Şifreleme anahtarı yönetimi hakkında

Yönetilen diskinizin şifrelenmesi için platform tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınızı kullanarak şifrelemeyi yönetebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz, yönetilen disklerdeki tüm verileri şifrelemek ve şifrelerini çözmek için kullanılacak *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. 

Aşağıdaki bölümlerde, anahtar yönetimiyle ilgili seçeneklerin her biri daha ayrıntılı olarak açıklanır.

### <a name="platform-managed-keys"></a>Platform tarafından yönetilen anahtarlar

Varsayılan olarak, yönetilen diskler platform tarafından yönetilen şifreleme anahtarlarını kullanır. Mevcut yönetilen disklere yazılan tüm yönetilen diskler, anlık görüntüler, görüntüler ve veriler, platform tarafından yönetilen anahtarlarla otomatik olarak şifrelenir.

### <a name="customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlar

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Kısıtlamalar

Şimdilik, müşteri tarafından yönetilen anahtarlar aşağıdaki kısıtlamalara sahiptir:

- Diskiniz için bu özellik etkinleştirilirse, devre dışı bırakılamaz.
    Bu sorunu geçici olarak çözmek için, [Azure PowerShell modülünü](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) veya [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)'yi kullanarak tüm verileri, müşteri tarafından yönetilen anahtarları kullanmayan tamamen farklı bir yönetilen diske kopyalamanız gerekir.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Desteklenen bölgeler

Müşterinin yönettiği anahtarlar, yönetilen disklerin kullanılabildiği tüm bölgelerde kullanılabilir.

Otomatik anahtar döndürme önizlemededir ve yalnızca aşağıdaki bölgelerde kullanılabilir:

- Doğu ABD
- Doğu ABD 2
- Orta Güney ABD
- Batı ABD
- Batı ABD 2
- Kuzey Avrupa
- West Europe
- Orta Fransa

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapsamakta olan kaynaklara otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya yönetilen diski bir Azure AD dizininden diğerine taşırsanız, yönetilen disklerle ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. [Azure AD dizinleri arasında abonelik aktarma](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Yönetilen diskler için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere, [Azure PowerShell modülü](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) veya [Azure Portal](disks-enable-customer-managed-keys-portal.md)ile nasıl etkinleştireceğinizi kapsayan makalelerimize bakın. Müşteri tarafından yönetilen anahtarları otomatik anahtar rotasyoniyle nasıl etkinleştireceğinizi öğrenmek için bkz. [otomatik anahtar dönüşüyle bir Azure Key Vault ve DiskEncryptionSet ayarlama (Önizleme)](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>VM verileriniz için ana bilgisayar uçtan uca şifreleme

Konakta şifrelemeyi etkinleştirdiğinizde, bu şifreleme VM 'nizin kendisi üzerinde başlatılır ve sanal makinenizin ayrıldığı Azure sunucusudur. Geçici diskiniz ve işletim sistemi/veri diski önbellekler için veriler bu VM konağında depolanır. Konakta şifrelemeyi etkinleştirdikten sonra, tüm bu veriler Rest 'de şifrelenir ve depolama hizmetine şifrelenmiş akışlar, burada kalıcı hale gelir. Temelde, ana bilgisayar üzerindeki şifreleme, verilerinizi uçtan uca şifreler. Konakta şifreleme, sanal makinenizin CPU 'sunu kullanmaz ve sanal makinenizin performansını etkilemez. 

Uçtan uca şifrelemeyi etkinleştirdiğinizde, geçici diskler ve kısa ömürlü işletim sistemi diskleri, platformda yönetilen anahtarlarla birlikte şifrelenir. İşletim sistemi ve veri diski önbellekleri, seçilen disk şifrelemesi türüne bağlı olarak, müşteri tarafından yönetilen ya da platform tarafından yönetilen anahtarlarla birlikte Rest ile şifrelenir. Örneğin, bir disk, müşteri tarafından yönetilen anahtarlarla şifrelenirse, disk için önbellek, müşteri tarafından yönetilen anahtarlarla şifrelenir ve bir disk, platform tarafından yönetilen anahtarlarla şifrelenirse, diskin önbelleği, platform tarafından yönetilen anahtarlarla şifrelenir.

### <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Desteklenen VM boyutları

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Ayrıca, VM boyutlarını programlı bir şekilde bulabilirsiniz. Program aracılığıyla nasıl alınacağını öğrenmek için [Azure PowerShell modülünün](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) veya [Azure CLı](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes) makalelerinin desteklenen VM boyutlarını bulma bölümüne bakın.

Konakta şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirmek için, [Azure PowerShell modülü](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)veya [Azure Portal](disks-enable-host-based-encryption-portal.md)nasıl etkinleştireceğinizi kapsayan makalelerimize göz atın.

## <a name="double-encryption-at-rest"></a>Rest 'te Çift şifreleme

Herhangi bir şifreleme algoritması, uygulama veya anahtarla ilişkili riskten endişe duyan yüksek güvenlikli duyarlı müşteriler artık, platform yönetilen şifreleme anahtarlarını kullanan altyapı katmanında farklı bir şifreleme algoritması/modu kullanarak ek şifreleme katmanını kabul edebilir. Bu yeni katman, kalıcı işletim sistemi ve veri diskleri, anlık görüntüler ve görüntülere, hepsi de çift şifrelemeyle birlikte şifrelenecek şekilde uygulanabilir.

### <a name="supported-regions"></a>Desteklenen bölgeler

İki şifreleme, yönetilen disklerin kullanılabildiği tüm bölgelerde kullanılabilir.

Yönetilen diskler için REST 'te çift şifrelemeyi etkinleştirmek için, [Azure PowerShell modülü](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) veya [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)ile nasıl etkinleştireceğinizi kapsayan makalelerimize bakın.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Sunucu tarafı şifrelemesi ile Azure disk şifrelemesi karşılaştırması

[Azure disk şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md) , Konuk VM içindeki müşteri tarafından yönetilen anahtarlarla yönetilen diskleri şifrelemek için Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini veya Windows 'un [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) özelliğini kullanır.  Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme, depolama hizmetindeki verileri şifreleyerek VM 'niz için herhangi bir işletim sistemi türünü ve görüntüsünü kullanmanızı sağlayarak ADE 'yi geliştirir.
> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapsamakta olan kaynaklara otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya yönetilen diski bir Azure AD dizininden diğerine taşırsanız, yönetilen disklerle ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, böylelikle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. [Azure AD dizinleri arasında abonelik aktarma](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell modülü](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)veya [Azure Portal](disks-enable-host-based-encryption-portal.md)ile konakta şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirin.
- [Azure PowerShell modülü](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) veya [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)ile yönetilen diskler için bekleyen çift şifrelemeyi etkinleştirin.
- [Azure PowerShell modülü](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) veya [Azure Portal](disks-enable-customer-managed-keys-portal.md)ile yönetilen diskler için müşteri tarafından yönetilen anahtarlar etkinleştirin.
- [Müşteri tarafından yönetilen anahtarlarla şifrelenmiş diskler oluşturmak için Azure Resource Manager şablonlarını keşfet](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault nedir?](../key-vault/general/overview.md)
