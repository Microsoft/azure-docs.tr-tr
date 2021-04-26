---
title: Azure Işlevleriyle Azure Cosmos DB değişiklik akışını kullanma
description: Azure Cosmos DB değişiklik akışına bağlanmak için Azure Işlevleri 'ni kullanın. Daha sonra her yeni olayda tetiklenen reaktif Azure işlevleri oluşturabilirsiniz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340252"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB ve Azure Işlevleri ile sunucusuz olay tabanlı mimariler
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Işlevleri, [değişiklik akışına](change-feed.md)bağlanmak için en kolay yolu sağlar. Azure Cosmos kapsayıcısının değişiklik akışındaki her yeni olayda otomatik olarak tetiklenecek küçük reaktif Azure Işlevleri oluşturabilirsiniz.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Cosmos DB için Azure Işlevleri tetikleyicisiyle çalışan sunucusuz olay tabanlı Işlevler" border="false":::

[Cosmos DB Için Azure işlevleri tetiklemesi](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)sayesinde, herhangi bir [çalışan altyapısını](./change-feed-processor.md)sürdürmenize gerek kalmadan [değişiklik akışı işlemcisinin](./change-feed-processor.md)ölçeklendirilmesine ve güvenilir olay algılama işlevlerinden yararlanabilirsiniz. Olay kaynağını belirleme işlem hattının geri kalanı hakkında endişelenmeden Azure Işlevinizin mantığına odaklanmanız yeterlidir. Tetikleyiciyi diğer [Azure işlevleri bağlamalarıyla](../azure-functions/functions-triggers-bindings.md#supported-bindings)de karıştırabilirsiniz.

> [!NOTE]
> Şu anda, Cosmos DB için Azure Işlevleri yalnızca çekirdek (SQL) API 'SI ile kullanım için desteklenir.

## <a name="requirements"></a>Gereksinimler

Sunucusuz bir olay tabanlı akış uygulamak için şunlar gerekir:

* **İzlenen kapsayıcı**: izlenen kapsayıcı, Izlenen Azure Cosmos kapsayıcısıdır ve değişiklik beslemenin oluşturulduğu verileri depolar. Tüm ekler, izlenen kapsayıcıya yapılan güncelleştirmeler kapsayıcının değişiklik akışına yansıtılır.
* **Kira kapsayıcısı**: kira kapsayıcısı, birden çok ve dinamik sunucusuz Azure işlev örneğinde durum tutar ve dinamik ölçeklendirmeyi sağlar. Bu Kiralama kapsayıcısı, Cosmos DB için Azure Işlevleri tetikleyicisi tarafından el ile veya otomatik olarak oluşturulabilir. Kira kapsayıcısını otomatik olarak oluşturmak için, [yapılandırmada](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) *Createleasecollectionifnotexists* bayrağını ayarlayın. Bölümlenmiş kira kapsayıcıları, bölüm anahtarı tanımına sahip olmak için gereklidir `/id` .

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetiklerinizi oluşturma

Azure işlevinizi Cosmos DB için Azure Işlevleri tetikleyicisi ile oluşturmak artık tüm Azure Işlevleri IDE ve CLı tümleştirmeleri arasında desteklenmektedir:

* Visual Studio kullanıcıları için [Visual Studio uzantısı](../azure-functions/functions-develop-vs.md) .
* Visual Studio Code kullanıcılar için [Visual Studio Code uzantısı](/azure/developer/javascript/tutorial-vscode-serverless-node-01) .
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
