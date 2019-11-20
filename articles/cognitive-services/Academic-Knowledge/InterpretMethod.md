---
title: Metodu yorumlama-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Akademik grafik verilerine ve Microsoft bilişsel hizmetler 'deki akademik dilbilgisine dayalı Kullanıcı sorgu dizeleri biçimli yorumlamalar döndürmek için yorumlama yöntemini kullanın.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706667"
---
# <a name="interpret-method"></a>Yorumlama yöntemi

**Yorumlama** REST API, bir son kullanıcı sorgu dizesi (yani, uygulamanızın bir kullanıcısı tarafından girilen bir sorgu) alır ve akademik grafik verilerine ve akademik dilbilgisine dayalı olarak Kullanıcı amacı biçimli yorumlamalar döndürür.

Etkileşimli bir deneyim sağlamak için, Kullanıcı tarafından girilen her karakterden sonra bu yöntemi defalarca çağırabilirsiniz. Bu durumda, otomatik Tamam önerilerini etkinleştirmek için **tam** parametreyi 1 olarak ayarlamanız gerekir. Uygulamanız için otomatik tamamlama gerekmiyorsa, **tam** parametreyi 0 olarak ayarlamanız gerekir.

**REST uç noktası:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>İstek Parametreleri

Ad     | Value | Gerekli mi?  | Açıklama
---------|---------|---------|---------
**query**    | Metin dizesi | Evet | Kullanıcı tarafından girilen sorgu.  Eğer tamamla 1 olarak ayarlanırsa sorgu otomatik tamamlama önerileri oluşturmak için bir ön ek olarak yorumlanır.        
**modelinizi**    | Metin dizesi | Hayır  | Sorgulamak istediğiniz modelin adı.  Şu anda, varsayılan değer *en*sonuncusudur.        
**Tamam** | 0 veya 1 | Hayır<br>Varsayılan: 0  | 1, dilbilgisi ve grafik verilerine göre otomatik tamamlama önerilerinin oluşturulduğu anlamına gelir.         
**count**    | Number | Hayır<br>Varsayılan: 10 | Döndürülecek en fazla yorumlamalar sayısı.         
**konumu**   | Number | Hayır<br>Varsayılan: 0  | Döndürülecek ilk yorum dizini. Örneğin, *Count = 2 & kayması = 0* , yorumlamalar 0 ve 1 döndürür. *Sayı = 2 & Konum = 2* , yorumlamalar 2 ve 3 ' ü döndürür.       
**aş**  | Number | Hayır<br>Varsayılan: 1000 | Milisaniye cinsinden zaman aşımı. Zaman aşımı sona erdiğinde yalnızca yorumlamalar döndürülüyor.

<br>
  
## <a name="response-json"></a>Yanıt (JSON)

Ad     | Açıklama
---------|---------
**query** |İstekten *sorgu* parametresi.
**Yorumlar** |Kullanıcı girişini dilbilgisinde eşleştirmek için bir 0 veya daha fazla farklı yol dizisi.
**yorumlamalar [x]. logprob**  |Yorum için göreli doğal günlük olma olasılığı. Daha büyük değerler daha olasıdır.
**yorumlamalar [x]. Parse**  |Sorgunun her bir bölümünün nasıl yorumlandığını gösteren bir XML dizesi.
**yorumlamalar [x]. kurallar**  |Dilbilgisi sırasında çağrılan dilbilgisinde tanımlanan 1 veya daha fazla kural dizisi. Akademik Bilgi API 'SI için her zaman 1 kural olacaktır.
**yorumlamalar [x]. kurallar [y]. Name**  |Kuralın adı.
**yorumlamalar [x]. kurallar [y]. çıktı**  |Kuralın çıkışı.
**yorumlamalar [x]. Rules [y]. Output. Type** |Kuralın çıktısının veri türü.  Akademik Bilgi API 'SI için, bu her zaman "sorgu" olacaktır.
**yorumlamalar [x]. Rules [y]. Output. Value**  |Kuralın çıktısı. Akademik Bilgi API 'SI için, bu, değerlendir ve calchistogram yöntemlerine geçirilebileceğiniz bir sorgu ifade dizesidir.
**kesilmiş** | İstek zaman aşımına uğradı doğru.

<br>

#### <a name="example"></a>Örnek:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Aşağıdaki yanıt, en üst iki (parametre *sayısı = 2*), caime göre kısmi Kullanıcı girişi kağıtlarını belirten büyük olasılıkla JAIME'nin yanı sıra JAIME *teeva* ve *kağıtları*  Hizmet, istek *tamamlandı = 1*olarak yalnızca yazar *JAIME* için tam eşleşmeleri göz önünde bulundurmaları yerine, sorgu tamamlamasına yönelik olarak üretilmiş. , Ayrıştırmaya gösterildiği gibi, ktıl 'nin *yeşil* değerinin anlamlu *yeşili*ile eşleştiğini unutmayın.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Bir yorum için varlık sonuçlarını almak için, **yorumlanan** API 'den *output. Value* kullanın ve bunu *Expr* parametresi aracılığıyla **değerlendir** API 'sine geçirin. Bu örnekte, ilk yorumlamanın sorgusu: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
