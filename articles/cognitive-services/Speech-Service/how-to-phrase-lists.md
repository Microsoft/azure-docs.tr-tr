---
title: Tümcecik listeleri-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşmayı metne dönüştürme sonuçlarını geliştirmek için `PhraseListGrammar` nesnesini kullanarak bir tümcecik listesi ile konuşma hizmetlerini sağlamayı öğrenin.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 61d3e4d2de6b8707ee7433815f8002e5d5e3e3d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464535"
---
# <a name="phrase-lists-for-speech-to-text"></a>Konuşmayı metne yönelik tümcecik listeleri

Bir tümcecik listesi ile konuşma hizmetleri sunarak, konuşma tanımanın doğruluğunu artırabilirsiniz. Tümcecik listeleri, bir kişinin adı veya belirli bir konum gibi, ses verilerinde bilinen tümcecikleri belirlemek için kullanılır.

Örnek olarak, "taşı" komutuna ve söylenen "Ward" bir hedefe sahipseniz, "Ward 'e taşı" girişini ekleyebilirsiniz. Bir tümcecik eklemek, ses "ilerlemek için taşı" yerine "ilerlemek" yerine tanınabilmesi olasılığını artırır.

Tek sözcükler veya bütün ifadeler, bir tümcecik listesine eklenebilir. Tanıma sırasında, sese tam eşleşme varsa, tümcecik listesindeki bir giriş kullanılır. Önceki örnekte oluşturma, tümcecik listesi "Ward 'a taşı" Ifadesini içeriyorsa ve ses yakalanan seslerin her ikisi de "ilerlemek için" ve "Ward 'a taşı" gibi bir daha tanınırsa, tanınma sonucu "daha yavaş geçiş" olarak tanınacaktır.

>[!Note]
> Şu anda, tümcecik listeleri yalnızca konuşmayı metin için destekler.

## <a name="how-to-use-phrase-lists"></a>Tümcecik listelerini kullanma

Aşağıdaki örneklerde `PhraseListGrammar` nesnesini kullanarak bir tümcecik listesinin nasıl oluşturulacağı gösterilmektedir.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Konuşma hizmetinin konuşmayı eşleştirmek için kullanacağı en fazla tümcecik listesi sayısı 1024 tümceciklerdir.

Ayrıca, Clear () öğesini çağırarak `PhraseListGrammar` ilişkili tümcecikleri temizleyebilirsiniz.

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Bir `PhraseListGrammar` nesnesindeki değişiklikler bir sonraki tanıma göre veya konuşma hizmetlerine yeniden bağlanma sonrasında etkili olur.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma SDK başvuru belgeleri](speech-sdk.md)
