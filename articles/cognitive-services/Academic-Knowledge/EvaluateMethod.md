---
title: Yöntemi değerlendir-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Bir sorgu ifadesine dayalı bir akademik varlıklar kümesi döndürmek için değerlendir metodunu kullanın.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705001"
---
# <a name="evaluate-method"></a>Yöntemi değerlendir

**Değerlendirme** REST API, bir sorgu ifadesine dayalı bir akademik varlıklar kümesi döndürmek için kullanılır.
<br>

**REST uç noktası:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>İstek Parametreleri  

Ad     | Değer | Gerekli mi?  | Açıklama
-----------|-----------|---------|--------
**ifadeyi**       | Metin dizesi | Evet | Hangi varlıkların döndürülüp döndürülmeyeceğini belirten bir sorgu ifadesi.
**modelinizi**      | Metin dizesi | Hayır  | Sorgulamak istediğiniz modelin adı.  Şu anda, varsayılan değer *en*sonuncusudur.        
**özelliklerine** | Metin dizesi | Hayır<br>varsayılanını Id | Yanıta dahil olan öznitelik değerlerini belirten, virgülle ayrılmış bir liste. Öznitelik adları büyük/küçük harfe duyarlıdır.
**count**        | Number | Hayır<br>Varsayılan: 10 | Döndürülecek sonuç sayısı.
**konumu**     | Number |   Hayır<br>Varsayılan: 0    | Döndürülecek ilk sonucun dizini.
**OrderBy** |   Metin dizesi | Hayır<br>Varsayılan: prob 'yi azaltarak | Varlıkları sıralamak için kullanılan bir özniteliğin adı. İsteğe bağlı olarak, artan/azalan belirlenebilir. Biçim: *Name: ASC* veya *Name: DESC*.
  
 <br>

## <a name="response-json"></a>Yanıt (JSON)

Ad | Açıklama
-------|-----   
**ifadeyi** |  İstekten *Expr* parametresi.
**varlıklar** |  Sorgu ifadesiyle eşleşen 0 veya daha fazla varlık dizisi. Her varlık doğal bir günlük olasılık değeri ve istenen diğer özniteliklerin değerlerini içerir.
**kesilmiş** | İstek zaman aşımına uğradı doğru.

<br>

#### <a name="example"></a>Örnek:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Genellikle, bir ifade **yorumlama** yöntemine bir yanıttan elde edilir.  Ancak sorgu ifadelerini kendiniz de oluşturabilirsiniz (bkz. [sorgu Ifadesi söz dizimi](QueryExpressionSyntax.md)).  
  
*Count* ve *sapmayı* parametrelerini kullanarak, çok sayıda sonuç tek bir istek gönderilmeden elde edilebilir ve bu da çok büyük (yüksek olabilecek yavaş) bir yanıta neden olur.  Bu örnekte, istek, *API değeri olarak* **yorumlanan** API yanıtından ilk yorumu için ifadeyi kullandı. *Count = 2* parametresi, 2 varlık sonuçlarının istendiğini belirtir. Ve *öznitelikleri = ti, Y, CC, aa. AuN, AA. Auıd* parametresi, her sonuç için başlık, yıl, alıntı sayısı, yazar adı ve yazar kimliğinin istendiğini belirtir.  Öznitelikler listesi için bkz. [varlık öznitelikleri](EntityAttributes.md) .
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
