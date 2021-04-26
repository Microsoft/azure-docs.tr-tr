---
title: Görüntü analizi nedir?
titleSuffix: Azure Cognitive Services
description: Görüntü Analizi hizmeti, görüntülerden birçok farklı görsel özelliği ayıklamak için önceden eğitilen AI modellerini kullanır.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: bilgisayar görme, görüntü işleme uygulamaları, görüntü işleme hizmeti
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766924"
---
# <a name="what-is-image-analysis"></a>Görüntü analizi nedir?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Görüntü İşleme resim Analizi hizmeti, görüntülerinizden çok çeşitli görsel özellikleri ayıklayabilir. Örneğin, bir görüntünün yetişkinlere yönelik içerik içerip içermediğini, belirli marka veya nesne bulmasını veya insan yüzlerini bulmasını belirleyebilirsiniz.

Görüntü analizini bir istemci kitaplığı SDK 'Sı aracılığıyla veya [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) doğrudan çağırarak kullanabilirsiniz. Başlamak için [hızlı](quickstarts-sdk/image-analysis-client-library.md) başlangıcı izleyin.

Bu belge aşağıdaki makale türlerini içerir:
* [Hızlı başlangıç](./quickstarts-sdk/image-analysis-client-library.md) , hizmete çağrı yapmanızı ve sonuçların kısa bir süre içinde elde etmenize olanak tanıyan adım adım yönergelerdir. 
* [Nasıl yapılır kılavuzlarında](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) , hizmeti daha belirli veya özelleştirilmiş yollarla kullanmaya yönelik yönergeler bulunur.
* [Kavramsal makaleler](concept-tagging-images.md) , hizmetin işlevselliği ve özellikleri hakkında ayrıntılı açıklamalar sağlar.
* [Öğreticiler](./tutorials/storage-lab-tutorial.md) daha fazla iş çözümlerinde bu hizmeti bir bileşen olarak nasıl kullanacağınızı gösteren kılavuzlardır.

## <a name="image-analysis-features"></a>Görüntü analizi özellikleri

Görsel özellikleri ve özellikleri hakkında Öngörüler sağlamak için görüntüleri çözümleyebilirsiniz. Aşağıdaki listedeki tüm özellikler, [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API 'si tarafından sağlanmaktadır. Başlamak için [hızlı](./quickstarts-sdk/image-analysis-client-library.md) başlangıcı izleyin.


### <a name="tag-visual-features"></a>Görsel özellikleri etiketleme

Görüntüde görsel özellikleri, binlerce tanınabilir nesne, çok sayıda, manzara ve eylem kümesinden belirleyin ve etiketleyin. Etiketler belirsiz veya ortak olmayan bir bilgi olmadığında, API yanıtı etiketin bağlamını açıklığa kavuşturmak için ipuçları sağlar. Etiketleme yalnızca temel konu ile sınırlı kalmayıp ortam (iç mekân veya dış mekân), mobilyalar, aletler, bitkiler, hayvanlar, aksesuarlar, araçlar ve benzer öğeleri de kapsar. [Görsel özellikleri etiketleme](concept-tagging-images.md)

### <a name="detect-objects"></a>Nesneleri Algıla

Nesne algılama, etiketlemeyle benzerdir ancak API, uygulanan her bir etiket için sınırlayıcı kutu koordinatlarını döndürür. Örneğin, bir görüntü köpek, kedi ve kişi içeriyorsa, Algıla işlemi bu nesneleri görüntüdeki koordinatlarıyla birlikte listeler. Görüntüdeki nesneler arasında daha fazla ilişki işlemek için bu işlevi kullanabilirsiniz. Ayrıca, görüntüde aynı etiketin birden çok örneği olduğunu da bilmenizi sağlar. [Nesneleri Algıla](concept-object-detection.md)

### <a name="detect-brands"></a>Markalar Algıla

Binlerce küresel logo veritabanındaki resimlerde veya videolarda ticari markalar belirler. Bu özelliği örneğin, sosyal medya veya en popüler medya ürün yerleştirmesi üzerinde en popüler olan markalar öğrenmek için kullanabilirsiniz. [Markalar Algıla](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Bir görüntüyü kategorilere ayırma

Üst/alt öğe kalıtım hiyerarşileri içeren bir [kategori sınıflandırması](Category-Taxonomy.md) kullanarak bir görüntüyü bütünüyle tanımlayın ve kategorilere ayırın. Kategoriler tek başına veya yeni etiketleme modellerimizle birlikte kullanılabilir.<br/>Şu anda, görüntülerin etiketlenmesi ve kategorilere ayrılması için yalnızca İngilizce desteklenmektedir. [Bir görüntüyü kategorilere ayırma](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Bir görüntüyü açıklama

Tam cümleler kullanarak bir görüntünün tamamı için okunabilir açıklamalar oluşturun. Görüntü İşleme algoritmaları, görüntüde tanımlanan nesneleri temel alan çeşitli açıklamalar oluşturur. Açıklamaların her biri değerlendirilir ve bir güvenilirlik puanı oluşturulur. Ardından, güvenilirlik puanı için azalan düzende sıralı bir liste döndürülür. [Bir görüntüyü açıklama](concept-describing-images.md)

### <a name="detect-faces"></a>Yüz algılama

Bir görüntüdeki yüzleri algılayın ve algılanan her bir yüz için bilgiler sunun. Görüntü İşleme algılanan her bir yüz için koordinatları, dikdörtgeni, cinsiyeti ve yaşı döndürür.<br/>Görüntü İşleme [yüz](../face/index.yml) hizmeti işlevlerinin bir alt kümesini sağlar. Yüz tanıma ve poz algılama gibi daha ayrıntılı analizler için yüz hizmetini kullanabilirsiniz. [Yüz algılama](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Görüntü türünü algılama

Bir görüntü ile ilgili özellikleri (görüntünün çizim olup olmaması veya küçük resim olup olmama olasılığı gibi) algılayın. [Görüntü türünü algılama](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Etki alanına özgü içeriği algılama

Bir görüntüde yer alan, ünlüler ve önemli yerler gibi etki alanına özgü içerikleri algılamak ve tanımak için etki alanı modelleri kullanın. Örneğin, bir görüntü kişi içeriyorsa Görüntü İşleme, görüntüde algılanan kişilerin bilinen ünlüler olup olmadığını tespit etmek üzere Ünlüler için bir etki alanı modeli kullanabilir. [Etki alanına özgü içeriği algılama](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Renk düzenini algılama

Bir görüntüdeki renk kullanımını analiz edin. Görüntü İşleme, bir görüntünün siyah-beyaz olup olmadığını belirlemenin yanı sıra renkli görüntüler için baskın renkleri ve vurgu renklerini tanıyabilir. [Renk düzenini algılama](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Küçük resim oluşturma

Görüntülere uygun küçük resimler oluşturmak üzere söz konusu görüntülerin içeriklerini analiz edin. Görüntü İşleme ilk olarak yüksek kaliteli bir küçük resim oluşturur ve sonra *ilgilendiğiniz alanı* öğrenmek için görüntü içindeki nesneleri analiz eder. Görüntü İşleme sonra görüntüyü, ilgilendiğiniz alanın gereksinimlerine uyacak şekilde kırpar. Oluşturulan küçük resim, ihtiyaçlarınız doğrultusunda, özgün görüntünün en boy oranından farklı bir en boy oranı kullanılarak sunulabilir. [Küçük resim oluşturma](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>İlgilendiğiniz alanı alın

*İlgilendiğiniz alanın* koordinatlarını döndürmek için bir görüntünün içeriğini analiz edin. Görüntüyü kırpmak ve küçük resim oluşturmak yerine, Görüntü İşleme bölgenin sınırlayıcı kutusu koordinatlarını döndürür, böylece çağıran uygulama özgün görüntüyü istediğiniz gibi değiştirebilir. [İlgilendiğiniz alanı alın](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Görüntü içeriklerini denetleme

Görüntü İşleme kullanarak bir görüntüdeki [yetişkinlere yönelik içeriği algılayabilir](concept-detecting-adult-content.md) ve farklı sınıflandırmaların güvenilirlik puanlarını getirebilirsiniz. İçerik bayrak belirleme eşiği, tercihlerinize uyum sağlamak için bir kayan ölçekte ayarlanabilir.

## <a name="image-requirements"></a>Görüntü gereksinimleri

Görüntü analizi, aşağıdaki gereksinimleri karşılayan görüntülerde çalışmaktadır:

- Sunulan görüntünün JPEG, PNG, GIF veya BMP biçiminde olması gerekir
- Görüntünün dosya boyutunun 4 megabayt (MB) değerini aşmaması gerekir
- Görüntünün boyutlarının 50 x 50 pikselden büyük olması gerekir

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, Görüntü İşleme hizmetini kullanan geliştiriciler Microsoft 'un müşteri verileri ilkelerine göre farkında olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Tercih ettiğiniz geliştirme dilindeki hızlı başlangıç kılavuzunu izleyerek görüntü analizini kullanmaya başlayın:

- [Hızlı başlangıç: Görüntü İşleme REST API veya istemci kitaplıkları](./quickstarts-sdk/image-analysis-client-library.md)
