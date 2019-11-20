---
title: CalcHistogram yöntemi-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Bir kağıt varlıkları kümesinin öznitelik değerlerinin dağılımını hesaplamak için CalcHistogram yöntemini kullanın.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705076"
---
# <a name="calchistogram-method"></a>CalcHistogram yöntemi

**Calchistogram** REST API, bir dizi bir varlık için öznitelik değerlerinin dağıtımını hesaplamak üzere kullanılır.          


**REST uç noktası:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>İstek Parametreleri

Ad  |Value | Gerekli mi?  |Açıklama
-----------|----------|--------|----------
**ifadeyi**    |Metin dizesi | Evet  |Histogramlarını hesaplanacak varlıkları belirten bir sorgu ifadesi.
**modelinizi** |Metin dizesi | Hayır |Sorgulamak istediğiniz modelin adını seçin.  Şu anda, varsayılan değer *en*sonuncusudur.
**özelliklerine** | Metin dizesi | Hayır<br>varsayılanını | Yanıta dahil olan öznitelik değerlerini belirten, virgülle ayrılmış bir liste. Öznitelik adları büyük/küçük harfe duyarlıdır.
**count** |Number | Hayır<br>Varsayılan: 10 |Döndürülecek sonuç sayısı.
**konumu**  |Number | Hayır<br>Varsayılan: 0 |Döndürülecek ilk sonucun dizini.
**aş**  |Number | Hayır<br>Varsayılan: 1000 |Milisaniye cinsinden zaman aşımı. Zaman aşımı sona erdiğinde yalnızca yorumlamalar döndürülüyor.

## <a name="response-json"></a>Yanıt (JSON)

Ad | Açıklama
--------|---------
**ifadeyi**  |İstekten expr parametresi.
**num_entities** | Eşleşen varlıkların toplam sayısı.
**histogramlar** |  İstekte belirtilen her öznitelik için bir çubuk histooluşan dizi.
**çubuk grafikler [x]. öznitelik** | Histogram 'ın hesaplandığı özniteliğin adı.
**çubuk grafikler [x]. distinct_values** | Bu öznitelik için eşleşen varlıklar arasındaki farklı değer sayısı.
**çubuk grafikler [x]. total_count** | Bu öznitelik için eşleşen varlıklar arasındaki toplam değer örneği sayısı.
**çubuk grafikler [x]. histogram** | Bu öznitelik için histogram verileri.
**çubuk grafikler [x]. histogram [y]. değer** |  Öznitelik için bir değer.
**çubuk grafikler [x]. histogram [y]. logprob**  |Bu öznitelik değeri ile eşleşen varlıkların toplam doğal günlük olasılığı.
**çubuk grafikler [x]. histogram [y]. Count**  |Bu öznitelik değerine sahip eşleşen varlık sayısı.
**kesilmiş** | İstek zaman aşımına uğradı doğru.


#### <a name="example"></a>Örnek:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Bu örnekte, 2010 tarihinden itibaren belirli bir yazar için yıla göre yayın sayısı için bir histogram oluşturmak amacıyla, ilk olarak sorgu dizesi ile **yorumlanan** API 'yi sorgu dizesiyle oluşturma *2012*:

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Yorumlama API 'sinden döndürülen ilk yorumdaki ifade *ve (bileşik (aa. AuN = = ' JAIME Teevan '), Y > 2012)* .
<br>Bu ifade değeri daha sonra **calchistogram** API 'sine geçirilir. *Attributes = Y, F. FN* parametresi, kağıt sayısı dağılımlarının yıl ve çalışma alanına göre olması gerektiğini belirtir, örneğin:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Bu isteğin yanıtı öncelikle sorgu ifadesiyle eşleşen 37 kağıtların olduğunu gösterir.  *Year* özniteliği için, sorguda belirtilen 2012 (örn. 2013, 2014 ve 2015) sonrasında her yıl için 3 farklı değer vardır.  3 farklı değer üzerinden toplam kağıt sayısı 37 ' dir.  Histogram, her *yıl*için değeri, toplam doğal günlük olasılığını ve eşleşen varlıkların sayısını gösterir.     

*Çalışma alanının* histogramı, 34 farklı çalışma alanı olduğunu gösterir. Bir kağıt, çok sayıda çalışma alanıyla ilişkilendirilebileceğinden, toplam sayı (53) eşleşen varlıkların sayısından daha büyük olabilir.  Farklı 34 değerler olsa da, yanıt yalnızca *Count = 4* parametresi nedeniyle ilk 4 ' ü içerir.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
