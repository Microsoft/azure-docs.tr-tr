---
title: 1,0 ' den 2,0 ' e IoT Edge canlı video analizlerini yükseltme
description: Bu makalede, Azure IoT Edge modülündeki canlı video analizi (LVA) yükseltilirken dikkate alınması gereken farklar ve farklı şeyler ele alınmaktadır.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955658"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>1,0 ' den 2,0 ' e IoT Edge canlı video analizlerini yükseltme

Bu makalede, Azure IoT Edge modülündeki canlı video analizi (LVA) yükseltilirken dikkate alınması gereken farklar ve farklı şeyler ele alınmaktadır.

## <a name="change-list"></a>Değişiklik listesi

> [!div class="mx-tdCol4BreakAll"]
> |Başlık|Canlı video analizi 1,0|Canlı video analizi 2,0|Description|
> |-------------|----------|---------|---------|
> |Kapsayıcı görüntüsü|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Azure IoT Edge üzerinde canlı video analizi için Microsoft tarafından yayınlanan Docker görüntüleri|
> |**MediaGraph düğümleri** |    |   |   |
> |Kaynaklar|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP kaynağı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub Ileti kaynağı |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP kaynağı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub Ileti kaynağı | Medya alımı ve iletileri için kaynak olarak davranan MediaGraph düğümleri.|
> |İşlemciler|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket algılama işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Çerçeve hızı filtre işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http uzantısı işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: GRPC uzantı işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sinyal kapısı işlemcisi |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket algılama işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Çerçeve hızı filtre işlemcisi**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http uzantısı işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: GRPC uzantı işlemcisi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sinyal kapısı işlemcisi | AI çıkarımı sunucularına göndermeden önce medyayı biçimlendirmeniz sağlayan MediaGraph düğümleri.|
> |Yapma|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Varlık havuzu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dosya havuzu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub ileti havuzu|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Varlık havuzu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dosya havuzu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub ileti havuzu| İşlenen medyayı depolamanızı sağlayan MediaGraph düğümleri.|
> |**Desteklenen Mediagraf grafikleri** |    |   |   |
> |Topolojiler|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi ve sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dış analiz ve sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket halindeyken olay tabanlı kayıt </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI üzerinde olay tabanlı kayıt</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dış olayda olay tabanlı kayıt </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi ve ardından AI ınkrileme |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi ve sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dış analiz ve sürekli video kaydı </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket halindeyken olay tabanlı kayıt </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI üzerinde olay tabanlı kayıt</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Dış olayda olay tabanlı kayıt </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Hareket Analizi ve ardından AI ınkrileme </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI kompozisyonu** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Ses ve video kaydı** </br>  | Bir grafiğin şema türünü, değerler için yer tutucu olarak parametrelerle tanımlamanızı sağlayan MediaGraph topolojileri.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>IoT Edge modülündeki canlı video analizlerini 1,0 ' den 2,0 ' e yükseltme
IoT Edge modülündeki canlı video analizlerini yükseltirken, aşağıdaki bilgileri güncelleştirdiğinizden emin olun.
### <a name="container-image"></a>Kapsayıcı görüntüsü
Dağıtım şablonunuzda, düğüm altındaki IoT Edge modülünde canlı video analizinizi bulun `modules` ve `image` alanı şu şekilde güncelleştirin:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> IoT Edge modülündeki canlı video analizinin adını değiştirmediyse, `lvaEdge` Modül düğümünün altında öğesine bakın.

### <a name="topology-file-changes"></a>Topoloji dosyası değişiklikleri
Topoloji dosyalarınızda, **`apiVersion`** 2,0 olarak ayarlandığından emin olun

### <a name="mediagraph-node-changes"></a>MediaGraph düğüm değişiklikleri


* Kamera kaynağınızdan alınan ses, artık video ile birlikte geçirilebilir. Herhangi bir grafik düğümüne yardım ile **yalnızca ses** veya **video** ya da video **ile videoları** doğrudan gönderebilirsiniz **`outputSelectors`** . Örneğin, yalnızca RTSP kaynağından video seçip aşağı akış olarak geçiş yapmak istiyorsanız, RTSP kaynak düğümüne aşağıdakileri ekleyin:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>, **`outputSelectors`** İsteğe bağlı bir özelliktir. Bu kullanılmazsa, medya grafiği sesi (etkinse) ve RTSP kamera aşağı akış üzerinden iletir. 

* `MediaGraphHttpExtension`Ve `MediaGraphGrpcExtension` işlemcilerde, aşağıdaki değişiklikleri aklınızda:  
    #### <a name="image-properties"></a>Görüntü özellikleri
    * `MediaGraphImageFormatEncoded` artık desteklenmiyor. 
      * Bunun yerine, **`MediaGraphImageFormatBmp`** veya **`MediaGraphImageFormatJpeg`** kullanın **`MediaGraphImageFormatPng`** . Örneğin,
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * HAM görüntüleri kullanmak istiyorsanız kullanın **`MediaGraphImageFormatRaw`** . Bunu kullanmak için görüntü düğümünü şu şekilde güncelleştirin:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Olası pixelFormat değerleri şunlardır: `yuv420p` , `rgb565be` ,, `rgb565le` `rgb555be` , `rgb555le` , `rgb24` , `bgr24` , `argb` , `rgba` , `abgr` , `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>GRPC uzantı işlemcisi için extensionConfiguration  
    * `MediaGraphGrpcExtension`İşlemcide, **`extensionConfiguration`** GRPC sözleşmesinin bir parçası olarak kullanılabilen isteğe bağlı bir dize olan adlı yeni bir özellik kullanılabilir. Bu alan, çıkarım sunucusuna herhangi bir veri geçirmek için kullanılabilir ve çıkarım sunucusunun bu verileri nasıl kullandığını tanımlayabilirsiniz.  
    Tek bir çıkarım sunucusunda paketlenmiş birden çok AI modeli olduğunda bu özelliğin bir kullanım durumu vardır. Bu özellikle, her AI modeli için bir düğüm kullanıma almanız gerekmez. Bunun yerine, bir uzantı sağlayıcısı olarak bir grafik örneği için, özelliği kullanarak farklı AI modellerinin nasıl Seçileni tanımlayabilir **`extensionConfiguration`** ve yürütme sırasında LVA, bu dizeyi Istenen AI modelini çağırmak için kullanabilir.  

    #### <a name="ai-composition"></a>AI kompozisyonu
    * Canlı video analizi 2,0 artık bir topoloji içinde birden fazla medya Graf uzantısı işlemcisi kullanmayı desteklemektedir. RTSP kamerasından medya çerçevelerini farklı AI modellerine, paralel veya her ikisinin birleşimine geçirebilirsiniz. Lütfen sıralı olarak kullanılan iki AI modelini gösteren örnek topolojiye bakın.

### <a name="disk-space-management-with-sink-nodes"></a>Havuz düğümleri ile disk alanı yönetimi
* **Dosya havuzu** Düğümünüzün artık IoT Edge modülündeki canlı video analizinin işlenen görüntüleri depolamak için ne kadar disk alanı kullandığını belirtebilirsiniz. Bunu yapmak için, **`maximumSizeMiB`** alanı FileSink düğümüne ekleyin. Örnek bir dosya havuzu düğümü aşağıdaki gibidir:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* **Varlık havuzu** düğümünüzün IoT Edge modülündeki canlı video analizinin işlenen görüntüleri depolamak için ne kadar disk alanı kullandığını belirtebilirsiniz. Bunu yapmak için **`localMediaCacheMaximumSizeMiB`** varlık havuzu düğümüne alanını ekleyin. Örnek varlık havuzu düğümü aşağıdaki gibidir:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  **Dosya havuzu** yolu temel dizin yolu ve dosya adı düzenine bölünür, ancak **varlık havuz** yolu temel dizin yolunu içerir.  

### <a name="frame-rate-management"></a>Kare hızı yönetimi
* **`MediaGraphFrameRateFilterProcessor`****IoT Edge 2,0 modülündeki canlı video analizinden** kullanım dışıdır.
    * İşlenmek üzere gelen videoyu örneklemek için, **`samplingOptions`** özelliği MediaGraph uzantı işlemcilere ( `MediaGraphHttpExtension` veya `MediaGraphGrpcExtension` ) ekleyin  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Telegraf kullanarak Prometheus biçimindeki modül ölçümleri
Bu sürümde, telegraf Azure Izleyici 'ye ölçüm göndermek için kullanılabilir. Burada ölçümler Log Analytics veya bir olay hub 'ına yönlendirilebilir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Olayların taksonomisi":::

Docker 'ı kullanarak özel bir yapılandırmaya sahip telegraf görüntüsünü kolayca oluşturabilirsiniz. [İzleme ve günlüğe kaydetme](monitoring-logging.md#azure-monitor-collection-via-telegraf) sayfasında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge 'da canlı video analiziyle çalışmaya başlama](get-started-detect-motion-emit-events-quickstart.md)