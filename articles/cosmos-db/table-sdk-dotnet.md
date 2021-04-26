---
title: Azure Cosmos DB Tablo API'si .NET SDK & kaynakları
description: Sürüm tarihleri, emeklilik tarihleri ve her sürüm arasında yapılan değişiklikler dahil olmak üzere .NET için Azure Cosmos DB Tablo API'si hakkında bilgi edinin.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566838"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API 'SI: Indirme ve sürüm notları
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Bağlantılar|
|---|---|
|**SDK indirmesi**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Hızlı Başlangıç**|[Azure Cosmos DB: .NET ve Tablo API'si ile uygulama derleme](create-table-dotnet.md)|
|**Öğretici**|[Azure Cosmos DB: .NET’te Tablo API’si ile geliştirme](tutorial-develop-table-dotnet.md)|
|**Şu anda desteklenen çerçeve**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) bakım modunda ve yakında kullanım dışı bırakılacak. Tablo API'si tarafından desteklenen en son özellikleri almaya devam etmek için lütfen yeni .NET Standard Kitaplığı [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) ' a yükseltin.

> Önizleme sırasında bir Tablo API hesabı oluşturduysanız, genel kullanıma açık Tablo API SDK’ları ile çalışmak için lütfen [yeni Tablo API hesabı](create-table-dotnet.md#create-a-database-account) oluşturun.
>

## <a name="release-notes"></a>Sürüm notları

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Hata düzeltmeleri

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Hata düzeltmeleri

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Çok bölgeli yazma desteği eklendi
* Microsoft.Azure.DocumentDB, Microsoft. OData. Core, Microsoft. OData. Edm, Microsoft. uzamsal üzerinde NuGet paketi bağımlılıkları düzeltildi

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Microsoft. Azure. Storage. Common ve Microsoft.Azure.DocumentDB üzerinde düzeltilen NuGet paketi bağımlılıkları.
* JsonConvert. DefaultSettings yapılandırıldığında tablo serileştirmesine yönelik hata düzeltmeleri.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Hatalı biçimlendirilmiş ETAGs için doğrudan modda doğrulama eklendi.
* Ağ Geçidi modunda sabit LINQ sorgu hatası.
* Zaman uyumlu API 'Ler artık SynchronizationContext ile iş parçacığı havuzunda çalışır.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Tablequerymaxıtemcount, TableQueryEnableScan, Tablequerymaxdegreeofparalleliı ve Tablequerycontinuationtokenlimitınkb 'yi TableRequestOptions 'a ekleyin
* Hata Düzeltmeleri

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Genel kullanılabilirlik sürümü

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0-Önizleme

* İlk önizleme yayını

## <a name="release-and-retirement-dates"></a>Yayın ve emeklilik tarihleri

Microsoft, daha yeni/desteklenen bir sürüme geçişi düzgünleştirmek için SDK 'nın devre dışı bırakılmasının ardından en az **12 ay** önce bildirim sağlar.

`Microsoft.Azure.CosmosDB.Table`Kitaplık Şu anda yalnızca .NET Framework için kullanılabilir ve bakım modunda ve yakında kullanım dışı bırakılacak. Yeni özellikler ve işlevler ve iyileştirmeler yalnızca [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).NET Standard kitaplığına eklenir, bu nedenle [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)'a yükseltmeniz önerilir.

[Windowsazure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) Önizleme paketi kullanım dışı bırakıldı. WindowsAzure. Storage-PremiumTable SDK, 15 Kasım 2018 tarihinde kullanımdan kaldırılacaktır, bu da kullanımdan kaldırılan SDK 'ya yönelik isteklere izin verilmez.

| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 Eylül 2019| |
| [2.1.0](#2.1.0) |22 Ocak 2019|01 Nisan 2020 |
| [2.0.0](#2.0.0) |26 Eylül 2018|01 Mart 2020 |
| [1.1.3](#1.1.3) |17 Temmuz 2018|01 Aralık 2019 |
| [1.1.1](#1.1.1) |26 Mart 2018|01 Aralık 2019 |
| [1.1.0](#1.1.0) |21 Şubat 2018|01 Aralık 2019 |
| [1.0.0](#1.0.0) |15 Kasım 2017|15 Kasım 2019 |
| 0.9.0-Önizleme |11 Kasım 2017 |11 Kasım 2019 |

## <a name="troubleshooting"></a>Sorun giderme

Hatayı alırsanız 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Microsoft. Azure. CosmosDB. Table NuGet paketini kullanmaya çalışırken, sorunu gidermeye yönelik iki seçeneğiniz vardır:

* Microsoft. Azure. CosmosDB. Table paketini ve bağımlılıklarını yüklemek için paket Yönetimi konsolu 'nu kullanın. Bunu yapmak için, çözümünüzün Paket Yöneticisi konsoluna aşağıdakini yazın. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Tercih ettiğiniz NuGet paketi Yönetim aracını kullanarak Microsoft. Azure. CosmosDB. Table ' i yüklemeden önce Microsoft. Azure. Storage. Common NuGet paketini yüklemeniz gerekir.

## <a name="faq"></a>SSS

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.

Azure Cosmos DB Tablo API'si hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB tanıtım tablo API'si](table-introduction.md). 
