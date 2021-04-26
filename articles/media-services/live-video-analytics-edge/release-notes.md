---
title: IoT Edge sürüm notlarında canlı video analizi-Azure
description: Bu konu, IoT Edge sürümler, geliştirmeler, hata düzeltmeleri ve bilinen sorunlar üzerine canlı video analizinin sürüm notlarını sağlar.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954404"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge sürüm notlarında canlı video analizi

>Bu URL 'YI kopyalayarak ve yapıştırarak bu sayfanın ne zaman yeniden ziyaret ettikleridir hakkında bildirim alın: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` RSS akışı okuyucusuna.

Bu makalede hakkında bilgi verilmektedir:

* En son yayınlar
* Bilinen sorunlar
* Hata düzeltmeleri
* Kullanım dışı işlevsellik

<hr width=100%>

## <a name="january-12-2021"></a>12 Ocak 2021

Bu yayın etiketi, modülün Ocak 2021 yenilemesi için kullanılır:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> Hızlı başlangıç ve öğreticiler bölümünde, dağıtım bildirimleri 2 (canlı-video-analiz: 2) etiketini kullanır. Bu nedenle, bu tür bildirimleri yeniden dağıtmak Edge > cihazlarınızda modülü güncellemelidir.
### <a name="bug-fixes"></a>Hata düzeltmeleri 

* , `ActivationSignalOffset` `MinimumActivationTime` Ve `MaximumActivationTime` sinyal kapısı işlemcilerinde bulunan alanlar, gereken özellikler olarak doğru ayarlanmıştı. Artık **isteğe bağlı** özelliklerdir.
* IoT Edge modülündeki canlı video analizlerinin belirli bölgelerde dağıtıldığında kilitlenmesine neden olan bir kullanım hatası düzeltildi.

<hr width=100%>

## <a name="december-14-2020"></a>14 Aralık 2020
Bu sürüm, IoT Edge üzerindeki canlı video analizinin genel önizleme sürümünü yenileme sürümüdür. Yayın etiketi

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Modül güncelleştirmeleri
* Grafik topolojisi başına birden fazla HTTP uzantı işlemcisi ve gRPC uzantı işlemcisi kullanımı için destek eklendi.
* [Havuz düğümleri Için disk alanı yönetimine](upgrading-lva-module.md#disk-space-management-with-sink-nodes)yönelik destek eklendi.
* `MediaGraphGrpcExtension` düğüm artık tek bir gRPC sunucusu içinde birden çok AI modeli kullanmak için [Extensionconfiguration](grpc-extension-protocol.md) özelliğini destekliyor.
* [Prometheus biçiminde](https://prometheus.io/docs/practices/naming/)canlı video analizi modülü ölçümleri toplama desteği eklendi. [Azure izleyici 'de ölçümleri ve görünümü toplama](monitoring-logging.md#azure-monitor-collection-via-telegraf) hakkında daha fazla bilgi edinin. 
* Çıkış seçimini filtreleme özelliği eklendi. Herhangi bir grafik düğümüne yardım ile **yalnızca ses** veya **video** ya da video **ile videoları** doğrudan gönderebilirsiniz `outputSelectors` . 
* Çerçeve hızı filtre işlemcisi **kullanım dışıdır**.  
    * Çerçeve hızı yönetimi artık grafik uzantısı işlemci düğümlerinin içinde kullanılabilir.

### <a name="visual-studio-code-extension"></a>Visual Studio Code uzantısı
* LVA medya grafiklerini yönetmenize yardımcı olmak için IoT Edge bir Visual Studio Code uzantısında [canlı video analizi](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) yayınlandı. Bu uzantı **LVA 2,0 modülü** ile birlikte çalışarak, şık ve kullanımı kolay bir grafik arabirimiyle medya grafiklerini düzenleme ve yönetme olanağı sunar.
## <a name="september-22-2020"></a>22 Eylül 2020

Bu yayın etiketi, modülün Eylül 2020 yenilemesi için kullanılır:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> Hızlı başlangıç ve öğreticiler bölümünde, dağıtım bildirimleri 1 (canlı-video-analiz: 1) etiketini kullanır. Bu nedenle, bu tür bildirimleri yeniden dağıtmak Edge > cihazlarınızda modülü güncellemelidir.

### <a name="module-updates"></a>Modül güncelleştirmeleri

* Yeni bir Graf uzantısı düğümü olan [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) , bilişsel hizmetler 'Den [uzamsal analiz](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(Önizleme) modülüyle tümleştirilebilen şekilde kullanılabilir.
* Linux ARM64 cihazları için destek eklendi-bu tür cihazlara dağıtım için [el ile adımları](deploy-iot-edge-device.md) kullanın.

### <a name="documentation-updates"></a>Belge güncelleştirmeleri

* IoT Edge, Azure Stack Edge cihazlarındaki canlı video analizlerini kullanmaya yönelik [yönergeler](deploy-azure-stack-edge-how-to.md) sunulmaktadır.
* [Özel görüntü işleme hizmetini](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) kullanarak senaryoya özgü bilgisayar görme modelleri geliştirmeye ve gerçek zamanlı olarak [Canlı videoyu analiz](custom-vision-tutorial.md) etmeye yönelik yeni öğretici.

<hr width=100%>

## <a name="august-19-2020"></a>19 Ağustos 2020

Bu yayın etiketi, modülün Ağustos 2020 yenilemesi için kullanılır:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Hızlı başlangıç ve öğreticiler bölümünde, dağıtım bildirimleri 1 (canlı-video-analiz: 1) etiketini kullanır. Bu nedenle, bu tür bildirimleri yeniden dağıtmak Edge > cihazlarınızda modülü güncellemelidir.

### <a name="module-updates"></a>Modül güncelleştirmeleri

* Artık, gRPC çerçevesini kullanarak IoT Edge ve özel uzantınız üzerinde canlı video analizi arasında yüksek veri içeriği aktarım performansı alabilirsiniz. Kullanmaya başlamak için [hızlı başlangıç](analyze-live-video-use-your-grpc-model-quickstart.md) bölümüne bakın.
* Canlı video analizinin daha geniş bölgesel dağıtımı ve yalnızca bulut hizmeti güncelleştirilmiştir.  
* Canlı video analizi artık dünya genelinde 25 daha fazla bölgede kullanılabilir. Tüm kullanılabilir bölgelerin [listesi](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) aşağıda verilmiştir.  
* Hızlı [çalışmaya yönelik kurulum](https://aka.ms/lva-edge/setup-resources-for-samples) , yeni bölgelerin desteğiyle birlikte güncelleştirilmiştir.
    * Zaten kaynakları kuran herkes için eylem çağrısı yoktur

### <a name="bug-fixes"></a>Hata düzeltmeleri 

* Kurulum komut dosyasında kullanımdan kaldırılan bir Azure uzantısının kullanımını kaldırma

<hr width=100%>

## <a name="july-13-2020"></a>13 Temmuz 2020

Bu yayın etiketi, modülün Temmuz 2020 yenilemesi için kullanılır:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Hızlı başlangıç ve öğreticiler bölümünde, dağıtım bildirimleri 1 (canlı-video-analiz: 1) etiketini kullanır. Bu nedenle, bu tür bildirimleri yeniden dağıtmak Edge > cihazlarınızda modülü güncellemelidir.

### <a name="module-updates"></a>Modül güncelleştirmeleri

* Artık bir varlık havuzu düğümü ve bir sinyal kapısı işlemci düğümünün bir dosya havuzu düğümü aşağı akış olan Graf topolojileri oluşturabilirsiniz. Bir örnek için [topolojiyi](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) inceleyin.

### <a name="bug-fixes"></a>Hata düzeltmeleri

* İstenen özellikleri doğrulamaya yönelik iyileştirmeler

<hr width=100%>

## <a name="june-1-2020"></a>1 Haziran 2020

Bu sürüm, IoT Edge üzerindeki canlı video analizinin ilk genel önizleme sürümüdür. Yayın etiketi

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Desteklenen işlevler

* Seçtiğiniz AI modüllerini kullanarak canlı video akışlarını çözümleyin ve isteğe bağlı olarak, uç cihazında veya bulutta video kaydedin
* IoT Edge tarafından [desteklenen](../../iot-edge/support.md) Linux AMD64 işletim sistemlerinde kullanın
* IoT Hub Azure portal veya Visual Studio Code kullanarak modül dağıtın ve yapılandırın
* [Grafik topolojilerini ve grafik örneklerini](media-graph-concept.md#media-graph-topologies-and-instances) , aşağıdaki doğrudan Yöntem çağrıları aracılığıyla uzaktan veya yerel olarak yönetin

    *   Graphtopologyıget
    *   Graphtopologyıset
    *   Graphtopologyıdelete
    *   Graphtopologyılist
    *   Graphınstanceget
    *   Graphınstanceset
    *   Graphınstancedelete
    *   Graphınstancelist

## <a name="next-steps"></a>Sonraki adımlar

[Genel Bakış](overview.md)
