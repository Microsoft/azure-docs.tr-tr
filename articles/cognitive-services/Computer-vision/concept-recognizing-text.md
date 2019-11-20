---
title: Yazılı, el yazısı metin tanıma-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si kullanarak görüntülerde yazdırılmış ve el yazısı metinleri tanıma ile ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e37b4c02692575f76582e8f62c3aff00b57424ed
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946297"
---
# <a name="recognize-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı ile yazılan metinleri tanıma

Görüntü İşleme görüntülerde görüntülenen yazdırılmış veya el yazısı metinleri algılayan ve ayıklayan bir dizi hizmet sağlar. Bu, notalma, tıbbi kayıtlar, güvenlik ve bankacılık gibi çeşitli senaryolarda faydalıdır. Aşağıdaki üç bölüm, her biri farklı kullanım durumları için en iyi duruma getirilmiş üç farklı metin tanıma API 'si ayrıntısıyla

## <a name="read-api"></a>API 'YI oku

Okuma API 'SI, en son tanınma modellerimizi kullanarak bir görüntüdeki metin içeriğini algılar ve tanımlanan metni makine tarafından okunabilen bir karakter akışına dönüştürür. Metin açısından ağır görüntüler (dijital olarak taranan belgeler gibi) ve çok sayıda görsel gürültü içeren görüntüler için iyileştirilmiştir. Her metin satırı için hangi tanıma modelinin kullanılacağını, hem yazdırılmış hem de el yazısı metinle birlikte destekleyeceğini belirleyen bir görüntü oluşturur. Okuma API 'SI, büyük belgeler bir sonuç döndürmek için birkaç dakika sürebileceğinden zaman uyumsuz olarak yürütülür.

Okuma işlemi, tanınan sözcüklerin orijinal satır gruplamalarını çıktıda saklar. Her satır sınırlayıcı kutu koordinatları ile gelir ve satırdaki her sözcüğün kendi koordinatları de vardır. Bir sözcük düşük güvenilirlikle tanınıyorsa, bu bilgiler de bu bilgileri verir. Daha fazla bilgi edinmek için bkz. [API başvuru belgelerine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) bakın.

> [!NOTE]
> Bu özellik yalnızca Ingilizce metin için kullanılabilir.

### <a name="image-requirements"></a>Görüntü gereksinimleri

Okuma API 'SI, aşağıdaki gereksinimleri karşılayan görüntülerle birlikte kullanılabilir:

- Resim JPEG, PNG, BMP, PDF veya TIFF biçiminde sunulmalıdır.
- Resmin boyutları 50 x 50 ve 10000 x 10000 piksel arasında olmalıdır. PDF sayfaları 17 x 17 inç veya daha küçük olmalıdır.
- Resmin dosya boyutu 20 megabayttan (MB) az olmalıdır.

### <a name="limitations"></a>Sınırlamalar

Serbest katmanlı bir abonelik kullanıyorsanız, okuma API 'SI yalnızca bir PDF veya TIFF belgesinin ilk iki sayfasını işler. Ücretli abonelikle, en fazla 200 sayfa işleyebilir. Ayrıca API 'nin sayfa başına en fazla 300 satırı algılayacağına de unutmayın.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optik karakter tanıma) API 'SI

Görüntü İşleme optik karakter tanıma (OCR) API 'SI okuma API 'siyle benzerdir, ancak eşzamanlı olarak yürütülür ve büyük belgeler için en iyi duruma getirilmemiştir. Daha önceki bir tanıma modeli kullanır, ancak daha fazla dilde çalışmaktadır; desteklenen dillerin tam listesi için bkz. [dil desteği](language-support.md#text-recognition) .

Gerekirse, OCR, tanınan metnin dönüşünün, yatay görüntü ekseni hakkında derece cinsinden döndürme farkını düzeltir. OCR Ayrıca, aşağıdaki çizimde görüldüğü gibi her sözcüğün çerçeve koordinatlarını de sağlar.

![Döndürülmekte olan bir görüntü ve metnin okunmakta ve ayırıcılarından biri](./Images/vision-overview-ocr.png)

Daha fazla bilgi edinmek için [OCR başvuru belgelerine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) bakın.

### <a name="image-requirements"></a>Görüntü gereksinimleri

OCR API 'SI aşağıdaki gereksinimleri karşılayan görüntülerde çalışmaktadır:

* Resim JPEG, PNG, GIF veya BMP biçiminde sunulmalıdır.
* Giriş resminin boyutu 50 x 50 ve 4200 x 4200 piksel arasında olmalıdır.
* Görüntüdeki metin, 90 derecenin herhangi bir katı ve 40 derecenin küçük bir açısı ile döndürülebilir.

### <a name="limitations"></a>Sınırlamalar

Metnin baskın olduğu fotoğraflardan, kısmen tanınan sözcüklerden hatalı pozitif sonuçlar gelebilir. Bazı fotoğraflarda, özellikle metin içermeyen fotoğraflar, görüntü türüne bağlı olarak değişiklik gösterebilir.

## <a name="recognize-text-api"></a>Metin Tanıma API 'SI

> [!NOTE]
> Metin Tanıma API 'SI, okuma API 'sinin yararına kullanım dışı bırakılıyor. Okuma API 'SI benzer yeteneklere sahiptir ve PDF, TIFF ve çok sayfalı dosyaları işleyecek şekilde güncelleştirilir.

Metin Tanıma API 'SI OCR ile benzerdir, ancak zaman uyumsuz olarak yürütülür ve güncelleştirilmiş tanıma modellerini kullanır. Daha fazla bilgi edinmek için [metın tanıma API başvuru belgelerine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) bakın.

### <a name="image-requirements"></a>Görüntü gereksinimleri

Metin Tanıma API 'SI aşağıdaki gereksinimleri karşılayan görüntülerle birlikte çalışmaktadır:

- Resim JPEG, PNG veya BMP biçiminde sunulmalıdır.
- Resmin boyutları 50 x 50 ve 4200 x 4200 piksel arasında olmalıdır.
- Resmin dosya boyutu 4 megabayttan (MB) az olmalıdır.

## <a name="limitations"></a>Sınırlamalar

Metin tanıma işlemlerinin doğruluğu görüntülerin kalitesine bağlıdır. Aşağıdaki faktörler yanlış okunmasına neden olabilir:

* Bulanık görüntüler.
* El yazısı veya bitişik el yazısı metin.
* Artistik yazı tipi stilleri.
* Küçük metin boyutu.
* Karmaşık arka planlar, gölgeler ya da metnin üzerinde parlama veya perspektif bozukluk.
* Sözcüklerin Beginnings büyük harfli veya eksik büyük harfler.
* Alt simge, üst simge veya üstü çizili metin.

## <a name="next-steps"></a>Sonraki adımlar

Basit C# bir uygulamada metin tanımayı uygulamak için [YAZDıRıLAN metin (OCR)](./quickstarts/csharp-print-text.md) hızlı başlangıcını Ayıkla ' yı izleyin.
