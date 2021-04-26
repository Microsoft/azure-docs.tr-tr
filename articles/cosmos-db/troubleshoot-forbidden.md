---
title: Yasak özel durumların Azure Cosmos DB sorunlarını giderme
description: Yasak özel durumları tanılamayı ve gidermeyi öğrenin.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971916"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Yasak özel durumları tanılama ve sorun giderme Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP durum kodu 403, isteğin tamamlanmasını yasak olduğunu gösterir.

## <a name="firewall-blocking-requests"></a>Güvenlik duvarı engelleme istekleri
Bu senaryoda, aşağıdaki gibi hataları görmek yaygındır:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Çözüm
Geçerli [güvenlik duvarı ayarlarınızın](how-to-configure-firewall.md) doğru olduğunu ve bağlanmaya çalıştığınız IP 'leri veya ağları içerdiğini doğrulayın.
Yakın zamanda bunları güncelleştirdiyseniz, değişikliklerin **uygulanması 15 dakika kadar** sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [IP güvenlik duvarını](how-to-configure-firewall.md)yapılandırın.
* [Sanal ağlardan](how-to-configure-vnet-service-endpoint.md)erişimi yapılandırın.
* [Özel uç noktalardan](how-to-configure-private-endpoints.md)erişimi yapılandırın.
