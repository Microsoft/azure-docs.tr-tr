---
title: Sözcük hizalama-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Hizalama bilgilerini almak için Çevir yöntemini kullanın ve isteğe bağlı ıncludehizalaması parametresini ekleyin.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837221"
---
# <a name="how-to-receive-word-alignment-information"></a>Sözcük hizalama bilgilerini alma

## <a name="receiving-word-alignment-information"></a>Sözcük hizalama bilgilerini alma
Hizalama bilgilerini almak için Çevir yöntemini kullanın ve isteğe bağlı ıncludehizalaması parametresini ekleyin.

## <a name="alignment-information-format"></a>Hizalama bilgisi biçimi
Hizalama, kaynağın her sözcüğü için aşağıdaki biçimin bir dize değeri olarak döndürülür. Her sözcük için bilgiler, Çince gibi boşlukla ayrılmış diller (betikler) gibi bir boşlukla ayrılır:

[[Sourcetextstartındex]: [Sourceıdındex] – [TgtTextStartIndex]: [TgtTextEndIndex]] *

Örnek Hizalama dizesi: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Diğer bir deyişle, iki nokta üst üste başlangıç ve bitiş dizinini ayırır, tire dilleri ayırır ve boşluklar sözcükleri ayırır. Bir sözcük, diğer dilde sıfır, bir veya birden çok sözcükten hizalanabilir ve hizalanmış sözcükler bitişik olmayabilir. Hiçbir hizalama bilgisi yoksa, hizalama öğesi boş olur. Yöntemi bu durumda bir hata döndürmez.

## <a name="restrictions"></a>Kısıtlamalar
Hizalama yalnızca bu noktada dil çiftlerinin bir alt kümesi için döndürülür:
* Ingilizce 'den başka bir dilde;
* Basitleştirilmiş Çince, geleneksel Çince ve Letonca Ingilizce dışında diğer dillerden Ingilizce 'ye
* Japonca 'dan Korece 'e veya Korece 'den Japonca 'ya kadar, tümce bir çeviri ise hizalama bilgilerini almazsınız. "Bu bir sınamadır", "Seni seviyorum" ve diğer yüksek frekanslı cümleler örneği.

## <a name="example"></a>Örnek

Örnek JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
