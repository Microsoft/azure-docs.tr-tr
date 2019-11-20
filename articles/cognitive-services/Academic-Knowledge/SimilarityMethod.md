---
title: Benzerlik yöntemi-Akademik Bilgi API 'SI
titlesuffix: Azure Cognitive Services
description: İki dizenin akademik benzerliğini hesaplamak için benzerlik yöntemini kullanın.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704863"
---
# <a name="similarity-method"></a>Benzerlik yöntemi

**Benzerlik** REST API, iki dize arasındaki akademik benzerliği hesaplamak için kullanılır. 
<br>

**REST uç noktası:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>İstek Parametreleri

Parametre        |Veri Türü      |Gerekli | Açıklama
----------|----------|----------|------------
**S1**        |Dize   |Evet  |Karşılaştırılacak dize *
**S2**        |Dize   |Evet  |Karşılaştırılacak dize *

<sub>* Karşılaştırılacak dizeler en fazla 1 MB uzunluğunda olmalıdır.</sub>
<br>

## <a name="response"></a>Yanıt

Ad | Açıklama
--------|---------
**SimilarityScore**        |S1 ve S2 'nin kosinüs benzerliğini temsil eden bir kayan nokta değeri, 1,0 ' e yakın değerler ve daha 1,0 yakın anlamı daha küçüktür

<br>

## <a name="successerror-conditions"></a>Başarı/hata koşulları

HTTP durumu | Reason | Yanıt
-----------|----------|--------
**200**         |Başarılı | Kayan noktalı sayı
**400**         | Hatalı istek veya istek geçersiz | Hata iletisi      
**500**         |İç sunucu hatası | Hata iletisi
**Zaman aşımına uğradı**     | İstek zaman aşımına uğradı.  | Hata iletisi

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Örnek: İki kısmi Özet için benzerliği hesaplama
#### <a name="request"></a>İstek:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Bu örnekte, **benzerlik** API 'sini kullanarak iki kısmi Özet arasında benzerlik puanı oluşturacağız.
#### <a name="response"></a>Yanıt:
```
0.520
```
#### <a name="remarks"></a>Açıklamalarının
Benzerlik puanı, akademik kavramların Word ekleme yoluyla değerlendirmesiyle belirlenir. Bu örnekte 0,52, iki kısmi soyutın biraz benzer olduğu anlamına gelir.
<br>
