---
title: Önceden oluşturulmuş modeller-LUSıS
titleSuffix: Azure Cognitive Services
description: Önceden oluşturulmuş modeller, etki alanları, amaçlar, utbotslar ve varlıklar sağlar. Uygulamanızı önceden oluşturulmuş bir etki alanıyla başlatabilir veya uygulamanıza daha sonra ilgili bir etki alanı ekleyebilirsiniz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: d9cb86c1c19649052e4796fd0a8909ce08381d55
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487583"
---
# <a name="prebuilt-models"></a>Önceden oluşturulmuş modeller

Önceden oluşturulmuş modeller, etki alanları, amaçlar, utbotslar ve varlıklar sağlar. Uygulamanızı önceden oluşturulmuş bir modelle başlatabilir veya uygulamanıza daha sonra ilgili bir model ekleyebilirsiniz. 

## <a name="types-of-prebuilt-models"></a>Önceden oluşturulmuş modellerin türleri

LUSıS, üç tür önceden oluşturulmuş model sağlar. Her bir model uygulamanıza dilediğiniz zaman eklenebilir. 

|Model türü|Şunları İçerir|
|--|--|
|[Alanını](luis-reference-prebuilt-domains.md)|Amaçlar, utterslar, varlıklar|
|Hedefler|Amaçlar, utbotslar|
|[Varlıklar](luis-reference-prebuilt-entities.md)|Yalnızca varlıklar| 

## <a name="prebuilt-domains"></a>Önceden oluşturulmuş etki alanları

Language Understanding (LUU), etki alanları veya ortak istemci uygulamaları kategorileri için birlikte çalışan, önceden oluşturulmuş [Amaç](luis-how-to-add-intents.md) ve [varlık](luis-concept-entity-types.md) modelleri olan *önceden oluşturulmuş etki alanları*sağlar. 

Önceden oluşturulmuş etki alanları, LUSıS Uygulamanıza eklenmeye hazır hale gelir. Önceden oluşturulmuş bir etki alanının amaçları ve varlıkları uygulamanıza eklendikten sonra tamamen özelleştirilebilir. 

> [!TIP]
> Önceden oluşturulmuş bir etki alanındaki amaçlar ve varlıklar en iyi şekilde çalışır. Mümkün olduğunda aynı etki alanındaki amaçları ve varlıkları birleştirmek daha iyidir.
> Yardımcı programlar önceden oluşturulmuş etki alanı, herhangi bir etki alanında kullanım için özelleştirebileceğiniz amaçlar içerir. Örneğin, uygulamanıza `Utilities.Repeat` ekleyebilir ve bu uygulamayı, kullanıcının uygulamanızda yinelemek isteyebileceğiniz her türlü eylemi tanımasını sağlayabilirsiniz. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Önceden oluşturulmuş bir etki alanı hedefinin davranışını değiştirme

Önceden oluşturulmuş bir etki alanının, LUIN uygulamanızda olmasını istediğiniz bir amaca benzer bir amaç içerdiğini, ancak farklı davranması gerektiğini fark edebilirsiniz. Örneğin, önceden oluşturulmuş olan etki **alanı, Restoran** rezervasyonu yapmak için bir `MakeReservation` amacı sağlar, ancak uygulamanızın bu amacı, otel rezervasyonları yapmak için kullanmasını istiyorsunuz. Bu durumda, otel rezervasyonları yapma amacına yönelik örnek yazarak bu amacın davranışını değiştirebilirsiniz. Ardından uygulamayı yeniden geri çeker. 

Önceden [oluşturulmuş etki alanları başvurusunda](./luis-reference-prebuilt-domains.md)önceden oluşturulmuş etki alanlarının tam listesini bulabilirsiniz.

## <a name="prebuilt-intents"></a>Önceden oluşturulmuş amaçlar

LUO, önceden oluşturulan etki alanlarından önceden oluşturulmuş amaçlar ve bunların utslarını sağlar. Tüm etki alanı eklenmeksizin amaçlar eklenebilir. Amaç eklemek, uygulamanıza amaç ve bu nesnelerin bir hedefini ekleme işlemidir. Amaç adı ve söylenişi listesinin her ikisi de değiştirilebilir.  

## <a name="prebuilt-entities"></a>Önceden oluşturulmuş varlıklar

LUO, tarihler, saatler, sayılar, ölçümler ve para birimi gibi ortak bilgi türlerini tanınması için önceden oluşturulmuş varlıklar kümesi içerir. Önceden oluşturulmuş varlık desteği, LUSıS uygulamanızın kültürüne göre farklılık gösterir. LUID 'nin desteklediği önceden oluşturulmuş varlıkların tam listesi için, kültüre göre destek dahil olmak üzere, [önceden oluşturulmuş varlık başvurusuna](./luis-reference-prebuilt-entities.md)bakın.

Uygulamanıza önceden oluşturulmuş bir varlık dahil edildiğinde, tahminleri yayımlanmış uygulamanıza dahil edilir. Önceden oluşturulmuş varlıkların davranışı önceden eğitilmiş **ve değiştirilemez** . 

> [!NOTE]
> **Builtin. DateTime** kullanım dışıdır. Tarih ve saat aralıklarının tanınabilmesi ve belirsiz tarih ve saatlerin daha iyi tanınmasını sağlayan [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md)ile değiştirilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanıza [önceden oluşturulmuş varlıklar eklemeyi](luis-prebuilt-entities.md) öğrenin.
