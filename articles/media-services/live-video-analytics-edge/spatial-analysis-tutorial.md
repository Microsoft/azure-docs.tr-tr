---
title: Uzamsal analiz için Görüntü İşleme ile canlı videoyu çözümleme-Azure
description: Bu öğreticide, canlı video analizinin Azure bilişsel hizmetler 'deki Görüntü İşleme uzamsal analiz AI özelliği ile birlikte nasıl kullanılacağı gösterilmektedir. Bu, (benzetimli) bir IP kamerasından canlı video akışını analiz edebilir.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 1c3fd411e60cabb504f01bece62d497ddeea31d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561024"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Uzamsal analizler için Görüntü İşleme ile canlı videoyu çözümleme (Önizleme)

Bu öğreticide, canlı video analizinin Azure bilişsel [Hizmetler 'deki uzamsal ANALYSIS AI hizmeti için görüntü işleme](https://azure.microsoft.com/services/cognitive-services/computer-vision/) ile birlikte nasıl kullanılacağı gösterilmektedir. Bu, (benzetimli) bir IP kamerasından canlı video akışını analiz edebilir. Bu çıkarım sunucusunun, fiziksel alanda insanlar ve taşıma arasındaki uzamsal ilişkileri anlamak için akış videosunu analiz etmenizi nasıl sağladığını göreceksiniz.  Video akışındaki çerçevelerin bir alt kümesi bu çıkarım sunucusuna gönderilir ve sonuçlar IoT Edge hub 'a gönderilir ve bazı koşullar karşılandığında, video klipleri Azure Media Services varlık olarak kaydedilir ve saklanır.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Kaynakları ayarlayın.
> * Kodu inceleyin.
> * Örnek kodu çalıştırın.
> * Olayları izleme.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Hizmet sorumluları oluşturma izinlerine sahip bir Azure aboneliğine ihtiyacınız olacak (**sahip rolü** bunu sağlar). Doğru izinleriniz yoksa, size doğru izinleri vermek için lütfen hesap yöneticinize ulaşın. 
## <a name="suggested-pre-reading"></a>Önerilen önceden okuma

Başlamadan önce şu makaleleri okuyun:

* [IoT Edge genel bakış üzerinde canlı video analizi](overview.md)
* [IoT Edge terminolojisinde canlı video analizi](terminology.md)
* [Medya grafiği kavramları](media-graph-concept.md)
* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)
* [Öğretici: IoT Edge modülünü geliştirme](../../iot-edge/tutorial-develop-for-linux.md)
* [Azure Stack Edge üzerinde canlı video analizi dağıtma](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Önkoşullar

Aşağıda, uzamsal analiz modülünü canlı video analizi modülüne bağlama önkoşulları verilmiştir.

* Geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) . [Azure IoT araçları uzantısına](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)sahip olduğunuzdan emin olun.
    * Geliştirme makinenizin bağlı olduğu ağın bağlantı noktası 5671 üzerinden gelişmiş Ileti kuyruğu protokolüne izin vermek için bağlı olduğundan emin olun. Bu kurulum, Azure IoT araçlarının Azure IoT Hub ile iletişim kurmasını sağlar.
* GPU hızlandırmalı [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) .  
    Azure Stack Edge 'i GPU hızlandırmalı kullanmanızı öneririz, ancak kapsayıcı, [NVIDIA Tesla T4 GPU 'su](https://www.nvidia.com/en-us/data-center/tesla-t4/)olan diğer tüm cihazlarda çalışır. 
* Uzamsal analiz için Azure bilişsel [hizmet görüntü işleme kapsayıcısı](https://azure.microsoft.com/services/cognitive-services/computer-vision/) .  
    Bu kapsayıcıyı kullanabilmeniz için, ilişkili **API anahtarını** ve bir **uç nokta URI 'sini** almak için bir görüntü işleme kaynağınız olması gerekir. API anahtarı Azure portal Görüntü İşleme genel bakış ve anahtarlar sayfalarında kullanılabilir. Kapsayıcıyı başlatmak için anahtar ve uç nokta gereklidir.

## <a name="overview"></a>Genel Bakış

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Uzamsal Analize genel bakış":::
 
Bu diyagramda, sinyallerin Bu öğreticide nasıl akagösterdiği gösterilmektedir. [Edge modülü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , Real-Time Akış Protokolü (RTSP) sunucusunu BARıNDıRAN bir IP kamerasına benzetir. Bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü, bu sunucudan video akışını çeker ve işlemci düğümüne video çerçeveleri gönderir `MediaGraphCognitiveServicesVisionExtension` .

MediaGraphCognitiveServicesVisionExtension düğümü bir ara sunucu rolünü yürütür. Video çerçevelerini belirtilen görüntü türüne dönüştürür. Ardından, görüntüyü **paylaşılan bellek** üzerine bir GRPC uç noktasının ARKASıNDA bulunan AI işlemlerini çalıştıran başka bir uç modüle geçirir. Bu örnekte, bu Edge modülü, uzamsal analiz modülüdür. MediaGraphCognitiveServicesVisionExtension işlemci düğümü iki şey yapar:

* Sonuçları toplar ve olayları [IoT Hub havuz](media-graph-concept.md#iot-hub-message-sink) düğümüne yayımlar. Düğüm daha sonra bu olayları [IoT Edge hub 'ına](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)gönderir. 
* Ayrıca, [sinyal kapısı işlemcisi](media-graph-concept.md#signal-gate-processor) kullanarak RTSP kaynağından 30 saniyelik bir video klibi yakalar ve bunu bir Media Services varlığı olarak depolar.

## <a name="create-the-computer-vision-resource"></a>Görüntü İşleme kaynağı oluşturma

[Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) ya da Azure clı aracılığıyla görüntü işleme türünde bir Azure kaynağı oluşturmanız gerekir. Kapsayıcıya erişim isteğiniz onaylandıktan ve Azure abonelik KIMLIĞINIZ kaydettirildikten sonra kaynağı oluşturabilirsiniz... https://aka.ms/csgateKullanım durumunu ve Azure ABONELIK kimliğinizi göndermek için adresine gidin.  Azure kaynağını, erişim Isteği formunda sağlanmış olan Azure aboneliğini kullanarak oluşturmanız gerekir.

### <a name="gathering-required-parameters"></a>Gerekli parametreler toplanıyor

Uzamsal analiz kapsayıcısı dahil olmak üzere, gereken tüm bilişsel hizmetler kapsayıcıları için üç birincil parametre vardır. Son Kullanıcı Lisans Sözleşmesi 'nin (EULA) kabul et değeriyle mevcut olması gerekir. Ayrıca, hem bir uç nokta URI 'si hem de API anahtarı gereklidir.

### <a name="keys-and-endpoint-uri"></a>Anahtarlar ve uç nokta URI 'SI

Bir anahtar, uzamsal analiz kapsayıcısını başlatmak için kullanılır ve ilgili bilişsel `Keys and Endpoint` hizmet kaynağının Azure Portal sayfasında kullanılabilir. Bu sayfaya gidin ve anahtarları ve uç nokta URI 'sini bulun.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Uç nokta URI 'SI":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack kenarını ayarlama

Azure Stack kenarını ayarlamak için aşağıdaki [adımları](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) Izleyin ve canlı video analizlerini ve uzamsal analiz modüllerini dağıtmak için aşağıdaki adımları izlemeye devam edin.

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

1. Depoyu bu konumdan kopyala: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Visual Studio Code, deponun indirildiği klasörü açın.
1. Visual Studio Code, src/buluttan cihaza-Console-App klasörüne gidin. Burada bir dosya oluşturun ve appsettings.js. Bu dosya, programı çalıştırmak için gereken ayarları içerir.
1. Aşağıdaki adımları izleyerek Azure Stack kenarından ıothubconnectionstring ' i alın:

    * Azure portal IoT Hub gidin ve sol gezinti bölmesinde açık ' a tıklayın `Shared access policies` .
    * `iothubowner`Paylaşılan erişim anahtarlarını al ' a tıklayın.
    * Öğesini kopyalayın  `Connection String – primary key` ve VSCode 'daki giriş kutusuna yapıştırın.
    
        Bağlantı dizesi şöyle görünür: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Aşağıdaki içeriği dosyaya kopyalayın. Değişkenleri değiştirdiğinizden emin olun.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Src/Edge klasörüne gidin ve. env adlı bir dosya oluşturun.
1. /CloudDrive/LVA-Sample/Edge-Deployment/-env dosyasının içeriğini kopyalayın. Metin aşağıdaki kod gibi görünmelidir.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Dağıtım şablonu ayarlama  

Üzerinde/src/Edge/deployment.spatialAnalysis.template.jsdağıtım dosyasını arayın. Şablondan, lvaEdge modülü, rtspsım modülü ve uzamsal analiz modülümüzdür.

Dağıtım şablonu dosyasında dikkat etmeniz gereken birkaç nokta vardır:

1. Bağlantı noktası bağlamayı ayarlayın.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` lvaEdge ve uzamsal analiz modülü createOptions içinde aynı olmalıdır ve konak olarak ayarlanmalıdır.
1. RTSP benzeticisinin çalışması için Birim sınırlarını oluşturduğunuzdan emin olun. Daha fazla bilgi için bkz. [Docker Volume takmaları kurma](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [SMB paylaşımıyla bağlantı kurmak için](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) [örnek Bulldozer video dosyasını](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) yerel paylaşıma kopyalayın.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Rtspsim modülünün aşağıdaki yapılandırmaya sahip olduğunu görün:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Dağıtım bildirimini oluşturma ve dağıtma

Dağıtım bildirimi, bir sınır cihazına hangi modüllerin dağıtıldığını tanımlar. Ayrıca, bu modüllerle ilgili yapılandırma ayarlarını tanımlar.

Şablon dosyasından bildirim oluşturmak ve ardından Edge cihazına dağıtmak için bu adımları izleyin.

1. Visual Studio Code’u açın.
1. AZURE ıOT HUB bölmesinin yanındaki IoT Hub bağlantı dizesini ayarlamak için diğer Eylemler simgesini seçin. Dizeyi `src/cloud-to-device-console-app/appsettings.json` dosyadan kopyalayabilirsiniz.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Uzamsal analiz: bağlantı dizesi":::
1. Sağ tıklayın `src/edge/deployment.spatialAnalysis.template.json` ve IoT Edge dağıtım bildirimi oluştur ' u seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Uzamsal analiz: dağıtım AMD64 JSON":::
    
    Bu eylem, src/Edge/config klasöründe deployment.amd64.jsadlı bir bildirim dosyası oluşturmalı.
1. Sağ tıklayın `src/edge/config/deployment.spatialAnalysis.amd64.json` , tek cihaz Için dağıtım oluştur ' u seçin ve ardından Edge cihazınızın adını seçin.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Uzamsal analiz: dağıtım şablonu JSON":::   
1. IoT Hub bir cihaz seçmeniz istendiğinde, açılır menüden Azure Stack Edge adını seçin.
1. 30 saniye sonra, pencerenin sol alt köşesinde Azure IoT Hub ' yi yenileyin. Edge cihazında artık aşağıdaki dağıtılan modüller gösterilmektedir:
    
    * IoT Edge (modül adı lvaEdge) üzerinde canlı video analizi.
    * Real-Time streaming Protocol (RTSP) Simülatörü (modül adı rtspsım).
    * Uzamsal analiz (modül adı spatialAnalysis).
    
Başarıyla dağıtırsanız, çıktıda aşağıdakine benzer bir ileti olacaktır:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Sonra `lvaEdge` `rtspsim` `spatialAnalysis` `rtspsim` cihazlar/modüller altında, ve modüllerini bulabilir ve bunların durumu "çalışıyor" olmalıdır.

## <a name="prepare-to-monitor-events"></a>Olayları izlemeye hazırlanma

Bu olayları görmek için şu adımları izleyin:

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. Gezgin bölmesini açın ve sol alt köşedeki Azure IoT Hub arayın.
1. Cihazlar düğümünü genişletin.
1. Azure Stack Kenarıza sağ tıklayıp Izlemeyi Başlat yerleşik olay uç noktasını seçin.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Uzamsal analiz: izlemeyi Başlat":::
     
## <a name="run-the-program"></a>Programı çalıştırma

Src/buluttan cihaza-Console-App/operations.jsüzerinde doğrudan yöntemleri çağırabilecek bir program. cs vardır. operations.jskurulum ve medya Graf kullanımı için bir topoloji sağlaması gerekiyor.  

operations.js:

* Topolojiyi şunun gibi ayarlayın:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Bu şekilde bir grafik örneği oluşturun, topoloji içindeki parametreleri buradan ayarlayın:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

>[!Note]
Uzamsal analiz modülüyle bağlantı kurmak için MediaGraphRealTimeComputerVisionExtension kullanımını inceleyin. $ {Grpkıvrık} öğesini **TCP://spatialAnalysis: <PORT_NUMBER>** olarak ayarlayın, ör. TCP://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Bir hata ayıklama oturumu çalıştırın ve **TERMINAL** yönergelerini izleyin, topoloji ayarlar, grafik örneğini ayarlar, grafik örneğini etkinleştirir ve son olarak kaynakları siler.

## <a name="interpret-results"></a>Sonuçları yorumlama

Bir medya grafiği örneği oluşturulduğunda, "Mediasessionkurulduğu" olayını, burada bir [örnek Mediasessionkurulu olayını](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)görmeniz gerekir.

Uzamsal analiz modülü Ayrıca, canlı video analizlerine AI Insight olayları gönderir ve ardından ıothub ' e, **çıktıda** da gösterilir. VARLıK, algılama nesneleri ve olay spaceanalytics olaylardır. Bu çıktı, canlı video analizine geçirilecek.

PersonZoneEvent için örnek çıkış (biliveservices. Vision. spatialanalysis-personcrossingçokgen. livevideoanalytics işlemi):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

`spatialAnalysis`Dağıtım bildirimi dosyanızın grafik düğümündeki "etkin" bayrağını değiştirerek, modülün, **PersonCount** ve **persondistance** gibi sunduğu farklı işlemleri deneyin.
>[!Tip]
> Çerçevede birden fazla kişi bulunan [örnek bir video dosyası](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) kullanın.

> [!NOTE]
> Tek seferde yalnızca bir işlem çalıştırabilirsiniz. Bu nedenle, lütfen yalnızca bir bayrağın **true** , diğerleri **ise false** olarak ayarlandığından emin olun.