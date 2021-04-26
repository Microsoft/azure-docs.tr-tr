---
title: MongoDB için Azure Cosmos DB API 'sine giriş
description: MongoDB için Azure Cosmos DB API 'sini kullanarak çok büyük miktarlarda verileri depolamak ve sorgulamak için Azure Cosmos DB nasıl kullanabileceğinizi öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: dfaa04077a5148f19f06aa5e1257b986377ea8c0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307138"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'si
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md), Microsoft'un görev açısından kritik uygulamalar için genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Azure Cosmos DB, [kullanıma hazır genel dağıtım](distribute-data-globally.md), dünya çapında [aktarım hızı ve depolama için esnek ölçeklendirme](partitioning-overview.md), 99. yüzdebirlik dilimde tek haneli milisaniyelik gecikme süreleri ve garantili yüksek kullanılabilirlik olanakları sunar ve bunların tümü [sektör lideri SLA’lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/) ile desteklenir. Azure Cosmos DB, şema ve dizin yönetimiyle ilgilenmenize gerek kalmadan [otomatik olarak verilerin dizinini oluşturur](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Çok modelli olan bu hizmet belge, anahtar-değer, grafik ve sütunlu veri modellerini destekler. Azure Cosmos DB hizmeti, Cassandra, MongoDB, Gremlin ve Azure Tablo depolama dahil olmak üzere yaygın NoSQL API 'Leri için tel protokoller uygular. Bu, Cosmos veritabanınız ile etkileşim kurmak için tanıdık NoSQL istemci sürücülerinizi ve araçlarınızı kullanmanıza olanak sağlar.

> [!NOTE]
> [Sunucusuz kapasite modu](serverless.md) artık Azure Cosmos DB MongoDB için API 'sinde kullanılabilir.

## <a name="wire-protocol-compatibility"></a>Kablo protokol uyumluluğu

Azure Cosmos DB MongoDB için kablo protokolünü uygular. Bu uygulama yerel MongoDB istemci SDK 'Ları, sürücüleri ve araçları ile şeffaf uyumluluk sağlar. Azure Cosmos DB MongoDB veritabanı altyapısını barındırmıyor. MongoDB tarafından desteklenen özelliklerin ayrıntıları şurada bulunabilir: 
- [Mongo DB sürüm 4,0 için Azure Cosmos DB API 'SI](mongodb-feature-support-40.md)
- [Mongo DB sürüm 3,6 için Azure Cosmos DB API 'SI](mongodb-feature-support-36.md)

Varsayılan olarak, MongoDB için Azure Cosmos DB API 'SI kullanılarak oluşturulan yeni hesaplar MongoDB tel protokolünün 3,6 sürümü ile uyumludur. Bu protokol sürümünü anlayan tüm MongoDB istemci sürücüleri Cosmos DB yerel olarak bağlanabiliyor olmalıdır.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="MongoDB için Azure Cosmos DB API’si" border="false":::

## <a name="key-benefits"></a>Önemli avantajlar

Cosmos DB, tam olarak yönetilen, genel olarak dağıtılmış, hizmet olarak veritabanı gibi başlıca avantajları [burada](introduction.md)açıklanmıştır. Ayrıca, popüler NoSQL API 'Lerin tel protokollerini yerel olarak uygulayarak Cosmos DB aşağıdaki avantajları sağlar:

* Uygulama mantığınızın önemli kısımlarını korurken uygulamanızı Cosmos DB 'e kolayca geçirin.
* Uygulamanızı taşınabilir tutun ve bulut satıcısı belirsiz olmaya devam edin.
* Cosmos DB tarafından desteklenen yaygın NoSQL API 'Leri için sektör lideri, mali olarak desteklenen SLA 'Ları alın.
* Esnek, Cosmos veritabanlarınızın sağlanan aktarım hızını ve depolamayı ihtiyaya göre ölçeklendirin ve yalnızca ihtiyacınız olan aktarım hızı ve depolama için ödeme yaparsınız. Bu, önemli maliyet tasarruflarına yol açar.
* Anahtar, çok bölgeli yazma çoğaltmasıyla birlikte genel dağıtım.

## <a name="cosmos-dbs-api-for-mongodb"></a>MongoDB için Cosmos DB API 'SI

Hızlı Başlangıç ' i izleyerek bir Azure Cosmos hesabı oluşturun ve mevcut MongoDB uygulamanızı Azure Cosmos DB kullanacak şekilde geçirin veya yenisini oluşturun:

* [Mevcut bir MongoDB Node.js Web uygulamasını geçirin](create-mongodb-nodejs.md).
* [MongoDB ve .NET SDK için Azure Cosmos DB API 'sini kullanarak bir Web uygulaması oluşturma](create-mongodb-dotnet.md)
* [MongoDB ve Java SDK için Azure Cosmos DB API 'sini kullanarak bir konsol uygulaması oluşturma](create-mongodb-java.md)

## <a name="next-steps"></a>Sonraki adımlar

İşte başlamanıza yardımcı olacak birkaç ipucu:

* Hesap bağlantı dizesi bilgilerinizi alma hakkında bilgi edinmek için [MongoDB uygulamasını Azure Cosmos DB](connect-mongodb-account.md) öğreticiye bağlama öğreticisini izleyin.
* Studio 3T 'de Cosmos veritabanınız ve MongoDB uygulamanız arasında bağlantı oluşturmayı öğrenmek için [Azure Cosmos DB öğreticisi Ile Studio 3T kullanın](mongodb-mongochef.md) .
* Verilerinizi Cosmos veritabanına aktarmak için [MongoDB verilerini Içeri aktarma Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) öğreticisine uygulayın.
* [Robo 3T](mongodb-robomongo.md)kullanarak Cosmos hesabına bağlanın.
* [Küresel olarak dağıtılan uygulamalar için okuma tercihlerini yapılandırmayı](../cosmos-db/tutorial-global-distribution-mongodb.md)öğrenin.
* [Sorun giderme kılavuzumuzdaki](mongodb-troubleshoot.md) yaygın olarak bulunan hataların çözümlerini bulun


<sup>Note: Bu makalede, MongoDB veritabanlarıyla kablo protokol uyumluluğu sağlayan Azure Cosmos DB bir özelliği açıklanmaktadır. Microsoft bu hizmeti sağlamak için MongoDB veritabanlarını çalıştırmaz. Azure Cosmos DB MongoDB, Inc ile bağlantılı değildir.</sup>
