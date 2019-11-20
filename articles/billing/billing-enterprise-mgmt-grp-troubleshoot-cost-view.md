---
title: Azure kurumsal maliyet görünümleri ile ilgili sorunları giderme
description: Azure portalındaki kurumsal maliyet görünümleriyle ilgili karşılaşabileceğiniz sorunların nasıl çözüleceğini öğrenin.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 7b4f7fbba83d9d336e42e1de60822a0aa9107269
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709652"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Kurumsal maliyet görünümleri ile ilgili sorunları giderme

Kurumsal kayıtlar içinde, kayıttaki kullanıcıların maliyetleri görmemesine neden olabilecek birçok ayar vardır.  Bu ayarlar, kayıt yöneticisi tarafından yönetilir. Veya kayıt doğrudan Microsoft üzerinden satın alınmadıysa ayarlar iş ortağı tarafından yönetilir.  Bu makale, ayarların ne olduğunu ve kaydı nasıl etkilediğini anlamanıza yardımcı olur. Bu ayarlar, Azure Rol tabanlı erişim denetimi (RBAC) rollerinden bağımsızdır.

## <a name="enable-access-to-costs"></a>Maliyetlere erişimi etkinleştirme

Maliyet bilgilerini ararken Yetkisiz veya *“Kaydınızda maliyet görünümleri devre dışı bırakıldı.”* gibi bir ileti görüyor musunuz?
![Abonelik için Geçerli Maliyet alanında “yetkisiz” ifadesini gösteren ekran görüntüsü.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Bunun nedeni aşağıdakilerden biri olabilir:

1. Azure’u bir kurumsal iş ortağı aracılığıyla satın aldınız ve iş ortağı henüz fiyatlandırmayı yayınlamadı. [Enterprise portal](https://ea.azure.com) içinde fiyatlandırma ayarını güncelleştirmesi için iş ortağınızla görüşün.
2. EA Direct müşterisiyseniz birkaç olasılık vardır:
    * Hesap Sahibisiniz ve Kayıt Yöneticiniz, **Hesap Sahibi için ücretleri görüntüleme** ayarını devre dışı bıraktı.  
    * Bölüm Yöneticisisiniz ve Kayıt Yöneticiniz, **Bölüm Yöneticisi için ücretleri görüntüleme** ayarını devre dışı bıraktı.
    * Erişim elde etmek için Kayıt Yöneticinizle görüşün. Kayıt Yöneticisi, [Enterprise portal](https://ea.azure.com/manage/enrollment)’daki ayarları güncelleştirebilir.

      ![Ücretleri görüntüleme için Enterprise Portal Ayarlarını gösteren ekran görüntüsü.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Varlık kullanılamıyor

Bir abonelik veya yönetim grubuna erişmeye çalışırken **Bu varlık kullanılamıyor** ifadesini gösteren bir hata iletisi alırsanız, bu öğeyi görüntülemek için doğru role sahip değilsiniz demektir.  

!["Varlık kullanılamıyor" iletisini gösteren ekran görüntüsü.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Azure abonelik veya yönetim grubu yöneticinizden erişim izni vermesini isteyin. Daha fazla bilgi için bkz. [RBAC ve Azure portalını kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar
- Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
