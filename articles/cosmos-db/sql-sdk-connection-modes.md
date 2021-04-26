---
title: SQL SDK bağlantı modlarını Azure Cosmos DB
description: Azure Cosmos DB SQL SDK 'lerinde bulunan farklı bağlantı modları hakkında bilgi edinin.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: cf985999bac0cf45eec5d8f0f5f9e921b6f4591c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934960"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>SQL SDK bağlantı modlarını Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

İstemcinin Azure Cosmos DB 'e bağlanması, özellikle de gözlemlenen istemci tarafı gecikme süresi için önemli performans etkilerine sahiptir. Azure Cosmos DB, HTTPS üzerinden ağ geçidi modu adlı basit, açık bir yeniden programlama modeli sunar. Ayrıca, iletişim modelinde da daha fazla olan ve ilk kimlik doğrulaması için TLS ve doğrudan mod adlı trafiği şifrelemek için etkin bir TCP protokolü sunar.

## <a name="available-connectivity-modes"></a>Kullanılabilir bağlantı modları

Kullanılabilir iki bağlantı modu şunlardır:

  * Ağ geçidi modu
      
    Ağ Geçidi modu tüm SDK platformlarında desteklenir. Uygulamanız, katı güvenlik duvarı kısıtlamalarına sahip bir kurumsal ağ içinde çalışıyorsa, standart HTTPS bağlantı noktasını ve tek bir DNS uç noktasını kullandığından, ağ geçidi modu en iyi seçimdir. Ancak performans zorunluluğunu getirir, ağ geçidi modunun, verilerin Azure Cosmos DB her okunışında veya üzerine yazıldığı her seferinde ek bir ağ atlaması içerir. Ayrıca, sınırlı sayıda soket bağlantısı olan ortamlarda uygulamalar çalıştırdığınızda ağ geçidi bağlantı modunu da öneririz.

    Azure Işlevlerinde SDK kullandığınızda, özellikle [Tüketim planında](../azure-functions/consumption-plan.md), [bağlantılardaki geçerli limitlerin](../azure-functions/manage-connections.md)farkında olun.

  * Doğrudan mod

    Doğrudan mod, TCP protokolü aracılığıyla bağlanırlığı destekler ve daha az ağ atlaması olduğundan daha iyi performans sunar. Uygulama, arka uç çoğaltmalarına doğrudan bağlanır. Doğrudan mod şu anda yalnızca .NET ve Java SDK platformlarında desteklenir.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB bağlantı modları" border="false":::

Bu bağlantı modları temelde, veri düzleminin istek gösteren yolu-belge okuma ve yazma işlemlerini, istemci makinenizden Azure Cosmos DB arka uçtaki bölümlere göre alır. Doğrudan mod, en iyi performans için tercih edilen seçenektir. istemcinin TCP bağlantılarını Azure Cosmos DB arka uçtaki bölümlerle doğrudan açmasına olanak sağlar ve istekleri doğrudan bir aracı olmadan *doğrudan* gönderebilirsiniz. Buna karşılık, ağ geçidi modunda, istemciniz tarafından yapılan istekler, Azure Cosmos DB Ön uçtaki "ağ geçidi" sunucusuna yönlendirilir ve bu da isteklerinizi Azure Cosmos DB arka uçta uygun bölümlere (ler) gönderir.

## <a name="service-port-ranges"></a>Hizmet bağlantı noktası aralıkları

Doğrudan modu kullandığınızda, ağ geçidi bağlantı noktalarına ek olarak, Azure Cosmos DB dinamik TCP bağlantı noktalarını kullandığından 10000 ve 20000 arasındaki bağlantı noktası aralığının açık olduğundan emin olmanız gerekir. [Özel uç noktalarında](./how-to-configure-private-endpoints.md)doğrudan mod KULLANıLıRKEN, TCP bağlantı noktalarının tam aralığı-0 ile 65535 arasında olmalıdır. Bu bağlantı noktaları açık değilse ve TCP protokolünü kullanmaya çalışırsanız, 503 hizmetinde kullanılamayan bir hata alabilirsiniz.

Aşağıdaki tabloda, çeşitli API 'Ler ve her API için kullanılan hizmet bağlantı noktaları için kullanılabilen bağlantı modlarının bir özeti gösterilmektedir:

|Bağlantı modu  |Desteklenen Protokol  |Desteklenen SDK 'lar  |API/hizmet bağlantı noktası  |
|---------|---------|---------|---------|
|Ağ geçidi  |   HTTPS    |  Tüm SDK 'lar    |   SQL (443), MongoDB (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443) <br> 10250 numaralı bağlantı noktası, coğrafi çoğaltma olmadan MongoDB örneği için varsayılan Azure Cosmos DB API 'sine eşlenir. Ancak 10255 ve 10256 bağlantı noktaları, coğrafi çoğaltma içeren örnekle eşlenir.   |
|Direct    |     TCP    |  .NET SDK Java SDK 'Sı    | Ortak/hizmet uç noktaları kullanılırken: 10000 ile 20000 arasında bağlantı noktaları<br>Özel uç noktalar kullanılırken: 0 ila 65535 aralığındaki bağlantı noktaları |

## <a name="next-steps"></a>Sonraki adımlar

Belirli SDK Platformu performans iyileştirmeleri için:

* [.NET v2 SDK performans ipuçları](performance-tips.md)

* [.NET v3 SDK performans ipuçları](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Java v4 SDK performans ipuçları](performance-tips-java-sdk-v4-sql.md)