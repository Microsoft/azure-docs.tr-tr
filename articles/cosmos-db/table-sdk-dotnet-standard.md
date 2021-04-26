---
title: Azure Cosmos DB Tablo API'si .NET Standard SDK & kaynakları
description: Sürüm tarihleri, emeklilik tarihleri ve her sürüm arasında yapılan değişiklikler dahil olmak üzere Azure Cosmos DB Tablo API'si ve .NET Standard SDK hakkında bilgi edinin.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: f41a895aef3476386b8b530fc3e265ffaa21e8c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597614"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tablo .NET Standard API: Indirme ve sürüm notları
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Bağlantılar  |
|---|---|
|**SDK indirmesi**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Örnek**|[Cosmos DB Tablo API'si .NET örneği](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Hızlı Başlangıç**|[Hızlı Başlangıç](create-table-dotnet.md)|
|**Öğretici**|[Öğretici](tutorial-develop-table-dotnet.md)|
|**Şu anda desteklenen çerçeve**|[Microsoft .NET Standart 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Sorun bildir**|[Sorun bildir](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2.0.0 serisi için sürüm notları
2.0.0 serisi, uç nokta Cosmos DB için performans iyileştirmeleri ve ad alanı birleştirme ile [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)bağımlılığını alır.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-Önizleme
* Cosmos DB uç noktası için performans iyileştirmeleri ve ad alanı birleştirme ile [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)bağımlılığını alan 2.0.0 tablo SDK 'sının ilk önizlemesi. Ortak API aynı kalır.

## <a name="release-notes-for-100-series"></a>1.0.0 serisi için sürüm notları
1.0.0 serisi [Microsoft.Azure.DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)'a bağımlılığı alır.

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Cosmosdb uç noktası ise TTL özelliğini ayarlamak için destek ekle 
* Zaman aşımı ve görev iptal edildi özel durumu üzerinde yeniden deneme ilkesini
* ASP .NET uygulamalarında görülen aralıklı görev iptal özel durumunu çözme
* Azure Tablo Depolaması 'nı çözme yalnızca ikincil uç nokta konum modundan al
* `Microsoft.Azure.DocumentDB.Core`Bağımlılık sürümünü, aralıklı null başvuru özel durumunu düzelten 2.11.2 'ye Güncelleştir
* `Odata.Core`Azure kabuğu ile uyumluluk çakışmasını gideren 7.6.4 için bağımlılık sürümünü Güncelleştir

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Tablo SDK varsayılan izleme düzeyini SourceLevels. off olarak ayarlayarak performans iyileştirmesi app.config aracılığıyla kabul edilebilir

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Cosmos DB Tablo API'si iletişim kurmak için REST yürütücüsü kullanmak üzere TableClientConfiguration altında yeni yapılandırma tanıtın

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5-Önizleme
* Hata düzeltmeleri

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Hata düzeltmeleri
* RestExecutorConfiguration için HttpClientTimeout seçeneğini belirtin.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4-Önizleme
* Hata düzeltmeleri
* RestExecutorConfiguration için HttpClientTimeout seçeneğini belirtin.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Hata düzeltmeleri

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Genel kullanılabilirlik sürümü

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0-Önizleme
* CloudTableClient 'ın nasıl yapılandırılabileceğini değişiklikler yapıldı. Şimdi oluşturma sırasında bir TableClientConfiguration nesnesi alır. TableClientConfiguration, hedef uç noktanın Cosmos DB Tablo API'si veya Azure Depolama Tablo API'si olmasına bağlı olarak istemci davranışını yapılandırmak için farklı özellikler sağlar.
* Özel bir sütunda sonuçları sıralanmış sırada döndürecek şekilde TableQuery desteği eklendi. Bu özellik yalnızca Cosmos DB tablo uç noktalarında desteklenir.
* Çeşitli sonuç türlerinde RequestCharges sergilemek için destek eklendi. Bu özellik yalnızca Cosmos DB tablo uç noktalarında desteklenir.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1-Önizleme
* Azure depolama tablo uç noktalarına yönelik SAS belirteci, TablePermissions, ServiceProperties ve ServiceStats işlemlerine yönelik destek ekleyin. 
   > [!NOTE]
   > Önceki Azure depolama tablo SDK 'larında bazı işlevler, istemci tarafı şifreleme gibi henüz desteklenmemektedir.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0-Önizleme
* Azure depolama tablo uç noktalarına yönelik çekirdek CRUD, toplu iş ve sorgu işlemleri için destek ekleyin. 
   > [!NOTE]
   > Önceki Azure depolama tablo SDK 'larında bazı işlevler, istemci tarafı şifreleme gibi henüz desteklenmemektedir.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1-Önizleme
* Azure Cosmos DB tablo .NET Standard SDK, Cosmos DB tablo veri modeline verimli erişim sağlayan bir platformlar arası .NET kitaplığıdır. Bu ilk sürüm, [.NET Framework için Cosmos db tablo SDK 'sı](table-sdk-dotnet.md)olarak benzer API 'Lerle birlikte tablo ve VARLıK CRUD + sorgu işlevleri 'nin tam kümesini destekler. 
   > [!NOTE]
   >  Azure Storage tablo uç noktaları, 0.9.1-Preview sürümünde henüz desteklenmiyor.

## <a name="release-and-retirement-dates"></a>Yayın ve emeklilik tarihleri
Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar.

Bu platformlar arası .NET Standard Kitaplığı [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [Microsoft. Azure. cosmosdb. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).NET Framework kitaplığının yerini alır.

### <a name="200-series"></a>2.0.0 serisi
| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [2.0.0-Önizleme](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0 serisi
| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 Eylül 2019 |--- |
| [1.0.5-Önizleme](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-Önizleme](#1.0.4-preview) |26 Temmuz 2019 |--- |
| 1.0.2-Önizleme |2 Mayıs 2019 |--- |
| [1.0.1](#1.0.1) |19 Nisan 2019 |--- |
| [1.0.0](#1.0.0) |13 Mart 2019 |--- |
| [0.11.0-Önizleme](#0.11.0-preview) |5 Mart 2019 |--- |
| [0.10.1-Önizleme](#0.10.1-preview) |22 Ocak 2019 |--- |
| [0.10.0-Önizleme](#0.10.0-preview) |18 Aralık 2018 |--- |
| [0.9.1-Önizleme](#0.9.1-preview) |18 Ekim 2018 |--- |


## <a name="faq"></a>SSS

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.
Azure Cosmos DB Tablo API'si hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB tanıtım tablo API'si](table-introduction.md).
