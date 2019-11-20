---
title: Uygulamanızı planlayın-LUSıS
titleSuffix: Azure Cognitive Services
description: İlgili uygulama amaçlarını ve varlıklarını ana hatlarıyla oluşturun ve ardından Language Understanding akıllı hizmetler (LUU) içinde uygulama planlarınızı oluşturun.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467704"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>LUSıS uygulama şemanızı konu etki alanı ve veri ayıklama ile planlayın

Bir LUSıS uygulama şeması, konu etki alanınız ile ilgili amaçları ve varlıkları içerir. Amaç, Kullanıcı araslarını sınıflandırır ve varlıklar, Kullanıcı dıklarındaki verileri ayıklar. 

## <a name="identify-your-domain"></a>Etki alanınızı tanımla

Bir LUSıS uygulaması, etki alanına özgü bir konunun etrafında ortalanır.  Örneğin, Bilet, fışıklar, otel ve Kiralama otomobillerini gerçekleştiren bir seyahat uygulamasına sahip olabilirsiniz. Başka bir uygulama uygulamayla ilgili içerik sunabilir, uygunluk çalışmalarını izleme ve amaçları ayarlama. Etki alanını tanımlamak, etki alanınız için önemli olan kelimeleri veya tümceleri bulmanıza yardımcı olur.

> [!TIP]
> LUSıS birçok yaygın senaryo için [önceden oluşturulmuş etki alanları](luis-how-to-use-prebuilt-domains.md) sunar.
> Önceden oluşturulmuş bir etki alanını uygulamanız için başlangıç noktası olarak kullanıp kullananağınızın olup olmadığını kontrol edin.

## <a name="identify-your-intents"></a>Amaçlarınızı tanımla

Uygulamanızın görevi için önemli olan [amaçları](luis-concept-intent.md) göz önünde bulundurun. 

Bir uçuş ve Kullanıcı hedefindeki hava durumunu denetlemek için işlevlerle bir seyahat uygulaması örneğini ele alalım. Bu eylemler için `BookFlight` ve `GetWeather` amaçlarını tanımlayabilirsiniz. 

Daha fazla işlev içeren daha karmaşık bir uygulamada, daha fazla amaç vardır ve bu amaçları dikkatle tanımlamanız gerekir, böylelikle amaçlar çok özgü değildir. Örneğin, `BookFlight` ve `BookHotel` ayrı amaçlar olması gerekebilir, ancak `BookInternationalFlight` ve `BookDomesticFlight` çok benzer olabilir.

> [!NOTE]
> Uygulamanızın işlevlerini gerçekleştirmek için ihtiyaç duyduğunuz kadar çok amaç kullanmak en iyi uygulamadır. Çok fazla amaç tanımlarsanız, YAĞıNLARı doğru şekilde sınıflandırılması zor olur. Çok az sayıda tanımlarsanız, bu durum örtüşeceğinden genel olabilir.

Genel Kullanıcı amacını belirlemeniz gerekmiyorsa, tüm örnek Kullanıcı araslarını hiçbiri amacına ekleyin. Uygulamanız daha fazla amaç gerektiren bir şekilde büyürse, daha sonra oluşturabilirsiniz. 

## <a name="create-example-utterances-for-each-intent"></a>Her amaç için örnek oluşturma

Hedefleri belirledikten sonra her bir amaç için 15 ila 30 örnek oluşturma oluşturun. Kullanmaya başlamak için bu sayıdan daha az sahip değildir veya her bir amaç için çok fazla sayıda utterme oluşturun. Her söylük, önceki söyleyden farklı olmalıdır. Söyleyenlerdeki her şey genel sözcük sayısı, kelime seçimi, fiil zaman hali ve noktalama işaretleri içerir. 

Daha fazla [bilgi için,](luis-concept-utterance.md) bu bilgileri gözden geçirin.

## <a name="identify-your-entities"></a>Varlıklarınızı tanımla

Örnekte, ayıklanmak istediğiniz varlıkları belirler. Bir uçuş sağlamak için hedef, tarih, Airline, Bilet kategorisi ve seyahat sınıfı gibi bilgilere ihtiyacınız vardır. Bu veri türleri için varlıklar oluşturun ve ardından, bir amacı elde etmek için önemli olduklarından, örnek türlerdeki [varlıkları](luis-concept-entity-types.md) işaretleyin. 

Uygulamanızda hangi varlıkların kullanılacağını belirlediğinizde, nesne türleri arasındaki ilişkileri yakalamak için farklı türde varlıkların olduğunu aklınızda bulundurun. [Luve Içindeki varlıklar](luis-concept-entity-types.md) , farklı türler hakkında daha fazla ayrıntı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Tipik [geliştirme çevrimi](luis-concept-app-iteration.md)hakkında bilgi edinin.  