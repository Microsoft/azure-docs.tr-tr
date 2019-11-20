---
title: Model sürümü oluşturma
titleSuffix: Azure Cognitive Services
description: V3 uç noktalarında model sürümlerini belirtme
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021204"
---
Metin Analizi API'si sürüm 3, verilerinizde kullanılan Metin Analizi modelini seçmenizi sağlar. İstekinizdeki modelin bir sürümünü seçmek için isteğe bağlı `model-version` parametresini kullanın. Bu parametre belirtilmemişse, API varsayılan olarak en son kararlı model sürümüne `latest`.

Kullanılabilir model sürümleri:
* `2019-10-01` (`latest`)

V3 uç noktalarından alınan her yanıt, kullanılan model sürümünü belirten bir `model-version` alanı içerir.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
