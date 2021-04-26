---
title: Optik karakter tanıma nedir?
titleSuffix: Azure Cognitive Services
description: Optik karakter tanıma (OCR) hizmeti görüntüdeki görünür metni ayıklar ve yapılandırılmış dizeler olarak döndürür.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536747"
---
# <a name="what-is-optical-character-recognition"></a>Optik karakter tanıma nedir?

Optik karakter tanıma (OCR) hizmeti, yazdırılmış veya el yazısı metinleri cadde işaretleri ve ürünlerin fotoğraflarından, Ayrıca belgeler &mdash; faturalarından, faturalardan, finansal raporlardan, Makalelerden ve daha fazlasına ayıklamanızı sağlar. Derin öğrenme tabanlı modeller kullanır ve çeşitli yüzeyler ve arka planlar üzerinde metinle birlikte çalışmaktadır.

OCR API 'Leri, yazdırılan metnin [birkaç dilde](./language-support.md)ayıklanarak desteklenir. Başlamak için [hızlı](./quickstarts-sdk/client-library.md) başlangıcı izleyin.

![OCR gösterileri](./Images/ocr-demo.gif)

Bu belge aşağıdaki makale türlerini içerir:
* [Hızlı başlangıç](./quickstarts-sdk/client-library.md) , hizmete çağrı yapmanızı ve sonuçların kısa bir süre içinde elde etmenize olanak tanıyan adım adım yönergelerdir. 
* [Nasıl yapılır kılavuzlarında](./Vision-API-How-to-Topics/call-read-api.md) , hizmeti daha belirli veya özelleştirilmiş yollarla kullanmaya yönelik yönergeler bulunur.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Desteklenen diller
OCR API 'Leri, yazdırma stili metni için toplam 73 dili destekler. [OCR tarafından desteklenen dillerin](./language-support.md#optical-character-recognition-ocr)tam listesine bakın. Elyazısı stili OCR yalnızca Ingilizce için desteklenir.

## <a name="input-requirements"></a>Giriş gereksinimleri

**Okuma** çağrısı görüntüleri ve belgeleri giriş olarak alır. Bunlar aşağıdaki gereksinimlere sahiptir:

* Desteklenen dosya biçimleri: JPEG, PNG, BMP, PDF ve TIFF
* PDF ve TIFF dosyaları için en fazla 2000 sayfa (ücretsiz katman için yalnızca ilk iki sayfa) işlenir.
* Dosya boyutu 50 MB 'tan az (ücretsiz katman için 4 MB) ve en az 50 x 50 piksel ve en fazla 10000 x 10000 piksel boyutunda olmalıdır. 

## <a name="read-api"></a>API 'YI oku 

Görüntü İşleme [okuma API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) 'Si, Azure 'un en son OCR teknolojisidir[(yenilikler),](./whats-new.md)yazdırılan metni (bazı dillerde), el yazısı metin (yalnızca İngilizce), rakamları ve görüntü ve çok sayfalı PDF belgelerinden gelen para birimi sembollerini ayıklar. Metin açısından kalın görüntülerden ve çok sayfalı PDF belgelerinden karışık dillerle metin ayıklamak en iyi duruma getirilmiştir. Aynı görüntüde veya belgede hem yazdırılmış hem de el yazısı metnin algılanmasının aynısını destekler.

![OCR, resimleri ve belgeleri ayıklanan metinle yapısal çıktıya dönüştürür](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Önemli özellikler

Okuma API 'SI aşağıdaki özellikleri içerir. 

* 73 dilde yazdırma metin ayıklama
* Ingilizce 'de el ile metin ayıklama
* Konum ve güvenirlik puanlarını içeren metin satırları ve sözcükler
* Dil kimliği gerekli değil
* Karışık diller için destek, karma mod (yazdırma ve el yazısı)
* Büyük, çok sayfalı belgelerden sayfaları ve sayfa aralıklarını seçin
* Metin satırları için doğal okuma sırası
* Metin satırları için el yazısı sınıflandırması
* Şirket içi dağıtım için Distrodaha az Docker kapsayıcısı olarak kullanılabilir

[OCR özelliklerini kullanmayı](./vision-api-how-to-topics/call-read-api.md)öğrenin.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Bulut API 'sini kullanma veya şirket içinde dağıtma
3. x bulut API 'Leri, çoğu müşteri için tercih edilen seçenektir ve bu, çok sayıda tümleştirme ve bu kutudan hızlı verimlilik açısından tercih edilir. Müşterilerinizin gereksinimlerine odaklanmanız durumunda Azure ve Görüntü İşleme Hizmeti tanıtıcı ölçeği, performansı, veri güvenliği ve uyumluluk gereksinimlerini karşılamanız gerekir.

Şirket içi dağıtım için, [okuma Docker kapsayıcısı (Önizleme)](./computer-vision-how-to-install-containers.md) , yeni OCR özelliklerini kendi yerel ortamınızda dağıtmanıza olanak sağlar. Kapsayıcılar, belirli güvenlik ve veri idare gereksinimleri için çok kullanışlıdır.

## <a name="ocr-api"></a>OCR APı 'SI

Eski [OCR API 'si](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) , daha eski bir tanıma modeli kullanır, yalnızca görüntüleri destekler ve algılanan metinle anında dönerek zaman uyumlu olarak yürütülür. Desteklenen dillerin bir listesi için [desteklenen DILLERIN](./language-support.md#optical-character-recognition-ocr) OCR sütununa bakın.

> [!WARNING]
> Görüntü İşleme 2,0 RecognizeText işlemleri, bu makalede ele alınan yeni [okuma API](#read-api) 'sinde kullanım dışı bırakılıyor sürecinde. Mevcut müşteriler [okuma işlemlerini kullanarak geçiş](upgrade-api-versions.md)yapması gerekir.

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, Görüntü İşleme hizmetini kullanan geliştiriciler Microsoft 'un müşteri verileri ilkelerine göre farkında olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [OCR (okuma) REST API veya istemci kitaplığı hızlı](./quickstarts-sdk/client-library.md)başlangıçlarını kullanmaya başlayın.
- [Okuma 3,2 REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)hakkında bilgi edinin.
