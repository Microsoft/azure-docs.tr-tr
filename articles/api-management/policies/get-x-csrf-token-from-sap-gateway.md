---
title: Azure API yönetim ilkesi örneği-X-CSRF deseninin uygulanması | Microsoft Docs
description: Azure API Management ilkesi örneği-birçok API tarafından kullanılan X-CSRF deseninin nasıl uygulanacağını gösterir. Bu örnek SAP Ağ Geçidi'ne özgüdür.
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
ms.openlocfilehash: 14ea9113bf5712d6ffce356d02abb7224c21771a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067718"
---
# <a name="implement-x-csrf-pattern"></a>X-CSRF modelini Uygula

Bu makalede, birçok API tarafından kullanılan X-CSRF deseninin nasıl uygulanacağını gösteren bir Azure API yönetim ilkesi örneği gösterilmektedir. Bu örnek SAP Ağ Geçidi'ne özgüdür. Bir ilke kodu ayarlamak veya düzenlemek için, [Ilke ayarlama veya düzenleme](../set-edit-policies.md)bölümünde açıklanan adımları izleyin. Diğer örnekleri görmek için bkz. [ilke örnekleri](../policy-samples.md).

## <a name="policy"></a>İlke

Kodu **gelen** bloğa yapıştırın.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Sonraki adımlar

APıM ilkeleri hakkında daha fazla bilgi edinin:

+ [Dönüştürme ilkeleri](../api-management-transformation-policies.md)
+ [İlke örnekleri](../policy-samples.md)

