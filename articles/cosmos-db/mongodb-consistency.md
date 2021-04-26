---
title: MongoDB için Azure Cosmos DB API 'sinin uyumluluk düzeylerini eşleme
description: MongoDB için Azure Cosmos DB API 'sinin tutarlılık düzeylerini eşleme.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333163"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Azure Cosmos DB için tutarlılık düzeyleri ve MongoDB için API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB aksine, yerel MongoDB kesin olarak tanımlanmış tutarlılık garantisi sağlamaz. Bunun yerine, yerel MongoDB kullanıcıların, istenen tutarlılık düzeyini elde etmek için, okuma işlemlerini birincil veya ikincil çoğaltmalara yönlendirecek bir yazma sorunu, bir okuma sorunu ve isMaster yönergesi sağlar.

MongoDB için Azure Cosmos DB API 'SI kullanılırken MongoDB sürücüsü, yazma bölgenizi birincil çoğaltma olarak değerlendirir ve diğer tüm bölgeler okuma çoğaltmasıdır. Azure Cosmos hesabınızla ilişkilendirilen bölgeyi birincil çoğaltma olarak seçebilirsiniz.

> [!NOTE]
> Azure Cosmos DB için varsayılan tutarlılık modeli oturumdur. Oturum, Cassandra ya da MongoDB tarafından yerel olarak desteklenmeyen istemci merkezli bir tutarlılık modelidir. Hangi tutarlılık modelinin seçeyli hakkında daha fazla bilgi için [Azure Cosmos DB ' deki tutarlılık düzeyleri](consistency-levels.md) ' ne bakın

Azure Cosmos DB, MongoDB için API 'sini kullanırken:

* Yazma sorunu, Azure Cosmos hesabınızda yapılandırılmış varsayılan tutarlılık düzeyiyle eşleştirilir.

* Azure Cosmos DB, MongoDB istemci sürücüsüyle belirtilen okuma kaygısını, bir okuma isteği üzerinde dinamik olarak yapılandırılmış Azure Cosmos DB tutarlılık düzeylerinden birine dinamik olarak eşler.  

* Bölgeyi ilk yazılabilir bölge olarak yaparak Azure Cosmos hesabınızla ilişkili belirli bir bölgeye "birincil" olarak ek açıklama ekleyebilirsiniz. 

## <a name="mapping-consistency-levels"></a>Tutarlılık düzeylerini eşleme

Aşağıdaki tabloda, MongoDB için Azure Cosmos DB API 'SI kullanılırken yerel MongoDB yazma/okuma sorunlarının Azure Cosmos tutarlılık düzeylerine nasıl eşlendiği gösterilmektedir:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB tutarlılık modeli eşleme" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Azure Cosmos hesabınız, güçlü tutarlılık dışında bir tutarlılık düzeyiyle yapılandırıldıysa, olasılık açısından *olasılığa sınırlı bir Stalet* (PBS) ölçüsüne bakarak istemcilerinizin iş yükleriniz için güçlü ve tutarlı okumalar alabilir olasılığını fark edebilirsiniz. Bu ölçüm Azure portal kullanıma sunulur, daha fazla bilgi edinmek için bkz. [Izleme olasılığa yönelik sınırlı Stalet (PBS) ölçümü](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Dayalı sınırlanmış stalet, nihai tutarlılığın ne kadar olduğunu gösterir. Bu ölçüm, Azure Cosmos hesabınızda halen yapılandırdığınız tutarlılık düzeyinden daha güçlü bir tutarlılık elde etme konusunda bir fikir sağlar. Diğer bir deyişle, yazma ve okuma bölgelerinin bir birleşimi için kesin olarak tutarlı okuma elde etme olasılığını (milisaniye olarak ölçülür) görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB için genel dağıtım ve tutarlılık düzeyleri hakkında daha fazla bilgi edinin:

* [Genel dağıtıma genel bakış](distribute-data-globally.md)
* [Tutarlılık düzeyine genel bakış](consistency-levels.md)
* [Yüksek kullanılabilirlik](high-availability.md)
