---
title: Etkin öğrenme önerileri-Soru-Cevap Oluşturma
description: Etkin öğrenme önerileri, sorularınızı ve yanıt çiftiyle Kullanıcı Gönderimlerini temel alan alternatif sorular önererek bilgi Bankalarınızın kalitesini iyileştirmenize olanak tanır.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353247"
---
# <a name="active-learning-suggestions"></a>Etkin öğrenme önerileri

_Etkin öğrenme önerileri_ özelliği, sorularınızı ve yanıt çiftiyle Kullanıcı Gönderimlerini temel alan alternatif sorular önererek bilgi Bankalarınızın kalitesini iyileştirmenize olanak tanır. Bu önerileri, mevcut sorulara ekleyerek veya reddetmek için gözden geçirin.

Bilgi tabanınız otomatik olarak değişmez. Değişikliklerin etkili olabilmesi için önerileri kabul etmelisiniz. Bu öneriler, sorular ekler ancak mevcut soruları değiştirmez veya kaldırmaz.

## <a name="what-is-active-learning"></a>Etkin öğrenimi nedir?

Soru-Cevap Oluşturma örtük ve açık geri bildirimlerle yeni soru çeşitlemeleri öğreniyor.

* [Örtük geri bildirim](#how-qna-makers-implicit-feedback-works) : derecelendiricisini, bir Kullanıcı sorusunun çok yakın puanlar içeren birden fazla yanıtı olduğunda ve bunu geri bildirim olarak düşündüğünde anlamıştır. Bunun gerçekleşmesi için herhangi bir şey yapmanız gerekmez.
* [Açık geri bildirim](#how-you-give-explicit-feedback-with-the-train-api) – bilgi bankasından en az çeşitlere sahip birden çok yanıt döndürülürse, istemci uygulama kullanıcıya doğru soru olduğunu sorar. Kullanıcının açık geri bildirimi, [eğitme API](../How-to/improve-knowledge-base.md#train-api)'siyle soru-cevap oluşturma gönderilir.

Her iki yöntem de, kümelenmiş benzer sorgularla birlikte derecelendiricisini sağlar.

## <a name="how-active-learning-works"></a>Etkin öğrenme nasıl kullanılır?

Etkin öğrenme, Soru-Cevap Oluşturma tarafından döndürülen en çok birkaç yanıtın puanlarını temel alarak tetiklenir. Sorguyla eşleşen QnA çiftleri arasındaki puan farklılıkları küçük bir aralıkta yer alıyorsa, sorgu olası her bir QnA çiftinin her biri için olası bir öneri (alternatif bir soru olarak) olarak kabul edilir. Belirli bir QnA çifti için önerilen soruyu kabul ettikten sonra, diğer çiftler için reddedilir. Önerileri kabul ettikten sonra, kaydetmeyi ve eğiteyi unutmamanız gerekir.

Etkin öğrenme, uç noktaların makul bir miktar ve çeşitli kullanım sorguları elde ettiği durumlarda olası en iyi önerileri sağlar. 5 veya daha fazla benzer sorgu kümelenirken, her 30 dakikada bir Soru-Cevap Oluşturma, kabul etmek veya reddetmek için Bilgi Bankası tasarımcısına yönelik kullanıcı tabanlı soruları önerir. Tüm öneriler benzerlik ile birlikte kümelenir ve diğer sorular için en önemli öneriler, son kullanıcılara göre belirli sorguların sıklığına göre görüntülenir.

Soru-Cevap Oluşturma portalında sorular Önerildikten sonra, bu önerileri gözden geçirmeniz ve kabul etmeniz veya reddetmeniz gerekir. Önerileri yönetmek için bir API yok.

## <a name="turn-on-active-learning"></a>Etkin öğrenmeyi açma

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Varsayılan olarak, etkin öğrenme **kapalıdır**.
Etkin öğrenimi kullanmak için:
* Soru-Cevap Oluşturma bilgi tabanınız için alternatif sorular toplayacak şekilde, [etkin öğrenmeyi açmanız](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) gerekir.
* Önerilen alternatif soruları görmek için düzenleme sayfasında [Görünüm seçeneklerini kullanın](../How-To/improve-knowledge-base.md#view-suggested-questions) .

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Varsayılan olarak, etkin öğrenme Soru-Cevap Oluşturma **yönetilmektedir (** Önizleme). Önerilen alternatif soruları görmek için düzenleme sayfasında [Görünüm seçeneklerini kullanın](../How-To/improve-knowledge-base.md#view-suggested-questions) .

---

## <a name="how-qna-makers-implicit-feedback-works"></a>Soru-Cevap Oluşturma örtük geri bildirimi nasıl kullanılır?

Soru-Cevap Oluşturma örtük geri bildirimleri, puan yakınlığını tespit etmek için bir algoritma kullanır ve ardından etkin öğrenme önerileri sağlar. Yakınlık belirleme algoritması basit bir hesaplama değil. Aşağıdaki örnekteki aralıklar düzeltilmez, ancak algoritmanın yalnızca etkisini anlamak için kılavuz olarak kullanılmalıdır.

Bir sorunun puanı %80 gibi oldukça emin olduğunda, etkin öğrenme için kabul edilen puanlar aralığı, yaklaşık %10 ' da geniştir. Güven puanı azaldıkça %40, puanlar aralığı da yaklaşık olarak %4 içinde azalır.

Aşağıdaki JSON yanıtında bir sorgudan Soru-Cevap Oluşturma generateAnswer, A, B ve C puanları yakınlıyordu ve öneri olarak kabul edilir.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

Soru-Cevap Oluşturma en iyi yanıt olduğunu bilmez. En iyi yanıtı seçmek ve yeniden eğitme için Soru-Cevap Oluşturma portalın öneriler listesini kullanın.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Tren API 'SI ile açık geri bildirim verme

Soru-Cevap Oluşturma cevapın en iyi yanıtı olduğu hakkında açık geri bildirimde bulunmak gerekir. En iyi yanıt nasıl belirlenir ve şunlar olabilir:

* Kullanıcı geri bildirimi, yanıtlardan birini seçme.
* Kabul edilebilir puan aralığını belirleme gibi iş mantığı.
* Hem Kullanıcı geri bildirimi hem de iş mantığı birleşimi.

Kullanıcı onu seçtikten sonra, Soru-Cevap Oluşturma doğru yanıtı göndermek için [eğitme API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) 'sini kullanın.

## <a name="next-step"></a>Sonraki adım

> [!div class="nextstepaction"]
> [Bilgi Bankası 'nı sorgulama](query-knowledge-base.md)