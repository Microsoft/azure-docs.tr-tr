---
title: Azure Cosmos öykünücüsü indirme ve sürüm notları
description: Azure Cosmos öykünücü sürüm notlarını okuyun ve indirin.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: cda3b0db1a4f1b098d8ea57887a047fcdef04243
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286494"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos öykünücüsü-sürüm notları ve indirme bilgileri

Bu makalede, Azure Cosmos öykünücü sürüm notları her sürümde yapılan Özellik güncelleştirmelerinin bir listesi ile gösterilir. Ayrıca, indirme ve kullanma öykünücünün en son sürümünü de listeler.

## <a name="download"></a>İndirin

| | |
|---------|---------|
|**MSI indirme**|[Microsoft İndirme Merkezi](https://aka.ms/cosmosdb-emulator)|
|**Başlarken**|[Azure Cosmos öykünücüsü ile yerel olarak geliştirme](local-emulator.md)|

## <a name="release-notes"></a>Sürüm notları

### <a name="270"></a>2.7.0

- Bu sürüm, .NET Core veya x86 .NET tabanlı istemcileri kullanırken kullanıcılardan SQL API hesabında sorgu yürütmesini önleyen bir gerileme düzeltir.

### <a name="246"></a>2.4.6

- Bu sürüm Azure Cosmos 2019 hizmetindeki özelliklerle birlikte Azure [Cosmos öykünücüsü ile yerel olarak geliştirme](local-emulator.md)bölümünde belirtilen özel durumlar ile eşlik sağlar. Ayrıca, komut satırı ve doğrudan mod bağlantısı kullanan SDK istemcileri için iç IP adresi geçersiz kılmaları aracılığıyla çağrıldığında öykünücü kapatmaya ilişkin çeşitli hataları düzeltir.

### <a name="243"></a>2.4.3

- MongoDB hizmetinin varsayılan olarak başlatılması devre dışı bırakıldı. Yalnızca SQL uç noktası varsayılan olarak etkindir. Kullanıcı, öykünücü "/EnableMongoDbEndpoint" komut satırı seçeneğini kullanarak uç noktayı el ile başlatmalıdır. Artık Gremlin, Cassandra ve Table gibi diğer tüm hizmet uç noktalarına benzer.
- Gremlin, Cassandra ve tablo uç noktalarının dış istemcilerden gelen istekleri doğru bir şekilde işleyemediğinde "/AllowNetworkAccess" ile Başlarken öykünücüsünde hata düzeltildi.
- Güvenlik duvarı kuralları ayarlarına doğrudan bağlantı bağlantı noktaları ekleyin.

### <a name="240"></a>2.4.0

- Ana bilgisayarda Pulse Istemcisi gibi ağ izleme uygulamaları varsa öykünücü ile ilgili bir sorun düzeltildi.
