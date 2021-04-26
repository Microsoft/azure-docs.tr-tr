---
title: Metin denetleme API-Content Moderator kullanarak orta metin
titleSuffix: Azure Cognitive Services
description: Çevrimiçi konsolundaki metin denetleme API 'sini kullanarak test-sürücü metin denetimi.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002009"
---
# <a name="moderate-text-from-the-api-console"></a>API konsolundan orta metin

Metin içeriğini küfür için taramak ve özel ve paylaşılan listelerle karşılaştırmak için Azure Content Moderator 'de [metin denetleme API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 'sini kullanın.

## <a name="get-your-api-key"></a>API anahtarınızı alın

Çevrimiçi konsolda API 'YI test etmeden önce, abonelik anahtarınız olmalıdır. Bu, **Ayarlar** sekmesinde, **OCP-apim-Subscription-Key** kutusunda bulunur. Daha fazla bilgi için bkz. [Genel Bakış](overview.md).

## <a name="navigate-to-the-api-reference"></a>API başvurusuna git

[Metin denetleme API başvurusuna](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)gidin. 

  **Metin ekranı** sayfası açılır.

## <a name="open-the-api-console"></a>API konsolunu açın

**Open API test konsolu** için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

  ![Metin-ekran sayfa bölgesi seçimi](images/test-drive-region.png)

  **Metin ekranı** API konsolu açılır.

## <a name="select-the-inputs"></a>Girişleri seçin

### <a name="parameters"></a>Parametreler

Metin ekranınızda kullanmak istediğiniz sorgu parametrelerini seçin. Bu örnek için **dil** için varsayılan değeri kullanın. Ayrıca işlem, yürütmenin bir parçası olarak olası dili otomatik olarak algılayacağından boş bırakabilirsiniz.

> [!NOTE]
> **Dil** parametresi için, `eng` makine yardımlı **Sınıflandırma** yanıtını (Önizleme özelliği) görmek üzere boş bırakın veya boş bırakın. **Bu özellik yalnızca İngilizce 'yi destekler**.
>
> **Küfür terimleri** algılama için, bu makalede listelenen desteklenen dillerin [ISO 639-3 kodunu](http://www-01.sil.org/iso639-3/codes.asp) kullanın veya boş bırakın.

**Otomatik Düzelt**, **PII** ve **sınıflandır (Önizleme)** için **doğru** öğesini seçin. **ListId** alanını boş bırakın.

  ![Metin-ekran konsolu sorgu parametreleri](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>İçerik türü

**Içerik türü** için, ekranın istediğiniz içerik türünü seçin. Bu örnek için varsayılan **metin/düz** içerik türünü kullanın. **OCP-apim-Subscription-Key** kutusuna abonelik anahtarınızı girin.

### <a name="sample-text-to-scan"></a>Taranacak örnek metin

**İstek gövdesi** kutusuna bir metin girin. Aşağıdaki örnek, metinde isteyerek bir yazım hatası gösterir.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Yanıtı çözümle

Aşağıdaki yanıt, API 'deki çeşitli öngörüleri gösterir. Olası bir küfür, kişisel veriler, sınıflandırma (Önizleme) ve otomatik düzeltilmiş sürüm içerir.

> [!NOTE]
> Makine destekli ' sınıflandırma ' özelliği önizlemededir ve yalnızca Ingilizce 'yi destekler.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

JSON yanıtındaki tüm bölümlerin ayrıntılı bir açıklaması için, [metin denetleme](text-moderation-api.md) kavramsal kılavuzuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

Kodunuzda REST API kullanın veya uygulamanızla tümleştirilecek [.NET SDK hızlı](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) başlangıcını izleyin.