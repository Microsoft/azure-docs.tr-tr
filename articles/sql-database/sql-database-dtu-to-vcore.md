---
title: DTU 'dan vCore 'a geçiş
description: DTU modelinden vCore modeline geçiş yapın. VCore 'a geçiş, standart ve Premium katmanları arasında yükseltme veya eski sürüme düşürme ile benzerdir.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: f34439b7750ca1858e71d4a36121eb65001fff50
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811268"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU tabanlı modelden sanal çekirdek tabanlı modele geçiş

## <a name="migrate-a-database"></a>Veritabanını geçirme

DTU tabanlı satın alma modelinden bir veritabanını sanal çekirdek tabanlı satın alma modeline geçirmek, DTU tabanlı satın alma modelindeki standart ve Premium hizmet katmanları arasında yükseltme veya eski sürüme düşürme ile benzerdir.

## <a name="migrate-databases-that-use-geo-replication"></a>Coğrafi çoğaltma kullanan veritabanlarını geçirme

DTU tabanlı modelden sanal çekirdek tabanlı satın alma modeline geçiş yapmak, standart ve Premium hizmet katmanlarındaki veritabanları arasında coğrafi çoğaltma ilişkilerini yükseltmekle veya eski sürüme düşürmeye benzer. Geçiş sırasında Coğrafi çoğaltmayı durdurmanız gerekmez, ancak şu sıralama kurallarını izlemeniz gerekir:

- Yükseltme sırasında öncelikle ikincil veritabanını yükseltmeniz ve ardından birincili yükseltmeniz gerekir.
- Eski sürüme düşürme sırasında sırayı tersine çevirin: önce birincil veritabanını indirgemeniz ve sonra ikincili indirgemeniz gerekir.

İki elastik havuz arasında coğrafi çoğaltma kullanırken, bir havuzu birincil ve diğeri, ikincil olarak atamanız önerilir. Bu durumda, elastik havuzları geçirirken aynı sıralama kılavuzunu kullanmanız gerekir. Ancak, hem birincil hem de ikincil veritabanları içeren elastik havuzlarınız varsa, havuzu birincil olarak daha yüksek kullanım ile değerlendirin ve sıralama kurallarını uygun şekilde izleyin.  

Aşağıdaki tablo, belirli geçiş senaryoları için rehberlik sağlar:

|Geçerli hizmet katmanı|Hedef hizmet katmanı|Geçiş türü|Kullanıcı eylemleri|
|---|---|---|---|
|Standart|Genel amaçlı|Geni|Herhangi bir sırada geçirebilir, ancak uygun sanal çekirdek boyutlandırmayı sağlamak için gerekir *|
|Premium|İş açısından kritik|Geni|Herhangi bir sırada geçirebilir, ancak uygun sanal çekirdek boyutlandırmayı sağlamak için gerekir *|
|Standart|İş açısından kritik|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
|İş açısından kritik|Standart|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Premium|Genel amaçlı|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Genel amaçlı|Premium|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
|İş açısından kritik|Genel amaçlı|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Genel amaçlı|İş açısından kritik|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
||||

Standart katmanda her 100 DTU, en az 1 sanal çekirdek gerektirir ve Premium katmandaki her 125 DTU en az 1 sanal çekirdek gerektirir. \*

## <a name="migrate-failover-groups"></a>Yük devretme gruplarını geçirme

Birden çok veritabanına sahip yük devretme gruplarının geçirilmesi, birincil ve ikincil veritabanlarının tek tek geçirilmesini gerektirir. Bu işlem sırasında, aynı noktalar ve sıralama kuralları da geçerlidir. Veritabanları sanal çekirdek tabanlı satın alma modeline dönüştürüldükten sonra, yük devretme grubu aynı ilke ayarları ile geçerli olmaya devam edecektir.

### <a name="create-a-geo-replication-secondary-database"></a>Coğrafi çoğaltma ikincil veritabanı oluşturma

Yalnızca birincil veritabanı için kullandığınız hizmet katmanını kullanarak coğrafi çoğaltma ikincil veritabanı (coğrafi-ikincil) oluşturabilirsiniz. Yüksek günlük oluşturma hızına sahip veritabanları için, birincil ile aynı işlem boyutuyla coğrafi ikincil oluşturmanız önerilir.

Tek bir birincil veritabanı için elastik havuzda bir coğrafi ikincil oluşturuyorsanız, havuzun `maxVCore` ayarının birincil veritabanının işlem boyutuyla eşleştiğinden emin olun. Başka bir elastik havuzda birincil için bir coğrafi-ikincil oluşturuyorsanız, havuzların aynı `maxVCore` ayarlarına sahip olması önerilir.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>DTU tabanlı bir veritabanını sanal çekirdek tabanlı veritabanına dönüştürmek için veritabanı kopyasını kullanma

Hedef işlem boyutu, kaynak veritabanının en büyük veritabanı boyutunu desteklediği sürece, DTU tabanlı işlem boyutu olan bir veritabanını, kısıtlama olmadan veya özel sıralamaya sahip bir veritabanı olarak bir veritabanına kopyalayabilirsiniz. Veritabanı kopyalama işlemi, kopyalama işleminin başlangıç saatinden itibaren verilerin bir anlık görüntüsünü oluşturur ve kaynak ile hedef arasında veri eşitlenmez.

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanları için kullanılabilen belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [tek veritabanları Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-single-databases.md).
- Elastik havuzlara yönelik belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [elastik havuzlar Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md).
