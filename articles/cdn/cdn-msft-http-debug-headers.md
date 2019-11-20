---
title: Microsoft 'tan Azure CDN için HTTP üstbilgilerini hata ayıkla | Microsoft Docs
description: Hata ayıklama önbelleği istek üstbilgileri, istenen varlığa uygulanan önbellek ilkesi hakkında ek bilgiler sağlar. Bu üstbilgiler, Microsoft 'tan Azure CDN özgüdür.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814079"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft 'tan Azure CDN için HTTP üst bilgisinde hata ayıkla
Hata ayıklama yanıt üst bilgisi `X-Cache`, içerik tarafından hangi CDN yığınının hangi katmana sunulduğunu, ayrıntılar sağlar. Bu üst bilgi, Microsoft 'tan Azure CDN özgüdür.

### <a name="response-header-format"></a>Yanıt üst bilgisi biçimi

Üstbilgi | Açıklama
-------|------------
X-önbellek: TCP_HIT | Bu üst bilgi, içerik CDN Edge önbelleğinden sunulduğunda döndürülür. 
X-önbellek: TCP_REMOTE_HIT | Bu üst bilgi, içerik CDN bölgesel önbelleğinden (kaynak kalkan katmanı) sunulduğunda döndürülür
X-önbellek: TCP_MISS | Bu üst bilgi, bir önbellek isabetsizliği olduğunda ve içerik kaynaktan sunulduğunda döndürülür. 


