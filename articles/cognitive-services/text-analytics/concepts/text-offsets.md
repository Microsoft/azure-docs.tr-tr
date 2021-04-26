---
title: Metin Analizi API'si metin uzaklıkları
titleSuffix: Azure Cognitive Services
description: Çok dilli ve Emoji kodlamalarının neden olduğu uzaklıklar hakkında bilgi edinin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932365"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Metin Analizi API'si çıkışındaki metin uzaklıkları

Çok dilli ve Emoji desteği, grapheme olarak adlandırılan tek bir görüntülenen karakteri temsil etmek için birden fazla [kod noktası](https://wikipedia.org/wiki/Code_point) kullanan Unicode kodlamalara yol açmıştır. Örneğin, 🌷 gibi emojıs, 👍 şekli kaplama tonu gibi görsel özniteliklerde ek karakterlerle oluşturmak için birkaç karakteri kullanabilir. Benzer şekilde, Hintçe kelime `अनुच्छेद` beş harf ve üç Birleşik işaret olarak kodlanır.

Olası çok dilli ve Emoji kodlamaları farklı uzunluklarıyla, Metin Analizi API'si yanıttaki uzaklıkları döndürebilir.

## <a name="offsets-in-the-api-response"></a>API yanıtında uzaklıklar. 

Her kaydırın, [adlandırılmış varlık tanıma](../how-tos/text-analytics-how-to-entity-linking.md) veya [yaklaşım Analizi](../how-tos/text-analytics-how-to-sentiment-analysis.md)gibi API yanıtı döndürüldüğünde, aşağıdakileri unutmayın:

* Yanıttaki öğeler, çağrılan uç noktaya özgü olabilir. 
* HTTP POST/GET yükleri [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)olarak kodlanır, bu, istemci tarafı derleyicinizdeki veya işletim sisteminizde varsayılan karakter kodlaması olabilir veya olmayabilir.
* Uzaklıklar, karakter sayımlarını değil [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standardına göre grafem sayılarına başvurur.

## <a name="extracting-substrings-from-text-with-offsets"></a>Uzaklıkları olan metinden alt dizeleri ayıklama

Uzaklıklar, .NET [substring ()](/dotnet/api/system.string.substring) yöntemi gibi karakter tabanlı alt dize yöntemleri kullanılırken sorunlara yol açabilir. Bir sorun, bir kaydırın bir alt dize yönteminin, son yerine çok karakterli grafem kodlamasının ortasında sonlanmasına neden olabilir.

.NET ' te, dize, tek tek karakter nesneleri yerine bir dizi metin öğesi olarak çalışmanıza olanak sağlayan [StringInfo](/dotnet/api/system.globalization.stringinfo) sınıfını kullanmayı düşünün. Ayrıca, tercih ettiğiniz yazılım ortamınızda grafem Bölümlendirici kitaplıklarını da arayabilirsiniz. 

Metin Analizi API'si kolaylık sağlamak için bu metinsel öğeleri de döndürür.

## <a name="offsets-in-api-version-31-preview"></a>API sürümündeki uzaklıklar 3,1-Önizleme

API sürümü 3,1-Preview. 1 ' den başlayarak, bir fark döndüren tüm Metin Analizi API'si uç noktaları parametresini destekleyecektir `stringIndexType` . Bu parametre, `offset` `length` API çıkışındaki ve özniteliklerini istenen dize yineleme düzeniyle eşleşecek şekilde ayarlar. Şu anda üç türü destekliyoruz:

1. `textElement_v8` (varsayılan): [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standardına göre tanımlanan şekilde, graphemes üzerinde dolaşır
2. `unicodeCodePoint`: Python 3 için varsayılan düzen olan [Unicode kod noktalarını](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564)yineleme
3. `utf16CodeUnit`: [UTF-16 kod birimleri](https://unicode.org/faq/utf_bom.html#UTF16)üzerinde yineleme yapın, JavaScript, Java ve .NET için varsayılan düzen

İstenen, `stringIndexType` tercih edilen programlama ortamıyla eşleşiyorsa, alt dize ayıklama standart alt dize veya dilim yöntemleri kullanılarak yapılabilir. 

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Analizine genel bakış](../overview.md)
* [Yaklaşım analizi](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Varlık tanıma](../how-tos/text-analytics-how-to-entity-linking.md)
* [Dil algılama](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Dil tanıma](../how-tos/text-analytics-how-to-language-detection.md)
