---
title: Özel Görüntü İşleme nedir?
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme Hizmeti'ni kullanarak Azure bulutunda özel görüntü sınıflandırıcıları oluşturmayı öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 9e455a42945ba4d6ce334d557b7390c3c50e7ff9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177823"
---
# <a name="what-is-custom-vision"></a>Özel Görüntü İşleme nedir?

Özel Görüntü İşleme kendi görüntü sınıflandırıcılarınızı oluşturmanıza, dağıtmanıza ve iyileştirmenize olanak tanıyan bir bilişsel hizmettir. Görüntü Sınıflandırıcısı, görsel özelliklerine göre resimlere (sınıfları temsil eden _sınıflar_) uygulanan bir AI hizmetidir. [Görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) hizmetinin aksine, özel görüntü işleme uygulanacak etiketleri belirlemenizi sağlar.

## <a name="what-it-does"></a>Neler yapar?

Özel Görüntü İşleme Hizmeti, resimlere etiket uygulamak için bir makine öğrenimi algoritması kullanır. Geliştirici, söz konusu özelliklere sahip olan ve özellik olmayan görüntü gruplarını göndermesi gerekir. Görüntüleri gönderme sırasında kendiniz etiketlendirin. Daha sonra algoritma bu verilere yönelik olarak bir test yapar ve kendisini aynı görüntülerle test ederek kendi doğruluğunu hesaplar. Algoritma eğitilirken, bunu test edebilir, yeniden eğitebilir ve en sonunda, yeni görüntüleri uygulamanızın gereksinimlerine göre sınıflandırmak için kullanabilirsiniz. İsterseniz modeli çevrimdışı kullanmak üzere dışarı aktarabilirsiniz.

### <a name="classification-and-object-detection"></a>Sınıflandırma ve nesne algılama

Özel Görüntü İşleme hizmeti iki bölüme ayrılabilir. **Görüntü sınıflandırması** bir görüntüye bir veya daha fazla etiket uygular. **Nesne algılama** benzerdir, ancak uygulanan etiketlerin bulunduğu görüntüdeki koordinatları de döndürür.

### <a name="optimization"></a>İyileştirme

Özel Görüntü İşleme Hizmeti, görüntüler arasındaki önemli farklılıkları hızlı bir şekilde tanımak için en iyi duruma getirilmiştir, bu sayede modelinize kısa bir veri miktarıyla prototip başlatabilirsiniz. Her etiket için 50 görüntü genellikle iyi bir başlangıç. Ancak, görüntüde hafif farklılıkları tespit etmek için en uygun değildir (örneğin, kalite güvencesi senaryolarındaki küçük bir yandan veya en yüzlerini algılama).

Ayrıca, belirli konu malzemesine sahip görüntüler için optimize edilmiş Özel Görüntü İşleme algoritmasından çok sayıda değişken arasından seçim yapabilirsiniz. Örneğin, yer işaretleri veya perakende öğeleri. Daha fazla bilgi için bkz. [sınıflandırıcı oluşturma](getting-started-build-a-classifier.md) Kılavuzu.

## <a name="what-it-includes"></a>Neleri içerir

Özel Görüntü İşleme Hizmeti, yerel SDK'lara ek olarak [Özel Görüntü İşleme giriş sayfası](https://customvision.ai/) üzerinden web tabanlı arabirim aracılığıyla da sunulmaktadır. Bir modeli bir arabirim aracılığıyla oluşturabilir, test edebilir ve eğitebilirsiniz ya da ikisini birlikte kullanabilirsiniz.

![Chrome tarayıcı penceresinde Özel Görüntü İşleme giriş sayfası](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, Özel Görüntü İşleme hizmetini kullanan geliştiriciler Microsoft 'un müşteri verileri ilkelerine göre farkında olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Web üzerinde Özel Görüntü İşleme kullanmaya başlamak için [sınıflandırıcının derleme](getting-started-build-a-classifier.md) kılavuzunu izleyin veya kodda temel bir senaryoyu uygulamak Için bir [görüntü sınıflandırması öğreticisini](csharp-tutorial.md) tamamlayın.
