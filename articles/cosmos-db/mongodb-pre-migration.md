---
title: MongoDB için Azure Cosmos DB API 'sine veri geçişinin geçiş öncesi adımları
description: Bu belge, MongoDB 'den Cosmos DB 'e veri geçişinin önkoşulları hakkında genel bakış sağlar.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anfeldma
ms.openlocfilehash: cdc5dc9cee3520d9a3f22ff710dfa193e6ef4fed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553298"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 'den Azure Cosmos DB MongoDB için API 'sine veri geçişleri için geçiş öncesi adımlar
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Bu MongoDB geçiş öncesi Kılavuzu, MongoDB 'yi ölçekte Azure Cosmos DB Mongo API 'sine geçirmeye yönelik bir seride ilk kez gönderilir. Kendi kendini yönetilen altyapıda MongoDB 'yi lisanslayan ve dağıtan müşteriler, Kullandıkça Öde fiyatlandırması ve esnek ölçeklenebilirlik ile Azure Cosmos DB gibi yönetilen bir bulut hizmetine geçiş yaparak veri kuruluşlarının maliyetini azaltmak ve yönetmek isteyebilir. Bu serinin amacı, müşteriyi geçiş işlemi boyunca rehberlik olmaktır:
>
> 1. [Geçiş öncesi](mongodb-pre-migration.md) -mevcut MongoDB verilerinin envanterini çıkarın, geçiş planlayın ve uygun geçiş araçlarını seçin.
> 2. Yürütme-sunulan [öğreticileri]()kullanarak MongoDB 'den Azure Cosmos DB 'e geçiş yapın.
> 3. [Geçiş sonrası](mongodb-post-migration.md) -yeni Azure Cosmos DB verilerinize karşı yürütmek üzere mevcut uygulamaları güncelleştirin ve iyileştirin.
>

Katı bir ön geçiş planı, takımınızın geçişinin dakikliklerini ve başarısı üzerinde bir fazla boyut etkiye sahip olabilir. Geçiş öncesi için iyi bir benzerleme vurguladı yeni bir proje başlatıyor. Gereksinimleri tanımlayarak başlayabilir, sonra dahil edilen görevleri özetler ve ayrıca en büyük görevleri ilk olarak tackled olacak şekilde önceliklendirin. Bu, Proje zamanlamanızı tahmin edilebilir hale getirme, ancak kuşkusuz, tahmin edilmemiş gereksinimlerin proje zamanlamasını karmaşıklaştırabilir ve karmaşık olmasına yardımcı olur. Geçişe geri dönme-geçiş öncesi aşamada kapsamlı bir yürütme planı oluşturma işlemi, süreçte beklenmedik geçiş görevlerini keşfetmenizi, geçiş sırasında zamandan tasarruf etmenizi ve hedeflerin karşılanmasını sağlamanıza yardımcı olma olasılığını en aza indirir.

Verilerinizi MongoDB 'ye (Şirket içi veya buluttaki) MongoDB için Azure Cosmos DB API 'sine geçirmeden önce şunları yapmalısınız:

1. [MongoDB için Azure Cosmos DB API 'sini kullanma hakkındaki önemli konuları okuyun](#considerations)
2. [Verilerinizi geçirmek için bir seçenek belirleyin](#options)
3. [İş yükleriniz için gereken aktarım hızını tahmin etme](#estimate-throughput)
4. [Verileriniz için en iyi bölüm anahtarını seçin](#partitioning)
5. [Verilerinize ayarlayabileceğiniz dizin oluşturma ilkesini anlayın](#indexing)

Geçiş için yukarıdaki önkoşulları zaten tamamladıysanız, [MongoDB verilerini Azure veritabanı geçiş hizmeti 'ni kullanarak MongoDB için Azure Cosmos DB API 'Sine geçirebilirsiniz](../dms/tutorial-mongodb-cosmos-db.md). Ayrıca, bir hesap oluşturmadıysanız, hesap oluşturma adımlarını gösteren [hızlı](create-mongodb-dotnet.md) başlangıçlara da gidebilirsiniz.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>MongoDB için Azure Cosmos DB API 'SI kullanılırken dikkat edilecek noktalar

Aşağıda, MongoDB için Azure Cosmos DB API 'SI hakkında özel özellikler verilmiştir:

- **Kapasite modeli**: Azure Cosmos DB veritabanı kapasitesi, verimlilik tabanlı bir modeli temel alır. Bu model, saniye [başına Istek birimlerini](request-units.md)temel alır. bu birim, saniye başına bir koleksiyonda yürütülen veritabanı işlemlerinin sayısını temsil eden bir birimdir. Bu kapasite [bir veritabanı veya koleksiyon düzeyinde](set-throughput.md)ayrılabilir ve bir ayırma modelinde veya [Otomatik ölçeklendirme sağlanan aktarım hızı](provision-throughput-autoscale.md)kullanılarak sağlanabilir.

- **Istek birimleri**: her veritabanı işleminin, Azure Cosmos DB Ilişkili bir istek birimleri (ru) maliyeti vardır. Yürütüldüğünde, bu, verilen bir saniyede kullanılabilir istek birimleri düzeyinden çıkarılır. Bir istek, şu anda ayrılmış RU/s ' den daha fazla RUs gerektiriyorsa, sorunu çözmenin iki seçeneği vardır veya sonraki ikinci olana kadar bekleyip işlemi yeniden deneyin.

- **Esnek kapasite**: belirli bir koleksiyon veya veritabanının kapasitesi herhangi bir zamanda değişebilir. Bu, veritabanının iş yükünüzün verimlilik gereksinimlerini esnek sağlamasına izin verir.

- **Otomatik** parçalama: Azure Cosmos DB yalnızca parça (veya bölüm anahtarı) gerektiren bir otomatik bölümlendirme sistemi sağlar. [Otomatik bölümleme mekanizması](partitioning-overview.md) tüm Azure Cosmos DB API 'lerinde paylaşılır ve kesintisiz veriler ve yatay dağıtım aracılığıyla ölçeklendirilmesine olanak tanır.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>MongoDB için Azure Cosmos DB API 'SI için geçiş seçenekleri

[MongoDB için Azure Cosmos DB API 'si Için Azure veritabanı geçiş hizmeti](../dms/tutorial-mongodb-cosmos-db.md) , tam olarak yönetilen bir barındırma platformu, geçiş izleme seçenekleri ve otomatik azaltma işleme sağlayarak veri geçişini kolaylaştıran bir mekanizma sağlar. Seçeneklerin tam listesi şunlardır:

|**Geçiş türü**|**Çözüm**|**Dikkat edilmesi gerekenler**|
|---------|---------|---------|
|Çevrimiçi|[Azure Veritabanı Geçiş Hizmeti](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; Büyük veri kümeleri için uygun ve canlı değişiklikleri çoğaltma <br/>&bull; Yalnızca diğer MongoDB kaynaklarıyla birlikte geçerlidir|
|Çevrimdışı|[Azure Veritabanı Geçiş Hizmeti](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; Büyük veri kümeleri için uygun ve canlı değişiklikleri çoğaltma <br/>&bull; Yalnızca diğer MongoDB kaynaklarıyla birlikte geçerlidir|
|Çevrimdışı|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Birden çok kaynağı ayarlama ve destekleyen kolay <br/>&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; Büyük veri kümeleri için uygun <br/>&bull; Checkişaret olmaması, geçiş işlemi sırasında herhangi bir sorunun tüm geçiş sürecinin yeniden başlatılmasını gerektirmeyeceği anlamına gelir.<br/>&bull; Sahipsiz bir sıra olmaması, birkaç hatalı dosyanın tüm geçiş sürecini durduramaması anlamına gelir. <br/>&bull; Belirli veri kaynakları için okuma aktarım hızını artırmak için özel kod gerekir|
|Çevrimdışı|[Mevcut Mongo araçları (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Kolayca ayarlama ve Tümleştirme <br/>&bull; Azaltıcı Les için özel işleme gerekir|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> İş yükleriniz için işleme gereksinimini tahmin etme

Azure Cosmos DB, verimlilik önceden sağlanır ve saniye başına Istek birimleri (RU) cinsinden ölçülür. Sanal makinelerin veya şirket içi sunucuların aksine, ru 'nın ölçeği dilediğiniz zaman daha kolay ve daha düşük. Sağlanan RUs sayısını anında değiştirebilirsiniz. Daha fazla bilgi için [Azure Cosmos DB istek birimleri](request-units.md)bölümüne bakın.

Veritabanı hesabı yapılandırmanıza, veri miktarına, belge boyutuna ve saniye başına gerekli okuma ve yazma verilerine göre Istek birimi miktarını öğrenmek için [Azure Cosmos DB kapasite Hesaplayıcı](https://cosmos.azure.com/capacitycalculator/) 'ı kullanabilirsiniz.

Aşağıdakiler, gerekli RUs sayısını etkileyen önemli faktörlerdir:
- **Belge boyutu**: bir öğe/belge boyutu arttıkça, öğe/belge okuma veya yazma Için tüketilen Rus sayısı da artar.

- **Belge özelliği sayısı**: bir belge oluşturmak veya güncelleştirmek Için tüketilen Rus sayısı, özelliklerinin sayısı, karmaşıklığı ve uzunluğu ile ilgilidir. [Dizin oluşturma özelliklerinin sayısını sınırlayarak](mongodb-indexing.md)yazma işlemleri için istek birimi tüketimini azaltabilirsiniz.

- **Sorgu desenleri**: sorgunun karmaşıklığı, sorgu tarafından kaç istek biriminin tüketildiğini etkiler. 

Sorguların maliyetini anlamanın en iyi yolu, Azure Cosmos DB örnek verileri kullanmak ve tüketilen ru sayısını çıkarmak için komut kullanarak [MongoDB kabuğundan örnek sorgular çalıştırmektir](connect-mongodb-account.md) `getLastRequestStastistics` :

`db.runCommand({getLastRequestStatistics: 1})`

Bu komut, aşağıdakine benzer bir JSON belgesinin çıktısını alacak:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[Tanılama ayarlarını](cosmosdb-monitor-resource-logs.md) , Azure Cosmos DB karşı yürütülen sorguların sıklığını ve desenlerini anlamak için de kullanabilirsiniz. Tanılama günlüklerinin sonuçları bir depolama hesabına, bir EventHub örneğine veya [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)gönderilebilir.  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Bölüm anahtarınızı seçin
Parça olarak da bilinen bölümlendirme, verileri geçirmeden önce önemli bir noktadır. Azure Cosmos DB, depolama ve aktarım hızı gereksinimlerini karşılamak üzere bir veritabanındaki kapasiteyi artırmak için tam olarak yönetilen bölümlendirme kullanır. Bu özellik, yönlendirme sunucularının barındırılmasına veya yapılandırılmasına gerek yoktur.   

Benzer şekilde bölümlendirme özelliği, kapasiteyi otomatik olarak ekler ve verileri uygun şekilde yeniden dengeler. Verileriniz için doğru bölüm anahtarını seçmeye ilişkin ayrıntılar ve öneriler için lütfen [bölüm anahtarı seçme makalesine](partitioning-overview.md#choose-partitionkey)bakın. 

## <a name="index-your-data"></a><a id="indexing"></a>Verilerinizin dizinini oluşturma

Azure Cosmos DB MongoDB sunucu sürümleri 3,6 ve üzeri için API 'SI yalnızca alanı otomatik olarak dizine ekler `_id` . Bu alan bırakılamaz. Bu, parça anahtarı başına alanın benzersizlik düzeyini otomatik olarak zorlar `_id` . Ek alanlara dizin eklemek için [MongoDB Dizin Yönetimi komutlarını](mongodb-indexing.md)uygularsınız. Bu varsayılan dizinleme ilkesi, tüm alanları varsayılan olarak dizinleyen Azure Cosmos DB SQL API’sinden farklıdır.

Azure Cosmos DB tarafından sunulan dizin oluşturma özellikleri, bileşik dizinler, benzersiz dizinler ve yaşam süresi (TTL) dizinlerinin eklenmesini içerir. Dizin yönetimi arabirimi, `createIndex()` komuta eşlenir. [Azure Cosmos DB MongoDB için API 'Sindeki dizin oluşturma](mongodb-indexing.md)hakkında daha fazla bilgi edinin.

[Azure veritabanı geçiş hizmeti](../dms/tutorial-mongodb-cosmos-db.md) , MongoDB koleksiyonlarını benzersiz dizinlerle otomatik olarak geçirir. Ancak, geçiş işleminden önce benzersiz dizinlerin oluşturulması gerekir. Azure Cosmos DB, Koleksiyonlarınızdan zaten veri olduğunda benzersiz dizinlerin oluşturulmasını desteklemez. Daha fazla bilgi için bkz. [Azure Cosmos DB Içindeki benzersiz anahtarlar](unique-keys.md).

## <a name="next-steps"></a>Sonraki adımlar
* [MongoDB verilerinizi veritabanı geçiş hizmetini kullanarak Cosmos DB geçirin.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos kapsayıcılarında ve veritabanlarında üretilen iş sağlama](set-throughput.md)
* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB genel dağıtım](distribute-data-globally.md)
* [Azure Cosmos DB’de dizin oluşturma](index-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
