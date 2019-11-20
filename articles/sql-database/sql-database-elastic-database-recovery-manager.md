---
title: Parça eşleme sorunlarını giderecek kurtarma Yöneticisi
description: Parça eşlemeleriyle ilgili sorunları gidermek için RecoveryManager sınıfını kullanın
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823846"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>RecoveryManager sınıfı ile parça eşleme sorunlarını düzeltme

[RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) sınıfı, ADO.NET uygulamalarına, parçalı bir veritabanı ortamındaki küresel parça HARITASI (GSM) ve yerel parça eşlemesi (LSM) arasındaki tutarsızlıkları kolayca algılamayı ve düzeltmesini sağlar.

GSM ve LSM, her veritabanının parçalı bir ortamda eşlenmesini izler. Bazen, GSM ve LSM arasında bir kesme oluşur. Bu durumda, kesmeyi algılamak ve onarmak için RecoveryManager sınıfını kullanın.

RecoveryManager sınıfı, [elastik veritabanı istemci kitaplığının](sql-database-elastic-database-client-library.md)bir parçasıdır.

![Parça Haritası][1]

Terim tanımları için bkz. [elastik veritabanı araçları sözlüğü](sql-database-elastic-scale-glossary.md). Parçalı bir çözümdeki verileri yönetmek için **Shardmapmanager** 'ın nasıl kullanıldığını anlamak için bkz. parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Kurtarma Yöneticisi 'nin kullanılması

Parçalı bir veritabanı ortamında, veritabanı başına tek bir kiracı ve sunucu başına çok sayıda veritabanı vardır. Ortamda çok sayıda sunucu da olabilir. Her bir veritabanı parça eşlemesinde eşlendiğinden, çağrılar doğru sunucuya ve veritabanına yönlendirilebilir. Veritabanları bir **parça anahtarına**göre izlenir ve her parçaya bir **dizi anahtar değer**atanır. Örneğin, bir parça anahtarı "D" ile "F" arasında müşteri adlarını temsil edebilir. Tüm parçaları (yani, veritabanları) ve bunların eşleme aralıklarını eşleme, **genel parça eşlemesinde (GSM)** bulunur. Her veritabanı Ayrıca, **yerel parça Haritası (LSM)** olarak bilinen parça üzerinde yer alan aralıkların haritasını içerir. Bir uygulama bir parça ile bağlanıyorsa, eşleme uygulamayla hızlı bir şekilde geri alma için önbelleğe alınır. LSM, önbelleğe alınmış verileri doğrulamak için kullanılır.

Aşağıdaki nedenlerden dolayı GSM ve LSM eşitlenmemiş olabilir:

1. Bir parçanın, aralığı artık kullanımda veya bir parçanın yeniden adlandırılmasına inanmayacak şekilde silinmesi. Bir parçayı silme, **yalnız bırakılmış parça eşlemesinde**oluşur. Benzer şekilde, yeniden adlandırılmış bir veritabanı, yalnız bırakılmış bir parça eşlemesine neden olabilir. Değişikliğin amacına bağlı olarak, parçanın kaldırılması veya parça konumunun güncelleştirilmesi gerekir. Silinen bir veritabanını kurtarmak için bkz. [silinen veritabanını geri yükleme](sql-database-recovery-using-backups.md).
2. Coğrafi Yük devretme olayı oluşur. Devam etmek için, birinin sunucu adını ve uygulama içindeki parça eşleme Yöneticisi 'nin veritabanı adını güncelleştirmesi ve sonra parça eşlemesindeki tüm parçalar için parça eşleme ayrıntılarını güncelleştirmesi gerekir. Coğrafi Yük devretme söz konusu olduğunda, bu kurtarma mantığının yük devretme iş akışı içinde otomatikleştirilmesi gerekir. Kurtarma eylemlerinin otomatikleştirilmesi, coğrafi olarak etkinleştirilmiş veritabanları için daha az yönetilebilirlik sunar ve el ile insan eylemlerini önler. Veri merkezi kesintisi olursa bir veritabanını kurtarmaya yönelik seçenekler hakkında bilgi edinmek için bkz. [Iş sürekliliği](sql-database-business-continuity.md) ve [olağanüstü durum kurtarma](sql-database-disaster-recovery.md).
3. Parça veya ShardMapManager veritabanı daha önceki bir zaman noktasına geri yüklendi. Yedeklemeleri kullanarak zaman kurtarma hakkında bilgi edinmek için bkz. [yedeklemeleri kullanarak kurtarma](sql-database-recovery-using-backups.md).

Azure SQL veritabanı elastik veritabanı araçları, coğrafi çoğaltma ve geri yükleme hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Genel Bakış: SQL veritabanı ile bulut iş sürekliliği ve veritabanı olağanüstü durum kurtarma](sql-database-business-continuity.md)
* [Elastik veritabanı araçlarını kullanmaya başlama](sql-database-elastic-scale-get-started.md)  
* [ShardMap yönetimi](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Bir ShardMapManager 'dan RecoveryManager alma

İlk adım, bir RecoveryManager örneği oluşturmaktır. [Getrecoverymanager yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) , geçerli [shardmapmanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) örneği için kurtarma yöneticisini döndürür. Parça haritadaki tutarsızlıklara yönelik olarak, ilk olarak belirli parça eşlemesi için RecoveryManager 'ı almalısınız.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Bu örnekte, RecoveryManager, ShardMapManager 'dan başlatılır. Bir ShardMap içeren ShardMapManager da zaten başlatılmış.

Bu uygulama kodu parça haritasını kullandığından, Factory yönteminde kullanılan kimlik bilgileri (önceki örnekte, smmConnectionString) bağlantı tarafından başvurulan GSM veritabanında okuma-yazma izinlerine sahip kimlik bilgileri olmalıdır dizisinde. Bu kimlik bilgileri genellikle veriye bağımlı yönlendirmeye yönelik bağlantıları açmak için kullanılan kimlik bilgilerinden farklıdır. Daha fazla bilgi için bkz. [elastik veritabanı istemcisinde kimlik bilgilerini kullanma](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Parça silindikten sonra ShardMap 'ten parça kaldırma

[Çıkarılabilir parça yöntemi](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) , parça eşlemesinden verilen parçayı ayırır ve parça ile ilişkili eşlemeleri siler.  

* Konum parametresi, parçalı olarak, özel olarak sunucu adı ve veritabanı adı olan parça konumudur.
* ShardMapName parametresi parça eşleme adıdır. Bu, yalnızca birden çok parça eşlemesi aynı parça eşleme Yöneticisi tarafından yönetilmiyorsa gereklidir. İsteğe bağlı.

> [!IMPORTANT]
> Bu tekniği yalnızca, güncelleştirilmiş eşleme aralığının boş olduğundan eminseniz kullanın. Yukarıdaki yöntemler, taşınmakta olan Aralık için verileri denetlemez, bu nedenle kodunuzda denetimleri eklemek en iyisidir.

Bu örnek, parça eşlemesinden parçaları kaldırır.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Parça Haritası, parçanın silinmesinden önce GSM içindeki parça konumunu yansıtır. Parça silindiği için bunun bilerek olduğu varsayılır ve parçalama anahtar aralığı artık kullanımda değildir. Aksi takdirde, zaman içinde nokta geri yüklemeyi çalıştırabilirsiniz. parçayı daha önceki bir zaman noktasından kurtarmak için. (Bu durumda, parçalı tutarsızlıkları algılamak için aşağıdaki bölümü gözden geçirin.) Kurtarmak için bkz. [Noktadan noktaya kurtarma](sql-database-recovery-using-backups.md).

Veritabanı silmenin kasıtlı olarak varsayıldığından, son yönetim Temizleme eylemi, parça eşleme Yöneticisi 'nde olan girişi silmek olur. Bu, uygulamanın istem dışı olarak beklenmeyen bir aralığa bilgi yazmasını engeller.

## <a name="to-detect-mapping-differences"></a>Eşleme farklarını algılamak için

[Detectmappingfarklar yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) , parça haritalarından (yerel veya genel) birini seçip, her iki parça HARITADA (GSM ve LSM) eşlemeleri bağdaştırın.

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Konum* , sunucu adını ve veritabanı adını belirtir.
* *Shardmapname* parametresi parça eşleme adıdır. Bu, yalnızca birden çok parça eşlemesi aynı parça eşleme Yöneticisi tarafından yönetiliyorsa gereklidir. İsteğe bağlı.

## <a name="to-resolve-mapping-differences"></a>Eşleme farklarını çözümlemek için

[Resolvemappingfarklılığı yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) , parça haritalarından birini (yerel veya genel) bir arada seçer ve her iki parça HARITADA (GSM ve LSM) eşlemeleri karşılaştırın.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *Recoverytoken* parametresi, belirli parça için GSM ve LSM arasındaki eşlemelerde farkları sıralar.
* [MappingDifferenceResolution numaralandırması](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) , parça eşlemeleri arasındaki farkı çözme yöntemini belirtmek için kullanılır.
* **MappingDifferenceResolution. KeepShardMapping** , lsm doğru eşlemeyi içerdiğinde ve bu nedenle parçadaki eşlemenin kullanılması gerektiği için önerilir. Bu, genellikle bir yük devretme işlemi varsa olur: parça artık yeni bir sunucuda bulunur. Parçanın öncelikle GSM 'den kaldırılması gerektiğinden (RecoveryManager. çıkarılabilir parça yöntemi kullanılarak), GSM üzerinde artık bir eşleme yok. Bu nedenle, parça eşlemesini yeniden oluşturmak için LSM kullanılması gerekir.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Parça geri yüklendikten sonra ShardMap 'e parça iliştirme

[Attachkıard yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) , verilen parçayı parça eşlemesine iliştirir. Daha sonra, parça eşleme tutarsızlıklarını algılar ve eşlemeleri parça geri yüklemesinin noktasındaki parçalarla eşleşecek şekilde güncelleştirir. Bu, veritabanının, zaman damgasıyla eklenen yeni bir veritabanına varsayılan olarak, ilk veritabanı adını (parça geri yüklenmeden önce) yansıtacak şekilde yeniden adlandırılabileceği varsayılır.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *Konum* parametresi, eklenen parçanın sunucu adı ve veritabanı adıdır.
* *Shardmapname* parametresi parça eşleme adıdır. Bu, yalnızca birden çok parça eşlemesi aynı parça eşleme Yöneticisi tarafından yönetilmiyorsa gereklidir. İsteğe bağlı.

Bu örnek, kısa süre önce önceki bir noktadan geri yüklenen parça eşlemesine parça ekler. Parça (LSM içindeki parça için eşleme) geri yüklendiğinden bu yana, GSM içindeki parça girişi ile tutarsız olabilir. Bu örnek kodun dışında, parça geri yüklendi ve veritabanının özgün adı olarak yeniden adlandırıldı. Geri yüklendiği için, LSM içindeki eşlemenin güvenilen eşleme olduğunu varsaymıştı.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Parçaları coğrafi Yük devretme (geri yükleme) sonrasında parça konumlarını güncelleştirme

Coğrafi Yük devretme varsa, ikincil veritabanına yazma erişimi yapılır ve yeni birincil veritabanı olur. Sunucunun adı ve potansiyel olarak veritabanı (yapılandırmanıza bağlı olarak) özgün Birincilden farklı olabilir. Bu nedenle, GSM ve LSM içindeki parça için olan eşleme girdileri düzeltilmelidir. Benzer şekilde, veritabanı farklı bir ada veya konuma veya daha önceki bir zaman noktasına geri yüklenirse, bu, parça haritalarındaki tutarsızlıklara neden olabilir. Parça eşleme Yöneticisi, açık bağlantıların doğru veritabanına dağıtımını işler. Dağıtım, parça eşlemesindeki verileri ve uygulama isteğinin hedefi olan parçalı anahtar değerini temel alır. Coğrafi Yük devretme sonrasında bu bilgilerin, kurtarılan veritabanının doğru sunucu adı, veritabanı adı ve parça eşlemesi ile güncelleştirilmeleri gerekir.

## <a name="best-practices"></a>En iyi uygulamalar

Coğrafi Yük devretme ve kurtarma, genellikle Azure SQL veritabanlarının iş sürekliliği özelliklerinden birini kullanarak uygulamanın bulut Yöneticisi tarafından yönetilen işlemlerdir. İş sürekliliği planlaması, iş işlemlerinin kesintiye uğramadan devam edememesini sağlamak için işlemler, yordamlar ve ölçüler gerektirir. Bu iş akışında, GSM ve LSM 'nin gerçekleştirilen kurtarma eylemine göre güncel kalmasını sağlamak için, RecoveryManager sınıfının bir parçası olarak kullanılabilir yöntemler kullanılmalıdır. GSM ve LSM 'nin bir yük devretme olayından sonra doğru bilgileri yansıtmasını sağlamaya yönelik beş temel adım vardır. Bu adımları yürütmek için uygulama kodu mevcut araçlarla ve iş akışıyla tümleştirilebilir.

1. ShardMapManager 'dan RecoveryManager 'ı alın.
2. Eski parçalamayı parça eşlemesinden ayırın.
3. Yeni parça konumu da dahil olmak üzere parça eşlemesine yeni parça ekleyin.
4. GSM ve LSM arasındaki eşlemede tutarsızlıklar tespit edin.
5. LSM 'ye güvenen GSM ve LSM arasındaki farkları çözün.

Bu örnek aşağıdaki adımları gerçekleştirir:

1. Yük devretme olayından önceki parça konumlarını yansıtan parça eşlemesinden parçaları kaldırır.
2. Yeni parça konumlarını yansıtan parça eşlemesine parçalar ekler ("Configuration. SecondaryServer" parametresi yeni sunucu adı, ancak aynı veritabanı adıdır).
3. Her parça için GSM ve LSM arasındaki eşleme farklarını algılayarak kurtarma belirteçlerini alır.
4. Her bir parçanın LSM 'sinden eşleştirmeye güvenerek tutarsızlıkları çözer.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
