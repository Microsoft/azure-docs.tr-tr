---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750638"
---
Şifrelemeyi, her yönetilen disk düzeyinde, kendi anahtarlarınız ile yönetmeyi tercih edebilirsiniz. Müşteri tarafından yönetilen anahtarlarla yönetilen diskler için sunucu tarafı şifreleme, Azure Key Vault ile tümleşik bir deneyim sunar. [RSA anahtarlarınızı](../articles/key-vault/keys/hsm-protected-keys.md) Key Vault içeri aktarabilir ya da Azure Key Vault yeni RSA anahtarları oluşturabilirsiniz. 

Azure yönetilen diskler, [zarf şifrelemesini](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)kullanarak tam saydam bir biçimde şifrelemeyi ve şifre çözmeyi işler. Bir [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 tabanlı veri şifreleme anahtarı (dek) kullanarak verileri şifreler, bu, sırasıyla anahtarlarınız kullanılarak korunur. Depolama hizmeti, veri şifreleme anahtarları oluşturur ve bunları RSA şifrelemesini kullanarak müşteri tarafından yönetilen anahtarlarla şifreler. Zarf şifreleme, sanal makinelerinizi etkilemeden anahtarlarınızı, uyumluluk ilkelerinize göre düzenli aralıklarla döndürmenize (değiştirmenize) olanak tanır. Anahtarlarınızı döndürdüğünüzde depolama hizmeti, veri şifreleme anahtarlarını yeni müşterinin yönettiği anahtarlarla yeniden şifreler. 

#### <a name="full-control-of-your-keys"></a>Anahtarlarınızın tam denetimi

Anahtarlarınızı şifrelemek ve şifresini çözmek için anahtarlarınızı kullanmak üzere Key Vault yönetilen disklere erişim vermeniz gerekir. Bu, veri ve anahtarlarınızın tam denetimini sağlar. Anahtarlarınızı devre dışı bırakabilir veya istediğiniz zaman yönetilen disklere erişimi iptal edebilirsiniz. Yalnızca yönetilen disklerin veya diğer güvenilen Azure hizmetlerinin Anahtarlarınıza erişebildiğinden emin olmak için Azure Key Vault izlemeye sahip şifreleme anahtarı kullanımını da denetleyebilirsiniz.

Premium SSD 'Ler, standart SSD 'Ler ve standart HDD 'Ler için: anahtarınızı devre dışı bıraktığınızda veya sildiğinizde, bu anahtarı kullanan disklere sahip VM 'Ler otomatik olarak kapatılır. Bu işlem sonrasında, anahtar tekrar etkinleştirilmemişse veya yeni bir anahtar atarsanız VM 'Ler kullanılamaz.    

Ultra diskler için: bir anahtarı devre dışı bıraktığınızda veya sildiğinizde, anahtarı kullanan Ultra disklere sahip VM 'Ler otomatik olarak kapatılır. VM 'Leri serbest bırakırsanız ve yeniden başlattıktan sonra, diskler anahtarı kullanmayı durdurur ve ardından VM 'Ler yeniden çevrimiçi olmayacaktır. VM 'Leri yeniden çevrimiçi duruma getirmek için yeni bir anahtar atamanız veya mevcut anahtarı etkinleştirmeniz gerekir.    

Aşağıdaki diyagramda, yönetilen disklerin müşteri tarafından yönetilen anahtarı kullanarak istek yapmak için Azure Active Directory ve Azure Key Vault nasıl kullandığı gösterilmektedir:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Yönetilen disk ve müşteri tarafından yönetilen anahtarlar iş akışı. Yönetici bir Azure Key Vault oluşturur, ardından bir disk şifreleme kümesi oluşturur ve disk şifreleme kümesini ayarlar. Küme, diskin kimlik doğrulaması için Azure AD 'yi kullanmasına izin veren bir VM ile ilişkilendirilir.":::

Aşağıdaki listede diyagram daha ayrıntılı olarak açıklanmaktadır:

1. Azure Key Vault Yöneticisi, Anahtar Kasası kaynakları oluşturur.
1. Anahtar Kasası Yöneticisi, RSA anahtarlarını Key Vault veya Key Vault yeni RSA anahtarları olarak içeri aktarır.
1. Bu yönetici, bir Azure Key Vault KIMLIĞI ve bir anahtar URL 'SI belirterek bir disk şifreleme kümesi kaynağı örneği oluşturur. Disk şifreleme kümesi, yönetilen diskler için anahtar yönetimini basitleştirmek üzere sunulan yeni bir kaynaktır. 
1. Bir disk şifreleme kümesi oluşturulduğunda, Azure Active Directory (AD) içinde [sistem tarafından atanan yönetilen bir kimlik](../articles/active-directory/managed-identities-azure-resources/overview.md) oluşturulur ve disk şifreleme kümesiyle ilişkilendirilir. 
1. Azure Anahtar Kasası Yöneticisi, anahtar kasasında işlem gerçekleştirmek için yönetilen kimlik iznini verir.
1. Bir VM kullanıcısı disk şifreleme kümesiyle ilişkilendirerek diskler oluşturur. VM kullanıcısı aynı zamanda, mevcut kaynaklar için müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi, disk şifreleme kümesiyle ilişkilendirerek da etkinleştirebilir. 
1. Yönetilen diskler Azure Key Vault istekleri göndermek için yönetilen kimliği kullanır.
1. Yönetilen diskler verileri okumak veya yazmak için, verileri şifrelemeyi ve şifre çözmeyi gerçekleştirmek üzere veri şifreleme anahtarını şifrelemek (sarmalamak) ve şifresini çözmek (kaydırmak) için Azure Key Vault istek gönderir. 

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için bkz. [PowerShell](/powershell/module/azurerm.keyvault/) ve [Azure Key Vault CLI](/cli/azure/keyvault)Azure Key Vault. Erişimi iptal etmek, şifreleme anahtarına Azure depolama tarafından erişilemediğinden, depolama hesabındaki tüm verilere erişimi etkin bir şekilde engeller.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Müşteri tarafından yönetilen anahtarların otomatik anahtar dönüşü (Önizleme)

En son anahtar sürümüne otomatik anahtar döndürmeyi etkinleştirmeyi seçebilirsiniz. Disk, disk şifreleme kümesi aracılığıyla bir anahtara başvurur. Bir disk şifrelemesi kümesi için Otomatik döndürmeyi etkinleştirdiğinizde sistem, disk şifreleme kümesine başvuran tüm yönetilen diskleri, anlık görüntüleri ve görüntüleri bir saat içinde anahtarın yeni sürümünü kullanacak şekilde otomatik olarak güncelleştirir. Bu özellik şu anda önizleme aşamasında olan sınırlı bölgelerde kullanılabilir. Bölgesel kullanılabilirlik için [Desteklenen bölgeler](#supported-regions) bölümüne bakın.