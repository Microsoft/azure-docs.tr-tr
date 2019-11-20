---
title: Cosmos DB bağlantı ilkesi için Azure Işlevleri tetikleyicisi
description: Cosmos DB için Azure Işlevleri tetikleyicisi tarafından kullanılan bağlantı ilkesini yapılandırma hakkında bilgi edinin
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 6e4c50e5dcc35450463d02bbed040754ea778e70
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093612"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetikleyicisi tarafından kullanılan bağlantı ilkesini yapılandırma

Bu makalede, Azure Cosmos hesabınıza bağlanmak üzere Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken bağlantı ilkesini nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="why-is-the-connection-policy-important"></a>Bağlantı ilkesi neden önemlidir?

İki bağlantı modu vardır-doğrudan mod ve ağ geçidi modu. Bu bağlantı modları hakkında daha fazla bilgi için bkz. [Performans ipuçları](./performance-tips.md#networking) makalesi. Varsayılan olarak, **ağ geçidi** , Cosmos DB Için Azure işlevleri tetikleyicisinde tüm bağlantıları oluşturmak için kullanılır. Ancak, performans odaklı senaryolar için en iyi seçenek olmayabilir.

## <a name="changing-the-connection-mode-and-protocol"></a>Bağlantı modunu ve protokolünü değiştirme

İstemci bağlantı ilkesini yapılandırmak için kullanılabilecek iki temel yapılandırma ayarı vardır: **bağlantı modu** ve **bağlantı protokolü**. Cosmos DB için Azure Işlevleri tetikleyicisi tarafından kullanılan varsayılan bağlantı modunu ve protokolünü ve tüm [Azure Cosmos DB bağlamalarını](../azure-functions/functions-bindings-cosmosdb-v2.md#output)değiştirebilirsiniz. Varsayılan ayarları değiştirmek için Azure işlevleri projenizde veya Azure işlevleri uygulamanızda `host.json` dosyayı bulmanız ve aşağıdaki [ek ayarı](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)eklemeniz gerekir:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Burada `connectionMode` istenen bağlantı moduna (doğrudan veya ağ geçidine) ve `protocol` istenen bağlantı protokolüne (TCP veya https) sahip olmanız gerekir. 

Azure işlevleri projeniz Azure işlevleri v1 çalışma zamanı ile çalışıyorsa, yapılandırmanın küçük bir ad farkı vardır; `documentDB` `cosmosDB`bunun yerine şunu kullanmalısınız:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Azure Işlevleri tüketim planı barındırma planı ile çalışırken her örnek, sürdüreme soket bağlantısı miktarına göre bir sınıra sahiptir. Doğrudan/TCP moduyla çalışırken, Tasarım yaparak daha fazla bağlantı oluşturulur ve [Tüketim planı sınırına](../azure-functions/manage-connections.md#connection-limit)ulaşarak, ağ geçidi modunu kullanabilir veya Azure işlevlerinizi [App Service modunda](../azure-functions/functions-scale.md#app-service-plan)çalıştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Işlevlerinde bağlantı sınırları](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB performans ipuçları](./performance-tips.md)
* [Kod örnekleri](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
