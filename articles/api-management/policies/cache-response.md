---
title: Azure API yönetimi ilkesi örneği-arka uç hizmetine özellik ekleme | Microsoft Docs
description: Azure API Management ilkesi örneği-bir arka uç hizmetine nasıl özellik ekleneceğini gösterir. Örneğin, hava durumu tahmini API'sinde enlem ve boylam yerine bir yer adını kabul edin.
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
ms.openlocfilehash: 705d7e44f64f8dc3bba669cd80dafdab078fcccc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067765"
---
# <a name="add-capabilities-to-a-backend-service"></a>Arka uç hizmetine özellik ekleme

Bu makalede, bir arka uç hizmetine nasıl özellik ekleneceğini gösteren bir Azure API yönetim ilkesi örneği gösterilmektedir. Örneğin, hava durumu tahmini API'sinde enlem ve boylam yerine bir yer adını kabul edin. Bir ilke kodu ayarlamak veya düzenlemek için, [Ilke ayarlama veya düzenleme](../set-edit-policies.md)bölümünde açıklanan adımları izleyin. Diğer örnekleri görmek için bkz. [ilke örnekleri](../policy-samples.md).

## <a name="policy"></a>İlke

Kodu **gelen** bloğa yapıştırın.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Sonraki adımlar

APıM ilkeleri hakkında daha fazla bilgi edinin:

+ [Dönüştürme ilkeleri](../api-management-transformation-policies.md)
+ [İlke örnekleri](../policy-samples.md)

