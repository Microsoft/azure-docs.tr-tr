---
title: PostgreSQL için Azure veritabanı 'nda sorgu deposu en iyi yöntemleri-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı-esnek sunucu 'da sorgu deposu için en iyi yöntemler açıklanmaktadır.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559238"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Sorgu deposu için en iyi uygulamalar-esnek sunucu

**Uygulama hedefi:** PostgreSQL için Azure veritabanı-esnek sunucu sürümleri 11, 12

Bu makalede, PostgreSQL için Azure veritabanı 'nda sorgu deposunu kullanmaya yönelik en iyi yöntemler özetlenmektedir.

## <a name="set-the-optimal-query-capture-mode"></a>En iyi sorgu yakalama modunu ayarlama
Sorgu deposunun sizin için önemli olan verileri yakalamasına izin verin. 

|**pg_qs pg_qs.query_capture_mode** | **Senaryo**|
|---|---|
|_Tümü_  |İş yükünüzü tüm sorgular ve bunların yürütülme sıklıklarıyla ve diğer istatistiklerde ayrıntılı şekilde çözümleyin. İş yükünüzün yeni sorgularını belirler. Kullanıcı veya otomatik Parametreleştirme fırsatlarını belirlemek için geçici sorguların kullanıldığını algıla. _Hepsi_ daha fazla kaynak tüketim maliyetiyle gelir. |
|_Sayfanın Üstü_  |En iyi sorgulara dikkat edin-istemciler tarafından verilen olanlardır.
|_Hiçbiri_ |Hiçbiri olarak ayarlanırsa, sorgu deposu yeni sorgu yakalamaz. Araştırmak istediğiniz bir sorgu kümesi ve zaman penceresi zaten yakalandı ve diğer sorguların getirebilme nedenlerini ortadan kaldırmak istiyorsunuz. _Hiçbiri_ , test ve tezgahtır işaretleme ortamları için uygun değildir. Önemli yeni sorguları izleme ve iyileştirme fırsatını kaçırdığı için _hiçbiri_ dikkatli kullanılmamalıdır. |


> [!NOTE] 
> **pg_qs. query_capture_mode** , **pgms_wait_sampling. query_capture_mode** yerine geçiyor. Pg_qs. query_capture_mode _none_ ise, pgms_wait_sampling. query_capture_mode ayarının etkisi yoktur. 


## <a name="keep-the-data-you-need"></a>İhtiyacınız olan verileri koruyun
**Pg_qs. retention_period_in_days** parametresi, sorgu deposu için veri saklama süresinin gün cinsinden belirtir. Daha eski sorgu ve istatistik verileri silinir. Varsayılan olarak, sorgu deposu, verileri 7 gün boyunca koruyacak şekilde yapılandırılmıştır. Kullanmayı planlamadığınız geçmiş verilerini saklamaktan kaçının. Verileri daha uzun süre tutmanız gerekiyorsa değeri arttırın.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)kullanarak parametreleri alma veya ayarlama hakkında bilgi edinin.
