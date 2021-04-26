---
title: Görüntü İşleme nedir?
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme Hizmeti, görüntüleri işlemeye ve bilgi döndürmeye yönelik gelişmiş algoritmalara erişmenizi sağlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: bilgisayar görme, görüntü işleme uygulamaları, görüntü işleme hizmeti
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286143"
---
# <a name="what-is-computer-vision"></a>Görüntü İşleme nedir?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Görüntü İşleme Hizmeti, resimleri işleyen ve ilgilendiğiniz görsel özelliklere göre bilgileri döndüren gelişmiş algoritmalara erişmenizi sağlar. 

| Hizmet|Açıklama|
|---|---|
|[Optik Karakter Tanıma (OCR)](overview-ocr.md)|Optik karakter tanıma (OCR) hizmeti görüntülerden metin ayıklar. Yeni okuma API 'sini kullanarak fotoğraflardan ve belgelerden yazdırılmış ve el yazısı metinleri ayıklayabilirsiniz. Derin öğrenme tabanlı modeller kullanır ve çeşitli yüzeyler ve arka planlar üzerinde metinle birlikte çalışarak. Bunlar iş belgeleri, faturalar, tahsilatlar, posterler, iş kartları, mektuplar ve beyaz tahtalar içerir. OCR API 'Leri, yazdırılan metnin [birkaç dilde](./language-support.md)ayıklanarak desteklenir. Başlamak için [OCR hızlı](quickstarts-sdk/client-library.md) başlangıcını izleyin.|
|[Görüntü Analizi](overview-image-analysis.md)| Görüntü Analizi hizmeti, resimlerde nesneler, yüzler, yetişkinlere yönelik içerik ve otomatik oluşturulan metin açıklamaları gibi birçok görsel özelliği ayıklar. Başlamak için [görüntü analizi hızlı](quickstarts-sdk/image-analysis-client-library.md) başlangıcını izleyin.|
| [Uzamsal Analiz](intro-to-spatial-analysis-public-preview.md)| Uzamsal analiz hizmeti bir video akışındaki kişilerin varlığını ve hareketini analiz eder ve diğer sistemlerin yanıt verebildiği olayları üretir. Başlamak için [uzamsal analiz kapsayıcısını](spatial-analysis-container.md) yükler.|

## <a name="computer-vision-for-digital-asset-management"></a>Dijital varlık yönetimi için Görüntü İşleme

Görüntü İşleme, birçok dijital varlık yönetimi (DAM) senaryosunu güçlendirin. DAM, zengin medya varlıklarını düzenleme, depolama ve alma ve dijital hakları ve izinleri yönetme iş sürecleridir. Örneğin, bir şirket görünür logo, yüz, nesne, renk vb. temel alarak görüntüleri gruplamak ve tanımlamak isteyebilir. Ya da [görüntüler için otomatik olarak açıklamalı alt yazılar oluşturmak](./Tutorials/storage-lab-tutorial.md) ve anahtar sözcükler eklemek isteyebilirsiniz. Bilişsel hizmetler, Azure Bilişsel Arama ve akıllı Raporlama kullanan hepsi bir arada DAM çözümü için GitHub 'da [bilgi araştırma Çözüm Hızlandırıcısı kılavuzuna](https://github.com/Azure-Samples/azure-search-knowledge-mining) bakın. Diğer DAM örnekleri için [görüntü işleme çözüm şablonları](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) deposuna bakın.

## <a name="image-requirements"></a>Görüntü gereksinimleri

Görüntü İşleme, şu gereksinimleri karşılayan görüntüleri analiz edebilir:

- Sunulan görüntünün JPEG, PNG, GIF veya BMP biçiminde olması gerekir
- Görüntünün dosya boyutunun 4 megabayt (MB) değerini aşmaması gerekir
- Görüntünün boyutlarının 50 x 50 pikselden büyük olması gerekir
  - Okuma API 'SI için, resmin boyutları 50 x 50 ve 10000 x 10000 piksel arasında olmalıdır.

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, Görüntü İşleme hizmetini kullanan geliştiriciler Microsoft 'un müşteri verileri ilkelerine göre farkında olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Tercih ettiğiniz geliştirme dilinizde bir hizmeti uygulamak ve çalıştırmak için hızlı başlangıcı izleyin.

* [Hızlı başlangıç: optik karakter tanıma (OCR)](quickstarts-sdk/client-library.md)
* [Hızlı başlangıç: görüntü analizi](quickstarts-sdk/image-analysis-client-library.md)
* [Hızlı başlangıç: uzamsal analiz kapsayıcısı](spatial-analysis-container.md)
