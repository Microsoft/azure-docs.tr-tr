---
title: İzleme ve günlüğe kaydetme-Azure
description: Bu makalede, IoT Edge üzerindeki canlı video analizlerinde izleme ve günlüğe kaydetme konusunda genel bir bakış sunulmaktadır.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564492"
---
# <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

Bu makalede, IoT Edge modülündeki canlı video analizinden uzaktan izleme olaylarının nasıl alınacağını öğreneceksiniz. 

Ayrıca, modülün oluşturduğu günlükleri nasıl denetleyeceğinizi de öğreneceksiniz.

## <a name="taxonomy-of-events"></a>Olayların taksonomisi

Canlı video analizi, aşağıdaki sınıflandırmaya göre olayları veya telemetri verilerini IoT Edge yayar:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Olay taksonomini gösteren diyagram.":::

* İşletimsel: bir kullanıcının eylemleri veya bir [medya grafiğinin](media-graph-concept.md) yürütülmesi sırasında oluşturulan olaylar
   
   * Birim: düşük olması bekleniyordu (birkaç dakika, hatta daha az)
   * Örnekler:

      - Kayıt başlatıldı (aşağıdaki örnekte gösterildiği gibi)
      - Kayıt durduruldu
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Tanılama: performans sorunlarını tanılamaya yardımcı olan olaylar

   * Birim: yüksek (birkaç kez bir dakika) olabilir
   * Örnekler:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) bilgileri (aşağıdaki örnekte gösterilen) 
      - Gelen video akışındaki boşluklar

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: video analizinin bir parçası olarak oluşturulan olaylar

   * Birim: yüksek (birkaç dakika veya daha fazla) olabilir
   * Örnekler:
      
      - Hareket algılandı (aşağıdaki örnekte gösterildiği gibi) 
      - Çıkarım sonucu

   ```      
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
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Modül tarafından yayılan olaylar [IoT Edge hub 'ına](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)gönderilir. Bunlar, diğer hedeflere yönlendirilebilir. 

### <a name="timestamps-in-analytic-events"></a>Analitik olaylardaki zaman damgaları

Daha önce belirtildiği gibi, video analizinin bir parçası olarak oluşturulan olaylar, bunlarla ilişkili zaman damgalarına sahiptir. Canlı videoyu grafik topolojinizin bir parçası olarak [kaydettiyse](video-recording-concept.md) , bu zaman damgaları kayıtlı videoda belirli bir olayın gerçekleştiği yeri bulmanıza yardımcı olur. Aşağıda, bir analitik olaydaki zaman damgasının [Azure Media Services bir varlık](terminology.md#asset)olarak kaydedilen videonun zaman çizelgesine nasıl eşleneceğini gösteren yönergeler verilmiştir.

İlk olarak, `eventTime` değeri ayıklayın. Kaydın uygun bir bölümünü almak için bu değeri bir [zaman aralığı filtresinde](playback-recordings-how-to.md#time-range-filters) kullanın. Örneğin, 30 saniye önce başlayan `eventTime` ve sonrasında 30 saniye biten videoları almak isteyebilirsiniz. Önceki örnekte, burada `eventTime` 2020-05-12T23:33:09.381 z, her ne kadar 30 saniye önce ve sonrasında BIR HLS bildirimi isteği `eventTime` Bu istek gibi görünür:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Yukarıdaki URL, medya çalma listeleri için URL 'Leri içeren bir [ana çalma listesi](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) döndürür. Medya çalma listesi şöyle bir giriş içerir:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Yukarıdaki giriş, bir değeri ile başlayan bir video parçasının kullanılabilir olduğunu bildirir `timestamp` `143039375031270` . `timestamp`Analitik olaydaki değer, medya çalma listesiyle aynı zaman ölçeğini kullanır. İlgili video parçasını tanımlamak ve doğru çerçeveye arama yapmak için kullanılabilir.

Daha fazla bilgi için bkz. HLS 'de [çerçeveye doğru arama hakkında bu makalelere](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) bakın.

## <a name="controlling-events"></a>Olayları denetleme

IoT Edge modülündeki canlı video analizi tarafından yayımlanan işletimsel ve Tanılama olaylarını denetlemek için aşağıdaki Module ikizi özelliklerini kullanabilirsiniz. Bu özellikler [module IKIZI JSON şemasında](module-twin-configuration-schema.md)belgelenmiştir.

- `diagnosticsEventsOutputName`: Modülden Tanılama olaylarını almak için bu özelliği ekleyin ve bunun için herhangi bir değer sağlayın. Modülün Tanılama olaylarını yayımlamasını durdurmak için bunu atlayın veya boş bırakın.
   
- `operationalEventsOutputName`: Modülden işlem olaylarını almak için bu özelliği ekleyin ve bunun için herhangi bir değer sağlayın. Modülün işlemsel olayları yayımlamasına izin vermek için bunu atlayın veya boş bırakın.
   
Analiz olayları, hareket algılama işlemcisi veya HTTP uzantısı işlemcisi gibi düğümler tarafından oluşturulur. IoT Hub 'ı havuzu, IoT Edge hub 'ına göndermek için kullanılır. 

Dağıtım bildiriminde ikizi modülünün özelliğini kullanarak [önceki tüm olayların yönlendirilmesini](../../iot-edge/module-composition.md#declare-routes) denetleyebilirsiniz `desired` `$edgeHub` :

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Önceki JSON 'da, `lvaEdge` IoT Edge modülündeki canlı video analizinin adıdır. Yönlendirme kuralı, [bildirimi rotalar](../../iot-edge/module-composition.md#declare-routes)bölümünde tanımlanan şemayı izler.

> [!NOTE]
> Analiz olaylarının IoT Edge hub 'ına ulaşmasını sağlamak için, herhangi bir hareket algılama işlemcisi düğümü ve/veya herhangi bir HTTP uzantısı işlemci düğümü için bir IoT Hub havuz düğümü aşağı akış olması gerekir.

## <a name="event-schema"></a>Olay şeması

Olaylar Edge cihazında olur ve kenarda veya bulutta tüketilebilir. IoT Edge canlı video analizi tarafından oluşturulan olaylar, Azure IoT Hub tarafından oluşturulan [akış mesajlaşma düzenine](../../iot-hub/iot-hub-devguide-messages-construct.md) uygun. Bu model, sistem özellikleri, uygulama özellikleri ve bir gövdeden oluşur.

### <a name="summary"></a>Özet

IoT Hub aracılığıyla gözlemlendiği her olayın ortak özellikler kümesi vardır:

|Özellik   |Özellik türü| Veri türü   |Açıklama|
|---|---|---|---|
|`message-id`   |sistemin |guid|  Benzersiz olay KIMLIĞI.|
|`topic`|   applicationProperty |string|    Azure Media Services hesabının yolunu Azure Resource Manager.|
|`subject`| applicationProperty |string|    Olayı yayan varlığın alt yolu.|
|`eventTime`|   applicationProperty|    string| Olayın oluşturulduğu zaman.|
|`eventType`|   applicationProperty |string|    Olay türü tanımlayıcısı. (Aşağıdaki bölüme bakın.)|
|`body`|body    |object|    Belirli olay verileri.|
|`dataVersion`  |applicationProperty|   string  |{Birincil}. Bazı|

### <a name="properties"></a>Özellikler

#### <a name="message-id"></a>ileti kimliği

Olay için bir genel benzersiz tanımlayıcı (GUID).

#### <a name="topic"></a>konu başlığı

Grafikle ilişkili Azure Media Services hesabını temsil eder.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Olayı yayan varlık:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject`Özelliği, genel olayları oluşturma modülüne eşlemenizi sağlar. Örneğin, geçersiz bir RTSP Kullanıcı adı veya parolası için, oluşturulan olay `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` düğümde olur.

#### <a name="event-types"></a>Olay türleri

Olay türleri, bu şemaya göre bir ad alanına atanır:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Olay sınıfları

|Sınıf adı|Description|
|---|---|
|Analiz  |İçerik analizinin bir parçası olarak oluşturulan olaylar.|
|Tanılama    |Sorunların ve performansın tanılanmasına yardımcı olan olaylar.|
|Operasyonel    |Kaynak işleminin bir parçası olarak oluşturulan olaylar.|

Olay türleri her bir olay sınıfına özeldir.

Örnekler:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Etkinlik saati

Olay saati bir ISO 8601 dizesinde biçimlendirilir. Olayın gerçekleştiği süreyi temsil eder.

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf aracılığıyla Azure Izleyici koleksiyonu

Bu ölçümler IoT Edge modülündeki canlı video analizinden raporlanır:  

|Ölçüm adı|Tür|Etiketle|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Ölçer|ıothub, edge_device, module_name graph_topology|Topoloji başına toplam etkin grafik sayısı.|
|lva_received_bytes_total|Sayaç|ıothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Bir düğüm tarafından alınan toplam bayt sayısı. Yalnızca RTSP kaynakları için desteklenir.|
|lva_data_dropped_total|Sayaç|ıothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Bırakılan tüm veriler (olaylar, medya vb.) sayacı.|

> [!NOTE]
> Bir [Prometheus uç noktası](https://prometheus.io/docs/practices/naming/) , kapsayıcının 9600 numaralı bağlantı noktasında kullanıma sunulur. Canlı video analizinizi "lvaEdge" IoT Edge modüle ayarlarsanız, ' a bir GET isteği göndererek ölçümlere erişebilecektir http://lvaEdge:9600/metrics .   

IoT Edge modülündeki canlı video analizinden ölçüm toplamayı etkinleştirmek için şu adımları izleyin:

1. Geliştirme bilgisayarınızda bir klasör oluşturun ve bu klasöre gidin.

1. Klasöründe, `telegraf.toml` aşağıdaki konfigürasyonları içeren bir dosya oluşturun:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > . TOML dosyasındaki değişkenleri değiştirdiğinizden emin olun. Değişkenler kaşlı ayraç () ile gösterilir `{}` .

1. Aynı klasörde aşağıdaki komutları içeren bir Dockerfile oluşturun:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Docker CLı komutlarını kullanarak Docker dosyasını oluşturun ve görüntüyü Azure Container Registry 'nize yayımlayın.
    
   Docker CLı kullanarak bir kapsayıcı kayıt defterine gönderim hakkında daha fazla bilgi için bkz. [Push ve pull Docker görüntüleri](../../container-registry/container-registry-get-started-docker-cli.md). Azure Container Registry hakkında diğer bilgiler için [belgelerine](../../container-registry/index.yml)bakın.


1. Azure Container Registry gönderme işlemi tamamlandıktan sonra, aşağıdaki düğümü dağıtım bildirimi dosyanıza ekleyin:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Bildirim dosyasındaki değişkenleri değiştirdiğinizden emin olun. Değişkenler kaşlı ayraç () ile gösterilir `{}` .


   Azure Izleyici, [hizmet sorumlusu aracılığıyla doğrulanabilir](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Azure Izleyici telegraf eklentisi [çeşitli kimlik doğrulama yöntemleri](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)sunar. 

  1. Hizmet sorumlusu kimlik doğrulamasını kullanmak için şu ortam değişkenlerini ayarlayın:  
     `AZURE_TENANT_ID`: Kimlik doğrulaması yapılacak kiracıyı belirtir.  
     `AZURE_CLIENT_ID`: Kullanılacak uygulama istemci KIMLIĞINI belirtir.  
     `AZURE_CLIENT_SECRET`: Kullanılacak uygulama gizli dizesini belirtir.  
     
     >[!TIP]
     > Hizmet sorumlusu ' **nı Izleme ölçümleri yayımcısı** rolüne verebilirsiniz. Hizmet sorumlusu oluşturmak ve rolü atamak için **[hizmet sorumlusu oluşturma](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** bölümündeki adımları izleyin.

1. Modüller dağıtıldıktan sonra, ölçümler tek bir ad alanı altında Azure Izleyici 'de görüntülenir. Ölçüm adları, Prometheus tarafından yayıldıklarınızla eşleşir. 

   Bu durumda, Azure portal IoT Hub 'ına gidin ve sol bölmedeki **ölçümler** ' i seçin. Ölçümleri burada görmeniz gerekir.

### <a name="log-analytics-metrics-collection"></a>Log Analytics ölçümleri koleksiyonu
[Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)birlikte [Prometheus uç noktasını](https://prometheus.io/docs/practices/naming/) kullanarak, kullanılan Cpupercent, memoryusedpercent gibi ölçümleri oluşturabilir ve [izleyebilirsiniz](../../azure-monitor/essentials/metrics-supported.md) .   

> [!NOTE]
> Aşağıdaki yapılandırma günlükleri toplamaz, **yalnızca ölçümler**. Ayrıca, günlükleri toplamak ve karşıya yüklemek için toplayıcı modülünü genişletmek uygulanabilir.

[![Log Analytics kullanarak ölçüm koleksiyonunu gösteren diyagram.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. [Ölçümleri nasıl toplayacağınızı](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector) öğrenin
1. Docker CLı komutlarını kullanarak [Docker dosyasını](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) oluşturun ve görüntüyü Azure Container Registry 'nize yayımlayın.
    
   Docker CLı kullanarak bir kapsayıcı kayıt defterine gönderim hakkında daha fazla bilgi için bkz. [Push ve pull Docker görüntüleri](../../container-registry/container-registry-get-started-docker-cli.md). Azure Container Registry hakkında diğer bilgiler için [belgelerine](../../container-registry/index.yml)bakın.

1. Azure Container Registry gönderme işlemi tamamlandıktan sonra, dağıtım bildirimine aşağıdakiler eklenir:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Modüller `edgeHub` `edgeAgent` ve `lvaEdge` Dağıtım bildirim dosyasında tanımlanan modüllerin adlarıdır. Lütfen modüllerin adlarının eşleştiğinden emin olun.   

    Aşağıdaki adımları izleyerek, `LogAnalyticsWorkspaceId` ve `LogAnalyticsSharedKey` değerlerinizi alabilirsiniz:
    1. Azure portal git
    1. Log Analytics çalışma alanlarınızı arayın
    1. Log Analytics çalışma alanınızı bulduktan sonra `Agents management` sol gezinti bölmesindeki seçeneğe gidin.
    1. Çalışma alanı KIMLIĞINI ve kullanabileceğiniz gizli anahtarları bulacaksınız.

1. Ardından, `Workbooks` sol gezinti bölmesindeki sekmeye tıklayarak bir çalışma kitabı oluşturun.
1. Kusto sorgu dilini kullanarak sorgular yazıp IoT Edge modüller tarafından kullanılan CPU yüzdesini alabilirsiniz.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Kusto sorgusu kullanan ölçümleri gösteren diyagram.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Günlüğe Kaydetme

Diğer IoT Edge modüllerinde olduğu gibi, uç cihazdaki [kapsayıcı günlüklerini de inceleyebilirsiniz](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) . Günlüklere yazılan bilgileri [aşağıdaki Module ikizi](module-twin-configuration-schema.md) özelliklerini kullanarak yapılandırabilirsiniz:

* `logLevel`

   * İzin verilen değerler,,, `Verbose` `Information` `Warning` `Error` ve `None` .
   * `Information` varsayılan değerdir. Günlüklerde hata, uyarı ve bilgi iletileri yer alacak.
   * Değerini olarak ayarlarsanız `Warning` , Günlükler hata ve uyarı iletileri içerir.
   * Değerini olarak ayarlarsanız `Error` , Günlükler yalnızca hata iletilerini içerir.
   * Değerini olarak ayarlarsanız `None` , hiçbir günlük oluşturulmaz. (Bu yapılandırmayı önermiyoruz.)
   * `Verbose`Yalnızca bir sorunu tanılamak için günlükleri Azure desteğiyle paylaşmanız gerekiyorsa kullanın.

* `logCategories`

   * Şu değerlerden biri veya daha fazlası için virgülle ayrılmış bir liste: `Application` , `Events` , `MediaPipeline` .
   * `Application, Events` varsayılan değerdir.
   * `Application`: Modülden modül başlangıç iletileri, ortam hataları ve doğrudan Yöntem çağrıları gibi üst düzey bilgiler.
   * `Events`: Bu makalede daha önce açıklanan tüm olaylar.
   * `MediaPipeline`: Bir RTSP özellikli kamerayla bağlantı kurma sorunları gibi sorun giderirken öngörülere neden olabilecek düşük düzey Günlükler.
   
### <a name="generating-debug-logs"></a>Hata ayıklama günlükleri oluşturuluyor

Belirli durumlarda, Azure desteği 'nin bir sorunu çözmesi için, daha önce açıklananlara göre daha ayrıntılı Günlükler oluşturmanız gerekebilir. Bu günlükleri oluşturmak için:

1. İle [Modül depolama alanını cihaz depolamasına bağlayın](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) `createOptions` . Hızlı başlangıçlardan bir [Dağıtım bildirim şablonuna](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) bakarsanız şu kodu görürsünüz:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Bu kod, sınır modülünün günlükleri cihaz depolama yoluna yazmasını sağlar `/var/local/mediaservices/` . 

 1. Aşağıdaki `desired` özelliği modüle ekleyin:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Modül artık hata ayıklama günlüklerini bir ikili biçimde cihaz depolama yoluna yazacak `/var/local/mediaservices/debuglogs/` . Bu günlükleri Azure desteğiyle paylaşabilirsiniz.

## <a name="faq"></a>SSS

Sorularınız varsa [izleme ve ölçümler hakkında SSS](faq.md#monitoring-and-metrics)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Sürekli video kaydı](continuous-video-recording-tutorial.md)