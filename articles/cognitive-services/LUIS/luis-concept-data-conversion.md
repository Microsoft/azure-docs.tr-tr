---
title: Veri dönüştürme-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) Öngörüler önce Konuşma nasıl değiştirilebilir öğrenin
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619795"
---
# <a name="convert-data-format-of-utterances"></a>Konuşma veri biçimini Dönüştür
LUO, tahmine göre bir kullanıcı için aşağıdaki dönüştürmeleri sağlar "

* Bilişsel [Hizmetler konuşma](../Speech-Service/overview.md) hizmetini kullanarak konuşmayı metne dönüştürme. 

## <a name="speech-to-text"></a>Konuşmayı metne dönüştürme

Konuşmadan metne, LUSıS ile tümleştirme olarak sunulmaktadır. 

### <a name="intent-conversion-concepts"></a>Amaç dönüştürme kavramları
LUIS metinde konuşma dönüştürülmesi konuşulan konuşma bir uç noktasına göndermesi ve LUIS tahmini yanıt almanızı sağlar. Bir tümleştirme işlemidir [konuşma](https://docs.microsoft.com/azure/cognitive-services/Speech) LUIS ile hizmet. Bir [öğreticide](../speech-service/how-to-recognize-intents-from-speech-csharp.md)konuşma amacı hakkında daha fazla bilgi edinin.

### <a name="key-requirements"></a>Önemli gereksinimler
Oluşturma gerekmez bir **Bing konuşma API'si** Bu tümleştirme için anahtar. A **Language Understanding** Azure portalında oluşturulan anahtarı bu tümleştirme için çalışır. Ludo başlangıç anahtarını kullanmayın.

### <a name="pricing-tier"></a>Fiyatlandırma Katmanı
Bu tümleştirme, her zamanki Language Understanding fiyatlandırma katmanlarından farklı bir [fiyatlandırma](luis-boundaries.md#key-limits) modeli kullanır. 

### <a name="quota-usage"></a>Kota kullanımı
Bkz: [anahtar sınırları](luis-boundaries.md#key-limits) bilgi. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Veri ayıklanıyor](luis-concept-data-extraction.md)

