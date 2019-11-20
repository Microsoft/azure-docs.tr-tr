---
title: Batch test TSV biçimi-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Batch testi için TSV biçimini anlayın
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507830"
---
# <a name="batch-testing-tsv-format"></a>Toplu işlem testi TSV biçimi

Toplu iş testi, [kaynak koddan](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) veya [indirilebilen bir yürütülebilir dosya](https://aka.ms/qna_btzip)olarak yüklenebilir. Batch testini çalıştırmak için komutun biçimi:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Larına|Beklenen değer|
|--|--|
|1|[TSV giriş alanlarıyla](#tsv-input-fields) biçimlendirilen TSV dosyasının adı|
|2|Soru-Cevap Oluşturma portalının Yayımla sayfasında ana BILGISAYARıNıZ ile uç nokta için URI.|
|3|UÇ nokta anahtarı, Soru-Cevap Oluşturma portalının Yayımla sayfasında bulunur.|
|4|sonuçlar için Batch testi tarafından oluşturulan TSV dosyasının adı.|

Toplu test için TSV biçimini anlamak ve uygulamak üzere aşağıdaki bilgileri kullanın. 

## <a name="tsv-input-fields"></a>TSV giriş alanları

|TSV giriş dosyası alanları|Notlar|
|--|--|
|KBID|Yayımlama sayfasında KB KIMLIĞINIZ bulundu.|
|Soru|Kullanıcının girebileceği soru.|
|Meta veri etiketleri|Seçim|
|Üst parametre|Seçim| 
|Beklenen yanıt KIMLIĞI|Seçim|

![Toplu iş testi için TSV dosyası için giriş biçimi.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV çıkış alanları 

|TSV çıkış dosyası parametreleri|Notlar|
|--|--|
|KBID|Yayımlama sayfasında KB KIMLIĞINIZ bulundu.|
|Soru|Giriş dosyasından girilen soru.|
|Yanıt|Bilgi tabanınızdan en iyi yanıt.|
|Yanıt KIMLIĞI|Yanıt KIMLIĞI|
|Puan|Yanıt için tahmin puanı. |
|Meta veri etiketleri|döndürülen Yanıtla ilişkili|
|Beklenen yanıt KIMLIĞI|isteğe bağlı (yalnızca beklenen yanıt KIMLIĞI verildiğinde)|
|Yargı|isteğe bağlı, değerler: doğru veya hatalı (yalnızca beklenen yanıt verildiğinde)|
