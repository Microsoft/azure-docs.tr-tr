---
title: MongoDB için Azure Cosmos DB API 'sine giriş
description: MongoDB için Azure Cosmos DB API 'sini kullanarak çok büyük miktarlarda verileri depolamak ve sorgulamak için Azure Cosmos DB nasıl kullanabileceğinizi öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754987"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'si

[Azure Cosmos DB](introduction.md), Microsoft'un görev açısından kritik uygulamalar için genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Azure Cosmos DB, [kullanıma hazır genel dağıtım](distribute-data-globally.md), dünya çapında [aktarım hızı ve depolama için esnek ölçeklendirme](partition-data.md), 99. yüzdebirlik dilimde tek haneli milisaniyelik gecikme süreleri ve garantili yüksek kullanılabilirlik olanakları sunar ve bunların tümü [sektör lideri SLA’lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/) ile desteklenir. Azure Cosmos DB, şema ve dizin yönetimiyle ilgilenmenize gerek kalmadan [otomatik olarak verilerin dizinini oluşturur](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Çok modelli olan bu hizmet belge, anahtar-değer, grafik ve sütunlu veri modellerini destekler. Varsayılan olarak, SQL API 'sini kullanarak Cosmos DB etkileşime geçebilirsiniz. Ayrıca Cosmos DB hizmeti, Cassandra, MongoDB, Gremlin ve Azure Tablo depolama da dahil olmak üzere yaygın NoSQL API 'Leri için hat protokolleri uygular. Bu, Cosmos veritabanınız ile etkileşim kurmak için tanıdık NoSQL istemci sürücülerinizi ve araçlarınızı kullanmanıza olanak sağlar.

## <a name="wire-protocol-compatibility"></a>Kablo protokol uyumluluğu

Azure Cosmos DB Cassandra, MongoDB, Gremlin ve Azure tabloları depolama dahil olmak üzere yaygın NoSQL veritabanlarının tel protokollerini uygular. Kablo protokollerinin yerel bir uygulamasını doğrudan ve Cosmos DB içinde etkin bir şekilde sunarak, NoSQL veritabanlarının mevcut istemci SDK 'larının, sürücülerinin ve araçlarının Cosmos DB saydam şekilde etkileşime girmesine izin verir. Cosmos DB, NoSQL veritabanlarının herhangi biri için kablo ile uyumlu API 'Ler sağlamak üzere veritabanlarının herhangi bir kaynak kodunu kullanmaz.

Varsayılan olarak, MongoDB için Azure Cosmos DB API 'SI kullanılarak oluşturulan yeni hesaplar MongoDB tel protokolünün 3,6 sürümü ile uyumludur. Bu protokol sürümünü anlayan tüm MongoDB istemci sürücüleri Cosmos DB yerel olarak bağlanabiliyor olmalıdır.

![MongoDB için Azure Cosmos DB API'si](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Önemli avantajlar

Cosmos DB, tam olarak yönetilen, genel olarak dağıtılmış, hizmet olarak veritabanı gibi başlıca avantajları [burada](introduction.md)açıklanmıştır. Ayrıca, popüler NoSQL API 'Lerin tel protokollerini yerel olarak uygulayarak Cosmos DB aşağıdaki avantajları sağlar:

* Uygulama mantığınızın önemli kısımlarını korurken uygulamanızı Cosmos DB 'e kolayca geçirin.
* Uygulamanızı taşınabilir tutun ve bulut satıcısı belirsiz olmaya devam edin.
* Cosmos DB tarafından desteklenen yaygın NoSQL API 'Leri için sektör lideri, mali olarak desteklenen SLA 'Ları alın.
* Esnek, Cosmos veritabanlarınızın sağlanan aktarım hızını ve depolamayı ihtiyaya göre ölçeklendirin ve yalnızca ihtiyacınız olan aktarım hızı ve depolama için ödeme yaparsınız. Bu, önemli maliyet tasarruflarına yol açar.
* Anahtar, çok yöneticili çoğaltma ile genel dağıtım.

## <a name="cosmos-dbs-api-for-mongodb"></a>MongoDB için Cosmos DB API 'SI

Hızlı Başlangıç ' i izleyerek bir Azure Cosmos hesabı oluşturun ve mevcut MongoDB uygulamanızı Azure Cosmos DB kullanacak şekilde geçirin veya yenisini oluşturun:

* [Mevcut bir MongoDB Node. js web uygulamasını geçirin](create-mongodb-nodejs.md).
* [MongoDB ve .NET SDK için Azure Cosmos DB API 'sini kullanarak bir Web uygulaması oluşturma](create-mongodb-dotnet.md)
* [MongoDB ve Java SDK için Azure Cosmos DB API 'sini kullanarak bir konsol uygulaması oluşturma](create-mongodb-java.md)

## <a name="next-steps"></a>Sonraki adımlar

İşte başlamanıza yardımcı olacak birkaç ipucu:

* Hesap bağlantı dizesi bilgilerinizi alma hakkında bilgi edinmek için [MongoDB uygulamasını Azure Cosmos DB](connect-mongodb-account.md) öğreticiye bağlama öğreticisini izleyin.
* Studio 3T 'de Cosmos veritabanınız ve MongoDB uygulamanız arasında bağlantı oluşturmayı öğrenmek için [Azure Cosmos DB öğreticisi Ile Studio 3T kullanın](mongodb-mongochef.md) .
* Verilerinizi Cosmos veritabanına aktarmak için [MongoDB verilerini Içeri aktarma Azure Cosmos DB](mongodb-migrate.md) öğreticisine uygulayın.
* [Robo 3T](mongodb-robomongo.md)kullanarak Cosmos hesabına bağlanın.
* [Küresel olarak dağıtılan uygulamalar için okuma tercihlerini yapılandırmayı](../cosmos-db/tutorial-global-distribution-mongodb.md)öğrenin.

<sup>Note: Bu makalede, MongoDB veritabanlarıyla kablo protokol uyumluluğu sağlayan Azure Cosmos DB bir özelliği açıklanmaktadır. Microsoft bu hizmeti sağlamak için MongoDB veritabanlarını çalıştırmaz. Azure Cosmos DB MongoDB, Inc ile bağlantılı değildir.</sup>
