---
title: Translator Metin Çevirisi API'si sözlük arama yöntemi
titleSuffix: Azure Cognitive Services
description: Sözlük arama yöntemi, bir sözcük için alternatif çeviriler ve az sayıda dizi deyim sağlar.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: bd725d41f75bdfb1048b5bee7e8224679dbece4c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837264"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Metin Çevirisi API'si 3,0: sözlük arama

Bir sözcük için alternatif çeviriler ve az sayıda deyim kümesi sağlar. Her çeviri, bir konuşma parçası ve arka çeviri listesi içerir. Arka Çeviriler, bir kullanıcının bağlamdaki çeviriyi anlamasına imkan sağlar. [Sözlük örnek](./v3-0-dictionary-examples.md) işlemi, her bir çeviri çiftinin örnek kullanımlarını görmek için detaya gitmeyi sağlar.

## <a name="request-url"></a>İstek URL'si

`POST` isteği gönder:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

<table width="100%">
  <th width="20%">Sorgu parametresi</th>
  <th>Açıklama</th>
  <tr>
    <td>api sürümü</td>
    <td>*Gerekli parametre*.<br/>İstemci tarafından istenen API 'nin sürümü. Değer `3.0`olmalıdır.</td>
  </tr>
  <tr>
    <td>Kaynak</td>
    <td>*Gerekli parametre*.<br/>Giriş metninin dilini belirtir. Kaynak dili, `dictionary` kapsamında yer alan [desteklenen dillerden](./v3-0-languages.md) biri olmalıdır.</td>
  </tr>
  <tr>
    <td>-</td>
    <td>*Gerekli parametre*.<br/>Çıkış metninin dilini belirtir. Hedef dil `dictionary` kapsamında bulunan [desteklenen dillerden](./v3-0-languages.md) biri olmalıdır.</td>
  </tr>
</table>

İstek üstbilgileri şunları içerir:

<table width="100%">
  <th width="20%">Üst bilgiler</th>
  <th>Açıklama</th>
  <tr>
    <td>Kimlik doğrulama üst bilgisi</td>
    <td><em>Gerekli istek üst bilgisi</em>.<br/><a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Kimlik doğrulaması için kullanılabilen seçeneklere</a>bakın.</td>
  </tr>
  <tr>
    <td>İçerik türü</td>
    <td>*Gerekli istek üst bilgisi*.<br/>Yükün içerik türünü belirtir. Olası değerler: `application/json`.</td>
  </tr>
  <tr>
    <td>İçerik uzunluğu</td>
    <td>*Gerekli istek üst bilgisi*.<br/>İstek gövdesinin uzunluğu.</td>
  </tr>
  <tr>
    <td>X-Clienttraceıd</td>
    <td>*İsteğe bağlı*.<br/>İsteği benzersiz şekilde tanımlamak için istemci tarafından oluşturulan bir GUID. `ClientTraceId`adlı bir sorgu parametresi kullanarak, sorgu dizesinde izleme KIMLIĞINI eklerseniz bu üstbilgiyi atlayabilirsiniz.</td>
  </tr>
</table> 

## <a name="request-body"></a>İstek gövdesi

İsteğin gövdesi bir JSON dizisidir. Her dizi öğesi, arama terimini temsil eden `Text`adlı dize özelliği olan bir JSON nesnesidir.

```json
[
    {"Text":"fly"}
]
```

Aşağıdaki sınırlamalar geçerlidir:

* Dizi en fazla 10 öğe içerebilir.
* Bir dizi öğesinin metin değeri boşluk dahil 100 karakteri aşamaz.

## <a name="response-body"></a>Yanıt gövdesi

Başarılı bir yanıt, Giriş dizisindeki her bir dize için bir sonuç içeren bir JSON dizisidir. Bir sonuç nesnesi aşağıdaki özellikleri içerir:

  * `normalizedSource`: kaynak terimin normalleştirilmiş biçimini sağlayan bir dize. Örneğin, istek "JOHN" ise, normalleştirilmiş form "John" olacaktır. Bu alanın içeriği [arama örneklerine](./v3-0-dictionary-examples.md)giriş haline gelir.
    
  * `displaySource`: Son Kullanıcı görüntüsü için en uygun biçimde kaynak terim sağlayan bir dize. Örneğin, giriş "JOHN" ise, görüntüleme formu "John" adının olağan yazım görüntüsünü yansıtır. 

  * `translations`: kaynak terime ait çevirilerin bir listesi. Listenin her öğesi, aşağıdaki özelliklere sahip bir nesnedir:

    * `normalizedTarget`: hedef dilde bu terimin normalleştirilmiş biçimini sağlayan bir dize. Bu değer, [arama örneklerine](./v3-0-dictionary-examples.md)giriş olarak kullanılmalıdır.

    * `displayTarget`: hedef dilde ve Son Kullanıcı görüntüsü için en uygun bir biçimde terim sağlayan bir dize. Genellikle bu, büyük/küçük harf bakımından `normalizedTarget` farklılık gösterir. Örneğin, "Juan" gibi uygun bir ad `normalizedTarget = "juan"` ve `displayTarget = "Juan"`sahip olur.

    * `posTag`: Bu terimi bir konuşma parçası etiketiyle ilişkilendiren bir dize.

        | Etiket adı | Açıklama  |
        |----------|--------------|
        | SIF      | Sıfatlar   |
        | ADV      | Zarflar      |
        | CONJ     | Bağlaçlar |
        | DET      | Determinler  |
        | MODAL    | Eylemlerinin        |
        | ISIM     | İsimleri        |
        | HAZıRLıĞı     | Ön pozisyonlar |
        | PRON     | Zamirler     |
        | Ü     | Eylemlerinin        |
        | FARKLı    | Diğer        |

        Uygulama notunun bir parçası olarak, bu Etiketler Ingilizce tarafı etiketleme ve ardından her kaynak/hedef çifti için en sık kullanılan etikete göre belirlenir. Bu nedenle, kullanıcılar genellikle Ispanyolca bir sözcüğü Ingilizce olarak farklı bir konuşma bölümü etiketine çeviriyorsa, Etiketler hatalı olabilir (Ispanyolca sözcüğe göre).

    * `confidence`: Bu çeviri çiftinin "güvenirlik" (veya daha doğru "eğitim verilerinde olasılık") temsil eden 0,0 ve 1,0 arasında bir değer. Bir kaynak sözcük için güven puanlarının toplamı 1,0 olabilir veya bu olmayabilir. 

    * `prefixWord`: sözcüğe, çeviri ön eki olarak görüntülenecek bir dize. Şu anda bu, genlere sahip olan ve genilaçilerin bulunduğu dillerde, genlerin genden oluşan determinladır. Örneğin, "Mosca" Ispanyolca ' da bir Feminine ad olduğu için "Mosca" Ispanyolca sözcüğünün ön eki "La" dır. Bu yalnızca, kaynak üzerinde değil, çeviri üzerine bağımlıdır. Önek yoksa, boş dize olur.
    
    * `backTranslations`: hedefin "geri çevirilerinin" bir listesi. Örneğin, hedefin çevrilebileceğini kaynak sözcükler. Listenin, istenen kaynak sözcüğü içermesi garanti edilir (örneğin, aranan kaynak sözcük "uçarak" ise, "Uçmış" `backTranslations` listesinde olacaktır). Ancak, ilk konumda olması garanti edilmez ve genellikle olmayacaktır. `backTranslations` listenin her öğesi, aşağıdaki özellikler tarafından tanımlanan bir nesnedir:

        * `normalizedText`: hedefin geri çevirisi olan kaynak terimin normalleştirilmiş biçimini veren bir dize. Bu değer, [arama örneklerine](./v3-0-dictionary-examples.md)giriş olarak kullanılmalıdır.        

        * `displayText`: Son Kullanıcı görüntüsü için en uygun formda hedefin geri çevirisi olan kaynak terim veren bir dize.

        * `numExamples`: Bu çeviri çifti için kullanılabilen örneklerin sayısını temsil eden bir tamsayı. Gerçek örneklerin [arama örneklerine](./v3-0-dictionary-examples.md)ayrı bir çağrısıyla alınması gerekir. Numara genellikle bir UX içinde görüntülemeyi kolaylaştırmak için tasarlanmıştır. Örneğin, bir kullanıcı arabirimi, örnek sayısı sıfırdan büyükse geri çeviriye köprü ekleyebilir ve örnek yoksa arka çeviriyi düz metin olarak gösterir. [Arama örneklerine](./v3-0-dictionary-examples.md) yapılan bir çağrı tarafından döndürülen gerçek örnek sayısının `numExamples`' den az olabileceğini, "kötü" örnekleri kaldırmak için çalışma sırasında ek filtreleme uygulanabileceğini unutmayın.
        
        * `frequencyCount`: verilerdeki bu çeviri çiftinin sıklığını temsil eden bir tamsayı. Bu alanın ana amacı, en sık kullanılan koşulların ilk olması için arka çevirileri sıralamak amacıyla bir kullanıcı arabirimi sağlamaktır.

    > [!NOTE]
    > Aranmakta olan terim Sözlükte yoksa, yanıt 200 ' dir (Tamam), ancak `translations` listesi boş bir liste.

## <a name="examples"></a>Örnekler

Bu örnekte, alternatif çevirilerin Ingilizce dönemi `fly` Ispanyolca araması gösterilmektedir.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Yanıt gövdesi (açıklık için kısaltılmış):

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Bu örnek, aranmakta olan terim geçerli sözlük çifti için mevcut olmadığında ne olacağını gösterir.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Terim sözlükte bulunamadığı için, yanıt gövdesi boş bir `translations` listesi içerir.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
