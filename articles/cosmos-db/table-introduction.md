---
title: Azure Cosmos DB Tablo API'si giriş
description: Azure tabloları API 'sini kullanarak düşük gecikme süresine sahip çok büyük hacimli anahtar-değer verilerini depolamak ve sorgulamak için Azure Cosmos DB nasıl kullanabileceğinizi öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 01/08/2021
ms.author: sngun
ms.openlocfilehash: 1cf3bf30b37a09b5dfe94bf1e754a7f8e9dcd82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98045674"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB: Tablo API’sine Giriş
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Azure Cosmos DB](introduction.md), Azure Tablo depolaması için yazılmış olan ve aşağıdaki gibi üst düzey özelliklere ihtiyaç duyan uygulamalar için Tablo API'sini sunar:

* [Anahtar genel dağıtımı](distribute-data-globally.md).
* Dünya çapında [adanmış aktarım hızı](partitioning-overview.md) (sağlanan aktarım hızı kullanılırken).
* 99 yüzdebirlikte tek basamaklı milisaniyelik gecikme süresi.
* Garantili yüksek kullanılabilirlik.
* Otomatik ikincil dizin oluşturma.

Azure Tablo depolama için yazılmış uygulamalar herhangi bir kod değişikliği olmadan Tablo API'sini kullanarak Azure Cosmos DB'ye geçirilebilir ve üst düzey özelliklerden yararlanabilir. Tablo API’si, .NET, Java, Python ve Node.js ile kullanılabilecek istemci SDK’larına sahiptir.

> [!NOTE]
> [Sunucusuz kapasite modu](serverless.md) artık Azure Cosmos db tablo API'si kullanılabilir.

> [!IMPORTANT]
> .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) bakım modunda ve yakında kullanım dışı bırakılacak. Tablo API'si tarafından desteklenen en son özellikleri almaya devam etmek için lütfen yeni .NET Standard Kitaplığı [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) ' a yükseltin.

## <a name="table-offerings"></a>Tablo teklifleri
Şu anda Azure Tablo Depolama hizmetini kullanıyorsanız, Azure Cosmos DB Tablo API’sine geçerek aşağıdaki avantajlara sahip olabilirsiniz:

| Özellik | Azure Tablo depolama | Azure Cosmos DB Tablo API’si |
| --- | --- | --- |
| Gecikme süresi | Hızlıdır, ancak gecikme süresi için üst sınır yoktur. | Okuma ve yazma işlemleri için tek basamaklı milisaniyelik gecikme süresi, her ölçekte, dünyanın her yerindeki 99. yüzdebirlik ' de okuma ve yazma işlemleri için <10 ms gecikme süresi ile desteklenir. |
| Aktarım hızı | Değişken aktarım hızı modeli. Tabloların 20.000 işlem/sn'lik bir ölçeklenebilirlik sınırı vardır. | SLA'lar ile desteklenen [tablo başına adanmış, ayrılmış aktarım hızı](request-units.md) ile yüksek düzeyde ölçeklenebilir. Hesapların aktarım hızı açısından üst sınırı yoktur ve tablo başına saniyede 10 milyondan fazla işlem desteklenir. |
| Genel dağıtım | Yüksek kullanılabilirlik için isteğe bağlı okunabilir bir ikincil okuma bölgesi olan tek bölge. | Bir bilgisayardan istediğiniz sayıda bölgeden [anahtar genel dağıtımı](distribute-data-globally.md) . Her zaman, dünyanın her yerinde [otomatik ve el ile yük devretme](high-availability.md) desteği. Herhangi bir bölgenin yazma işlemlerini kabul etmesine izin vermek için birden fazla yazma bölgesi. |
| Dizinleme | Yalnızca PartitionKey ve RowKey’de birincil dizin. İkincil dizin yok. | Varsayılan olarak dizin yönetimi olmadan tüm özelliklerde otomatik ve tamamen dizin oluşturma. |
| Sorgu | Sorgu yürütme birincil anahtar için dizini kullanır, aksi durumda tarar. | Sorgular, hızlı sorgu süreleri için özelliklerde otomatik dizin oluşturma avantajından yararlanabilir. |
| Tutarlılık | Birincil bölge içinde güçlü. İkincil bölge içinde nihai. | Uygulama gereksinimlerinize bağlı olarak kullanılabilirlik, gecikme süresi, aktarım hızı ve tutarlılığı karşılamak için [iyi tanımlanmış beş tutarlılık düzeyi](consistency-levels.md) . |
| Fiyatlandırma | Tüketim tabanlı. | Hem [tüketim tabanlı](serverless.md) hem de [sağlanan kapasite](set-throughput.md) modlarında kullanılabilir. |
| SLA’lar | çoğaltma stratejisine bağlı olarak% 99,9% 99,99 oranında kullanılabilirlik. | % 99,999, tek bölgede hesapta kullanılabilirliği ve% 99,99 yazma kullanılabilirliği ve çok bölgeli hesaplarda% 99,999 yazma kullanılabilirliği. Kullanılabilirlik, gecikme süresi, aktarım hızı ve tutarlılığı kapsayan [kapsamlı SLA 'lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/) . |

## <a name="get-started"></a>başlarken

[Azure portalındaki](https://portal.azure.com) yeni Azure Cosmos DB hesabı. Ardından [.NET kullanarak Tablo API'sı için hızlı başlangıç](create-table-dotnet.md) makalemizi inceleyin. 

> [!IMPORTANT]
> Önizleme sırasında bir Tablo API hesabı oluşturduysanız, genel kullanıma açık Tablo API SDK’ları ile çalışmak için lütfen [yeni Tablo API hesabı](create-table-dotnet.md#create-a-database-account) oluşturun.
>

## <a name="next-steps"></a>Sonraki adımlar

İşte başlamanıza yardımcı olacak birkaç ipucu:
* [Tablo API'sini kullanarak bir .NET uygulaması derleme](create-table-dotnet.md)
* [.NET’te Tablo API’siyle geliştirme](tutorial-develop-table-dotnet.md)
* [Tablo API’sini kullanarak tablo verilerini sorgulama](tutorial-query-table.md)
* [Tablo API'sini kullanarak Azure Cosmos DB genel dağıtımını ayarlamayı öğrenin](tutorial-global-distribution-table.md)
* [Azure Cosmos DB tablo .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB tablosu .NET SDK 'Sı](table-sdk-dotnet.md)
* [Azure Cosmos DB tablosu Java SDK 'Sı](table-sdk-java.md)
* [Azure Cosmos DB tablo Node.js SDK](table-sdk-nodejs.md)
* [Python için Azure Cosmos DB Tablosu SDK'sı](table-sdk-python.md)
