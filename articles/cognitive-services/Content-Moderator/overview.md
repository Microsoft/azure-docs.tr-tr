---
title: Azure Content Moderator nedir?
titleSuffix: Azure Cognitive Services
description: Kullanıcıların oluşturduğu içeriği izlemek, işaretlemek, değerlendirmek ve uygunsuz malzemeleri filtrelemek için Content Moderator kullanmayı öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: a78a92a33075a97ddadb2e1fe677b7ded541d12c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565585"
---
# <a name="what-is-azure-content-moderator"></a>Azure Content Moderator nedir?

Azure Content Moderator API'si rahatsız edici, riskli veya istenmeyen olabilecek öğeleri tespit etme amacıyla metin, görüntü ve video içeriğinde denetim gerçekleştiren bir bilişsel hizmettir. Hizmet, böyle bir öğeyle karşılaştığında içeriğe uygun etiketler (bayraklar) ekler. Uygulamanız da bu bayraklı içeriği dikkate alarak düzenlemelere uygunluk ya da kullanıcılar için istenen ortamın oluşturulması amacıyla işlem gerçekleştirebilir. Farklı içerik bayraklarının ne belirtdiklerine ilişkin daha fazla bilgi edinmek için [denetleme API 'leri](#moderation-apis) bölümüne bakın.

## <a name="where-it-is-used"></a>Kullanıldığı yerler

Aşağıda bir yazılım geliştirme uzmanının veya ekibinin Content Moderator özelliklerinden faydalanmak isteyebileceği birkaç senaryo verilmiştir:

- Çevrimiçi Pazar, bu orta düzey ürün kataloglarını ve diğer Kullanıcı tarafından oluşturulan içeriği koyar.
- Kullanıcı tarafından oluşturulan oyun yapıtları ve sohbet odalarını kullanan oyun şirketleri.
- Kullanıcıları tarafından eklenen görüntü, metin ve videoların bulunduğu sosyal mesaj platformları.
- İçerikleri için merkezi denetleme uygulayan kurumsal medya şirketleri.
- K-12 Eğitim çözüm sağlayıcıları öğrenciler ve eğitimciler için uygun olmayan içeriği filtreleyerek.

> [!NOTE]
> Geçersiz alt düzey yararlanma görüntülerini algılamak için Content Moderator kullanamazsınız. Ancak, nitelikli kuruluşlar bu içerik türü için [PhotoDNA bulut hizmeti](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") 'i kullanabilir.

## <a name="what-it-includes"></a>Neleri içerir

Content Moderator hizmeti hem REST çağrıları hem de .NET SDK'sı ile sunulan çeşitli web hizmeti API'lerinden oluşur. Ayrıca hizmete yardımcı olmak veya denetleme işlevinde ince ayar yapmak için insanlar tarafından gerçekleştirilen inceleme hizmeti de sunar.

## <a name="moderation-apis"></a>Yönetim API’leri

Content Moderator hizmeti, uygunsuz veya sakıncalı olabilecek malzemelere yönelik içeriği denetleyen denetleme API 'Lerini içerir.

![Content Moderator denetleme API 'Leri için blok diyagramı](images/content-moderator-mod-api.png)

Aşağıdaki tabloda farklı denetleme API 'Leri açıklanmaktadır.

| API grubu | Açıklama |
| ------ | ----------- |
|[**Metin denetimi**](text-moderation-api.md)| Saldırgan içeriği, cinsel açık veya müstehcen içerik, küfür ve kişisel veriler için metin tarar.|
|[**Özel terim listeleri**](try-terms-list-api.md)| Metinleri yerleşik terimlere ek olarak özel terim listesine göre tarar. İçerik ilkelerinize göre içerik engellemek veya izin vermek için özel listeler kullanabilirsiniz.|  
|[**Görüntü denetimi**](image-moderation-api.md)| Görüntülerde yetişkinlere yönelik veya müstehcen görüntü taraması yapar, Optik Karakter Tanıyıcı (OCR) ile görüntüdeki metinleri tanır ve yüzleri algılar.|
|[**Özel görüntü listeleri**](try-image-list-api.md)| Görüntüleri özel görüntü listesine göre tarar. Özel görüntü listelerini kullanarak tekrar tekrar sınıflandırmak istemediğiniz yaygın içerik örneklerini filtreleyebilirsiniz.|
|[**Video denetimi**](video-moderation-api.md)| Videolarda yetişkinlere yönelik veya müstehcen içerik taraması yapar ve bu içeriklerle ilgili zaman işaretçilerini döndürür.|

## <a name="review-apis"></a>API’leri inceleme

Inceleme API 'Leri, denetleme işlem hattınızı insan gözden geçirenler ile tümleştirmenize olanak sağlar. [İnceleme aracı](#the-review-tool) (aşağıda) ile insan döngüsü iş akışları oluşturmak ve otomatikleştirmek için [işler](review-api.md#jobs), [incelemeler](review-api.md#reviews)ve [iş akışı](review-api.md#workflows) işlemlerini kullanın.

> [!NOTE]
> Iş akışı API 'SI henüz .NET SDK 'da kullanılamıyor ancak REST uç noktasıyla birlikte kullanılabilir.

![Content Moderator gözden geçirme API 'Leri için diyagramı engelle](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Inceleme aracı

Content Moderator hizmeti Ayrıca, insan moderatör 'nin işlemesi için içerik incelemelerini barındıran Web tabanlı [İnceleme aracını](Review-Tool-User-Guide/human-in-the-loop.md)da içerir. İnsan girişi hizmeti eğitmez ancak hizmetin ve insanlardan oluşan ekibin birlikte kullanılması, geliştiricilerin verimlilik ve doğruluk arasındaki doğru dengeyi yakalamasını sağlar. Inceleme aracı Ayrıca çeşitli Content Moderator kaynakları için Kullanıcı dostu bir ön uç sağlar.

![Content Moderator insan inceleme aracı giriş sayfası](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, Content Moderator hizmetini kullanan geliştiriciler Microsoft 'un müşteri verileri ilkelerine göre farkında olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Web üzerinde Try Content moderator](quick-start.md)yönergelerini izleyerek Content moderator hizmetini kullanmaya başlayın.