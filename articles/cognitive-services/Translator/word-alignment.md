---
title: Sözcük hizalama-çevirmen
titleSuffix: Azure Cognitive Services
description: Hizalama bilgilerini almak için Çevir yöntemini kullanın ve isteğe bağlı ıncludehizalaması parametresini ekleyin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898028"
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
          "proj&quot;: &quot;0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
