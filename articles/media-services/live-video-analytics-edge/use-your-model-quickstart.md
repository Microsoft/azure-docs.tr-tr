---
title: Kendi HTTP modelinizi kullanarak canlı videoyu çözümleyin-Azure
description: Bu hızlı başlangıçta, kendi HTTP modelinizi kullanarak (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için bilgisayar vizyonu uygularsınız.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: d3ba937abcc7bbfd9bb2afe7b15aec28ebb57446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508520"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Hızlı başlangıç: kendi HTTP modelinizi kullanarak canlı videoyu çözümleyin

Bu hızlı başlangıçta, (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için IoT Edge 'da canlı video analizinin nasıl kullanılacağı gösterilmektedir. Nesneleri algılamak için bir bilgisayar Vision modelinin nasıl uygulanacağını göreceksiniz. Canlı video akışındaki çerçevelerin bir alt kümesi bir çıkarım hizmetine gönderilir. Sonuçlar IoT Edge hub 'ına gönderilir. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Önkoşullar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Örnek videoyu gözden geçirin

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Genel Bakış

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Medya grafiğini oluşturma ve dağıtma

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Sonuçları yorumlama

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer hızlı başlangıç yapmayı düşünüyorsanız oluşturduğunuz kaynakları saklayın. Aksi takdirde, Azure portal gidin, kaynak gruplarınıza gidin, bu hızlı başlangıcı çalıştırdığınız kaynak grubunu seçin ve tüm kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

* [YoloV3 modelinin güvenli bir sürümünü](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) deneyin ve IoT Edge cihaza dağıtın. 

Gelişmiş kullanıcılar için ek güçlükleri gözden geçirin:

* RTSP simülatörü kullanmak yerine RTSP desteği olan bir [IP kamerası](https://en.wikipedia.org/wiki/IP_camera) kullanın. [ONVIF uyumlu](https://www.onvif.org/conformant-products/) ürünler sayfasında RTSP 'YI destekleyen IP kameralarını arayabilirsiniz. Profiller G, S veya T ile uyumlu olan cihazları arayın.
* Azure Linux VM yerine AMD64 veya x64 Linux cihazı kullanın. Bu cihaz, IP kamerası ile aynı ağda olmalıdır. [Linux üzerinde Azure IoT Edge çalışma zamanını Install](../../iot-edge/how-to-install-iot-edge.md)bölümündeki yönergeleri izleyebilirsiniz. Ardından, [ilk IoT Edge modülünüzü bir sanal Linux cihazına dağıtma](../../iot-edge/quickstart-linux.md)konusundaki yönergeleri Izleyerek cihazı Azure IoT Hub kaydettirin.