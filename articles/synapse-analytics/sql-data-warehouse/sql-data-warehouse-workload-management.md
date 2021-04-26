---
title: İş yükü yönetimi
description: Azure SYNAPSE Analytics 'te iş yükü yönetimini uygulama kılavuzu.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5640c1e254c4738ab53881544a09808b4894a462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676978"
---
# <a name="what-is-workload-management"></a>İş yükü yönetimi nedir?

Karışık iş yüklerini çalıştırmak, meşgul sistemlerdeki kaynak zorluklarını ortaya çıkarabilir.  Çözüm mimarları, Turs 'ye kadar yeterli kaynak olduğundan emin olmak için klasik veri ambarı etkinliklerini (verileri yükleme, dönüştürme ve sorgulama) ayırmanın yollarını arar.  

Fiziksel sunucu yalıtımı, aşırı kullanılan, fazla kullanılabilir olan altyapının cep veya önbelleklerin donanım başlatma ve durdurma ile sürekli olarak bulunduğu bir durumda olmasına neden olabilir.  Başarılı bir iş yükü yönetim şeması, kaynakları etkili bir şekilde yönetir, verimli kaynak kullanımı sağlar ve yatırım getirisi (ROı) en üst düzeye çıkarır.

Veri ambarı iş yükü, transpire 'in bir veri ambarı ile ilişkili tüm işlemlerine başvurur. Bu bileşenlerin derinliği ve düzeyleri, veri ambarının vade düzeyine bağlıdır.  Veri ambarı iş yükü şunları kapsar:

- Verileri ambara yükleme işleminin tamamı
- Veri ambarı analizini ve raporlamayı gerçekleştirme
- Veri ambarındaki verileri yönetme
- Veri ambarından verileri dışarı aktarma

Veri ambarının performans kapasitesi, [veri ambarı birimlerine](what-is-a-data-warehouse-unit-dwu-cdwu.md)göre belirlenir.

- Tüm performans profillerine ayrılan kaynakları görüntülemek için bkz. [bellek ve eşzamanlılık sınırları](memory-concurrency-limits.md).
- Kapasiteyi ayarlamak için [ölçeği değiştirebilir veya](quickstart-scale-compute-portal.md)azaltabilirsiniz.

## <a name="workload-management-concepts"></a>İş yükü yönetimi kavramları

Geçmişte, Azure 'da SYNAPSE SQL SYNAPSE, [kaynak sınıflar](resource-classes-for-workload-management.md)aracılığıyla sorgu performansını yönetiyordunuz.  Rol üyeliğine göre bir sorguya bellek atamaya izin verilen kaynak sınıfları.  Kaynak sınıflarıyla ilgili birincil zorluk, yapılandırıldıktan sonra iş yükünü denetlemek için bir idare veya özellik yoktu.  

Örneğin, smallrc 'ye geçici bir kullanıcı rolü üyeliği verilmesi, kullanıcının sistemdeki belleğin %100 ' i kullanmasına izin verilir.  Kaynak sınıflarıyla birlikte, önemli iş yükleri için ayrılan kaynakları ayıracağından ve kaynakların kullanılabilir olduğundan emin olmanın bir yolu yoktur.

Azure SYNAPSE 'de adanmış SQL havuzu iş yükü yönetimi üç üst düzey kavramdan oluşur: [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md), [iş yükü önemi](sql-data-warehouse-workload-importance.md)ve [iş yükü yalıtımı](sql-data-warehouse-workload-isolation.md).  Bu yetenekler, iş yükünüzün sistem kaynaklarını kullanma konusunda daha fazla denetim sağlar.

İş yükü sınıflandırması, bir iş yükü grubuna istek atama ve önem düzeylerini ayarlama kavramıdır.  Tarihsel olarak, bu atama [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class)kullanarak rol üyeliğiyle yapılır.  Bu eylem artık [Iş yükü oluşturma CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)aracılığıyla yapılabilir.  Sınıflandırma özelliği, isteklerin sınıflandırılacağı etiket, oturum ve zaman gibi daha zengin bir seçenek kümesi sağlar.

İş yükü önemi, bir isteğin kaynaklara erişim sırasındaki sırayı etkiler.  Yoğun bir sistemde, yüksek öneme sahip bir istek kaynaklara ilk kez erişebilir.  Önem derecesi Ayrıca, kilitler için sıralı erişim de sağlar.

İş yükü yalıtımı, bir iş yükü grubu için kaynakları ayırır.  Bir iş yükü grubunda ayrılan kaynaklar, yürütmeyi sağlamak için özel olarak o iş yükü grubu için tutulur.  İş yükü grupları ayrıca, kaynak sınıfları gibi istek başına atanan kaynak miktarını tanımlamanızı sağlar.  İş yükü grupları bir istek kümesinin tüketebileceği kaynak miktarını ayırabilme veya üst sınır alma olanağı sağlar.  Son olarak, iş yükü grupları, isteklere sorgu zaman aşımı gibi kurallar uygulamak için bir mekanizmadır.  

## <a name="next-steps"></a>Sonraki adımlar

- İş yükü sınıflandırması hakkında daha fazla bilgi için bkz. [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).  
- İş yükü yalıtımı hakkında daha fazla bilgi için bkz. [Iş yükü yalıtımı](sql-data-warehouse-workload-isolation.md).  
- İş yükü önemi hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md).  
- İş yükü yönetimi izleme hakkında daha fazla bilgi için bkz. [Iş yükü yönetim portalı izleme](sql-data-warehouse-workload-management-portal-monitor.md).  
