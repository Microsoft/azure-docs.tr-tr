---
title: include dosyası
description: include dosyası
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648303"
---
İstemci uygulamanın, uygulamanın anlamlı veya uygulama için uygun olup olmadığını bilmeleri gerekir. Oluşturma işleminin bir parçası olarak, istemci uygulama tarafından yanıtlanamayan bir şekilde, her uygulamaya **none** amacı eklenir.

LUSıS, utterance için **hiçbiri** amacını döndürürse, istemci uygulamanız kullanıcının konuşmayı sonlandırmasını isteyip istemediğini veya konuşmaya devam etmek için daha fazla yol vermesini ister. 

**Hiçbiri** hedefini boş bırakırsanız, konu etki alanı dışında tahmin edilecek bir söylenişi, mevcut konu etki alanı amaçlarınında tahmin edilir. Sonuç olarak, bir sohbet bot gibi istemci uygulamasının yanlış bir tahmine göre hatalı işlemler gerçekleştirmesi gerekir. 

1. Sol panelden **Intents** (Amaçlar) öğesini seçin.

1. **None** (Yok) amacını seçin. Kullanıcının girebileceği ancak uygulamanızla ilgili olmayan üç konuşma girin:

    |`None` örnek söylenme|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
