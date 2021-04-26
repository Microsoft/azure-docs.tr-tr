---
title: Azure Key Vault Managed HSM kullanan en iyi yöntemler
description: Bu belgede, kullanmak için en iyi uygulamalardan bazıları açıklanmaktadır Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482526"
---
# <a name="best-practices-when-using-managed-hsm"></a>Yönetilen HSM kullanırken en iyi uygulamalar

## <a name="control-access-to-your-managed-hsm"></a>Yönetilen HSM 'nize erişimi denetleme

Yönetilen HSM, şifreleme anahtarlarını koruyan bir bulut hizmetidir. Bu anahtarlar hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamalara ve kullanıcılara izin vererek yönetilen HSM 'lere erişimin güvenli hale geldiğinden emin olun. Bu [makalede](access-control.md) erişim modeline genel bakış sunulmaktadır. Kimlik doğrulama ve yetkilendirme ve rol tabanlı erişim denetimi açıklanmaktadır.
- HSM yöneticileri için bir [Azure Active Directory güvenlik grubu](../../active-directory/fundamentals/active-directory-manage-groups.md) oluşturun (kişilere yönetici rolü atamak yerine). Bu, bireysel hesap silme durumunda "Yönetim kilidi 'ni" engeller.
- Yönetim gruplarınız, abonelikleriniz, kaynak gruplarınız ve yönetilen HSM 'lere erişimi kilitleme-yönetim gruplarınızı, aboneliklerinizi ve Kaynak gruplarınızı erişimi denetlemek için Azure RBAC kullanın
- [YÖNETILEN HSM yerel RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)kullanarak her anahtar rol ataması oluşturun.
- Görevlerin ayrılmasını sağlamak için, aynı sorumluya birden çok rol atamaktan kaçının. 
- Rol atamak için en az ayrıcalık erişim sorumlusunu kullanın.
- Kesin izin kümesiyle özel rol tanımı oluşturun.

## <a name="choose-regions-that-support-availability-zones"></a>Kullanılabilirlik alanlarını destekleyen bölgeleri seçin

- En iyi yüksek kullanılabilirlik ve bölge esnekliği sağlamak için [kullanılabilirlik alanları](../../availability-zones/az-overview.md) desteklendiği Azure bölgeleri ' ni seçin. Bu bölgeler, Azure portal "önerilen bölgeler" olarak görüntülenir.

## <a name="backup"></a>Backup

- HSM 'nizin düzenli yedeklemelerini aldığınızdan emin olun. Yedeklemeler HSM düzeyinde ve belirli anahtarlar için yapılabilir. 

## <a name="turn-on-logging"></a>Günlüğü aç

- HSM 'niz için [günlük kaydını açın](logging.md) . Ayrıca uyarıları ayarlayın.

## <a name="turn-on-recovery-options"></a>Kurtarma seçeneklerini aç

- [Geçici silme](../general/soft-delete-overview.md) , varsayılan olarak açık.
- Geçici silme etkinleştirildikten sonra bile HSM 'nin silinmesini zorla önlemek istiyorsanız Temizleme korumasını açın.

## <a name="next-steps"></a>Sonraki adımlar

- Tam HSM yedekleme/geri yükleme hakkında bilgi için bkz. [tam yedekleme/geri yükleme](backup-restore.md) .
- Günlüğe kaydetmeyi yapılandırmak için Azure Izleyici 'yi nasıl kullanacağınızı öğrenmek için bkz. [YÖNETILEN HSM günlüğü](logging.md)
- Bkz. anahtar yönetimi için [YÖNETILEN HSM anahtarlarını yönetme](key-management.md) .
- Rol atamalarını yönetmek için bkz. [YÖNETILEN HSM rol yönetimi](role-management.md) .
