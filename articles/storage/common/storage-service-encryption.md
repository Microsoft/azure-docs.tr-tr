---
title: Bekleyen veri için Azure Depolama şifrelemesi
description: Azure depolama, verilerinizi buluta kalıcı yapmadan önce otomatik olarak şifreleyerek korur. Depolama hesabınızdaki verilerin şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarınızla şifrelemeyi yönetebilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641122"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Bekleyen veri için Azure Depolama şifrelemesi

Azure depolama, bulutta kalıcı olduğunda verilerinizi otomatik olarak şifrelemek için sunucu tarafı şifreleme (SSE) kullanır. Azure depolama şifrelemesi, verilerinizi korur ve kuruluşunuzun güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur.

## <a name="about-azure-storage-encryption"></a>Azure depolama şifrelemesi hakkında

Azure depolama 'daki veriler, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak şifrelenmiş ve şifresi çözülür, en güçlü blok şifrelemeleri KULLANILABILIR ve FIPS 140-2 uyumludur. Azure depolama şifrelemesi, Windows 'da BitLocker şifrelemeye benzer.

Azure depolama şifrelemesi, hem Kaynak Yöneticisi hem de klasik depolama hesapları da dahil olmak üzere tüm depolama hesapları için etkinleştirilmiştir. Azure depolama şifrelemesi devre dışı bırakılamıyor. Verileriniz varsayılan olarak güvenli hale getirildiğinden, Azure depolama şifrelemesi 'nin avantajlarından yararlanmak için kodunuzu veya uygulamalarınızı değiştirmeniz gerekmez.

Bir depolama hesabındaki veriler, performans katmanından (Standart veya Premium), erişim katmanından (sık veya seyrek erişimli) veya dağıtım modelinden (Azure Resource Manager veya klasik) bağımsız olarak şifrelenir. Arşiv katmanındaki tüm Bloblar da şifrelenir. Tüm Azure depolama artıklığı seçenekleri şifrelemeyi destekler ve coğrafi çoğaltma etkinleştirildiğinde hem birincil hem de ikincil bölgelerdeki tüm veriler şifrelenir. Blob 'lar, diskler, dosyalar, kuyruklar ve tablolar dahil olmak üzere tüm Azure depolama kaynakları şifrelenir. Tüm nesne meta verileri de şifrelenir. Azure depolama şifrelemesi için ek bir maliyet yoktur.

20 Ekim 2017 ' den sonra Azure depolama 'ya yazılan her Blok Blobu, ekleme Blobu veya Sayfa Blobu şifrelenir. Bu tarihten önce oluşturulan Bloblar, bir arka plan işlemi tarafından şifrelenmeye devam eder. 20 Ekim 2017 ' den önce oluşturulan bir Blobun şifrelemeye zorlamak için, blobu yeniden yazabilirsiniz. Bir Blobun şifreleme durumunu denetleme hakkında bilgi edinmek için bkz. [bir Blobun şifreleme durumunu denetleme](../blobs/storage-blob-encryption-status.md).

Azure depolama şifrelemesini temel alan şifreleme modülleri hakkında daha fazla bilgi için bkz. [şifreleme API 'si: yeni nesil](/windows/desktop/seccng/cng-portal).

Azure yönetilen diskler için şifreleme ve anahtar yönetimi hakkında daha fazla bilgi için bkz. [Azure yönetilen disklerin sunucu tarafı şifrelemesi](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>Şifreleme anahtarı yönetimi hakkında

Yeni bir depolama hesabındaki veriler varsayılan olarak Microsoft tarafından yönetilen anahtarlarla şifrelenir. Verilerinizin şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanmaya devam edebilir veya kendi anahtarlarınız ile şifrelemeyi yönetebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz iki seçeneğiniz vardır. Her iki tür anahtar yönetimi veya her ikisini de kullanabilirsiniz:

- Blob depolamadaki ve Azure dosyalarındaki verileri şifrelemek ve şifrelerini çözmek için, *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. <sup>1, 2</sup> müşteri tarafından yönetilen anahtarların Azure Key Vault veya Azure Key Vault yönetilen donanım güvenlik MODELI (HSM) (Önizleme) içinde depolanması gerekir. Müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları kullanma](./customer-managed-keys-overview.md).
- BLOB depolama işlemlerinde, *müşteri tarafından sağlanmış bir anahtar* belirtebilirsiniz. Blob depolamaya karşı okuma veya yazma isteği yapan bir istemci, blob verilerinin şifrelenme ve şifresinin çözülmesi üzerinde ayrıntılı denetim isteğine yönelik bir şifreleme anahtarı içerebilir. Müşteri tarafından sunulan anahtarlar hakkında daha fazla bilgi için bkz. [BLOB depolama için istekte bir şifreleme anahtarı sağlama](../blobs/encryption-customer-provided-keys.md).

Aşağıdaki tabloda, Azure depolama şifrelemesi için anahtar yönetim seçenekleri karşılaştırılmaktadır.

| Anahtar yönetimi parametresi | Microsoft tarafından yönetilen anahtarlar | Müşteri tarafından yönetilen anahtarlar | Müşteri tarafından sunulan anahtarlar |
|--|--|--|--|
| Şifreleme/şifre çözme işlemleri | Azure | Azure | Azure |
| Desteklenen Azure depolama hizmetleri | Tümü | BLOB depolama, Azure dosyaları<sup>1, 2</sup> | Blob depolama |
| Anahtar depolama | Microsoft anahtar deposu | Azure Key Vault veya Key Vault HSM | Müşterinin kendi anahtar deposu |
| Anahtar döndürme sorumluluğu | Microsoft | Müşteri | Müşteri |
| Anahtar denetimi | Microsoft | Müşteri | Müşteri |

<sup>1</sup> müşteri tarafından yönetilen anahtarları kuyruk depolama ile kullanmayı destekleyen bir hesap oluşturma hakkında bilgi için, bkz. [kuyruklar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)oluşturma.<br />
<sup>2</sup> müşteri tarafından yönetilen anahtarları tablo depolama ile kullanmayı destekleyen bir hesap oluşturma hakkında bilgi için bkz. [tablolar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap oluşturma](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Microsoft tarafından yönetilen anahtarlar uyumluluk gereksinimleri uyarınca uygun şekilde döndürülür. Belirli anahtar döndürme gereksinimleriniz varsa, Microsoft, döndürmeyi kendiniz yönetebilmeniz ve denetedebilmeniz için müşteri tarafından yönetilen anahtarlara geçmeniz önerilir.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Şüpheli verileri altyapı şifrelemesiyle şifreleme

Verilerin güvenliğini sağlamak için yüksek düzeyde güvence gerektiren müşteriler, Azure depolama altyapısı düzeyinde 256 bit AES şifrelemesini de etkinleştirebilir. Altyapı şifrelemesi etkinleştirildiğinde, bir depolama hesabındaki veriler, &mdash; hizmet düzeyinde iki kez ve altyapı düzeyinde &mdash; iki farklı şifreleme algoritmalarıyla bir kez şifrelenir. Azure depolama verilerinin çift şifrelemesi, şifreleme algoritmalarından veya anahtarlardan birinin tehlikeye girdiği bir senaryoya karşı koruma sağlar. Bu senaryoda, ek şifreleme katmanı verilerinizi korumaya devam eder.

Hizmet düzeyi şifreleme, Azure Key Vault ile Microsoft tarafından yönetilen anahtarların veya müşteri tarafından yönetilen anahtarların kullanımını destekler. Altyapı düzeyinde şifreleme, Microsoft tarafından yönetilen anahtarları kullanır ve her zaman ayrı bir anahtar kullanır.

Altyapı şifrelemesini sağlayan bir depolama hesabı oluşturma hakkında daha fazla bilgi için, bkz. [verilerin çift şifrelenmesi için altyapı şifrelemesi etkinleştirilmiş bir depolama hesabı oluşturma](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault nedir?](../../key-vault/general/overview.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
- [BLOB depolama için şifreleme kapsamları](../blobs/encryption-scope-overview.md)
- [BLOB depolama için istekte bir şifreleme anahtarı sağlayın](../blobs/encryption-customer-provided-keys.md)
