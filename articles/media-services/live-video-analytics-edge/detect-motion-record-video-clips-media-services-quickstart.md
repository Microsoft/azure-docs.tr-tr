---
title: Hareket algılama, video Azure Media Services kaydetme
description: Bu hızlı başlangıçta canlı video analizinin IoT Edge, canlı video akışındaki hareketleri tespit etmek ve video kliplerini Azure Media Services kaydetmek için nasıl kullanılacağı gösterilmektedir.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 135e68e2630d74dace6c3a6b70bb3666f77aad89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575543"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Hızlı başlangıç: hareketi algılama, videoyu Media Services kaydetme

Bu makalede, [olay tabanlı kayıt](event-based-video-recording-concept.md)için IoT Edge üzerinde canlı video analizlerini kullanma adımları gösterilmektedir. Azure 'da IoT Edge bir cihaz ve sanal bir canlı video akışı olarak bir Linux VM kullanır. Bu video akışı, taşınan nesnelerin varlığı için çözümlenir. Hareket algılandığında, olaylar Azure IoT Hub gönderilir ve video akışının ilgili bölümü Azure Media Services bir varlık olarak kaydedilir.

Bu makalede [Başlarken hızlı](get-started-detect-motion-emit-events-quickstart.md)başlangıcı hakkında daha fazla yer vardır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > Hizmet sorumluları oluşturma izinlerine sahip bir Azure aboneliğine ihtiyacınız olacak (**sahip rolü** bunu sağlar). Doğru izinleriniz yoksa, size doğru izinleri vermek için lütfen hesap yöneticinize ulaşın. 
* [Azure IoT araçları uzantısı](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)ile makinenizde [Visual Studio Code](https://code.visualstudio.com/) .
* Daha önce [Başlarken hızlı başlangıç](get-started-detect-motion-emit-events-quickstart.md) adımlarını tamamlamadıysanız, aşağıdaki adımları izleyin:
    * [Azure kaynakları ayarlama](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Modülleri dağıtma](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [Visual Studio Code’u yapılandırma](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)
    > [!TIP]
    > Oluşturulan Azure kaynaklarıyla ilgili sorunlar yaşıyorsanız, bazı sık karşılaşılan sorunları çözmek için lütfen **[sorun giderme kılavuzumuzu](troubleshoot-how-to.md#common-error-resolutions)** görüntüleyin.
## <a name="review-the-sample-video"></a>Örnek videoyu gözden geçirin

Azure kaynaklarını kurmak için yukarıdaki adımların bir parçası olarak, bir park lotunun (kısa) bir videosu IoT Edge cihaz olarak kullanılan Azure 'daki Linux VM 'sine kopyalanacaktır. Bu video dosyası, bu öğretici için canlı bir akışın benzetimini yapmak üzere kullanılacaktır.

[VLC oynatıcı](https://www.videolan.org/vlc/)gibi bir uygulamayı kullanabilir, başlatabilir, başlatabilir `Ctrl+N` ve [Park lotu video örneği](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) bağlantısını yapıştırarak kayıttan yürütmeyi başlatabilirsiniz. 5 saniyelik işaret hakkında bir beyaz araba, Park partisi üzerinden geçer.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Aşağıdaki adımları tamamladığınızda, arabasının bu hareketini saptamak için IoT Edge üzerinde canlı video analizi kullandınız ve 5 saniyelik bir işaret üzerinden başlayarak bir video klibini kaydedecaksınız. Aşağıdaki diyagram, genel akışın görsel gösterimidir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Hareket olaylarına göre kıymetlere olay tabanlı video kaydı":::

## <a name="use-direct-method-calls"></a>Doğrudan Yöntem çağrılarını kullanın

Doğrudan yöntemleri çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz. Modülün sunduğu tüm doğrudan yöntemleri anlamak için [IoT Edge üzerindeki canlı video analizlerine yönelik doğrudan yöntemleri](direct-methods.md) okuyun. 

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::

### <a name="invoke-graphtopologylist"></a>Graphtopologyılist komutunu çağır
Bu adım modüldeki tüm [grafik topolojilerini](media-graph-concept.md#media-graph-topologies-and-instances) numaralandırır.

1. "LvaEdge" modülüne sağ tıklayıp bağlam menüsünden "modül doğrudan yöntemini çağır" seçeneğini belirleyin.
1. Visual Studio Code penceresinin üst ortasında bir düzenleme kutusu açılır penceresi görürsünüz. Düzenle kutusuna "Graphtopologyılist" yazın ve ENTER tuşuna basın.
1. Sonra, aşağıdaki JSON yükünü kopyalayın ve düzenleme kutusuna yapıştırın ve ENTER tuşuna basın.
    
```
{
    "@apiVersion" : "2.0"
}
```

Birkaç saniye içinde, aşağıdaki Yanıt ile Visual Studio Code açılır penceresinde çıkış penceresini görürsünüz
    
```
[DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```
    
Grafik topolojisi oluşturulmadığından yukarıdaki yanıt beklenmez.

### <a name="invoke-graphtopologyset"></a>Graphtopologyıset komutunu çağır

Graphtopologyılist ' i çağırmak için özetlenen adımlarla aynı adımları kullanarak, yük olarak aşağıdaki JSON 'u kullanarak bir [Graph topolojisi](media-graph-concept.md#media-graph-topologies-and-instances) ayarlamak Için Graphtopologyıset komutunu çağırabilirsiniz. "EVRtoAssetsOnMotionDetection" adlı bir grafik topolojisi oluşturacaksınız.

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
            "name": "rtspUserName",
            "type": "String",
            "description": "rtsp source user name.",
            "default": "dummyUserName"
        },
        {
            "name": "rtspPassword",
            "type": "String",
            "description": "rtsp source password.",
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
        }                              
    ],         

      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "name": "motionDetection",
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
          "inputs": [
            {
              "nodeName": "motionDetection"
            }
          ]
        }
      ]
    }
}
```

Yukarıdaki JSON yükü, beş parametre (dördü varsayılan değere sahip) tanımlayan bir grafik topolojisi oluşturulmasına neden olur. Topolojide bir kaynak düğümü ([RTSP kaynağı](media-graph-concept.md#rtsp-source)), iki işlemci düğümü ([hareket algılama işlemcisi](media-graph-concept.md#motion-detection-processor) ve [sinyal kapısı işlemcisi](media-graph-concept.md#signal-gate-processor)ve iki havuz düğümü vardır (IoT Hub havuz ve [varlık havuzu](media-graph-concept.md#asset-sink)). Topolojinin görsel temsili yukarıda gösterilmektedir.

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt&quot;: &quot;2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default&quot;: &quot;iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Döndürülen durum 201 ' dir ve yeni bir Graph topolojisi oluşturulduğunu gösterir. Sonraki adımlar için aşağıdaki doğrudan yöntemleri deneyin:

* Graphtopologyıset komutunu yeniden çağırın ve döndürülen durum kodunun 200 olup olmadığını kontrol edin. Durum kodu 200, mevcut bir grafik topolojisinin başarıyla güncelleştirildiğini gösterir.
* Graphtopologyıset komutunu yeniden çağırın, ancak açıklama dizesini değiştirin. Yanıttaki durum kodunun 200 olup olmadığını ve açıklamanın yeni değere güncelleştirildiğinden emin olun.
* Önceki bölümde özetlenen Graphtopologyılist ' i çağırın ve bundan böyle döndürülen yükün "EVRtoAssetsOnMotionDetection" grafik topolojisini görmek istediğinizi kontrol edin.

### <a name="invoke-graphtopologyget"></a>Graphtopologyıget çağırma

Şimdi aşağıdaki yük ile Graphtopologyıget komutunu çağır
```

{
    "@apiVersion" : "2.0",
    "name&quot; : &quot;EVRtoAssetsOnMotionDetection"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt&quot;: &quot;2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default&quot;: &quot;iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Yanıt yükünde aşağıdaki özelliklere göz önünde edin:

* Durum kodu, başarıyı belirten 200 ' dir.
* Yükün "oluşturma" ve "lastModified" zaman damgası vardır.

### <a name="invoke-graphinstanceset"></a>Graphınstanceset 'i çağır

Ardından, yukarıdaki grafik topolojisine başvuran bir grafik örneği oluşturun. [Burada](media-graph-concept.md#media-graph-topologies-and-instances)açıklandığı gibi, grafik örnekleri aynı grafik topolojisine sahip birçok kameradan canlı video akışlarını analiz etmenize olanak tanır.

Şimdi aşağıdaki yük ile Graphınstanceset Direct metodunu çağırın:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Şunlara dikkat edin:

* Yukarıdaki yük, grafik örneğinin oluşturulması gereken Graf topolojisi adını (EVRtoAssetsOnMotionDetection) belirler.
* Yük, topoloji yükünde varsayılan bir değere sahip olmayan "rtspUrl" için parametre değeri içeriyor.

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görürsünüz:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt&quot;: &quot;2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Yanıt yükünde aşağıdaki özelliklere göz önünde edin:

* Durum kodu, yeni bir örneğin oluşturulduğunu belirten 201 ' dir.
* Durum, grafik örneğinin oluşturulduğunu ancak etkinleştirilmediğini gösteren "etkin değil" olarak belirlenir. Daha fazla bilgi için bkz. [medya grafiği](media-graph-concept.md) durumları.

Sonraki adımlar için aşağıdaki doğrudan yöntemleri deneyin:

* Aynı yük ile Graphınstanceset komutunu yeniden çağırın ve döndürülen durum kodunun artık 200 olduğunu unutmayın.
* Graphınstanceset komutunu tekrar çağırın, ancak farklı bir açıklama ile, yanıt yükünde güncelleştirilmiş açıklamayı, grafik örneğinin başarıyla güncelleştirildiğini belirten bir yere unutmayın.
* Graphınstanceset 'i çağırın, ancak adı "Sample-Graph-3" olarak değiştirin ve yanıt yükünü gözlemleyin. Yeni bir grafik örneği oluşturulduğunu (yani, durum kodu 201 olduğunu) unutmayın. Hızlı başlangıç ile işiniz bittiğinde bu tür yinelenen örnekleri temizlemeyi unutmayın.

### <a name="prepare-for-monitoring-events"></a>İzleme olaylarını hazırlama

Oluşturduğunuz medya grafiği, hareketi algılamak için hareket algılama işlemcisi düğümünü kullanır ve bu tür olaylar IoT Hub aktarılmalıdır. Bu tür olayları gözlemleyebilmek için aşağıdaki adımları izleyin

1. Visual Studio Code Gezgin bölmesini açın ve sol alt köşedeki Azure IoT Hub arayın.
1. Cihazlar düğümünü Genişlet
1. LVA-Sample-cihaza sağ tıklayın ve "yerleşik olay Izlemeyi Izlemeye başla" seçeneğini seçin.

    ![Yerleşik olay Izlemeyi Izlemeye başla](./media/quickstarts/start-monitoring-iothub-events.png)
    
    > [!NOTE]
    > IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    Saniyeler içinde çıkış penceresinde aşağıdaki iletileri görürsünüz:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>Graphınstanceactivate çağır

Şimdi, etkin video akışını modül aracılığıyla Başlatan grafik örneğini etkinleştirin. Aşağıdaki yük ile Graphınstanceactivate doğrudan metodunu çağırın:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Yanıt yükünde 200 durum kodu, grafik örneğinin başarıyla etkinleştirildiğini gösterir.

### <a name="invoke-graphinstanceget"></a>Graphınstanceget 'i çağır

Şimdi aşağıdaki yük ile Graphınstanceget doğrudan metodunu çağırın:

```
{
    "@apiVersion" : "2.0",
    "name&quot; : &quot;Sample-Graph-2"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt&quot;: &quot;2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Yanıt yükünde aşağıdaki özelliklere göz önünde edin:

* Durum kodu, başarıyı belirten 200 ' dir.
* Durum, grafik örneğinin Şu anda "etkin" durumda olduğunu gösteren "etkin" olarak belirlenir.

## <a name="observe-results"></a>Sonuçları gözlemleyin

Yukarıda oluşturduğunuz ve etkinleştirdiğiniz grafik örneği, gelen canlı video akışındaki hareketi algılamak ve IoT Hub havuza olay göndermek için hareket algılama işlemcisi düğümünü kullanır. Bu olaylar daha sonra IoT Hub, artık gözlemlenebilir. Çıkış penceresinde aşağıdaki iletiler görüntülenir

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143039375044290,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Yukarıdaki iletilerde aşağıdaki özelliklere göz önünde

* Her ileti bir "Body" bölümü ve bir "applicationProperties" bölümü içerir. Bu bölümlerin neyi temsil ettiğini anlamak için [IoT Hub oluştur ve oku](../../iot-hub/iot-hub-devguide-messages-construct.md)makalesini okuyun.
* İlk ileti, RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlantı kurup sunabilediğini belirten bir tanılama olayıdır ve (sanal) canlı bir akış almaya başlayabilir.
* ApplicationProperties 'teki "Subject" değeri, iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. Bu durumda, ileti RTSP kaynak düğümünden gönderilir.
* applicationProperties 'teki "eventType" bir tanılama olayı olduğunu gösterir.
* "eventTime" olayın gerçekleştiği saati gösterir.
* "gövde", tanılama olayı hakkında veri içerir-bu, [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) iletisidir.
* İkinci ileti bir analiz olayıdır. Videonun başlangıcı arasındaki gecikmeye karşılık gelen Mediasessionon iletisinden yaklaşık 5 saniye gönderildiğini ve araba park lotuna göre ne zaman bir süre sonra gönderileceğini kontrol edebilirsiniz.
* ApplicationProperties 'teki "Subject", bu iletiyi oluşturan grafikteki hareket algılama işlemcisi düğümüne başvurur
* Olay bir çıkarım olayıdır ve bu nedenle gövde "timestamp" ve "ınma" verilerini içerir.
* "ikinci dereceden" bölümü "tür" ın "Motion" olduğunu ve "Motion" olayı hakkında ek verilere sahip olduğunu gösterir.

Göreceğiniz sıradaki ileti aşağıdaki gibi olacaktır.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

* Üçüncü ileti, Işlemsel bir olaydır. Kaydı başlatmak için tetikleyici olarak işlem yapan hareket algılama iletisinden hemen sonra gönderilip gönderilmediğini kontrol edebilirsiniz.
* ApplicationProperties 'teki "Subject", bu iletiyi oluşturan grafikteki varlık havuzu düğümüne başvurur.
* Gövde, çıkış konumuyla ilgili bilgiler içerir, bu durumda Videonun kaydedildiği Azure Media Service varlığının adıdır. Bu değeri aklınızda olacak şekilde, daha sonra hızlı başlangıçta kullanacaksınız.

Topolojide, sinyal kapısı işlemci düğümü, 30 saniyelik etkinleştirme süreleriyle yapılandırılmıştır, bu da grafik topolojisinin, kıymete kabaca 30 saniyelik bir video olarak kaydedileceği anlamına gelir. Video kaydedilirken, hareket algılama işlemcisi düğümü, çıkış penceresinde görüntülenecek olan çıkarım olaylarını yaymasına devam edecektir. Bir süre sonra aşağıdaki iletiyi görürsünüz.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

* Bu ileti ayrıca Işlemsel bir olaydır. Recordınavailable olayı, oyuncuların/istemcilerin videonun kayıttan yürütmesini başlatması için varlığa yeterli miktarda veri yazıldığını gösterir
* ApplicationProperties 'teki "Subject", bu iletiyi oluşturan grafikteki varlık havuzu düğümüne başvurur
* Gövde, çıkış konumuyla ilgili bilgiler içerir, bu durumda Videonun kaydedildiği Azure Media Service varlığının adıdır.

Grafik örneğinin çalışmaya devam etmesine izin verirseniz bu iletiyi görürsünüz.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

* Bu ileti ayrıca Işlemsel bir olaydır. Recordingdurdurulan olay, kaydın durdurulduğunu belirtir.
* Kayıt sırasında, sinyal kapısı işlemci düğümündeki etkinleştirme sürelerinin değerleriyle eşleşen, kayıt işleminden itibaren yaklaşık 30 saniye geçtiğini unutmayın.
* ApplicationProperties 'teki "Subject", bu iletiyi oluşturan grafikteki varlık havuzu düğümüne başvurur.
* Gövde, çıkış konumuyla ilgili bilgiler içerir, bu durumda Videonun kaydedildiği Azure Media Service varlığının adıdır.

Grafik örneğinin çalışmaya devam etmesine izin verirseniz, RTSP simülatör, video dosyasının sonuna ulaşır ve Durdur/bağlantıyı keser. RTSP kaynak düğümü daha sonra benzeticide yeniden bağlanır ve işlem yinelenir.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Temizlemek için ek doğrudan Yöntem çağrıları çağırma

Şimdi, grafik örneğini devre dışı bırakmak ve silmek için doğrudan yöntemleri çağırın (Bu sırada).

### <a name="invoke-graphinstancedeactivate"></a>Graphınstancedeactivate öğesini çağır

Aşağıdaki yük ile Graphınstancedeactivate doğrudan metodunu çağırın:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu grafik örneğinin başarıyla devre dışı bırakıldığını gösterir.

Sonraki adımlar için aşağıdakileri deneyin:

* Önceki bölümlerde gösterildiği gibi Graphınstanceget komutunu çağırın ve "durum" değerini gözlemleyin.

### <a name="invoke-graphinstancedelete"></a>Graphınstancedelete 'i çağır

Aşağıdaki yük ile Graphınstancedelete doğrudan yöntemini çağır

```
{
    "@apiVersion" : "2.0",
    "name&quot; : &quot;Sample-Graph-2"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Yanıtta 200 durum kodu, grafik örneğinin başarıyla silindiğini gösterir.

### <a name="invoke-graphtopologydelete"></a>Graphtopologyıdelete komutunu çağır

Aşağıdaki yük ile GraphTopologyDelete doğrudan metodunu çağırın:

```
{
    "@apiVersion" : "2.0",
    "name&quot; : &quot;EVRtoAssetsOnMotionDetection"
}
```

Birkaç saniye içinde çıkış penceresinde aşağıdaki yanıtı görmeniz gerekir

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu, MediaGraph topolojisinin başarıyla silindiğini gösterir.

Sonraki adımlar için aşağıdaki doğrudan yöntemleri deneyin:

* Graphtopologyılist ' i çağırın ve modülde hiç grafik topolojisi olmadığını gözlemleyin.
* Graphtopologyılist ile aynı yük ile Graphınstancelist öğesini çağırın ve bir grafik örneği numaralandırılmıyor olduğunu gözlemleyin.

## <a name="playing-back-the-recorded-video"></a>Kaydedilen videoyu kayıttan yürütme

Sonra, kaydettiğiniz videoyu oynatmak için Azure portal kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)oturum açın, arama kutusuna "Media Services" yazın.
1. Azure Media Services hesabınızı bulun ve açın.
1. Media Services listesinde varlıklar girişini bulun ve seçin.

    ![Media Services listesinde varlıklar girişi](./media/quickstarts/asset-entity.png)
1. Bu hızlı başlangıç Azure Media Services ilk kullanım ise, yalnızca bu hızlı başlangıçta oluşturulan varlıklar listelenir ve en eskbirini seçebilirsiniz.
1. Aksi takdirde, yukarıdaki Işletimsel olaylarda outputLocation olarak verilen varlığın adını kullanın.
1. Açılan ayrıntılar sayfasında, akış URL 'SI metin kutusunun hemen altındaki "Yeni oluştur" bağlantısına tıklayın.

    ![Akış URL 'SI](./media/quickstarts/asset-streaming-url.png)
1. "Akış Bulucu Ekle" için açılan bölmede, Varsayılanları kabul edin ve alt kısımdaki "Ekle" düğmesine basın.
1. Varlık ayrıntıları sayfasında, video oynatıcı artık videonun ilk çerçevesine yüklenmelidir ve Oynat düğmesine basabilirsiniz. Videonun Park lotuna hareket ettiği bir kısmını gördüğünüzden emin olun.

    ![Oynama](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Grafiği etkinleştirdikten sonra sanal canlı video başladıktan sonra, gün saati değerleri ilgili değildir ve bu oynatıcı kısayolu aracılığıyla gösterilmez. Sürekli video kaydı ve kayıttan yürütme öğreticisi, zaman damgalarını nasıl kullanabileceğinizi gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekecekseniz, bu hızlı başlangıçta oluşturulan kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

* IoT Edge [doğrudan yöntemlerle](direct-methods.md) canlı video analizlerinin nasıl çağıralınacağını öğrenin.
* Tanılama iletileri hakkında daha fazla bilgi edinin.    
