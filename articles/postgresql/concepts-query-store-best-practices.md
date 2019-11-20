---
title: PostgreSQL için Azure veritabanı 'nda sorgu deposu en iyi yöntemleri-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı-tek sunucu 'da sorgu deposu için en iyi yöntemler açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764228"
---
# <a name="best-practices-for-query-store"></a>Sorgu deposu için en iyi uygulamalar

**Şunlara uygulanır:** PostgreSQL için Azure veritabanı-tek sunuculu sürümler 9,6, 10, 11

Bu makalede, PostgreSQL için Azure veritabanı 'nda sorgu deposunu kullanmaya yönelik en iyi yöntemler özetlenmektedir.

## <a name="set-the-optimal-query-capture-mode"></a>En iyi sorgu yakalama modunu ayarlama
Sorgu deposunun sizin için önemli olan verileri yakalamasına izin verin. 

|**pg_qs.query_capture_mode** | **Senaryo**|
|---|---|
|_Tümü_  |İş yükünüzü tüm sorgular ve bunların yürütülme sıklıklarıyla ve diğer istatistiklerde ayrıntılı şekilde çözümleyin. İş yükünüzün yeni sorgularını belirler. Kullanıcı veya otomatik Parametreleştirme fırsatlarını belirlemek için geçici sorguların kullanıldığını algıla. _Hepsi_ daha fazla kaynak tüketim maliyetiyle gelir. |
|_Sayfanın Üstü_  |En iyi sorgulara dikkat edin-istemciler tarafından verilen olanlardır.
|_Yok._ |Araştırmak istediğiniz bir sorgu kümesi ve zaman penceresi zaten yakalandı ve diğer sorguların getirebilme nedenlerini ortadan kaldırmak istiyorsunuz. _Hiçbiri_ , test ve tezgahtır işaretleme ortamları için uygun değildir. Önemli yeni sorguları izleme ve iyileştirme fırsatını kaçırdığı için _hiçbiri_ dikkatli kullanılmamalıdır. Bu eski zaman Windows üzerinde veri kurtaramazsınız. |

Sorgu deposu bekleme istatistikleri için bir mağaza da içerir. Bekleme istatistiklerini yöneten ek bir yakalama modu sorgusu var: **pgms_wait_sampling. query_capture_mode** , _none_ veya _All_olarak ayarlanabilir. 

> [!NOTE] 
> **pg_qs. query_capture_mode** , **pgms_wait_sampling. query_capture_mode**yerine geçiyor. Pg_qs. query_capture_mode _none_ise, pgms_wait_sampling. query_capture_mode ayarının etkisi yoktur. 


## <a name="keep-the-data-you-need"></a>İhtiyacınız olan verileri koruyun
**Pg_qs. retention_period_in_days** parametresi, sorgu deposu için veri saklama süresinin gün cinsinden belirtir. Daha eski sorgu ve istatistik verileri silinir. Varsayılan olarak, sorgu deposu, verileri 7 gün boyunca koruyacak şekilde yapılandırılmıştır. Kullanmayı planlamadığınız geçmiş verilerini saklamaktan kaçının. Verileri daha uzun süre tutmanız gerekiyorsa değeri arttırın.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Bekleme istatistiği örnekleme sıklığını ayarla 
**Pgms_wait_sampling. history_period** parametresi, bekleme olaylarının ne sıklıkta örnekleneceğini belirtir (milisaniye olarak). Süre ne kadar kısa olursa örnekleme daha fazla sıklıkla yapılır. Daha fazla bilgi alındı, ancak bu daha fazla kaynak tüketimine sahiptir. Sunucu yük altındaysa veya ayrıntı düzeyi gerekmiyorsa bu süreyi artırın


## <a name="get-quick-insights-into-query-store"></a>Sorgu deposu hakkında hızlı öngörüler edinin
Sorgu deposundaki veriler hakkında hızlı Öngörüler almak için Azure portal [sorgu performansı içgörüleri](concepts-query-performance-insight.md) kullanabilirsiniz. Görselleştirmeler, en uzun çalışan sorguları ve zaman içinde en uzun bekleme olaylarını yüzey halinde görüntüler.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)kullanarak parametreleri alma veya ayarlama hakkında bilgi edinin.