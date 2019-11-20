---
title: Azure Cosmos DB bölümleniyor
description: Azure Cosmos DB bölümlemeye genel bakış.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 38b4e4c2541bf30bd9c95d9c0ec61779b0d1d7bb
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753254"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB bölümleniyor

Azure Cosmos DB, uygulamanızın performans ihtiyaçlarını karşılamak üzere bir veritabanındaki ayrı kapsayıcıları ölçeklendirmek için bölümleme kullanır. Bölümleme bölümünde, bir kapsayıcıdaki öğeler *mantıksal bölümler*adlı farklı alt kümelere bölünür. Mantıksal bölümler, bir kapsayıcıdaki her öğeyle ilişkili bir *bölüm anahtarının* değerine göre oluşturulur. Mantıksal bir bölümdeki tüm öğeler aynı bölüm anahtarı değerine sahiptir.

Örneğin, bir kapsayıcı öğeleri barındırır. Her öğenin `UserID` özelliği için benzersiz bir değeri vardır. `UserID` kapsayıcıdaki öğeler için bölüm anahtarı görevi görür ve 1.000 benzersiz `UserID` değeri varsa, kapsayıcı için 1.000 mantıksal bölümler oluşturulur.

Öğenin mantıksal bölümünü belirleyen bir bölüm anahtarına ek olarak, bir kapsayıcıdaki her öğe bir *öğe kimliğine* (mantıksal bölüm içinde benzersiz) sahiptir. Bölüm anahtarını ve öğe KIMLIĞINI birleştirmek, öğenin benzersiz şekilde tanımlandığı öğenin *dizinini*oluşturur.

[Bölüm anahtarının seçilmesi](partitioning-overview.md#choose-partitionkey) , uygulamanızın performansını etkileyecek önemli bir karardır.

## <a name="managing-logical-partitions"></a>Mantıksal bölümleri yönetme

, Kapsayıcının ölçeklenebilirlik ve performans ihtiyaçlarını etkili bir şekilde karşılamak üzere fiziksel bölümlerin mantıksal bölümlerinin yerleşimini saydam ve otomatik olarak yönetir. Azure Cosmos DB Bir uygulamanın aktarım hızı ve depolama gereksinimleri artdıkça, yükü daha fazla sayıda sunucuda otomatik olarak yaymak için mantıksal bölümleri Azure Cosmos DB. 

Azure Cosmos DB, mantıksal bölümleri fiziksel bölümler arasında yaymak için karma tabanlı bölümleme kullanır. Bir öğenin bölüm anahtarı değerini karma Azure Cosmos DB. Karma hale getirilmiş sonuç fiziksel bölümü belirler. Ardından Azure Cosmos DB, bölüm anahtarı karmalarının anahtar alanını fiziksel bölümler arasında eşit olarak ayırır.

Tek bir mantıksal bölüm içindeki verilere erişen sorgular, birden çok bölüme erişen sorgulardan daha uygun maliyetli bir hesaplardır. İşlemlere (saklı yordamlarda veya tetikleyicilere) yalnızca tek bir mantıksal bölümdeki öğelere karşı izin verilir.

Azure Cosmos DB bölümleri nasıl yönettiği hakkında daha fazla bilgi edinmek için bkz. [mantıksal bölümler](partition-data.md). (Uygulamalarınızı derlemek veya çalıştırmak için dahili ayrıntıların anlaşılması gerekmez, ancak bunu merak eden bir okuyucu için buraya eklemiş olmanız gerekmez.)

## <a id="choose-partitionkey"></a>Bölüm anahtarı seçme

Aşağıda, bölüm anahtarı seçmeye yönelik iyi bir kılavuz verilmiştir:

* Tek bir mantıksal bölümün 10 GB depolama üst sınırı vardır.  

* Azure Cosmos kapsayıcıları, saniyede en az 400 istek birimi (RU/sn) aktarım hızına sahiptir. Bir veritabanında aktarım hızı sağlandığında, kapsayıcı başına en az RU, saniyede 100 istek birimi (RU/s) olur. Aynı bölüm anahtarına yapılan istekler bir bölüme ayrılan aktarım hızını aşamaz. İstekler ayrılan aktarım hızını aşarsa, istekler hız sınırlı olacaktır. Bu nedenle, uygulamanızın içinde "etkin noktalar" ile sonuçlanmaması gereken bir bölüm anahtarı seçmek önemlidir.

* Geniş bir değer aralığına ve mantıksal bölümler arasında eşit olarak yayılan erişim düzenlerine sahip bir bölüm anahtarı seçin. Bu, veri depolama ve verimlilik için kaynakların mantıksal bölümler arasında dağıtılabilecek şekilde, kapsayıcıda verileri ve etkinliği mantıksal bölümler kümesi genelinde yaymaya yardımcı olur.

* İş yükünü tüm bölümler arasında eşit ve zaman içinde eşit olarak yayılan bir bölüm anahtarı seçin. Bölüm anahtarınız tercih etmeniz, etkili bölüm sorguları ve işlemler gereksinimini, ölçeklenebilirlik elde etmek için birden çok bölüme dağıtma hedefine karşı dengelemelidir.

* Bölüm anahtarları adayları, sorgularda filtre olarak sık görüntülenen özellikleri içerebilir. Filtre koşuluna bölüm anahtarı eklenerek sorgular etkin bir şekilde yönlendirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB bölümünde bölümlendirme ve yatay ölçeklendirme](partition-data.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de sağlanan aktarım hızı](request-units.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de küresel dağıtım](distribute-data-globally.md)hakkında bilgi edinin.
