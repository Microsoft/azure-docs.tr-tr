---
title: Hızlandırılmış veritabanı kurtarma
description: Azure SQL veritabanı, Azure SQL veritabanı 'nda tek veritabanları ve havuza alınmış veritabanları için hızlı ve tutarlı veritabanı kurtarma, anlık işlem geri alma ve agresif günlük kesilme sağlayan yeni bir özelliğe sahiptir ve Azure SQL Data 'daki veritabanları Ambarını.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: eff81693ff4c34dc00f66e9e5ea22e56d3ff9d77
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808097"
---
# <a name="accelerated-database-recovery"></a>Hızlandırılmış veritabanı kurtarma

**Hızlandırılmış veritabanı kurtarma (ADR)** , SQL veritabanı altyapısı kurtarma işlemini yeniden tasarlayarak, özellikle uzun süre çalışan işlemler söz konusu olduğunda veritabanı kullanılabilirliğini büyük ölçüde geliştiren yenı bir SQL veritabanı altyapısı özelliğidir. ADR Şu anda Azure SQL veritabanı 'nda tek veritabanları ve havuza alınmış veritabanları ve Azure SQL veri ambarı 'nda (Şu anda genel önizlemededir) veritabanları için kullanılabilir. ADR 'nin başlıca avantajları şunlardır:

- **Hızlı ve tutarlı veritabanı kurtarma**

  ADR ile, uzun süre çalışan işlemler genel kurtarma süresini etkilemez, sistemdeki etkin işlem sayısı veya boyutları ne olursa olsun hızlı ve tutarlı veritabanı kurtarmayı etkinleştirir.

- **Anlık işlem geri alma**

  ADR ile işlem geri alma işlemi, işlemin etkin olduğu zamandan veya gerçekleştirilen güncelleştirmelerin sayısına bakılmaksızın anında gerçekleşir.

- **Agresif günlük kesilmesi**

  ADR ile, işlem günlüğü, etkin uzun süre çalışan işlemlerin varlığına bile, denetimin dışına büyümesini önleyen bir şekilde kesilir.

## <a name="the-current-database-recovery-process"></a>Geçerli veritabanı kurtarma işlemi

SQL Server [veritabanı kurtarma, bu kurtarma modelini](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) izler ve aşağıdaki diyagramda gösterilen ve diyagramda daha ayrıntılı şekilde açıklanacak üç aşamadan oluşur.

![geçerli kurtarma işlemi](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analiz aşaması**

  Son başarılı denetim noktasının başından (veya en eski kirli sayfa LSN), her bir işlemin durumunu SQL Server durdurulduğunda tespit etmek için, işlem günlüğü sonuna kadar ileri tarama.

- **Yeniden yineleme aşaması**

  Tüm kaydedilmiş işlemleri yeniden gerçekleştirerek veritabanını kilitlenme süresi sırasında duruma getirmek için, işlem günlüğü sonuna kadar en eski işlenmemiş işlemden ileri tarama.

- **Geri alma aşaması**

  Kilitlenme süresi itibariyle etkin olan her işlem için, bu işlemin gerçekleştirdiği işlemleri geri alarak günlüğü geriye doğru geçer.

Bu tasarıma bağlı olarak, SQL veritabanı altyapısının beklenmeyen bir yeniden başlatmadan kurtarmak için geçen süre (kabaca), kilitlenme sırasında sistemdeki en uzun etkin işlemin boyutuyla orantılıdır. Kurtarma işlemi tamamlanmamış tüm işlemlerin geri alınmasını gerektirir. Gerekli sürenin uzunluğu, işlemin gerçekleştirildiği iş ile orantılıdır ve etkin olduğu zaman. Bu nedenle, SQL Server kurtarma işlemi uzun süren işlemlerin (büyük bir tabloya karşı büyük toplu ekleme işlemleri veya dizin derleme işlemleri gibi) bulunması uzun zaman alabilir.

Ayrıca, bu tasarıma göre büyük bir işlemin iptal edilmesi/geri alınması, yukarıda açıklandığı gibi aynı geri alma kurtarma aşamasını kullandığından uzun zaman alabilir.

Buna ek olarak, uzun süreli işlemler olduğunda SQL veritabanı altyapısı işlem günlüğünü kesemez çünkü kurtarma ve geri alma işlemleri için karşılık gelen günlük kayıtları gereklidir. Bu SQL veritabanı altyapısının tasarımının bir sonucu olarak, bazı müşteriler, işlem günlüğü boyutunun çok büyük bir süre büyüdüğü ve çok büyük miktarlarda sürücü alanı tükettiği sorunu ortaya koyor.

## <a name="the-accelerated-database-recovery-process"></a>Hızlandırılmış veritabanı kurtarma işlemi

ADR, SQL veritabanı altyapısı kurtarma sürecini tamamen yeniden tasarlayarak Yukarıdaki sorunları giderir:

- Günlüğü en eski etkin işlemin başlangıcına kadar taramak zorunda kalmadan, bunu sabit bir zaman/anında yapın. ADR ile, işlem günlüğü yalnızca son başarılı denetim noktasından (veya en eski kirli sayfa günlüğü sıra numarası (LSN)) işlenir. Sonuç olarak, kurtarma süresi uzun süren işlemlerden etkilenmez.
- Tüm işlem için günlüğü işlemek zorunda olmadığından, gerekli işlem günlüğü alanını en aza indirin. Sonuç olarak, işlem günlüğü, denetim noktaları ve yedeklemeler gerçekleşilerek kesilebilir.

ADR yüksek düzeyde, tüm fiziksel veritabanı değişikliklerinin sürümü çalıştırılarak hızlı veritabanı kurtarmaya erişir ve yalnızca sınırlı olan ve neredeyse anında geri alınabilecek mantıksal işlemleri geri alabilir. Kilitlenme süresi itibariyle etkin olan herhangi bir işlem iptal edildi olarak işaretlenir ve bu nedenle, bu işlemler tarafından oluşturulan tüm sürümler eşzamanlı kullanıcı sorguları tarafından yoksayılabilir.

ADR kurtarma işlemi, geçerli kurtarma işlemiyle aynı üç aşamadan oluşur. Bu aşamaların ADR ile nasıl çalıştığı, aşağıdaki diyagramda gösterilmektedir ve diyagram sonrasında daha ayrıntılı olarak açıklanmıştır.

![ADR kurtarma işlemi](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analiz aşaması**

  İşlem, yeniden oluşturma işlemi ve sürümü olmayan işlemler için günlük kayıtlarının kopyalanması eklenmesiyle birlikte bugün ile aynı kalır.
  
- Yeniden **yineleme** aşaması

  İki aşamaya kesildi (P)
  - 1\. aşama

      SLog öğesinden Yinele (en eski, son denetim noktasına kadar). Yalnızca sLog ' dan birkaç kaydı işlemesi gerektiğinden, yineleme hızlı bir işlemdir.
      
  - 2\. aşama

     Işlem günlüğünden yineleme, son denetim noktasından başlar (en eski işlenmemiş işlem yerine)
     
- **Geri alma aşaması**

   ADR ile geri alma aşaması, sürüm temelli olmayan işlemleri ve kalıcı sürüm deposunu (PVS), satır düzeyinde sürüm tabanlı geri alma işlemini gerçekleştirmek üzere mantıksal geri alma ile geri almak için sLog kullanarak neredeyse anında tamamlar.

## <a name="adr-recovery-components"></a>ADR kurtarma bileşenleri

ADR 'nin dört temel bileşeni şunlardır:

- **Kalıcı sürüm deposu (PVS)**

  Kalıcı sürüm deposu, geleneksel `tempdb` sürümü deposu yerine veritabanında oluşturulan satır sürümlerinin kalıcı hale getirilmesine yönelik yeni bir SQL veritabanı altyapısı mekanizmasıdır. PVS, kaynak yalıtımına izin verir ve okunabilir ikinciller kullanılabilirliğini geliştirir.

- **Mantıksal olarak döndürülüyor**

  Mantıksal geri döndürme, satır düzeyinde sürüm tabanlı geri alma gerçekleştirmekten sorumlu zaman uyumsuz bir işlemdir ve tüm sürümlenmiş işlemler için anlık işlem geri alma ve geri alma sağlar. Mantıksal döndürmeyi şu şekilde gerçekleştirilir:

  - Tüm durdurulan işlemleri izleyin ve bunları diğer işlemlere görünmez olarak işaretleyin. 
  - İşlem günlüğünü fiziksel olarak taramak ve tek seferde değişiklikleri geri almak yerine tüm kullanıcı işlemleri için PVS 'yi kullanarak geri alma işlemi gerçekleştiriliyor.
  - İşlem iptalinden hemen sonra tüm kilitler serbest bırakılıyor. Abort yalnızca bellekteki değişiklikleri işaretlemeyi içerdiğinden, işlem çok verimlidir ve bu nedenle kilitlerin uzun bir süre tutulması gerekmez.

- **sLog**

  sLog, sürüm dışı işlemlere ait günlük kayıtlarını depolayan, ikincil bir bellek içi günlük akışıdır (meta veri önbelleği eksik doğrulaması, kilit alma işlemleri vb.). SLog:

  - Düşük birim ve bellek içi
  - Denetim noktası işlemi sırasında serileştirilerek diskte kalıcı hale getirildi
  - İşlemler işleme olarak düzenli aralıklarla kesildi
  - Yalnızca sürümlenmemiş işlemleri işleyerek Yinele ve geri al işlemini hızlandırır  
  - Yalnızca gerekli günlük kayıtlarını koruyarak agresif işlem günlüğü kesilmesine izin vermez

- **Leyicisi**

  Temizleyici, düzenli aralıklarla uyandığı ve gerekli olmayan sayfa sürümlerini temizlediğinde zaman uyumsuz işlemdir.

## <a name="who-should-consider-accelerated-database-recovery"></a>Hızlandırılmış veritabanı kurtarmayı kimler kabul etmelidir

Aşağıdaki müşteri türleri ADR 'yi etkinleştirmeyi düşünmelidir:

- Uzun süren işlemlere sahip iş yükleri olan müşteriler.
- Etkin işlemlerin, işlem günlüğünün önemli ölçüde büyümesine neden olduğu durumları gördük müşteriler.  
- Uzun süreli veritabanı SQL Server süresi dolan müşteriler (beklenmeyen SQL Server yeniden başlatma veya el ile işlem geri alma gibi).

