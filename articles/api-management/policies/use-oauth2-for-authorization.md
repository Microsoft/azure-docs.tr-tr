---
title: Örnek Azure API Yönetimi İlkesi-ağ geçidi ve arka uç arasında yetkilendirme için OAuth2 kullanın
titleSuffix: Azure API Management
description: Azure API Management ilkesi örneği-ağ geçidi ile arka uç arasında yetkilendirme için OAuth2 kullanmayı gösterir. AAD'den erişim belirtecini alma ve bunu arka uca iletme işlemini de gösterir.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076105"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Ağ Geçidi ile arka uç arasında yetkilendirme için OAuth2 kullanma

Bu makalede, ağ geçidi ile arka uç arasında yetkilendirme için OAuth2 kullanmayı gösteren bir Azure API yönetim ilkesi örneği gösterilmektedir. AAD'den erişim belirtecini alma ve bunu arka uca iletme işlemini de gösterir. 

Bir ilke kodu ayarlamak veya düzenlemek için, [Ilke ayarlama veya düzenleme](../set-edit-policies.md)bölümünde açıklanan adımları izleyin. Diğer örnekleri görmek için bkz. [ilke örnekleri](../policy-reference.md).

Aşağıdaki betik {{Property}} içinde görünen özellikleri kullanır. Özellikler hakkında bilgi edinmek ve bunları API Management ilkelerinde kullanmayı öğrenmek için [Bu](../api-management-howto-properties.md) konuya bakın.
 
## <a name="policy"></a>İlke

Kodu **gelen** bloğa yapıştırın.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Sonraki adımlar

APıM ilkeleri hakkında daha fazla bilgi edinin:

+ [Dönüştürme ilkeleri](../api-management-transformation-policies.md)
+ [İlke örnekleri](../policy-reference.md)