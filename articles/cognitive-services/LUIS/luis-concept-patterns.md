---
title: Desenler yardım tahmini-LUSıS
titleSuffix: Azure Cognitive Services
description: Bir desen çok daha fazla konuşma sağlamadan bir amaç için daha yüksek doğruluk derecesi elde etmek sağlar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6234a13d07ac024849d1c890d82ef03e19c11af4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012007"
---
# <a name="patterns-improve-prediction-accuracy"></a>Desenlerini tahmin doğruluğunu artırmak
Desenler, çeşitli konuşma çok benzer olduğunda doğruluğunu artırmak için tasarlanmıştır.  Bir desen çok daha fazla konuşma sağlamadan bir amaç için daha yüksek doğruluk derecesi elde etmek sağlar. 

## <a name="patterns-solve-low-intent-confidence"></a>Düşük hedefi güvenle desenleri çözün
Kuruluş şeması ile ilgili olarak çalışan bir rapor bir insan kaynakları uygulamasında göz önünde bulundurun. Bir çalışanın adı ve ilişki LUIS çalışanlar ilgili döndürür. Tom, çalışanın sahip bir yönetici adı Alice ve adlı Astları ekibi dikkate alın: Michael Rebecca ve Carl.

![Kuruluş Şeması görüntüsü](./media/luis-concept-patterns/org-chart.png)

|Konuşmalar|Tahmin hedefi|Intent puanı|
|--|--|--|
|Tom'ın alt kimdir?|GetOrgChart|.30|
|Tom alt kimdir?|GetOrgChart|.30|

LUIS, bir uygulamanın farklı uzunluktaki cümle, farklı sözcük sırasını ve hatta farklı sözcük (eş anlamlılar "alt", "manage", "rapor") ile 10 ile 20 konuşma arasında varsa, düşük güvenilirlik puanı döndürebilir. LUSıS 'in sözcük sırasının önemini anlamalarına yardımcı olmak için bir düzen oluşturun. 

Desen aşağıdaki durumlarda çözer: 

* Amaç puanı düşük
* Doğru amaç, en üst puan değildir ancak en üst puana çok yakın değildir. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Desenler hedefi garantisi değildir.
Desenlerini tahmin teknolojilerinin bir karışımını kullanın. Bir desende bir amaç için bir şablon utterance ayarı hedefi tahmin garantisi değil ancak güçlü bir sinyaldir. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Desenler makine tarafından öğrenilen varlık algılamayı iyileştirmez

Bir model öncelikle amaç ve rollerin tahminine yardımcı olmak için tasarlanmıştır. Model. herhangi bir varlık, serbest biçimli varlıkları ayıklamak için kullanılır. Desenler varlıkları kullanırken, bir desen makine tarafından öğrenilen bir varlığı algılamaya yardımcı olmaz.  

Birden çok kuralı tek bir düzende daraltarak, geliştirilmiş varlık tahminini görmeyi beklememeniz gerekmez. Basit varlıkların tetiklenmesi için, bu tür varlıkları eklemeniz veya liste varlıklarını kullanmanız gerekir, ancak sizin düzeniniz tetikleymeyecektir.

## <a name="patterns-use-entity-roles"></a>Varlık rolleri desenleri kullanın
İki veya daha fazla varlık desenindeki bağlamsal ilişkiliyse, varlık desenleri kullanın [rolleri](luis-concept-roles.md) varlıklarla ilgili bağlamsal bilgi ayıklamak için.  

## <a name="prediction-scores-with-and-without-patterns"></a>Desenler olmadan ve tahmin puanları
Yeterli örnek konuşma göz önünde bulundurulduğunda, LUIS tahmin olasılık desensiz imkanımız olacaktır. Desen çok konuşma sağlamaya gerek kalmadan güvenilirlik puanı artırın.  

## <a name="pattern-matching"></a>Desen eşleştirme
Bir düzeni desen içinde varlıkları ilk algılama ve ardından bir kelimelerin rest ve sözcük sırasını deseninin doğrulama göre eşleştirilir. Varlıklar, deseni eşleştirmek için bir desen için gereklidir. Desen karakter düzeyinde değil belirteci düzeyinde uygulanır. 

## <a name="pattern-only-apps"></a>Yalnızca örüntü uygulamalar
Her bir amaç için bir desen olduğu sürece, örnekleri olmayan amaçlar içeren bir uygulama oluşturabilirsiniz. Tek bir uygulama için, bu, örnek bir varlık gerektirdiğinden, bu model makine tarafından öğrenilen varlıklar içermemelidir. 

## <a name="best-practices"></a>En iyi uygulamalar
Bilgi [en iyi uygulamalar](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Desen sözdizimi

[Model sözdizimi başvurusundan](reference-pattern-syntax.md)bir model sözdizimi öğrenin. 

## <a name="next-steps"></a>Sonraki adımlar

Desenler hakkında daha fazla bilgi edinin:

* [Desenler ekleme](luis-how-to-model-intent-pattern.md)
* [Nasıl eklenir. herhangi bir varlık](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Desenler sözdizimi](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Bu öğreticide desenlerini uygulama hakkında bilgi edinin](luis-tutorial-pattern.md)
