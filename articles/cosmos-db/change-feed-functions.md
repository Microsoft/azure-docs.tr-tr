---
title: Azure Işlevleriyle Azure Cosmos DB değişiklik akışını kullanma
description: Azure Işlevleriyle Azure Cosmos DB değişiklik akışını kullanın
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 95fec1ef57c1d70ea484de9ad49b3410ed8594a4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757063"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB ve Azure Işlevleri ile sunucusuz olay tabanlı mimariler

Azure Işlevleri, [değişiklik akışına](change-feed.md)bağlanmak için en kolay yolu sağlar. Azure Cosmos kapsayıcısının değişiklik akışındaki her yeni olayda otomatik olarak tetiklenecek küçük reaktif Azure Işlevleri oluşturabilirsiniz.

![Cosmos DB için Azure Işlevleri tetikleyicisiyle çalışan sunucusuz olay tabanlı Işlevler](./media/change-feed-functions/functions.png)

[Cosmos DB Için Azure işlevleri tetiklemesi](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)sayesinde, herhangi bir [çalışan altyapısını](./change-feed-processor.md)sürdürmenize gerek kalmadan [değişiklik akışı işlemcisinin](./change-feed-processor.md)ölçeklendirilmesine ve güvenilir olay algılama işlevlerinden yararlanabilirsiniz. Olay kaynağını belirleme işlem hattının geri kalanı hakkında endişelenmeden Azure Işlevinizin mantığına odaklanmanız yeterlidir. Tetikleyiciyi diğer [Azure işlevleri bağlamalarıyla](../azure-functions/functions-triggers-bindings.md#supported-bindings)de karıştırabilirsiniz.

> [!NOTE]
> Şu anda, Cosmos DB için Azure Işlevleri yalnızca çekirdek (SQL) API 'SI ile kullanım için desteklenir.

## <a name="requirements"></a>Gereksinimler

Sunucusuz bir olay tabanlı akış uygulamak için şunlar gerekir:

* **İzlenen kapsayıcı**: izlenen kapsayıcı, Izlenen Azure Cosmos kapsayıcısıdır ve değişiklik beslemenin oluşturulduğu verileri depolar. Tüm ekler, izlenen kapsayıcıya yapılan güncelleştirmeler kapsayıcının değişiklik akışına yansıtılır.
* **Kira kapsayıcısı**: kira kapsayıcısı, birden çok ve dinamik sunucusuz Azure işlev örneğinde durum tutar ve dinamik ölçeklendirmeyi sağlar. Bu Kiralama kapsayıcısı, Cosmos DB için Azure Işlevleri tetikleyicisi tarafından el ile veya otomatik olarak oluşturulabilir. Kira kapsayıcısını otomatik olarak oluşturmak için, [yapılandırmada](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) *Createleasecollectionifnotexists* bayrağını ayarlayın. Bölümlenmiş kira kapsayıcıları `/id` bölüm anahtarı tanımına sahip olmak için gereklidir.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetiklerinizi oluşturma

Azure işlevinizi Cosmos DB için Azure Işlevleri tetikleyicisi ile oluşturmak artık tüm Azure Işlevleri IDE ve CLı tümleştirmeleri arasında desteklenmektedir:

* Visual Studio kullanıcıları için [Visual Studio uzantısı](../azure-functions/functions-develop-vs.md) .
* Visual Studio Code kullanıcıları için [Visual Studio Core uzantısı](/azure/javascript/tutorial-vscode-serverless-node-01) .
* Ve son olarak, platformlar arası IDE deneyimi için [temel CLI araçları](../azure-functions/functions-run-local.md#create-func) .

## <a name="run-your-trigger-locally"></a>Tetikleyiciyi yerel olarak çalıştırın

Azure bir abonelik olmadan sunucusuz olay tabanlı akışlarınızı oluşturup geliştirmek için [Azure işlevinizi](../azure-functions/functions-develop-local.md) [Azure Cosmos DB öykünücüsü](./local-emulator.md) ile yerel olarak çalıştırabilirsiniz.

Bulutta canlı senaryoları test etmek isterseniz, kredi kartı veya Azure aboneliği gerekmeden [Cosmos DB ücretsiz olarak deneyebilirsiniz](https://azure.microsoft.com/try/cosmosdb/) .

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerde değişiklik akışı hakkında daha fazla bilgi edinebilirsiniz:

* [Değişiklik akışına genel bakış](change-feed.md)
* [Değişiklik akışını okuma yolları](read-change-feed.md)
* [Değişiklik akışı işlemci kitaplığını kullanma](change-feed-processor.md)
* [Değişiklik akışı işlemci kitaplığıyla çalışma](change-feed-processor.md)
* [Azure Cosmos DB ve Azure Işlevleri 'ni kullanarak sunucusuz veritabanı hesaplama](serverless-computing-database.md)
