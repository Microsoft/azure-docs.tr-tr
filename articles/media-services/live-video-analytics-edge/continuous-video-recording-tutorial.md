---
title: Bulut öğreticiden buluta sürekli video kaydı ve kayıttan yürütme öğreticisi-Azure
description: Bu öğreticide, Azure IoT Edge üzerinde Azure Live video analizi 'ni kullanarak buluta sürekli olarak video kaydetme ve Azure Media Services kullanarak bu videonun herhangi bir bölümünü akışa alma hakkında bilgi edineceksiniz.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: d534fb174afa700671b6a3991a6aafa6357eb167
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280352"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Öğretici: buluta sürekli video kaydetme ve buluttan kayıttan yürütme

Bu öğreticide, Azure IoT Edge üzerinde Azure Live video analizi 'ni kullanarak buluta [sürekli video kaydı](continuous-video-recording-concept.md) (CVR) gerçekleştirme ve Azure Media Services kullanarak bu videonun herhangi bir bölümünü akışa alma hakkında bilgi edineceksiniz. Bu özellik, bir kameradan gün veya hafta boyunca bir filmden bir arşiv Arşivi sürdürmek için ihtiyaç duyduğu güvenlik ve uyumluluk gibi senaryolar için yararlıdır. 

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * İlgili kaynakları ayarlayın.
> * CVR 'yi gerçekleştiren kodu inceleyin.
> * Örnek kodu çalıştırın.
> * Sonuçları inceleyin ve videoyu görüntüleyin.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma  

Başlamadan önce şu makaleleri okuyun:

* [IoT Edge genel bakış üzerinde canlı video analizi](overview.md)
* [IoT Edge terminolojisinde canlı video analizi](terminology.md)
* [Medya grafiği kavramları](media-graph-concept.md) 
* [Sürekli video kayıt senaryoları](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticinin önkoşulları şunlardır:

* [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ve [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) uzantıları ile geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) .

    > [!TIP]
    > Docker 'ı yüklemek isteyip istemediğiniz sorulabilir. Bu istemi yoksayın.
* Geliştirme makinenizde [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) .
* [Canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)doldurun.

Bu adımların sonunda, Azure aboneliğinizde ilgili Azure kaynaklarınızın dağıtılması gerekir:

* Azure IoT Hub
* Azure Storage hesabı
* Azure Media Services hesabı
* Azure 'da [IoT Edge çalışma zamanı](../../iot-edge/how-to-install-iot-edge.md) yüklü olan Linux VM

> [!TIP]
> Oluşturulan Azure kaynaklarıyla ilgili sorunlar yaşıyorsanız, bazı sık karşılaşılan sorunları çözmek için lütfen **[sorun giderme kılavuzumuzu](troubleshoot-how-to.md#common-error-resolutions)** görüntüleyin.

## <a name="concepts"></a>Kavramlar

Medya [grafiği kavram](media-graph-concept.md) makalesinde açıklandığı gibi bir medya grafiği şunları tanımlamanızı sağlar:

- Medyanın yakalanması gereken yer.
- Nasıl işlenecektir.
- Sonuçların teslim edileceği yer. 
 
 CVR 'yi gerçekleştirmek için, bir RTSP özellikli kameradan videoyu yakalamanız ve sürekli olarak [Azure Media Services bir varlık](terminology.md#asset)kaydetmeniz gerekir. Bu diyagramda, bu medya grafiğinin grafik temsili gösterilmektedir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Medya grafiği":::

Bu öğreticide, bir RTSP kamerasının benzetimini yapmak için [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) kullanılarak oluşturulan bir kenar modülünü kullanacaksınız. Medya grafiğinde, bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü kullanarak canlı akışı alabilir ve videoyu bir varlığa kaydeden [varlık havuzu düğümüne](media-graph-concept.md#asset-sink)gönderebilirsiniz. Bu öğreticide kullanılacak video, üst [yönlü bir kesişim örneğidir](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Başlamadan önce, [önkoşullardan](#prerequisites)üçüncü madde işaretini tamamladığınızdan emin olun. Kaynak kurulum betiği bittikten sonra, klasör yapısını göstermek için süslü ayraçları seçin. ~/CloudDrive/LVA-Sample dizininde oluşturulmuş birkaç dosya görürsünüz.

![Uygulama ayarları](./media/quickstarts/clouddrive.png)

Bu öğreticide ilgilendiğiniz dosyalar şunlardır:

* **~/CloudDrive/LVA-Sample/Edge-Deployment/.exe**: Visual Studio Code bir uç cihaza modül dağıtmak için kullandığı özellikleri içerir.
* **~/CloudDrive/LVA-Sample/appsettings.json**: örnek kodu çalıştırmak için Visual Studio Code tarafından kullanılır.

Bu adımlar için dosyalar gereklidir:

1. GitHub bağlantısından depoyu kopyalayın https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Visual Studio Code başlatın ve depoyu indirdiğiniz klasörü açın.
1. Visual Studio Code, src/buluttan cihaza-Console-App klasörüne gidin ve **üzerindeappsettings.js** adlı bir dosya oluşturun. Bu dosya, programı çalıştırmak için gereken ayarları içerir.
1. Dosyadaki ~/CloudDrive/LVA-Sample/appsettings.jsiçeriğini kopyalayın. Metin şöyle görünmelidir:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    IoT Hub bağlantı dizesi, Azure IoT Hub aracılığıyla Edge modüllerine komut göndermek için Visual Studio Code kullanmanıza olanak sağlar.
    
1. Ardından src/Edge klasörüne gidin ve **. env** adlı bir dosya oluşturun.
1. ~/CloudDrive/LVA-Sample/Edge-Deployment/.exe dosyasında içeriğini kopyalayın. Metin şöyle görünmelidir:

    ```
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
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Örnek dosyaları inceleyin

Visual Studio Code içinde src/Edge/deployment.template.jsaçın. Bu şablon, sınır cihazına (Azure Linux VM) hangi Edge modüllerinin dağıtılacağını tanımlar. **Modüller** bölümünde aşağıdaki adlarla iki giriş vardır:

* **Lvaedge**: bu, IoT Edge modülündeki canlı video analizinden.
* **rtspsim**: Bu RTSP simülatör.

Ardından src/buluttan cihaza-Console-App klasörüne gidin. Burada, oluşturduğunuz dosyada, diğer birkaç dosyayla birlikte appsettings.jsgörürsünüz:

* **C2D-Console-App. csproj**: Visual Studio Code için proje dosyası.
* **operations.js**: Bu dosya, çalıştırdığınız farklı işlemleri listeler.
* **Program. cs**: örnek program kodu:
    * Uygulama ayarlarını yükler.
    * IoT Edge modülünde canlı video analizi tarafından kullanıma sunulan doğrudan yöntemleri çağırır. [Doğrudan yöntemlerini](direct-methods.md)çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz.
    * , **TERMINAL** penceresinde programın çıkışını ve **Çıkış** penceresinde modül tarafından oluşturulan olayları incelemeniz için duraklamalar.
    * Kaynakları temizlemek için doğrudan yöntemleri çağırır.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma 

Dağıtım bildirimi, bir sınır cihazına hangi modüllerin dağıtıldığını ve bu modüllerin yapılandırma ayarlarını tanımlar. Şablon dosyasından bir bildirim oluşturmak ve ardından bunu Edge cihazına dağıtmak için bu adımları izleyin.

1. Visual Studio Code’u başlatın.
1. Sol alt köşedeki **Azure ıOT hub** bölmesinin yanındaki **daha fazla eylem** simgesini seçerek IoT Hub bağlantı dizesini ayarlayın. Dosyayı src/buluttan-cihazdan-Console-App/appsettings.jsdosyasından kopyalayın. 

    ![IoT Hub bağlantı dizesi ayarla](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Dosyasında src/Edge/deployment.template.jsöğesine sağ tıklayın ve **IoT Edge dağıtım bildirimi oluştur**' u seçin. Visual Studio Code, dağıtım şablonu dosyasında bulunan değişkenleri değiştirmek için. env dosyasındaki değerleri kullanır. Bu eylem, **üzerindedeployment.amd64.js** adlı src/Edge/config klasöründe bir bildirim dosyası oluşturur.

   ![IoT Edge dağıtım bildirimi oluştur](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Dosyasında src/Edge/config/deployment.amd64.jsöğesine sağ tıklayın ve **tek cihaz Için dağıtım oluştur**' u seçin.

   ![Tek cihaz için dağıtım oluştur](./media/quickstarts/create-deployment-single-device.png)
1. Daha sonra **IoT Hub bir cihaz seçmeniz** istenir. Aşağı açılan listeden LVA-örnek-cihaz ' ı seçin.
1. Yaklaşık 30 saniye içinde, sol alt bölümdeki Azure IoT Hub yenileyin. Edge cihazında aşağıdaki modüllerin dağıtıldığını görmeniz gerekir:
    * IoT Edge 'da canlı video analizi (modül adı **Lvaedge**)
    * RTSP simülatör (modül adı **rtspsım**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Modülleri izlemeye hazırlanma 

Canlı video akışını kaydetmek için IoT Edge modülünde canlı video analizlerini kullandığınızda, olayları IoT Hub gönderir. Bu olayları görmek için şu adımları izleyin:

1. Visual Studio Code Gezgin bölmesini açın ve sol alt köşedeki **Azure IoT Hub** arayın.
1. **Cihazlar** düğümünü genişletin.
1. LVA-örnek-cihaz dosyasına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

    ![Yerleşik olay uç noktasını Izlemeye başla](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="run-the-program"></a>Programı çalıştırma 

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. Src/buluttan cihaza-Console-App/operations.js' a gidin.
1. **Graphtopologyset** düğümü altında aşağıdakileri düzenleyin:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Ardından, **Graphınstanceset** ve **Graphtopologydelete** düğümlerinin altında, **topologyname** değerinin önceki grafik topolojisinde **Name** özelliğinin değeriyle eşleştiğinden emin olun:

    `"topologyName" : "CVRToAMSAsset"`  
1. [Topolojiyi](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) bir tarayıcıda açın ve assetNamePattern bölümüne bakın. Benzersiz bir ada sahip bir varlığınızın olduğundan emin olmak için dosyadaki operations.jsgrafik örneği adını (örnek grafik-1 ' in varsayılan değerinden) değiştirmek isteyebilirsiniz.

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. F5 ' i seçerek bir hata ayıklama oturumu başlatın. **TERMINAL** penceresinde yazdırılmış bazı iletiler görürsünüz.
1. operations.jsdosya üzerinde Graphtopologyılist ve Graphınstancelist çağrıları ile başlatılır. Önceki hızlı başlangıçlardan veya öğreticilerden sonra kaynakları temizlediyseniz, bu eylem boş listeler döndürür ve aşağıdaki gibi **ENTER**' u seçmeniz için duraklamalar:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. **TERMINAL** penceresinde **ENTER** seçeneğini belirledikten sonra, bir sonraki doğrudan yöntem çağrısı kümesi yapılır:
   * Önceki Topologyıurl kullanarak Graphtopologyıset çağrısı
   * Aşağıdaki gövdeyi kullanarak Graphınstanceset öğesine çağrı
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
     ```
   * Grafik örneğini başlatmak ve video akışını başlatmak için Graphınstanceactivate çağrısı
   * Grafik örneğinin çalışır durumda olduğunu göstermek için Graphınstancelist öğesine yapılan ikinci çağrı 
1. **TERMINAL** penceresindeki çıktı, şimdi **devam etmek Için ENTER tuşuna basarak** duraklatılır. Şu anda **ENTER** ' ı seçmeyin. Doğrudan çağrdığınız yöntemler için JSON yanıtı yüklerini görmek üzere yukarı kaydırın.
1. Artık Visual Studio Code **Çıkış** penceresine geçerseniz, IoT Edge modülündeki canlı video analizi tarafından IoT Hub gönderilen iletileri görürsünüz.

   Bu iletiler aşağıdaki bölümde ele alınmıştır.
1. Grafik örneği çalışmaya devam eder ve videoyu kaydeder. RTSP simülatörü kaynak videoyu döngüye sokmaya devam eder. Kaydı durdurmak için, **TERMINAL** penceresine dönün ve **ENTER**' u seçin. Bir sonraki çağrı dizisi, kullanarak kaynakları temizlemek için yapılır:

   * Graph örneğini devre dışı bırakmak için Graphınstancedeactivate öğesine bir çağrı.
   * Örneği silmek için Graphınstancedelete öğesine çağrı.
   * Topolojiyi silmek için Graphtopologyıdelete çağrısı.
   * Listenin artık boş olduğunu göstermek için Graphtopologyılist öğesine yapılan son çağrı.

## <a name="interpret-the-results"></a>Sonuçları yorumlama 

Medya grafiğini çalıştırdığınızda IoT Edge modülündeki canlı video analizi, IoT Edge hub 'ına belirli tanılama ve çalışma olayları gönderir. Bu olaylar, Visual Studio Code **Çıkış** penceresinde gördüğünüz iletilerdir. Bir gövde bölümü ve bir applicationProperties bölümü içerirler. Bu bölümlerin neyi temsil ettiğini anlamak için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, uygulama özellikleri ve gövdenin içeriği canlı video analizi modülü tarafından tanımlanır.

## <a name="diagnostics-events"></a>Tanılama olayları 

### <a name="mediasession-established-event"></a>MediaSession tarafından belirlenen olay

Grafik örneği etkinleştirildiğinde, RTSP kaynak düğümü rtspsım modülünde çalışan RTSP sunucusuna bağlanmaya çalışır. Başarılı olursa, bu olayı yazdırır:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* İleti bir tanılama olayıdır (Mediasessionkurdu). RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlantı kurmadığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* ApplicationProperties 'teki konu bölümü, iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. Bu durumda, ileti RTSP kaynak düğümünden gelmektedir.
* ApplicationProperties 'teki eventType bölümü, bunun bir tanılama olayı olduğunu gösterir.
* EventTime bölümü, olayın gerçekleştiği saati gösterir.
* Gövde bölümü, bu durumda [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntıları olan tanılama olayı hakkındaki verileri içerir.

## <a name="operational-events"></a>İşletimsel olaylar 

### <a name="recordingstarted-event"></a>RecordingStarted olayı

Varlık havuzu düğümü video kaydetmeye başladığında, **Microsoft. Media. Graph. işletimsel. RecordingStarted** türünde bu olayı yayar:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki varlık havuzu düğümüne başvurur.

Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir. Bu durumda, Videonun kaydedildiği Azure Media Services varlığın adıdır. Bu değeri bir yere getirin.

### <a name="recordingavailable-event"></a>RecordingAvailable olayı

Adından da anlaşılacağı gibi, kayıt başladığında RecordingStarted olayı gönderilir, ancak video verileri henüz varlığa yüklenmemiş olabilir. Varlık havuzu düğümü, varlığa video verisi yüklemiştir, **Microsoft. Media. Graph. işletimsel. RecordingAvailable** türünde bu olayı yayar:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Bu olay, oyuncuların veya istemcilerin videonun kayıttan yürütmesini başlatması için varlığa yeterli miktarda veri yazıldığını gösterir.

ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki AssetSink düğümüne başvurur.

Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir. Bu durumda, Videonun kaydedildiği Azure Media Services varlığın adıdır.

### <a name="recordingstopped-event"></a>Recordingdurdurulan olay

Grafik örneğini devre dışı bıraktıktan sonra, varlık havuzu düğümü video kaydını kıymete bırakır. **Microsoft. Media. Graph. Operational. Recordingdurdurulan** türünde bu olayı yayar:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Bu olay, kaydın durdurulduğunu gösterir.

ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki AssetSink düğümüne başvurur.

Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir, bu durumda Videonun kaydedildiği Azure Media Services varlığın adıdır.

## <a name="media-services-asset"></a>Media Services varlık  

Medya grafiği tarafından oluşturulan Media Services varlığı, Azure portal oturum açarak ve videoyu görüntüleyerek inceleyebilirsiniz.

1. Web tarayıcınızı açın ve [Azure Portal](https://portal.azure.com/)gidin. Portalda oturum açmak için kimlik bilgilerinizi girin. Varsayılan görünüm hizmet panonuzu içerir.
1. Aboneliğinizdeki kaynaklar arasında Media Services hesabınızı bulun ve hesap bölmesini açın.
1. **Media Services** listesinden **varlıklar** ' ı seçin.

    ![Media Services varlıklar](./media/continuous-video-recording-tutorial/assets.png)
1. Samplevarlık-CVRToAMSAsset-Sample-Graph-1 adında listelenen bir varlık bulacaksınız. Bu, Graf topolojisi dosyanızda seçilen adlandırma modelidir.
1. Varlığı seçin.
1. Varlık ayrıntıları sayfasında **akış URL 'si** metin kutusunda **Yeni oluştur** ' u seçin.

    ![Yeni varlık](./media/continuous-video-recording-tutorial/new-asset.png)

1. Açılan sihirbazda, varsayılan seçenekleri kabul edin ve **Ekle**' yi seçin. Daha fazla bilgi için bkz. [video kayıttan yürütme](video-playback-concept.md).

    > [!TIP]
    > [Akış uç noktanızın çalıştığından](../latest/stream-streaming-endpoint-concept.md)emin olun.
1. Player Videoyu yüklemesi gerekir. Görüntülemek için **Yürüt** ' ü seçin.

> [!NOTE]
> Videonun kaynağı bir kamera akışını taklit eden bir kapsayıcı olduğundan, videodaki zaman damgaları grafik örneğini etkinleştirdiğinizde ve devre dışı bırakıldığında ile ilgilidir. Çoklu gün kaydetme ve bu arşivin bölümlerini görüntüleme hakkında bilgi almak için bkz. [çok günlük kayıt](playback-multi-day-recordings-tutorial.md) öğreticisinin yürütülmesi. Bu öğreticide, videoda ekranda görüntülenen zaman damgalarını de görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer öğreticileri denemek istiyorsanız, oluşturduğunuz kaynaklara açık tutun. Aksi takdirde, Azure portal gidin, Kaynak gruplarınızı inceleyin, bu öğreticiyi çalıştırdığınız kaynak grubunu seçin ve kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

* RTSP simülatörü kullanmak yerine RTSP desteğiyle bir [IP kamerası](https://en.wikipedia.org/wiki/IP_camera) kullanın. Profiller G, S veya T ile uyumlu cihazlar ' ı arayarak [ONVIF uyumlu ürünler sayfasında](https://www.onvif.org/conformant-products/) , RTSP desteğiyle IP kameralarını arayabilirsiniz.
* AMD64 veya x64 Linux cihazı kullanın (Azure Linux VM kullanarak). Bu cihaz, IP kamerası ile aynı ağda olmalıdır. [Linux üzerinde Azure IoT Edge çalışma zamanını Install](../../iot-edge/how-to-install-iot-edge.md)içindeki yönergeleri izleyin. Ardından, cihazı Azure IoT Hub 'a kaydetmek için [ilk IoT Edge modülünüzü bir sanal Linux cihaz](../../iot-edge/quickstart-linux.md) hızlı başlangıçlarına dağıtma ' daki yönergeleri izleyin.