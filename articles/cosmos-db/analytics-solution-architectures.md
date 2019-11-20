---
title: Azure Cosmos DB 'de küresel olarak dağıtılmış analiz kullanan çözümler.
description: Azure Cosmos DB ' de küresel olarak dağıtılan analizler kullanılarak derlenebilir çözümler hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681689"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Azure Cosmos DB 'de küresel olarak dağıtılmış analiz kullanan çözümler

Bu makalede, Azure Cosmos DB ' de küresel olarak dağıtılan analizler kullanılarak derlenen çözümler açıklanmaktadır.

## <a name="retail-and-consumer-goods"></a>Perakende ve tüketici malları

Gerçek zamanlı öneriler ve teklifler sunmak için Azure Cosmos DB Spark desteğini kullanabilirsiniz. Müşterilerin gerçek zamanlı kişiselleştirme ve ürün önerileriyle ihtiyaç duydukları öğeleri bulmasına yardımcı olabilirsiniz.

* Ürün kataloglarında gerçek zamanlı öneriler oluşturmak için, Apache Spark çalışma zamanı tarafından sunulan yerleşik Machine Learning desteğini kullanabilirsiniz.

* Yaşam süresi değerini sağlayan hedeflenen öneriler sağlamak için, veri akışı verileri, veri satın alma ve müşteri verileri ' ne tıklayabilirsiniz.

* Azure Cosmos DB küresel dağıtım özelliğini kullanarak, bölgelere yayılan yüksek hacimli ürün verileri, milisaniye cinsinden analiz edilebilir.

* Coğrafi olarak dağıtılmış kullanıcılar ve veriler için hızlı bir şekilde Öngörüler elde edebilirsiniz. Doğru zamanda doğru kullanıcıyı doğru zamanda sunarak yükseltme dönüştürme oranını artırabilirsiniz.

* Statik müşteri verileriyle birleştirerek canlı verileri zenginleştirmek için yerleşik Spark akış özelliğinden yararlanabilirsiniz. Bu şekilde, daha kişiselleştirilmiş ve hedeflenmiş reklamları gerçek zamanlı olarak, müşterilerin yaptığı gibi, bağlamında da dağıtabilirsiniz.

Aşağıdaki görüntüde, fiyatlandırma ve yükseltmeleri iyileştirmek için Azure Cosmos DB Spark desteğinin nasıl kullanıldığı gösterilmektedir:

![Fiyatlandırma ve yükseltmeleri iyileştirmek için Azure Cosmos DB Spark desteği](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Aşağıdaki görüntüde, Azure Cosmos DB Spark desteğinin gerçek zamanlı öneri altyapısında nasıl kullanıldığı gösterilmektedir:

![Gerçek zamanlı öneri altyapısında Spark desteğini Azure Cosmos DB](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Üretim ve IoT

Azure Cosmos DB yerleşik analiz platformu, küresel ölçekte Milyonlarca cihazdan IoT verilerinin gerçek zamanlı analizini etkinleştirmenizi sağlar. Hava durumu desenleri, tahmine dayalı analiz ve enerji iyileştirmeleri için öngörülü Öngörüler elde edebilirsiniz.

* Azure Cosmos DB kullanarak, gerçek zamanlı varlık ölçümleri ve hava durumu faktörleri gibi verilerin içeriğini alabilir ve ardından akıllı kılavuz Analizi uygulayarak alana bağlı cihazların performansını iyileştirebilirsiniz. Akıllı kılavuz analizi, kılavuz güvenilirliğini artırmak ve tüketicilere kişiselleştirilmiş enerji hizmetleri sunmak için, işletim maliyetlerini denetleyen anahtardır.

Aşağıdaki görüntüde, IoT cihazlarından ölçümleri okumak ve akıllı kılavuz Analizi uygulamak için Azure Cosmos DB Spark desteğinin nasıl kullanıldığı gösterilmektedir:

![IoT cihazlarından ölçümleri okumak için Azure Cosmos DB Spark desteği](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Tahmine dayalı bakım

* Küçük bir ayrıntıya gitme gibi varlıkların, derinlemesine ve derin platformlar arasında saklanması karmaşık bir Endeavor. Bu varlıklar dünya genelinde bulunur ve veri petabaytlarca oluşturur. Azure Cosmos DB kullanarak, büyük miktarlarda algılayıcı telemetrisi, mağaza varlık parçaları ve algılayıcı eşlemeleri verilerini işlemek için Spark akışını kullanan uçtan uca tahmine dayalı bir veri işlem hattı oluşturabilirsiniz.

* Varlık hatalarını gerçekleşmeden önce tahmin etmek ve hata oluşmadan önce bakım iş emirleri vermek için makine öğrenimi modelleri oluşturabilir ve dağıtabilirsiniz.

Aşağıdaki görüntüde, tahmine dayalı bakım sistemi oluşturmak için Azure Cosmos DB Spark desteğinin nasıl kullanıldığı gösterilmektedir:

![Tahmine dayalı bakım sistemi oluşturmak için Azure Cosmos DB Spark desteği](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Aşağıdaki görüntüde gerçek zamanlı bir araç Tanılama sistemi oluşturmak için Azure Cosmos DB Spark desteğinin nasıl kullanıldığı gösterilmektedir:

![Gerçek zamanlı bir araç Tanılama sistemi oluşturmak için Azure Cosmos DB Spark desteği](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Oyun

* Azure Cosmos DB yerleşik Spark desteğiyle, mümkün olan en iyi oyun deneyimini oluşturmak için gelişmiş analiz ve makine öğrenimi modellerini dakikalar içinde kolayca oluşturmanızı, ölçeklendirmenizi ve dağıtmanızı sağlar.

* Yüksek dönüştürme oranlarına ulaşmak için ilgili kişiselleştirilmiş teklifler oluşturmak üzere oynatıcı, satın alma ve davranışsal verileri çözümleyebilirsiniz.

* Spark Machine Learning 'i kullanarak, oyun telemetri verilerini analiz edebilir ve Öngörüler elde edebilirsiniz. Yavaş yükleme sürelerini ve oyun içi sorunları tanılayabilir ve engelleyebilirsiniz.

Aşağıdaki görüntüde, oyun analizinin Azure Cosmos DB Spark desteğinin nasıl kullanıldığı gösterilmektedir:

![Oyun analizinden Azure Cosmos DB Spark desteği](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB avantajları hakkında bilgi edinmek için [genel bakış](introduction.md) makalesine bakın.
* [MongoDB için Azure Cosmos DB API 'SI ile çalışmaya başlama](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API kullanmaya başlayın](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API 'sini kullanmaya başlayın](graph-introduction.md)
* [Azure Cosmos DB Tablo API'si kullanmaya başlayın](table-introduction.md)
