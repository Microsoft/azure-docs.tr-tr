---
title: Özel Karar Alma Hizmeti nedir?
titlesuffix: Azure Cognitive Services
description: Bu makalede, Özel Karar Alma Hizmeti’ne genel bir bakış sunulmaktadır.
services: cognitive-services
author: alekh
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 073b2bf6df21e05481cde043d8ddcfd49822e94f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704237"
---
# <a name="what-is-custom-decision-service"></a>Özel Karar Alma Hizmeti nedir?

Tipik bir web uygulamasında veya mobil uygulamada ön sayfa bağlantısı birçok makaleye veya diğer içerik türlerine bağlantı sunar. Ön sayfa yüklendikçe Özel Karar Alma Hizmeti’nin ön sayfada yer alan makaleleri derecelendirmesini isteyebilir. Bu nedenle, bir kullanıcı üzerine tıklayarak bir makaleyi seçtiğinde, Özel Karar Alma Hizmeti’ne ikinci bir istek gönderilebilir ve böylece o kullanıcı kararının çıktısı günlüğe kaydedilir.

Özel Karar Alma Hizmeti yalnızca içeriğiniz için bir RSS akışı ve uygulamanıza birkaç JavaScript satırının eklenmesini gerektirdiğinden, kullanımı kolaydır.

Özel Karar Alma Hizmeti, içeriğinizi makine öğrenmesi için özelliklere dönüştürür. Sistem; metin, görüntüler, videolar ve genel yaklaşım açısından içeriğinizi anlamak için bu özellikleri kullanır. [Varlık Bağlama](../entitylinking/home.md), [Metin Analizi](../text-analytics/overview.md), [Duygu Tanıma](../emotion/home.md) ve [Görüntü İşleme](../computer-vision/home.md) gibi diğer birçok [Microsoft Bilişsel Hizmetleri](https://www.microsoft.com/cognitive-services)’ni kullanır.

Özel Karar Alma için bazı yaygın kullanım örnekleri şunlardır:

* Haber web sitesindeki makaleleri kişiselleştirme
* Medya portalındaki video içeriğini kişiselleştirme
* Reklamın yönlendirdiği web sayfalarını veya reklam yerleşimlerini iyileştirme
* Alışveriş web sitesindeki önerilen ürünleri derecelendirme.

Özel Karar Alma Hizmeti şu anda *ücretsiz genel önizleme aşamasındadır*. Bir web sitesindeki veya uygulamadaki makalelerin listesini kişiselleştirebilir. Özellik ayıklama, İngilizce dilindeki içerikle en iyi şekilde çalışır. İspanyolca, Fransızca, Almanca, Portekizce ve Japonca gibi diğer diller için [sınırlı işlevsellik](../text-analytics/overview.md) sunulur. Yeni işlevsellik kullanılabilir oldukça bu belge de revize edilecektir.

Özel Karar Alma Hizmeti, içerik kişiselleştirme etki alanında bulunmayan uygulamalarda kullanılabilir. Bu uygulamalar, özel önizleme için ideal olabilir. Daha fazla bilgi için [bize ulaşın](https://azure.microsoft.com/overview/sales-number/).

## <a name="api-usage-modes"></a>API kullanımı modları

Özel Karar Alma Hizmeti hem web sayfalarına hem de mobil uygulamalara uygulanabilir. API’ler bir tarayıcıdan veya uygulamadan çağrılabilir. API kullanımı her ikisine benzer, ancak bazı ayrıntılar farklıdır.

## <a name="glossary-of-terms"></a>Terimler sözlüğü

Bu belgede bazı terimler sık sık geçmektedir:

* **Eylem kümesi**: Özel Karar Alma Hizmeti derecelendirmek için içerik öğeleri kümesi. Bu küme bir *RSS* veya *Atom* uç noktası olarak belirtilebilir.
* **Sıralama**: Özel Karar Alma Hizmeti her istek bir veya daha fazla eylem kümesini belirtir. Sistem bu kümelerden tüm içerik seçeneklerini seçerek yanıt verir ve bunları sıralı bir düzende döndürür.
* **Geri çağırma işlevi**: Belirttiğiniz bu işlev, Kullanıcı arabiriminizdeki içeriği işler. İçerik, Özel Karar Alma Hizmeti tarafından döndürülen sıralı düzene göre sıralanır.
* **Ödül**: Kullanıcının işlenmiş içeriğe nasıl yanıt verdiğini gösteren bir ölçü. Özel Karar Alma Hizmeti, tıklamaları kullanarak kullanıcı yanıtını ölçer. Tıklamalar, uygulamanıza eklenen özel kod kullanılarak sisteme bildirilir.

## <a name="next-steps"></a>Sonraki adımlar

* Özel Karar Alma Hizmeti’ne [uygulamanızı kaydetme](custom-decision-service-get-started-register.md)
* [Bir web sayfasını](custom-decision-service-get-started-browser.md) veya [akıllı telefon uygulamasını](custom-decision-service-get-started-app.md) iyileştirmeye başlayın.
* Sağlanan işlevler hakkında daha fazla bilgi edinmek için [API başvurusu](custom-decision-service-api-reference.md)’na bakın.
