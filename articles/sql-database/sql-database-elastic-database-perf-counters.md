---
title: Parça eşleme yöneticisini izlemek için performans sayaçları
description: ShardMapManager sınıfı ve verilere bağımlı yönlendirme performansı sayaçları
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823894"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Parça eşleme Yöneticisi 'nin performansını izlemek için performans sayaçları oluşturma

Performans sayaçları, [veri bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md) işlemlerinin performansını izlemek için kullanılır. Bu sayaçlara, "elastik veritabanı: parça yönetimi" kategorisinin altında performans Izleyicisinde erişilebilir.

Özellikle de [verilere bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md)kullanırken parça [Haritası yöneticisinin](sql-database-elastic-scale-shard-map-management.md)performansını yakalayabilirsiniz. Sayaçlar, Microsoft. Azure. SqlDatabase. Elayapışscale. Client sınıfının yöntemleriyle oluşturulur.  


**En son sürüm için:** [Microsoft. Azure. SQLDatabase. Elayapışscale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)öğesine gidin. Ayrıca bkz. [bir uygulamayı en son elastik veritabanı istemci kitaplığını kullanacak şekilde yükseltme](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Ön koşullar

* Performans kategorisi ve sayaçlarını oluşturmak için Kullanıcı, uygulamayı barındıran makinede yerel **Yöneticiler** grubunun bir parçası olmalıdır.  
* Bir performans sayacı örneği oluşturmak ve sayaçları güncelleştirmek için, kullanıcının **Yöneticiler** veya **Performans İzleyicisi Kullanıcı** grubunun üyesi olması gerekir.

## <a name="create-performance-category-and-counters"></a>Performans kategorisi ve sayaçları oluşturma

Sayaçları oluşturmak için, [Shardmapmanagementfactory sınıfının](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)CreatePerformanceCategoryAndCounters yöntemini çağırın. Yalnızca bir yönetici yöntemi yürütebilir:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Yöntemi yürütmek için [Bu](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell betiğini de kullanabilirsiniz.
Yöntemi aşağıdaki performans sayaçlarını oluşturur:  

* **Önbelleğe alınan eşlemeler**: parça eşlemesi için önbelleğe alınan eşlemelerin sayısı.
* **DDR işlemi/sn**: parça eşlemesi için veri bağımlı yönlendirme işlemleri oranı. Bu sayaç, bir [Openconnectionforkey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) çağrısı hedef parça ile başarılı bir bağlantı ile sonuçlanana zaman güncelleştirilir.
* **Eşleme arama önbelleği isabet sayısı/sn**: parça eşlemesindeki eşlemeler için başarılı önbellek arama işlemlerinin oranı.
* **Eşleme arama önbelleği isabetsizliği/sn**: parça eşlemesinde eşlemeler için başarısız önbellek arama işlemlerinin oranı.
* **Önbellekte/sn 'ye eklenen veya güncellenen eşlemeler**: parça haritası için önbellekte eşlemelerin eklendiği veya güncelleştirildiği oran.
* **Önbellekten kaldırılan eşlemeler/sn**: eşlemelerin, parça haritası için önbellekten kaldırılma hızıdır.

Performans sayaçları, işlem başına her önbelleğe alınmış parça haritası için oluşturulur.  

## <a name="notes"></a>Notlar

Aşağıdaki olaylar, performans sayaçlarının oluşturulmasını tetikler:  

* ShardMapManager, parça haritaları içeriyorsa, [kardmapmanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) 'ı [Eager yüklemesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)ile başlatma. Bunlar [Getsqlshardmapmanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) ve [Trygetsqlshardmapmanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) yöntemlerini içerir.
* Parçalı haritanın başarılı bir şekilde aranması ( [Getshardmap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [getlistshardmap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) veya [getrangeshardmap](https://msdn.microsoft.com/library/azure/dn824173.aspx)()).
* CreateShardMap () kullanılarak parça eşlemesinin başarıyla oluşturulması.

Performans sayaçları, parça haritasında ve eşlemelerde gerçekleştirilen tüm önbellek işlemleri tarafından güncelleştirilir. DeleteShardMap () kullanılarak parça haritasının başarıyla kaldırılması, performans sayaçları örneğinin silinmesine neden olur.  

## <a name="best-practices"></a>En iyi uygulamalar

* ShardMapManager nesnesinin oluşturulmasından önce, performans kategorisi ve sayaçlarının oluşturulması yalnızca bir kez gerçekleştirilmelidir. CreatePerformanceCategoryAndCounters () komutunun her yürütülmesi önceki sayaçları temizler (tüm örnekler tarafından bildirilen verileri kaybetmektedir) ve yenilerini oluşturur.  
* Performans sayacı örnekleri işlem başına oluşturulur. Önbellekten bir parça eşlemesinin herhangi bir uygulama kilitlenmesi veya kaldırılması, performans sayaçları örneklerinin silinmesine neden olur.  

### <a name="see-also"></a>Ayrıca bkz.

[Elastik Veritabanı özelliklerine genel bakış](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
