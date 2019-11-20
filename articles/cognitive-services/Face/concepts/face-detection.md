---
title: Yüz algılama ve öznitelikler kavramları
titleSuffix: Azure Cognitive Services
description: Yüz algılama, görüntüde insan yüzlerini bulma ve isteğe bağlı olarak farklı türlerde yüz ile ilgili verileri döndürme eylemi olur.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743059"
---
# <a name="face-detection-and-attributes"></a>Yüz algılama ve öznitelikler

Bu makalede, yüz algılama ve yüz öznitelik verilerinin kavramları açıklanmaktadır. Yüz algılama, görüntüde insan yüzlerini bulma ve isteğe bağlı olarak farklı türlerde yüz ile ilgili verileri döndürme eylemi olur.

Görüntüdeki yüzeyleri algılamak için [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) işlemini kullanırsınız. En azından, algılanan her yüz yanıtta bir faceRectangle alanına karşılık gelir. Sol, üst, genişlik ve yükseklik için bu piksel koordinatları kümesi, bulunan yüzü işaretler. Bu koordinatları kullanarak, yüzün konumunu ve boyutunu alabilirsiniz. API yanıtında, yüzler en büyükten en küçüğe boyut sırasıyla listelenir.

## <a name="face-id"></a>Yüz kimliği

Yüz KIMLIĞI, görüntüde algılanan her bir yüz için benzersiz bir tanımlayıcı dizesidir. [Yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API çağrısı IÇIN yüz kimliği isteyebilirsiniz.

## <a name="face-landmarks"></a>Yüz tanıma yer işaretleri

Yüz yüzleri, bir yüz üzerinde yer alan, pupıls veya burun ipucu gibi kolay bir arama noktası kümesidir. Varsayılan olarak önceden tanımlanmış 27 yer işareti noktası vardır. Aşağıdaki şekilde tüm 27 noktaları gösterilmektedir:

![Tüm 27 yer işareti etiketli yüz diyagramı](../Images/landmarks.1.jpg)

Noktaların koordinatları piksel birimlerinde döndürülür.

## <a name="attributes"></a>Öznitelikler

Öznitelikler, [yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 'si tarafından isteğe bağlı olarak tespit edilebilir bir özellikler kümesidir. Aşağıdaki öznitelikler tespit edilebilir:

* **Yaş**. Belirli bir yüzün yıllık tahmini yaşı.
* **Bulanıklaştırma**. Görüntüdeki yüzün bulanıklaştırıltı. Bu öznitelik, sıfır ve bir, düşük, orta veya yüksek bir tamsayı derecelendirmesi arasında bir değer döndürür.
* **Duygu**. Verilen yüz için algılama güvenlerinin bulunduğu bir liste. Güvenilirlik puanları normalleştirilir ve tüm lamalar genelinde puan ekler. Dönen Özellikler mutluluklar, sadyeti, bağımsız, Anger, Contempt, disgust, sürpriz ve korku.
* **Pozlama**. Görüntüdeki yüzün pozlaması. Bu öznitelik sıfır ile bir ve çok pozlandırmayı, goodExposure veya aşırı Pozlandırma derecelendirmesi arasında bir değer döndürür.
* **Yüz artı**. Verilen yüzün tahmini yüz artı olma durumu ve uzunluğu.
* **Cinsiyeti**. Verilen yüzün tahmini cinsiyeti. Olası değerler erkek, kadın ve genderküçüktür.
* **Gözlük**. Verilen yüzün gözlük olup olmadığı. Olası değerler gözlük, Readinbahar, güneş gözlüğü ve yüzme Goggles.
* **Saç**. Yüzün saç türü. Bu öznitelik, saç görünür olup olmadığını, balblerin algılanıp algılanmadığını ve hangi saç renklerinin algılandığını gösterir.
* **Baş poz**. 3B alanda yüzün yönü. Bu öznitelik, derece cinsinden sıklık, zar ve yaw açılarla açıklanır. Değer aralıkları-90 derece ile 90 derece,-180 derece, 180 derece, ve-90 derece, sırasıyla derece olarak değişir. Açılı eşlemeler için aşağıdaki diyagrama bakın:

    ![Aralık, top ve yaw eksenlerinin etiketlendiği bir baş](../Images/headpose.1.jpg)
* **Makeup**. Yüzün oluşturulup oluşturulmayacağını belirtir. Bu öznitelik, eyeMakeup ve lipMakeup için bir Boole değeri döndürür.
* **Gürültü**. Yüz görüntüde algılanan görsel gürültü. Bu öznitelik, sıfır ve bir, düşük, orta veya yüksek bir tamsayı derecelendirmesi arasında bir değer döndürür.
* **Occluson**. Yüzün parçalarını engelleyen nesneler olup olmadığı. Bu öznitelik, eyeOccluded, foreheadOccluded ve mouthOccluded için bir Boole değeri döndürür.
* **Gülümseme**. Verilen yüzün gülümseme ifadesi. Bu değer, gülümseme olmaması için sıfır ve bir açık gülümseme için sıfır arasındadır.

> [!IMPORTANT]
> Yüz nitelikleri, istatistiksel algoritmaların kullanımı aracılığıyla tahmin edilir. Her zaman doğru olmayabilir. Öznitelik verilerine dayalı kararlar verirken dikkatli olun.

## <a name="input-data"></a>Giriş verileri

Giriş görüntülerinizin en doğru algılama sonuçlarını vermesini sağlamak için aşağıdaki ipuçlarını kullanın:

* Desteklenen giriş resim biçimleri JPEG, PNG, ilk çerçeve için GIF ve BMP.
* Görüntü dosyası boyutu 4 MB 'tan büyük olmamalıdır.
* Algılanabilir yüz boyut aralığı 36 x 36 ile 4096 x 4096 pikseldir. Bu aralığın dışındaki yüzler saptanmaz.
* Teknik zorluk sorunları nedeniyle bazı yüzler algılanmayabilir. Aşırı yüz açıları (baş poz) veya yüz kapatma (yüz gözlüğü gibi nesneler) algılamayı etkileyebilir. En iyi ve yakın yüzler en iyi sonuçları verir.

Video akışından yüz tespit ediyorsanız, video kameranızdaki belirli ayarları ayarlayarak performansı artırabilirsiniz:

* **Yumuşatma**: birçok video kameraları bir yumuşatma efekti uygular. Çerçeveler arasında bulanıklaştırma oluşturup netliği azalttığından, bunu kapatmanız gerekir.
* **Perde hızı**: daha hızlı bir perde hızı, kareler arasındaki hareket miktarını azaltır ve her çerçeveyi daha net hale getirir. 1/60 saniyelik veya daha hızlı bir şekilde perde hızını öneririz.
* **Perde açısı**: bazı kameralar, perde hızı yerine perde açısını belirtir. Mümkünse, daha düşük bir perde açısı kullanmanız gerekir. Bu, daha net video çerçevelerine neden olur.

    >[!NOTE]
    > Aşağı perde açısı olan bir kamera her karede daha az ışık alır, bu nedenle görüntü daha koyu olur. Kullanılacak doğru düzeyi belirlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Yüz algılama kavramlarıyla ilgili bilgi sahibi olduğunuza göre, belirli bir görüntüde yüzeyleri algılayan bir komut dosyası yazmayı öğrenin.

* [Görüntüdeki yüzeyleri algılama](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)