---
title: En son elastik veritabanı istemci kitaplığına yükselt
description: Elastik veritabanı istemci kitaplığını yükseltmek için NuGet kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793424"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>En son elastik veritabanı istemci kitaplığını kullanmak için bir uygulamayı yükseltme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastik veritabanı istemci kitaplığının](elastic-database-client-library.md) yeni sürümleri, NuGet ve Visual Studio 'Daki NuGet Paket Yöneticisi arabirimi aracılığıyla kullanılabilir. Yükseltmeler, istemci kitaplığının yeni özelliklerine yönelik hata düzeltmeleri ve destek içerir.

**En son sürüm için:** [Microsoft. Azure. SQLDatabase. Elayapışscale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)öğesine gidin.

Uygulamanızı yeni kitaplıkla yeniden oluşturun ve yeni özellikleri desteklemek için Azure SQL veritabanı 'nda veritabanlarınızda depolanan mevcut parça Haritası Yöneticisi meta verilerini değiştirin.

Bu adımları sırasıyla gerçekleştirmek, meta veri nesneleri güncelleştirilirken istemci kitaplığının eski sürümlerinin artık ortamınızda mevcut olmamasını sağlar. Bu, eski sürüm meta veri nesnelerinin yükseltmeden sonra oluşturulmayacağı anlamına gelir.

## <a name="upgrade-steps"></a>Yükseltme adımları

**1. uygulamalarınızı yükseltin.** Visual Studio 'da, kitaplığı kullanan tüm geliştirme projelerinize en son istemci kitaplığı sürümünü indirip başvuru yapın; sonra yeniden derleyin ve dağıtın.

* Visual Studio çözümünüzde **Araçlar**  -->  **NuGet Paket Yöneticisi**  -->   **çözüm için NuGet Paketlerini Yönet**' i seçin.
* (Visual Studio 2013) Sol panelde **güncelleştirmeler**' i seçin ve ardından pencerede görüntülenen **Azure SQL veritabanı elastik ölçek istemci kitaplığındaki** **Güncelleştir** düğmesini seçin.
* (Visual Studio 2015) Filtre kutusunu, **yükseltme kullanılabilir** olarak ayarlayın. Güncelleştirilecek paketi seçin ve **Güncelleştir** düğmesine tıklayın.
* (Visual Studio 2017) İletişim kutusunun üst kısmında **güncelleştirmeler**' i seçin. Güncelleştirilecek paketi seçin ve **Güncelleştir** düğmesine tıklayın.
* Derleyin ve dağıtın.

**2. betiklerinizi yükseltin.** Parçaları yönetmek için **PowerShell** betikleri kullanıyorsanız, [Yeni kitaplık sürümünü indirin](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) ve komut dosyalarını yürütebileceğiniz dizine kopyalayın.

**3. bölünmüş birleştirme hizmetinizi yükseltin.** Parçalı verileri yeniden düzenlemek için elastik veritabanı bölünmüş birleştirme aracını kullanırsanız, [aracın en son sürümünü indirip dağıtın](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Hizmeti için ayrıntılı yükseltme adımları [burada](elastic-scale-overview-split-and-merge.md)bulunabilir.

**4. parça eşleme Yöneticisi veritabanlarınızı yükseltin**. Azure SQL veritabanı 'nda parça haritalarınızı destekleyen meta verileri yükseltin.  Bunu, PowerShell veya C# kullanarak gerçekleştirebildiğiniz iki yolu vardır. Her iki seçenek de aşağıda gösterilmiştir.

***Seçenek 1: PowerShell kullanarak meta verileri yükseltme***

1. NuGet için en son komut satırı yardımcı programını [buradan](https://nuget.org/nuget.exe) indirin ve bir klasöre kaydedin.
2. Bir komut Istemi açın, aynı klasöre gidin ve komutu verin: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Az önce indirdiğiniz yeni istemci DLL sürümünü içeren alt klasöre gidin, örneğin: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Elastik veritabanı istemci yükseltme betiğini [betik merkezi](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)' nden INDIRIN ve DLL dosyasını içeren klasöre kaydedin.
5. Bu klasörden komut isteminden "PowerShell .\upgrade.ps1" komutunu çalıştırın ve istemleri izleyin.

***Seçenek 2: C kullanarak meta verileri yükseltme #***

Alternatif olarak, ShardMapManager ' ı açan, tüm parçalar üzerinde yineleme yapan bir Visual Studio uygulaması oluşturun ve bu örnekte olduğu gibi [Upgradelocalstore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) ve [upgradeglobalstore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) yöntemlerini çağırarak meta veri yükseltmesini gerçekleştirir:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Meta veri yükseltmeleri için bu teknikler, zarar olmadan birden çok kez uygulanabilir. Örneğin, eski bir istemci sürümü yanlışlıkla zaten güncelleştirildikten sonra bir parça oluşturursa, altyapınız genelinde en son meta veri sürümünün mevcut olduğundan emin olmak için yükseltmeyi tüm parçalar arasında yeniden çalıştırabilirsiniz.

**Note:**  İstemci kitaplığının güncel olarak yayımlanan yeni sürümleri, Azure SQL veritabanı 'nda parça eşleme Yöneticisi meta verilerinin önceki sürümleriyle çalışmaya devam eder ve tam tersi de geçerlidir.   Ancak, en son istemcideki yeni özelliklerden faydalanmak için meta verilerin yükseltilmesi gerekir.   Meta veri yükseltmelerin, kullanıcı verilerine veya uygulamaya özgü verileri, yalnızca parça eşleme Yöneticisi tarafından oluşturulan ve kullanılan nesneleri etkilemeyeceğini unutmayın.  Ve uygulamalar yukarıda açıklanan yükseltme sırasıyla çalışmaya devam eder.

## <a name="elastic-database-client-version-history"></a>Elastik veritabanı istemci sürümü geçmişi

Sürüm geçmişi için [Microsoft. Azure. SQLDatabase. Elaol Scale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) öğesine gidin

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png