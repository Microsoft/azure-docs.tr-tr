---
title: Yüz tanıma kavramları
titleSuffix: Azure Cognitive Services
description: Bu makalede, doğrulama kavramları, benzer bul, Gruplandır, yüz tanıma işlemleri ve temel alınan veri yapıları açıklanmaktadır.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743067"
---
# <a name="face-recognition-concepts"></a>Yüz tanıma kavramları

Bu makalede, doğrulama kavramları, benzer bul, Gruplandır, yüz tanıma işlemleri ve temel alınan veri yapıları açıklanmaktadır. Büyük ölçüde tanıma, benzer veya aynı kişiye ait olup olmadıklarını tespit etmek üzere iki farklı yüzü karşılaştırma işini açıklar.

## <a name="recognition-related-data-structures"></a>Tanıma ilgili veri yapıları

Tanıma işlemleri, genellikle aşağıdaki veri yapılarını kullanır. Bu nesneler bulutta depolanır ve KIMLIK dizeleri tarafından başvurulabilirler. KIMLIK dizeleri her zaman bir abonelik içinde benzersizdir. Ad alanları yinelenebilir.

|Ad|Açıklama|
|:--|:--|
|DetectedFace| Bu tek yüz temsili, [yüz algılama](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) işlemi tarafından alınır. KIMLIĞI, oluşturulduktan sonra 24 saat sonra dolar.|
|PersistedFace| Bir gruba, çok yönlü liste veya kişi gibi algılayıcısı eklenmiş yüz nesneleri eklendiğinde, PersistedFace nesneleri olur. Bunlar herhangi bir zamanda [alınabilir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) ve süresi dolmaz.|
|Çok [yönlü liste](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) veya [largecelist](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Bu veri yapısı, PersistedFace nesnelerinin assýralanan listesidir. Çok yönlü bir liste, benzersiz bir KIMLIĞE, bir ad dizesine ve isteğe bağlı olarak bir kullanıcı veri dizesine sahiptir.|
|[Kişiler](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Bu veri yapısı, aynı kişiye ait olan PersistedFace nesnelerinin bir listesidir. Benzersiz bir KIMLIĞE, bir ad dizesine ve isteğe bağlı olarak bir kullanıcı veri dizesine sahiptir.|
|[Persongroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) veya [largepersongroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Bu veri yapısı, kişi nesnelerinin bir listesi olan bir listesidir. Benzersiz bir KIMLIĞE, bir ad dizesine ve isteğe bağlı olarak bir kullanıcı veri dizesine sahiptir. Bir PersonGroup, tanıma işlemlerinde kullanılmadan önce [eğitilmiş](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) olmalıdır.|

## <a name="recognition-operations"></a>Tanıma işlemleri

Bu bölümde, dört tanıma işlemlerinin daha önce açıklanan veri yapılarını nasıl kullandığı açıklanmaktadır. Her bir tanıma işleminin ayrıntılı bir açıklaması için bkz. [genel bakış](../Overview.md).

### <a name="verify"></a>Doğrulama

[Doğrulama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) Işlemi, Detectedface veya PersistedFace 'dan BIR yüz kimliği ve başka BIR yüz kimliği ya da bir kişi nesnesi alır ve aynı kişiye ait olup olmadığını belirler. Bir kişi nesnesi geçirirseniz, isteğe bağlı olarak, söz konusu kişinin, performansı artırmak için sahip olduğu bir PersonGroup 'a geçirebilirsiniz.

### <a name="find-similar"></a>Benzer bul

[Benzer bul](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) Işlemi, Detectedface veya PersistedFace 'dan BIR yüz kimliği ve çok yönlü bir liste ya da başka yüz kimlikleri dizisi alır. Çok yönlü bir liste ile, verilen yüzle benzer yüzlerin daha küçük bir listesini döndürür. Yüz kimlikleri dizisiyle benzer şekilde daha küçük bir dizi döndürür.

### <a name="group"></a>Grup

[Grup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) Işlemi, Detectedface veya PersistedFace 'dan gelen bir dizi ası kimliği alır ve aynı kimlikleri birkaç küçük dizide gruplanmış olarak döndürür. Her "Grup" dizisi benzer görünen yüz kimliklerini içerir. Tek bir "messyGroup" dizisi, hiçbir benzerliklerin bulunamadıkları yüz kimliklerini içerir.

### <a name="identify"></a>Belirleme

[Belirlenmesi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) Işlemi, Detectedface veya PersistedFace Ile bir persongroup 'tan bir veya birkaç yüz kimliği alır ve her bir yüzün ait olabileceği kişi nesnelerinin bir listesini döndürür. Döndürülen kişi nesneleri, bir tahmin güven değeri olan aday nesneler olarak sarmalanır.

## <a name="input-data"></a>Giriş verileri

Giriş görüntülerinizin en doğru tanınma sonuçlarını vermesini sağlamak için aşağıdaki ipuçlarını kullanın:

* Desteklenen giriş resim biçimleri JPEG, PNG, GIF (ilk çerçeve), BMP.
* Görüntü dosyası boyutu 4 MB 'tan büyük olmamalıdır.
* Kişi nesneleri oluştururken, farklı açılı ve aydınlatma çeşitleri sunan fotoğraflar kullanın.
* Bazı yüzler, şu gibi teknik sorunlar nedeniyle tanınmayabilir:
  * Aşırı aydınlatmaya sahip görüntüler, örneğin, ciddi geri aydınlatma.
  * Bir veya her iki gözdeki engelleri engeller.
  * İnce tür veya yüz şeklindeki farklar.
  * Yaş nedeniyle yüz görünümünde değişiklikler.
  * Aşırı yüz ifadeleri.

## <a name="next-steps"></a>Sonraki adımlar

Yüz tanıma kavramlarıyla ilgili bilgi sahibi olduğunuza göre, eğitilen bir kişinin yüzlerini tanımlayan bir komut dosyası yazmayı öğrenin.

* [Görüntülerdeki yüzeyleri tanımla](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)