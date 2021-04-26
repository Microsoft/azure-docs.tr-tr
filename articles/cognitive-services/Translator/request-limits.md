---
title: İstek limitleri-çevirmen
titleSuffix: Azure Cognitive Services
description: Bu makalede, çevirmen için istek sınırları listelenir. Ücretler, istek başına 5.000 karakter sınırlaması olan istek sıklığıyla değil, karakter sayısına göre ücretlendirilir. Karakter sınırları, abonelik tabanlıdır ve F0 ile 2.000.000 karakter arasında sınırlanır.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727951"
---
# <a name="request-limits-for-translator"></a>Çevirmen için istek sınırları

Bu makale, çevirmen çevirisi, alfabelik, cümle uzunluğu algılama, dil algılama ve alternatif çeviriler için azaltma sınırları sağlar.

## <a name="character-and-array-limits-per-request"></a>İstek başına karakter ve dizi sınırları

Her çeviri isteği, çeviri yaptığınız tüm hedef dillerde 10.000 karakterle sınırlıdır. Örneğin, üç farklı dile çevrilecek 3.000 karakterlik bir çeviri isteği gönderilmesi, istek sınırını karşılayan 3000x3 = 9.000 karakterlik bir istek boyutuyla sonuçlanır. İstek sayısına göre değil, karakter başına ücretlendirilirsiniz. Daha kısa istekler göndermeniz önerilir.

Aşağıdaki tabloda, çeviricisinin her bir işlemi için dizi öğesi ve karakter sınırları listelenmektedir.

| İşlem | Dizi öğesinin en büyük boyutu |    En fazla dizi öğesi sayısı |    En fazla Istek boyutu (karakter) |
|:----|:----|:----|:----|
| Çeviri | 10,000| 100| 10,000 |
| Karakter Dönüştürme | 5.000| 10| 5.000 |
| Algılama | 50,000 |100 |50,000 |
| BreakSentence | 50,000| 100 |50,000 |
| Sözlük Arama| 100 |10| 1.000 |
| Sözlük Örnekleri | "metin için 100 ve çeviri için 100 (200 toplam)| 10|2.000 |

## <a name="character-limits-per-hour"></a>Saat başına karakter sınırları

Saat başına karakter sınırınız, çevirmen abonelik katmanınızı temel alır.

Saatlik kota, saat boyunca eşit olarak tüketilmelidir. Örneğin, saat başına 2.000.000 karakterlik F0 katman sınırında, karakterler dakikada kabaca 33.300 karakterden daha hızlı bir şekilde tüketilmelidir (2.000.000 karakter, 60 dakikaya bölünür).

Bu sınırlara ulaşan veya daha fazla bilgi alırsanız veya kısa bir süre içinde kotanın bir kısmını çok büyük bir süre gönderirseniz, muhtemelen kota dışı bir yanıt alacaksınız. Eşzamanlı isteklerde sınır yoktur.

| Katman | Karakter sınırı |
|------|-----------------|
| F0 | 2.000.000 karakter/saat |
| S1 | 40.000.000 karakter/saat |
| S2/C2 | 40.000.000 karakter/saat |
| S3/C3 | 120.000.000 karakter/saat |
| S4/C4 | 200.000.000 karakter/saat |

[Çok hizmet abonelikleri](./reference/v3-0-reference.md#authentication) limitleri S1 katmanıyla aynı.

Bu sınırlar, Microsoft 'un standart çeviri modelleriyle kısıtlıdır. Özel çevirici kullanan özel çeviri modelleri, her model için saniyede 1.800 karakter ile sınırlıdır.

## <a name="latency"></a>Gecikme süresi

Özel modeller kullanılırken, çevirmen, Standart modeller ve 120 saniye kullanarak en fazla 15 saniye gecikme süresine sahiptir. Genellikle, *100 karakter içindeki metin* yanıtları 150 milisaniyelik ile 300 milisaniyeye döndürülür. Özel çevirmen modelleri sürekli istek hızında benzer gecikme özelliklerine sahiptir ve istek hızlarınız aralıklı olduğunda daha yüksek bir gecikme süresine sahip olabilir. Yanıt süreleri, istek ve dil çiftinin boyutuna bağlı olarak değişir. Bu zaman diliminde bir çeviri veya [hata yanıtı](./reference/v3-0-reference.md#errors) almazsanız, kodunuzu, ağ bağlantınızı kontrol edin ve yeniden deneyin.

## <a name="sentence-length-limits"></a>Tümce uzunluğu sınırları

[Breakcümlesi](./reference/v3-0-break-sentence.md) işlevini kullanırken, cümle uzunluğu 275 karakterle sınırlıdır. Bu diller için özel durumlar mevcuttur:

| Dil | Kod | Karakter sınırı |
|----------|------|-----------------|
| Çince | zh | 166 |
| Almanca | seçimini | 800 |
| İtalyanca | içerdiği | 800 |
| Japonca | Sofya | 166 |
| Portekizce | pt | 800 |
| Spanish | es | 800 |
| Tayca | 11 | 180 |

> [!NOTE]
> Bu sınır, çeviriler için geçerlidir.

## <a name="next-steps"></a>Sonraki adımlar

* [Fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Bölgesel kullanılabilirlik](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator başvurusu](./reference/v3-0-reference.md)