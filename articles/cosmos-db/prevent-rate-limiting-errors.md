---
title: MongoDB işlemlerine yönelik Azure Cosmos DB API için hız sınırlaması hatalarını engelleyin.
description: MongoDB işlemlerine yönelik Azure Cosmos DB API 'nizin SSR (sunucu tarafı yeniden deneme) özelliği ile hataları sınırlandırma oranını nasıl önleyeceğinizi öğrenin.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507939"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>MongoDB işlemlerine yönelik Azure Cosmos DB API 'sinin hız sınırlaması hatalarını engelleyin
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB işlemlerine yönelik Azure Cosmos DB API 'SI, bir koleksiyonun aktarım hızı sınırını (ru) aşarsa hız sınırlaması (16500/429) hatalarıyla başarısız olabilir. 

Sunucu tarafı yeniden deneme (SSR) özelliğini etkinleştirebilir ve sunucunun bu işlemleri otomatik olarak yeniden denemesini sağlayabilirsiniz. İstek, hesabınızdaki tüm koleksiyonlar için kısa bir gecikmeden sonra yeniden denenir. Bu özellik, istemci uygulamasındaki hız sınırlaması hatalarını işlemeye yönelik kullanışlı bir alternatiftir.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. MongoDB hesabı için Azure Cosmos DB API 'nize gidin.

1. **Ayarlar** bölümünün altındaki **Özellikler** bölmesine gidin.

1. **Sunucu tarafında yeniden dene**' yi seçin.

1. Hesabınızdaki tüm koleksiyonlar için bu özelliği etkinleştirmek üzere **Etkinleştir** ' e tıklayın.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="MongoDB için Azure Cosmos DB API 'sinin sunucu tarafı yeniden deneme özelliğinin ekran görüntüsü":::

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

1. SSR 'nin hesabınız için zaten etkin olup olmadığını denetleyin:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Etkinleştir** Veritabanı hesabınızdaki tüm koleksiyonlar için SSR. Bu değişikliğin etkili olması için 15 dakika kadar sürebilir.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Aşağıdaki komut, yetenekler listesinden "Disableratelimitingyanıtlarının" öğesini kaldırarak veritabanı hesabınızdaki tüm koleksiyonlar için SSR 'yi **devre dışı bırakacak** . Bu değişikliğin etkili olması için 15 dakika kadar sürebilir.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular
* İstekler nasıl yeniden denenir?
    * İstekler, 60 saniyelik bir zaman aşımıyla ulaşılana kadar sürekli olarak yeniden denenir (tekrar ve tekrar). Zaman aşımına ulaşıldığında, istemci [Exceededtimelimit özel durumu alır (50)](mongodb-troubleshoot.md).
*  SSR 'nin etkilerini nasıl izleyebilirim?
    *  Cosmos DB ölçümleri bölmesinde sunucu tarafı yeniden denenen hız sınırlandırma hatalarını (429s) görüntüleyebilirsiniz. Bu hataların, işlendikleri ve sunucu tarafında yeniden denendikleri için SSR etkinleştirildiğinde istemciye gitmeyeceğini göz önünde bulundurun. 
    *  [Cosmos DB kaynak günlüklerinizde](cosmosdb-monitor-resource-logs.md)"Estimateddelayfromratelimitingınmilliseconds" içeren günlük girişlerini arayabilirsiniz.
*  SSR tutarlılık düzeyimi etkiler mi?
    *  SSR bir isteğin tutarlılığını etkilemez. İstekler hız sınırlıysa (429 hatası ile) sunucu tarafında yeniden denenir. 
*  SSR, İstemcimin alabileceği herhangi bir hata türünü etkiler mi?
    *  Hayır, SSR yalnızca sunucu tarafı yeniden denenerek hız sınırlandırma hatalarını (429s) etkiler. Bu özellik, istemci uygulamasındaki hız sınırlaması hatalarını idare etmesini önler. [Diğer tüm hatalar](mongodb-troubleshoot.md) istemciye gider. 

## <a name="next-steps"></a>Sonraki adımlar

Yaygın hatalarda sorun giderme hakkında daha fazla bilgi için şu makaleye bakın:

* [MongoDB için Azure Cosmos DB API 'sindeki yaygın sorunları giderme](mongodb-troubleshoot.md)
