---
title: Azure API yönetim ilkesi örneği-günlük kaydı için Stackbir hata gönder | Microsoft Docs
description: Azure API Management ilkesi örneği-günlüğe kaydetme için Stackbelirt 'e hata göndermek üzere bir hata günlüğü ilkesinin nasıl ekleneceğini gösterir.
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067586"
---
# <a name="send-errors-to-stackify-for-logging"></a>Günlüğe kaydetme için Stackbelirt hatalarını gönderme

Bu makalede, günlüğe kaydetme için Stackbelirt hata günlüğü ilkesinin nasıl ekleneceğini gösteren bir Azure API Management Policy örneği gösterilmektedir. Bir ilke kodu ayarlamak veya düzenlemek için, [Ilke ayarlama veya düzenleme](../set-edit-policies.md)bölümünde açıklanan adımları izleyin. Diğer örnekleri görmek için bkz. [ilke örnekleri](../policy-samples.md).

## <a name="policy"></a>İlke

Kodu, **hata üzerindeki** bloğa yapıştırın.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Sonraki adımlar

APıM ilkeleri hakkında daha fazla bilgi edinin:

+ [Dönüştürme ilkeleri](../api-management-transformation-policies.md)
+ [İlke örnekleri](../policy-samples.md)

