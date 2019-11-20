---
title: İş yükü yönetimi
description: Azure SQL veri ambarı 'nda iş yükü yönetimini uygulama kılavuzu.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/30/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 2563ea4ab498c11c846cfe79f0e668f7d491c2e7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692345"
---
# <a name="what-is-workload-management"></a>İş yükü yönetimi nedir?

Karışık iş yüklerini çalıştırmak, meşgul sistemlerdeki kaynak zorluklarını ortaya çıkarabilir.  Çözüm mimarları, turlar için yeterli kaynak olduğundan emin olmak için klasik veri ambarı etkinliklerini ayırmanın (verileri yükleme, dönüştürme ve sorgulama) yollarını arar.  

Fiziksel sunucu yalıtımı, aşırı kullanılan, fazla kullanılabilir olan altyapının cep veya önbelleklerin donanım başlatma ve durdurma ile sürekli olarak bulunduğu bir durumda olmasına neden olabilir.  Başarılı bir iş yükü yönetim şeması, kaynakları etkili bir şekilde yönetir, verimli kaynak kullanımı sağlar ve yatırım getirisi (ROı) en üst düzeye çıkarır.

Veri ambarı iş yükü, transpire 'in bir veri ambarı ile ilişkili tüm işlemlerine başvurur. Bu bileşenlerin derinliği ve düzeyleri, veri ambarının vade düzeyine bağlıdır.  Veri ambarı iş yükü şunları kapsar: 
- Verileri ambara yükleme işleminin tamamı 
- Veri ambarı analizini ve raporlamayı gerçekleştirme
- Veri ambarındaki verileri yönetme 
- Veri ambarından verileri dışarı aktarma

Veri ambarının performans kapasitesi, [veri ambarı birimlerine](what-is-a-data-warehouse-unit-dwu-cdwu.md)göre belirlenir.
- Tüm performans profillerine ayrılan kaynakları görüntülemek için bkz. [bellek ve eşzamanlılık sınırları] bellek-eşzamanlılık-limits.md).
- Kapasiteyi ayarlamak için [ölçeği değiştirebilir veya](quickstart-scale-compute-portal.md)azaltabilirsiniz.


## <a name="workload-management-concepts"></a>İş yükü yönetimi kavramları
Geçmişte, [kaynak sınıfları](resource-classes-for-workload-management.md)aracılığıyla SQL veri ambarı 'nda sorgu performansını yönetiyordunuz.  Rol üyeliğine göre bir sorguya bellek atamaya izin verilen kaynak sınıfları.  Kaynak sınıflarıyla ilgili birincil zorluk, yapılandırıldıktan sonra iş yükünü denetlemek için bir idare veya özellik yoktu.  

Örneğin, smallrc 'ye geçici bir kullanıcı rolü üyeliği verilmesi, kullanıcının sistemdeki belleğin %100 ' i kullanmasına izin verilir.  Kaynak sınıflarıyla birlikte, önemli iş yükleri için ayrılan kaynakları ayıracağından ve kaynakların kullanılabilir olduğundan emin olmanın bir yolu yoktur.

SQL Data Warehouse üzerinde iş yükü yönetimi üç üst düzey kavramdan oluşur: [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md), [iş yükü önemi](sql-data-warehouse-workload-importance.md) ve [iş yükü yalıtımı](sql-data-warehouse-workload-isolation.md).  Bu yetenekler, iş yükünüzün sistem kaynaklarını kullanma konusunda daha fazla denetim sağlar.

İş yükü sınıflandırması, bir iş yükü grubuna istek atama ve önem düzeylerini ayarlama kavramıdır.  Tarihsel olarak, bu atama [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)kullanarak rol üyeliğiyle yapılır.  Bu artık [Iş yükü oluşturma CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)aracılığıyla yapılabilir.  Sınıflandırma özelliği, isteklerin sınıflandırılacağı etiket, oturum ve zaman gibi daha zengin bir seçenek kümesi sağlar.

İş yükü önemi, bir isteğin kaynaklara erişim sırasındaki sırayı etkiler.  Yoğun bir sistemde, yüksek öneme sahip bir istek kaynaklara ilk kez erişebilir.  Önem derecesi Ayrıca, kilitler için sıralı erişim de sağlar. 

İş yükü yalıtımı, bir iş yükü grubu için kaynakları ayırır.  Bir iş yükü grubunda ayrılan kaynaklar, yürütmeyi sağlamak için özel olarak o iş yükü grubu için tutulur.  İş yükü grupları ayrıca, kaynak sınıfları gibi istek başına atanan kaynak miktarını tanımlamanızı sağlar.  İş yükü grupları bir istek kümesinin tüketebileceği kaynak miktarını ayırabilme veya üst sınır alma olanağı sağlar.  Son olarak, iş yükü grupları, isteklere sorgu zaman aşımı gibi kurallar uygulamak için bir mekanizmadır.  


## <a name="next-steps"></a>Sonraki adımlar

- İş yükü sınıflandırması hakkında daha fazla bilgi için bkz. [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).  
- İş yükü yalıtımı hakkında daha fazla bilgi için bkz. [Iş yükü yalıtımı](sql-data-warehouse-workload-isolation.md).  
- İş yükü önemi hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md).  