---
title: Azure Cosmos DB Tablo API'si .NET Standard SDK & kaynakları
description: Sürüm tarihleri, emeklilik tarihleri ve her sürüm arasında yapılan değişiklikler dahil olmak üzere Azure Cosmos DB Tablo API'si ve .NET Standard SDK hakkında bilgi edinin.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: c1422b6411fc60383cffa6c3082108bb940c4343
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996212"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tablo .NET Standard API 'SI: Notları indir ve serbest bırak
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK'sını indirme**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Örnek**|[Cosmos DB Tablo API'si .NET örneği](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Hızlı Başlangıç**|[Hızlı Başlangıç](create-table-dotnet.md)|
|**Öğretici**|[Öğretici](tutorial-develop-table-dotnet.md)|
|**Geçerli desteklenen çerçevesi**|[Microsoft .NET Standart 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Sorun bildir**|[Sorun bildir](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2\.0.0 serisi için sürüm notları
2.0.0 serisi, uç nokta Cosmos DB için performans iyileştirmeleri ve ad alanı birleştirme ile [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)bağımlılığını alır.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview
* Cosmos DB uç noktası için performans iyileştirmeleri ve ad alanı birleştirme ile [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)bağımlılığını alan 2.0.0 tablo SDK 'sının ilk önizlemesi. Ortak API aynı kalır.

## <a name="release-notes-for-100-series"></a>1\.0.0 serisi için sürüm notları
1.0.0 Series, [Microsoft. Azure. DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)'a bağımlılığı alır.

### <a name="a-name105105"></a><a name="1.0.5"/>1.0.5
* Cosmos DB Tablo API'si iletişim kurmak için REST yürütücüsü kullanmak üzere TableClientConfiguration altında yeni yapılandırma tanıtın

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5-Önizleme
* Hata düzeltmeleri

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Hata düzeltmeleri
* RestExecutorConfiguration için HttpClientTimeout seçeneğini belirtin.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-Önizleme
* Hata düzeltmeleri
* RestExecutorConfiguration için HttpClientTimeout seçeneğini belirtin.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Hata düzeltmeleri

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Genel kullanılabilirlik sürümü

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Önizleme
* CloudTableClient 'ın nasıl yapılandırılabileceğini değişiklikler yapıldı. Şimdi oluşturma sırasında bir TableClientConfiguration nesnesi alır. TableClientConfiguration, hedef uç noktanın Cosmos DB Tablo API'si veya Azure Depolama Tablo API'si olmasına bağlı olarak istemci davranışını yapılandırmak için farklı özellikler sağlar.
* Özel bir sütunda sonuçları sıralanmış sırada döndürecek şekilde TableQuery desteği eklendi. Bu özellik yalnızca Cosmos DB tablo uç noktalarında desteklenir.
* Çeşitli sonuç türlerinde RequestCharges sergilemek için destek eklendi. Bu özellik yalnızca Cosmos DB tablo uç noktalarında desteklenir.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-Önizleme
* Azure depolama tablo uç noktalarına yönelik SAS belirteci, TablePermissions, ServiceProperties ve ServiceStats işlemlerine yönelik destek ekleyin. 
   > [!NOTE]
   > Önceki Azure depolama tablo SDK 'larında bazı işlevler, istemci tarafı şifreleme gibi henüz desteklenmemektedir.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Önizleme
* Azure depolama tablo uç noktalarına yönelik çekirdek CRUD, toplu iş ve sorgu işlemleri için destek ekleyin. 
   > [!NOTE]
   > Önceki Azure depolama tablo SDK 'larında bazı işlevler, istemci tarafı şifreleme gibi henüz desteklenmemektedir.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-Önizleme
* Azure Cosmos DB tablo .NET Standard SDK, Cosmos DB tablo veri modeline verimli erişim sağlayan bir platformlar arası .NET kitaplığıdır. Bu ilk sürüm, [.NET Framework için Cosmos db tablo SDK 'sı](table-sdk-dotnet.md)olarak benzer API 'Lerle birlikte tablo ve VARLıK CRUD + sorgu işlevleri 'nin tam kümesini destekler. 
   > [!NOTE]
   >  Azure Storage tablo uç noktaları, 0.9.1-Preview sürümünde henüz desteklenmiyor.

## <a name="release-and-retirement-dates"></a>Yayın ve emeklilik tarihleri
Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar.

Bu platformlar arası .NET Standard Kitaplığı [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [Microsoft. Azure. cosmosdb. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).NET Framework kitaplığının yerini alır.

### <a name="200-series"></a>2.0.0 serisi
| Version | Yayınlanma Tarihi | Sona erme tarihi |
| --- | --- | --- |
| [2.0.0-Önizleme](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0 serisi
| Version | Yayınlanma Tarihi | Sona erme tarihi |
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
