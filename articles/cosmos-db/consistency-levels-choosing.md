---
title: Azure Cosmos DB kullanan uygulamanız için doğru tutarlılık düzeyini seçme
description: Azure Cosmos DB içinde uygulamanız için doğru tutarlılık düzeyini seçme.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 89c81e978c5f3dbbb8fac1ea5e75fc506612308f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384912"
---
# <a name="choose-the-right-consistency-level"></a>Doğru tutarlılık düzeyini seçme 

Yüksek kullanılabilirlik, düşük gecikme süresi veya her ikisi için çoğaltmaya bağlı dağıtılmış veritabanları, okuma tutarlılığı ile kullanılabilirlik, gecikme süresi ve aktarım hızı arasında temel zorunluluğunu getirir yapın. Ticari olarak kullanılabilen en çok dağıtılmış veritabanları, geliştiricilerin iki üstün tutarlılık modeli arasından seçim yapmasını ister: *güçlü* tutarlılık ve *nihai* tutarlılık. Azure Cosmos DB, geliştiricilerin iyi tanımlanmış beş tutarlılık modeli arasından seçim yapmasına olanak sağlar: *güçlü*, *sınırlanmış Eskime durumu*, *oturum*, *tutarlı ön ek* ve *nihai*. Bu tutarlılık modellerinin her biri iyi tanımlanmış, sezgisel ve belirli gerçek dünyada senaryolar için kullanılabilir. Beş tutarlılık modelinin her biri, kesin [kullanılabilirlik ve performans avantajları](consistency-levels-tradeoffs.md) sağlar ve kapsamlı SLA 'lar tarafından desteklenir. Aşağıdaki basit konular, birçok yaygın senaryoda doğru seçim yapmanıza yardımcı olur.

## <a name="sql-api-and-table-api"></a>SQL API ve Tablo API'si

Uygulamanız SQL API veya Tablo API'si kullanılarak derlenip aşağıdaki noktaları göz önünde bulundurun:

- Birçok gerçek Dünya senaryosunda, oturum tutarlılığı en iyi seçenektir ve önerilen seçenektir. Daha fazla bilgi için bkz. [uygulamanıza yönelik oturum belirtecini yönetme](how-to-manage-consistency.md#utilize-session-tokens).

- Uygulamanız güçlü tutarlılık gerektiriyorsa, sınırlanmış stalet tutarlılığı düzeyi kullanmanız önerilir.

- Yazma işlemlerinde oturum tutarlılığı ve tek basamaklı milisaniyelik gecikme süresi tarafından sağlananlara daha sıkı tutarlılık garantisi gerekiyorsa, sınırlanmış stalet tutarlılığı düzeyi kullanmanız önerilir.  

- Uygulamanız nihai tutarlılık gerektiriyorsa, tutarlı ön ek tutarlılık düzeyi kullanmanız önerilir.

- Oturum tutarlılığı tarafından sağlananlara göre daha az sıkı tutarlılık garantisi gerekiyorsa, tutarlı ön ek tutarlılık düzeyi kullanmanız önerilir.

- En yüksek kullanılabilirliğe ve en düşük gecikme süresine ihtiyaç duyuyorsanız, nihai tutarlılık düzeyini kullanın.

- Performanstan ödün vermeden daha yüksek veri dayanıklılığı gerekiyorsa, uygulama katmanında özel bir tutarlılık düzeyi oluşturabilirsiniz. Daha fazla bilgi için bkz. [uygulamalarınızda özel eşitleme uygulama](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB ve Gremlin API 'Leri

- Apache Cassandra ve Cosmos DB tutarlılık düzeylerinde sunulan "tutarlılık düzeyi okuma" arasında eşleme hakkında daha fazla bilgi için bkz. [tutarlılık düzeyleri ve Cosmos DB API 'leri](consistency-levels-across-apis.md#cassandra-mapping).

- MongoDB ve Azure Cosmos DB tutarlılık düzeylerinin "okuma sorunu" ile eşleme ayrıntıları için bkz. [tutarlılık düzeyleri ve Cosmos DB API 'leri](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Uygulamada tutarlılık garantisi

Uygulamada genellikle daha güçlü tutarlılık garantisi alabilirsiniz. Bir okuma işleminin tutarlılık garantisi, istediğiniz veritabanı durumunun yeniliği ve sıralamasına karşılık gelir. Okuma tutarlılığı, yazma/güncelleştirme işlemlerinin sıralaması ve yayılması ile bağlantılıdır.  

* Tutarlılık düzeyi, **sınırlı stalet**olarak ayarlandığında, Cosmos DB istemcilerin, daha önceki bir yazma değerini her zaman okumasını sağlar ve bu pencere, bir gecikme süresi penceresiyle sınırlı bir gecikme olur.

* Tutarlılık düzeyi **güçlü**olarak ayarlandığında, stalet penceresi sıfıra eşdeğerdir ve istemciler, yazma işleminin en son taahhüt edilen değerini okumak için garanti edilir.

* Kalan üç tutarlılık düzeyi için, stalet penceresi büyük ölçüde iş yükünüze bağımlıdır. Örneğin, veritabanında yazma işlemi yoksa, **son,** **oturum**veya **tutarlı ön ek** tutarlılık düzeylerine sahip bir okuma işlemi, güçlü tutarlılık düzeyiyle bir okuma işlemiyle aynı sonuçları elde etmek olasıdır.

Azure Cosmos hesabınız, güçlü tutarlılık dışında bir tutarlılık düzeyiyle yapılandırıldıysa, olasılık açısından olasılığa bağlı olarak, istemcileriniz için iş yükleriniz için güçlü ve tutarlı okumalar alabilir.  *Leklik* (PBS) ölçümü. Bu ölçüm Azure portal kullanıma sunulur, daha fazla bilgi edinmek için bkz. [Izleme olasılığa yönelik sınırlı Stalet (PBS) ölçümü](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Dayalı sınırlanmış stalet, nihai tutarlılığın ne kadar olduğunu gösterir. Bu ölçüm, Azure Cosmos hesabınızda halen yapılandırdığınız tutarlılık düzeyinden daha güçlü bir tutarlılık elde etme konusunda bir fikir sağlar. Diğer bir deyişle, yazma ve okuma bölgelerinin bir birleşimi için kesin olarak tutarlı okuma elde etme olasılığını (milisaniye olarak ölçülür) görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde tutarlılık düzeyleri hakkında daha fazla bilgi edinin:

* [Cosmos DB API 'lerde tutarlılık düzeyi eşleme](consistency-levels-across-apis.md)
* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans seçenekleri](consistency-levels-tradeoffs.md)
* [Uygulamanız için oturum belirtecini yönetme](how-to-manage-consistency.md#utilize-session-tokens)
* [Gerçekçi sınırlı Eskime durumu (PBS) ölçüsünü izleyin](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
