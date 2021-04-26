---
title: Azure Cosmos DB Cassandra API’sine giriş
description: Mevcut uygulamaları "kaldırma ve kaydırma" için Azure Cosmos DB nasıl kullanabileceğinizi ve Cassandra sürücülerini ve CQL kullanarak yeni uygulamalar oluşturmayı öğrenin
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
ms.openlocfilehash: 2a778c2e9a44bda148fd40112776858943cffe4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188624"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API’sine giriş
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API’si, [Apache Cassandra](https://cassandra.apache.org) için yazılmış uygulamalara yönelik veri deposu olarak kullanılabilir. Başka bir deyişle, mevcut Cassandra uygulamanız artık CQLv4 ile uyumlu mevcut [Apache sürücülerini](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) kullanarak Azure Cosmos DB Cassandra API’si ile iletişim kurabilir. Çoğu durumda, yalnızca bir bağlantı dizesini değiştirerek Azure Cosmos DB Cassandra API kullanarak Apache Cassandra kullanarak geçiş yapabilirsiniz. 

Cassandra API, Cassandra sorgu dili (CQL), Cassandra tabanlı araçlar (csqlsh gibi) ve daha önce bildiğiniz Cassandra istemci sürücüleri kullanılarak Azure Cosmos DB depolanan verilerle etkileşime girebilmenizi sağlar.

> [!NOTE]
> [Sunucusuz kapasite modu](serverless.md) artık Azure Cosmos DB Cassandra API kullanılabilir.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Azure Cosmos DB için Apache Cassandra API'si kullanmanın avantajı nedir?

**İşletim yönetimi gerekmez**: Tam olarak yönetilen bulut hizmeti olarak Azure Cosmos DB Cassandra API’si, işletim sistemleri, JVM ve YAML dosyaları genelindeki sayısız ayarı ve bunların etkileşimlerini yönetme ve izleme yükünü ortadan kaldırır. Azure Cosmos DB; aktarım hızı, gecikme süresi, depolama, kullanılabilirlik için izleme ve yapılandırılabilir uyarılar sunar.

**Açık kaynak standardı**: tam olarak yönetilen bir hizmet olmasına rağmen Cassandra API, yerel [Apache Cassandra hat tel protokolünün](cassandra-support.md)büyük bir yüzey alanını desteklediğinden, yaygın olarak kullanılan ve bulut belirsiz açık kaynak standardı üzerinde uygulamalar oluşturmanıza olanak tanır.

**Performans yönetimi**: Azure Cosmos DB, yüzde 99. dilim için SLA ile desteklenen, garantili düşük gecikme süreli okuma ve yazmalar sunar. Kullanıcıların yüksek performanslı ve düşük gecikme süreli okuma ve yazma işlemleri sağlamak için işletim yükünden endişelenmesi gerekmez. Başka bir deyişle, kullanıcıların zamanlama sıkıştırması, kaldırılmış uygulama kayıtlarının yönetilmesi, Bloom filtrelerinin ve çoğaltmaların ayarlanması ile ilgilenmesi gerekmez. Azure Cosmos DB, bu sorunları yönetme yükünü ortadan kaldırır ve uygulama mantığına odaklanmanıza olanak sağlar.

**Mevcut kodu ve araçları kullanma olanağı**: Azure Cosmos DB, mevcut Cassandra SDK’ları ve araçları ile kablo protokolü düzeyinde uyumluluk sunar. Bu uyumluluk, küçük değişikliklerle Azure Cosmos DB Cassandra API’si ile mevcut kod tabanınızı kullanabilmenizi sağlar.

**Aktarım hızı ve depolama** esnekliği: Azure Cosmos DB tüm bölgelerde işleme sağlar ve sağlanan aktarım hızını Azure Portal, POWERSHELL veya CLI işlemleriyle ölçeklendirebilir. Öngörülebilir performansla gerekirse, tablolarınızın depolama ve aktarım hızını [ölçeklendiresnek](manage-scale-cassandra.md) .

**Genel dağıtım ve kullanılabilirlik**: Azure Cosmos DB, tüm Azure bölgelerinde verileri genel olarak dağıtma olanağı sağlar ve bir yandan düşük gecikme süreli veri erişimini ve yüksek kullanılabilirliği sağlarken diğer yandan yerel olarak verileri sunar. Azure Cosmos DB, ek işletim yükü getirmeden bir bölge içinde %99,99 gibi yüksek kullanılabilirlik ve bölgeler arasında %99,999'luk okuma ve yazma kullanılabilirliği sağlar. [Verileri genel olarak dağıtma](distribute-data-globally.md) makalesinden daha fazla bilgi edinin. 

**Seçimde tutarlılık**: Azure Cosmos DB, tutarlılık ve performans arasında en iyi oranı elde etmek için beş iyi tanımlanmış tutarlılık düzeyi seçeneği sunar. Bu tutarlılık düzeyleri güçlü, sınırlanmış eskime durumu, oturum, tutarlı önek ve son şeklindedir. Bu iyi tanımlanmış, pratik ve sezgisel tutarlılık düzeyleri, geliştiricilerin tutarlılık, kullanılabilirlik ve gecikme süresi arasında sağlam bir denge kurmasına olanak tanır. [Tutarlılık düzeyleri](consistency-levels.md) makalesinden daha fazla bilgi edinin. 

**Kurumsal düzey**: Azure Cosmos DB, kullanıcıların platformu güvenli bir şekilde kullanabilmesini sağlamak için [uyumluluk sertifikaları](https://www.microsoft.com/trustcenter) sunar. Azure Cosmos DB ayrıca durağan ve hareketli durumlarda şifreleme, IP güvenlik duvarı ve denetim düzlemi etkinlikleri için denetim günlükleri sunar.

**Olay** kaynağını belirleme: Cassandra API, bir kalıcı değişiklik günlüğüne erişim sağlar ve bu [değişiklik akışı](cassandra-change-feed.md), doğrudan veritabanından olay kaynağını kolaylaştırmaya yardımcı olabilir. Apache Cassandra 'da, tek denk değişiklik verileri yakalama (CDC), yalnızca belirli tabloları arşivleme için bayrakla işaretleme ve CDC günlüğü için yapılandırılabilir bir diske ulaşıldığında bu tablolara yazmaları reddetme (ilgili yönleri otomatik olarak yönetilir Cosmos DB).

## <a name="next-steps"></a>Sonraki adımlar

* Cassandra API verilerini oluşturmak ve yönetmek için aşağıdaki dile özgü uygulamaları derlemeye hızlıca başlayabilirsiniz:
  - [Node.js uygulaması](create-cassandra-nodejs.md)
  - [.NET uygulaması](create-cassandra-dotnet.md)
  - [Python uygulaması](create-cassandra-python.md)

* Java uygulaması kullanarak [Cassandra API hesabı, veritabanı ve tablo oluşturmaya](create-cassandra-api-account-java.md) başlayın.

* Java uygulaması kullanarak [Cassandra API tablosuna örnek verileri yükleme](cassandra-api-load-data.md).

* Java uygulaması kullanarak [Cassandra API hesabından verileri sorgulama](cassandra-api-query-data.md).

* Azure Cosmos DB Cassandra API’si tarafından desteklenen Apache Cassandra özellikleri hakkında bilgi edinmek için [Cassandra desteği](cassandra-support.md) makalesine bakın.

* [Sık Sorulan Sorular](cassandra-faq.md)’ı okuyun.
