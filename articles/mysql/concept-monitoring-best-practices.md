---
title: En iyi uygulamalar izleniyor-MySQL için Azure veritabanı
description: Bu makalede MySQL için Azure veritabanınızı izlemeye yönelik en iyi uygulamalar açıklanmaktadır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355055"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>MySQL için Azure veritabanı 'nı izlemeye yönelik en iyi yöntemler-tek sunucu

Veritabanı işlemlerinizi izlemek için kullanılabilecek en iyi uygulamalar hakkında bilgi edinin ve veri boyutu büyüdükçe performansın güvenliğinin aşılmadığından emin olun. Platforma yeni yetenekler eklediğimiz için, bu bölümde ayrıntılı olarak açıklanan en iyi uygulamaları geliştirmeye devam edeceğiz.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Geçerli izleme araç setinin düzeni

MySQL için Azure veritabanı, kullanımı kolayca izlemek, kaynakları eklemek veya kaldırmak için kullanabileceğiniz araçlar ve yöntemler sağlar (CPU, bellek veya g/ç gibi), olası sorunları giderebilir ve bir veritabanının performansını artırmaya yardımcı olabilir. Çeşitli zaman aralıkları için Ortalama, en yüksek ve en düşük değerleri görmek üzere [performans ölçümlerini](concepts-monitoring.md#metrics) düzenli aralıklarla izleyebilirsiniz.

Bir ölçüm eşiğine yönelik [uyarılar ayarlayabilirsiniz](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) , bu nedenle sunucu bu sınırlara ulaştıysa ve uygun işlemleri gerçekleştirmek için bilgilendirilir.  

Veritabanına atanan kaynakların uygulama iş yükünü işleyebileceğini doğrulamak için veritabanı sunucusunu izleyin. Veritabanı kaynak limitlerine ulaşarak şunları göz önünde bulundurun:
    * En üstteki kaynak kullanan sorguları tanımlama ve iyileştirme. 
    * Hizmet katmanını yükselterek daha fazla kaynak ekleme.

### <a name="cpu-utilization"></a>CPU kullanımı
CPU kullanımını izleme ve veritabanı CPU kaynaklarını tüketme. CPU kullanımı %90 veya daha fazla ise, Vçekirdekler sayısını artırarak veya bir sonraki fiyatlandırma katmanına kadar ölçeklendirerek işlem ölçeğini ölçeklendirmelisiniz.  CPU 'YU yukarı/aşağı ölçeklendirdiğinizden üretilen iş veya eşzamanlılık için beklendiğinden emin olun. 

### <a name="memory"></a>Bellek 
Veritabanı sunucusu için kullanılabilir bellek miktarı, [sanal çekirdek sayısıyla](concepts-pricing-tiers.md)orantılıdır. Belleğin iş yükü için yeterli olduğundan emin olun. Belleğin okuma ve yazma işlemleri için yeterli olduğunu doğrulamak için uygulamanıza yük testi yapın. Veritabanı bellek tüketimi, tanımlı eşiğin ötesinde sık büyürse, sanal çekirdekleri veya daha yüksek performans katmanını artırarak örneğinizi yükseltmeniz gerektiğini gösterir. En uzun süredeki sorguları belirlemek için [sorgu deposu](concepts-query-store.md), [sorgu performansı önerilerini](concepts-performance-recommendations.md) kullanın. İyileştirmek için fırsatları araştırın. 

### <a name="storage"></a>Depolama 
MySQL sunucusu için sağlanan [depolama miktarı](howto-create-manage-server-portal.md#scale-compute-and-storage) , sunucunuza yönelik IOPS 'yi belirler. Hizmet tarafından kullanılan depolama alanı, veritabanı dosyalarını, işlem günlüklerini, sunucu günlüklerini ve yedekleme anlık görüntülerini içerir. Tüketilen disk alanının Toplam sağlanan disk alanının yüzde 85 ' unun üzerinde sürekli olarak aşmadığından emin olun. Bu durumda, biraz alan boşaltmak için veritabanı sunucusundaki verileri silmeniz veya arşivlemeniz gerekir. 

### <a name="network-traffic"></a>Ağ trafiği 

**Ağ alma performansı, ağ aktarım performansı** : MySQL örneğine/saniye başına megabayt cinsinden ağ trafiği hızı. Sunucunun aktarım hızı gereksinimini değerlendirmeniz ve üretilen iş beklenenden düşükse trafiği sürekli olarak izlemeniz gerekir. 

### <a name="database-connections"></a>Veritabanı bağlantıları 
**Veritabanı bağlantıları** : MySQL Için Azure veritabanı 'na bağlı istemci oturumlarının sayısı, [Seçilen SKU boyutu için bağlantı limitleriyle](concepts-server-parameters.md#max_connections) hizalanmalıdır. 


## <a name="next-steps"></a>Sonraki adımlar

- [MySQL için Azure veritabanı performansı için en iyi uygulama](concept-performance-best-practices.md)
- [MySQL için Azure veritabanı 'nı kullanarak sunucu işlemleri için en iyi uygulama](concept-operation-excellence-best-practices.md)
