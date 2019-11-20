---
title: MySQL için Azure veritabanı 'nda performans önerileri
description: Bu makalede MySQL için Azure veritabanı 'nda performans önerisi özelliği açıklanmaktadır
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1371b286f5e9bd82345e17c11e910c68fac9fed2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603157"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda performans önerileri

**Uygulama hedefi:** MySQL için Azure veritabanı 5,7

Performans önerileri özelliği, gelişmiş performans için özelleştirilmiş öneriler oluşturmak üzere veritabanlarınızı analiz eder. Bu önerileri oluşturmak için analiz, şema dahil çeşitli veritabanı özelliklerine bakar. Performans önerileri özelliğini tam olarak kullanmak için sunucunuzda [sorgu deposunu](concepts-query-store.md) etkinleştirin. Performans şeması KAPALıYSA, Query Store 'u açmak performance_schema ve özellik için gereken performans şeması gereçlerinin bir alt kümesini sunar. Herhangi bir performans önerisi uygulandıktan sonra, bu değişikliklerin etkisini değerlendirmek için performansı sınamalısınız.

## <a name="permissions"></a>İzinler

Performans Önerileri özelliğini kullanarak analiz çalıştırmak için **Sahip** veya **Katkıda bulunan** izinleri gereklidir.

## <a name="performance-recommendations"></a>Performans önerileri

[Performans Önerileri](concepts-performance-recommendations.md) özelliği, performansı iyileştirme potansiyeli olan dizinleri tanımlamak için sunucunuzdaki iş yüklerini analiz eder.

MySQL sunucunuzun Azure portal sayfasındaki menü çubuğunun **akıllı performans** bölümünde **performans önerilerini** açın.

![Performans Önerileri giriş sayfası](./media/concepts-performance-recommendations/performance-recommendations-page.png)

**Çözümle** ' yi seçin ve analiz işlemini başlatmak için bir veritabanı seçin. İş yükünüze bağlı olarak, çözümlemenin tamamlanması birkaç dakika sürebilir. Analiz tamamlanınca portalda bir bildirim olur. Analiz, veritabanınızı ayrıntılı bir şekilde inceleme işlemini gerçekleştirir. Yoğun olmayan dönemler sırasında analiz gerçekleştirmenizi öneririz.

**Öneriler** penceresi, varsa önerilerin bir listesini ve bu öneriyi oluşturan ılgılı sorgu kimliğini gösterir. Sorgu KIMLIĞIYLE, sorgu hakkında daha fazla bilgi edinmek için [MySQL. query_store](concepts-query-store.md#mysqlquery_store) görünümünü kullanabilirsiniz.

![Performans önerileri yeni sayfa](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Öneriler otomatik olarak uygulanmaz. Öneriyi uygulamak için, sorgu metnini kopyalayın ve tercih ettiğiniz istemciden çalıştırın. Öneriyi değerlendirmek için sınamayı ve izlemeyi unutmayın.

## <a name="recommendation-types"></a>Öneri türleri

Şu anda yalnızca *Dizin önerileri oluşturma* desteklenir.

### <a name="create-index-recommendations"></a>Dizin önerileri oluşturma

*Dizin önerilerini oluşturma* , iş yükünde en sık çalıştırılan veya zaman alan sorguları hızlandırmak için yeni dizinler önerir. Bu öneri türü, [sorgu deposunun](concepts-query-store.md) etkinleştirilmesini gerektirir. Sorgu deposu sorgu bilgilerini toplar ve çözümlemenin öneriyi yapmak için kullandığı ayrıntılı sorgu çalışma zamanı ve sıklık istatistiklerini sağlar.

## <a name="next-steps"></a>Sonraki adımlar
- MySQL için Azure veritabanı 'nda [izleme ve ayarlama](concepts-monitoring.md) hakkında daha fazla bilgi edinin.