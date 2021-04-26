---
title: Bulut öğreticiden buluta ve kayıttan yürütmeye yönelik olay tabanlı video kaydı-Azure
description: Bu öğreticide, bulutta olay tabanlı bir video kaydı kaydetmek ve buluttan kayıttan yürütmek için Azure Live video analizinin Azure IoT Edge nasıl kullanılacağını öğreneceksiniz.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: de1a4d423108ddb18af4f8478c9bac60672d6062
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279825"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Öğretici: buluta yönelik olay tabanlı video kaydı ve buluttan kayıttan yürütme

Bu öğreticide, Bulutta Azure Media Services canlı bir video kaynağının bölümlerini seçmeli olarak kaydetmek için Azure IoT Edge Azure Live video analizlerinin nasıl kullanılacağını öğreneceksiniz. Bu kullanım örneği, bu öğreticide [olay tabanlı video kaydı](event-based-video-recording-concept.md) (EVR) olarak adlandırılır. Canlı videonun bölümlerini kaydetmek için bir nesne algılama AI modeli kullanarak videodaki nesneleri arayabilir ve video kliplerini yalnızca belirli bir nesne türü algılandığında kaydedebilirsiniz. Ayrıca, Media Services kullanarak kayıtlı video kliplerini nasıl kayıttan çalıştıracağınızı öğreneceksiniz. Bu özellik, ilgi çekici bir video klip Arşivi tutmanın gerektiği çeşitli senaryolar için yararlıdır. 

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * İlgili kaynakları ayarlayın.
> * EVR 'yi gerçekleştiren kodu inceleyin.
> * Örnek kodu çalıştırın.
> * Sonuçları inceleyin ve videoyu görüntüleyin.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma  

Başlamadan önce şu makaleleri okuyun:

* [IoT Edge genel bakış üzerinde canlı video analizi](overview.md)
* [IoT Edge terminolojisinde canlı video analizi](terminology.md)
* [Medya grafiği kavramları](media-graph-concept.md) 
* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)
* [Öğretici: IoT Edge modülünü geliştirme](../../iot-edge/tutorial-develop-for-linux.md)
* [Dağıtımı düzenleme. * .template.js](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* [IoT Edge dağıtım bildiriminde yolların nasıl bildirilemeyeceğini gösteren](../../iot-edge/module-composition.md#declare-routes) bölüm

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticinin önkoşulları şunlardır:

* [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ve [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) uzantıları ile geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) .

    > [!TIP]
    > Docker 'ı yüklemek isteyip istemediğiniz sorulabilir. Bu istemi yoksayın.
* Geliştirme makinenizde [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) .
* [Canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)doldurun ve [ortamı ayarlayın](./detect-motion-emit-events-quickstart.md?pivots=programming-language-csharp#set-up-your-development-environment)

Bu adımların sonunda, Azure aboneliğinizde ilgili Azure kaynaklarınızın dağıtılması gerekir:

* Azure IoT Hub
* Azure Storage hesabı
* Azure Media Services hesabı
* Azure 'da [IoT Edge çalışma zamanı](../../iot-edge/how-to-install-iot-edge.md) yüklü olan Linux VM

> [!TIP]
> Oluşturulan Azure kaynaklarıyla ilgili sorunlar yaşıyorsanız, bazı sık karşılaşılan sorunları çözmek için lütfen **[sorun giderme kılavuzumuzu](troubleshoot-how-to.md#common-error-resolutions)** görüntüleyin.

## <a name="concepts"></a>Kavramlar

Olay tabanlı video kaydı, bir olay tarafından tetiklenen videoyu kaydetme sürecini ifade eder. Bu olay şu kaynaktan oluşturulabilir:
- Video sinyalinin kendisini işleme, örneğin, videoda hareketli bir nesne algılandıktan sonra.
- Bağımsız bir kaynak, örneğin kapısının açılış. 

Alternatif olarak, yalnızca bir ınpoger hizmeti belirli bir olayın oluştuğunu algıladığında kayıt tetikleyebilirsiniz. Bu öğreticide, bir otobana hareket eden bir eğitim videosunu kullanacaksınız ve her kamyon algılandığında video kliplerini kaydedeceksiniz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Medya grafiği":::

Diyagram, bir [medya grafiğinin](media-graph-concept.md) ve istenen senaryoyu gerçekleştiren ek modüllerin bir resim gösterimidir. Dört IoT Edge modül dahil edilir:

* IoT Edge modülünde canlı video analizi.
* Bir HTTP uç noktasının arkasında bir AI modeli çalıştıran bir Edge modülü. Bu AI modülü, birçok nesne türünü tespit eden [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modelini kullanır.
* Diyagramda bir nesne sayacı olarak adlandırılan nesneleri saymak ve filtrelemek için özel bir modül. Bir nesne sayacı oluşturup bu öğreticide dağıtırsınız.
* Bir RTSP kamerasının benzetimini yapmak için bir [RTSP simülatör modülü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) .
    
Diyagramda gösterildiği gibi, trafiğin sanal bir şekilde canlı videosunu yakalamak ve bu videoyu iki yola göndermek için medya grafiğinde bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü kullanacaksınız:

* İlk yol bir HTTP uzantısı düğümüne ait. Düğüm, alanı kullanarak bir değere göre ayarlanmış bir değere örnek `samplingOptions` olarak gelir ve ardından çerçeveleri görüntüler halinde bir nesne algılayıcısı olan YOLOV3 AI modülüne geçirir. Düğüm, model tarafından algılanan nesneler (bir trafikte araçlar) olan sonuçları alır. HTTP uzantısı düğümü daha sonra IoT Hub ileti havuzu düğümü aracılığıyla sonuçları IoT Edge hub 'ına yayınlar.
* ObjectCounter modülü, nesne algılama sonuçlarını (trafikte araçlar) içeren IoT Edge hub 'ından ileti alacak şekilde ayarlanır. Modül, bu iletileri denetler ve bir ayar aracılığıyla yapılandırılan belirli bir türdeki nesneleri arar. Böyle bir nesne bulunduğunda bu modül IoT Edge hub 'ına bir ileti gönderir. Bu "nesne bulundu" iletileri daha sonra medya grafiğinin IoT Hub kaynak düğümüne yönlendirilir. Böyle bir ileti alındığında, medya grafiğindeki IoT Hub kaynak düğümü, [sinyal kapısı işlemci](media-graph-concept.md#signal-gate-processor) düğümünü tetikler. Sinyal kapısı işlemci düğümü daha sonra yapılandırılan bir süre için açılır. Video, o süre için varlık havuzu düğümüne ağ geçidi üzerinden akar. Canlı akışın bu bölümü daha sonra [varlık havuzu](media-graph-concept.md#asset-sink) düğümü aracılığıyla Azure Media Services hesabınızdaki bir [varlığa](terminology.md#asset) kaydedilir.

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Başlamadan önce, [önkoşullardan](#prerequisites)üçüncü madde işaretini tamamladığınızdan emin olun. Kaynak kurulum betiği bittikten sonra, klasör yapısını göstermek için süslü ayraçları seçin. ~/CloudDrive/LVA-Sample dizininde oluşturulmuş birkaç dosya görürsünüz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Uygulama ayarları":::

Bu öğreticide ilgilendiğiniz dosyalar şunlardır:

* **~/CloudDrive/LVA-Sample/Edge-Deployment/.exe**: Visual Studio Code bir uç cihaza modül dağıtmak için kullandığı özellikleri içerir.
* **~/CloudDrive/LVA-Sample/appsetting.json**: örnek kodu çalıştırmak için Visual Studio Code tarafından kullanılır.

Bu adımlar için dosyalara ihtiyacınız olacaktır.

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

## <a name="examine-the-template-file"></a>Şablon dosyasını inceleyin 

Önceki adımda Visual Studio Code başladıysanız ve örnek kodu içeren klasörü açtınız.

Visual Studio Code, src/Edge öğesine gidin. Oluşturduğunuz. env dosyası ve birkaç dağıtım şablonu dosyası görürsünüz. Bu şablon, sınır cihazına (Azure Linux VM) hangi Edge modüllerinin dağıtılacağını tanımlar. . Env dosyası, Media Services kimlik bilgileri gibi bu şablonlarda kullanılan değişkenlerin değerlerini içerir.

Src/Edge/deployment.objectCounter.template.jsaçın. **Modüller** bölümünde, önceki "kavramlar" bölümünde listelenen öğelere karşılık gelen dört giriş vardır:

* **Lvaedge**: bu, IoT Edge modülündeki canlı video analizinden.
* **yolov3**: Bu, Yolo v3 modeli KULLANıLARAK oluşturulan AI modülüdür.
* **rtspsim**: Bu RTSP simülatör.
* **Objectcounter**: yolov3 'deki sonuçlarda belirli nesneler için görünen modüldür.

ObjectCounter modülü için, "görüntü" değeri için kullanılan dizeye ($ {MODULES. objectCounter}) bakın. Bu, IoT Edge modülünü geliştirmeye yönelik [öğreticiye](../../iot-edge/tutorial-develop-for-linux.md) dayanır. Visual Studio Code, objectCounter modülünün kodunun src/Edge/modules/objectCounter altında olduğunu otomatik olarak tanır. 

IoT Edge dağıtım bildiriminde yolları bildirme hakkında [Bu bölümü](../../iot-edge/module-composition.md#declare-routes) okuyun. Ardından, şablon JSON dosyasındaki yolları inceleyin. Nasıl yapılacağını aklınızda edin:

* LVAToObjectCounter, belirli olayları objectCounter modülündeki belirli bir uç noktaya göndermek için kullanılır.
* ObjectCounterToLVA, lvaEdge modülünde belirli bir uç noktaya (IoT Hub kaynak düğümü olması gerekir) bir tetikleyici olayı göndermek için kullanılır.
* objectCounterToIoTHub, bu öğreticiyi çalıştırdığınızda objectCounter 'dan çıktıyı görmenizi sağlayacak bir hata ayıklama aracı olarak kullanılır.

> [!NOTE]
> "Kamyon" olarak etiketlenen nesneleri en az %50 olan bir güvenilirlik düzeyine bakmak üzere ayarlanan objectCounter modülünün istenen özelliklerini denetleyin.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma 

Dağıtım bildirimi, bir sınır cihazına hangi modüllerin dağıtıldığını ve bu modüllerin yapılandırma ayarlarını tanımlar. Şablon dosyasından bir bildirim oluşturmak ve ardından bunu Edge cihazına dağıtmak için bu adımları izleyin.

Visual Studio Code kullanarak Docker 'da oturum açmak için [Bu yönergeleri](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) izleyin. Ardından **IoT Edge çözüm oluştur ve Gönder '** i seçin. Bu adım için src/Edge/deployment.objectCounter.template.jskullanın.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="IoT Edge çözümü oluşturun ve gönderin":::

Bu eylem nesne sayma için objectCounter modülünü oluşturur ve görüntüyü Azure Container Registry iter.

* Ortam değişkenlerinin. env dosyasında tanımlı CONTAINER_REGISTRY_USERNAME_myacr ve CONTAINER_REGISTRY_PASSWORD_myacr sahip olup olmadığını denetleyin.

Bu adım, üzerinde src/Edge/config/deployment.objectCounter.amd64.jsüzerinde IoT Edge dağıtım bildirimi oluşturur. Bu dosyaya sağ tıklayın ve **tek cihaz Için dağıtım oluştur**' u seçin.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Tek bir cihaz için dağıtım oluşturma":::

IoT Edge üzerinde canlı video analiziyle ilgili ilk öğreticeniz varsa, Visual Studio Code IoT Hub bağlantı dizesini girmenizi ister. Dosyadaki appsettings.jskopyalayabilirsiniz.

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Sonra, Visual Studio Code IoT Hub bir cihaz seçmenizi ister. LVA-örnek-cihaz olması gereken IoT Edge cihazınızı seçin.

Bu aşamada, Edge modüllerinin IoT Edge cihazınıza dağıtılması başladı.
Yaklaşık 30 saniye içinde, Visual Studio Code sol alt bölümdeki Azure IoT Hub yenileyin. LvaEdge, rtspsim, yolov3 ve objectCounter adlı dört modülün dağıtıldığını görmeniz gerekir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Dört modül dağıtıldı":::

## <a name="prepare-for-monitoring-events"></a>İzleme olaylarını hazırlama

ObjectCounter modülünün ve IoT Edge modülündeki canlı video analizinden olayları görmek için şu adımları izleyin:

1. Visual Studio Code Gezgin bölmesini açın ve sol alt köşedeki **Azure IoT Hub** arayın.
1. **Cihazlar** düğümünü genişletin.
1. LVA-örnek-cihaz dosyasına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Yerleşik olay uç noktasını izlemeye başla":::

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
1. <!--In Visual Studio Code, go-->Src/buluttan cihaza-Console-App/operations.js' a gidin.
1. **Graphtopologyset** düğümü altında aşağıdakileri düzenleyin:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. Ardından, **Graphınstanceset** ve **Graphtopologydelete** düğümlerinin altında şunları düzenleyin:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. F5 ' i seçerek bir hata ayıklama oturumu başlatın. **TERMINAL** penceresinde yazdırılmış bazı iletiler görürsünüz.
1. operations.jsdosya üzerinde Graphtopologyılist ve Graphınstancelist çağrıları ile başlatılır. Önceki hızlı başlangıçlardan veya öğreticilerden sonra kaynakları temizlediyseniz, bu eylem, gösterildiği gibi, **ENTER**' u seçmeniz için boş listeler ve duraklamalar döndürür:

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
            "topologyName": "EVRtoAssetsOnObjDetect",
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
    
   * Graf örneğini başlatmak ve video akışını başlatmak için Graphınstanceactivate çağrısı
   * Grafik örneğinin çalışır durumda olduğunu göstermek için Graphınstancelist öğesine yapılan ikinci çağrı
     
1. **TERMINAL** penceresindeki çıktı, şimdi **devam etmek Için ENTER tuşuna basarak** duraklatılır. Şu anda **ENTER** ' ı seçmeyin. Doğrudan çağrdığınız yöntemler için JSON yanıtı yüklerini görmek üzere yukarı kaydırın.
1. Artık Visual Studio Code **Çıkış** penceresine geçerseniz, IoT Edge modülündeki canlı video analizi tarafından IoT Hub gönderilen iletileri görürsünüz.

   Bu iletiler aşağıdaki bölümde ele alınmıştır.
1. Grafik örneği çalışmaya devam eder ve videoyu kaydeder. RTSP simülatörü kaynak videoyu döngüye sokmaya devam eder. Aşağıdaki bölümde açıklandığı gibi iletileri gözden geçirin. Ardından, örneği durdurmak için, **TERMINAL** penceresine dönün ve **ENTER**' u seçin. Bir sonraki çağrı dizisi, kullanarak kaynakları temizlemek için yapılır:

   * Graph örneğini devre dışı bırakmak için Graphınstancedeactivate öğesine bir çağrı.
   * Örneği silmek için Graphınstancedelete öğesine çağrı.
   * Topolojiyi silmek için Graphtopologyıdelete çağrısı.
   * Listenin artık boş olduğunu göstermek için Graphtopologyılist öğesine yapılan son çağrı.

## <a name="interpret-the-results"></a>Sonuçları yorumlama 

Medya grafiğini çalıştırdığınızda IoT Edge modülündeki canlı video analizi, IoT Edge hub 'ına belirli tanılama ve çalışma olayları gönderir. Bu olaylar, Visual Studio Code **Çıkış** penceresinde gördüğünüz iletilerdir. Bir gövde bölümü ve bir applicationProperties bölümü içerirler. Bu bölümlerin neyi temsil ettiğini anlamak için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, uygulama özellikleri ve gövdenin içeriği canlı video analizi modülü tarafından tanımlanır.

## <a name="diagnostics-events"></a>Tanılama olayları

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı 

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü RTSP simülatör kapsayıcısı üzerinde çalışan RTSP sunucusuna bağlanmaya çalışır. Başarılı olursa, bu olay yazdırılır. Olay türü **Microsoft. Media. MediaGraph. Diagnostics. Mediasessionsetup**' tur.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* İleti bir tanılama olayıdır (Mediasessionkurdu). RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlantı kurmadığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* ApplicationProperties 'teki konu bölümü, iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. Bu durumda, ileti RTSP kaynak düğümünden gelmektedir.
* ApplicationProperties 'teki eventType bölümü, bunun bir tanılama olayı olduğunu gösterir.
* EventTime bölümü, olayın gerçekleştiği saati gösterir. Bu, trafik videosunun (MKV dosyası) modüle canlı bir akış olarak gelmesi için başladığı süredir.
* Gövde bölümü, bu durumda [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntıları olan tanılama olayı hakkındaki verileri içerir.


## <a name="operational-events"></a>İşletimsel olaylar

Medya grafiği bir süre çalıştıktan sonra, sonuç olarak objectCounter modülünden bir olay alırsınız. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

ApplicationProperties bölümünde olay saati bulunur. Bu, objectCounter modülünün yolov3 modülünün sonuçlarının ilgilendiğiniz nesneleri (structuralks) içerdiklerini gözlemlediği süredir.

Videoda diğer structuralks algılandığında bu olaylardan daha fazla bilgi görebilirsiniz.

### <a name="recordingstarted-event"></a>RecordingStarted olayı

Nesne sayacı olayı gönderdikten hemen hemen sonra, **Microsoft. Media. Graph. işletimsel. RecordingStarted** türünde bir olay görürsünüz:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki varlık havuzu düğümüne başvurur. Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir. Bu durumda, Videonun kaydedildiği Azure Media Services varlığın adıdır. Bu değeri bir yere getirin.

### <a name="recordingavailable-event"></a>RecordingAvailable olayı

Varlık havuzu düğümü, varlığa video yüklediğini, **Microsoft. Media. Graph. işletimsel. RecordingAvailable** türünde bu olayı yayar:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Bu olay, oyuncuların veya istemcilerin videonun kayıttan yürütmesini başlatması için varlığa yeterli miktarda veri yazıldığını gösterir. ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki AssetSink düğümüne başvurur. Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir. Bu durumda, Videonun kaydedildiği Azure Media Services varlığın adıdır.

### <a name="recordingstopped-event"></a>Recordingdurdurulan olay

[Topolojide](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)sinyal kapısı işlemci düğümü için etkinleştirme ayarlarını (maximumActivationTime) incelerseniz, bu ağ geçidinin, üzerinden 30 saniyelik video gönderildikten sonra kapatılacak şekilde ayarlandığını görürsünüz. Her ne kadar 30 saniye sonra, RecordingStarted olayından sonra, **Microsoft. Media. Graph. Operational. Recordingdurduruldu** türünde bir olay görmeniz gerekir. Bu olay, varlık havuzu düğümünün varlık için video kaydını durdurduğunu gösterir.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Bu olay, kaydın durdurulduğunu gösterir. ApplicationProperties 'teki konu bölümü, bu iletiyi oluşturan grafikteki AssetSink düğümüne başvurur. Gövde bölümü, çıkış konumuyla ilgili bilgiler içerir. Bu durumda, Videonun kaydedildiği Azure Media Services varlığın adıdır.

## <a name="media-services-asset"></a>Media Services varlık  

Grafik tarafından oluşturulan Media Services varlığını, Azure portal oturum açarak ve videoyu görüntüleyerek inceleyebilirsiniz.

1. Web tarayıcınızı açın ve [Azure Portal](https://portal.azure.com/)gidin. Portalda oturum açmak için kimlik bilgilerinizi girin. Varsayılan görünüm hizmet panonuzu içerir.
1. Aboneliğinizdeki kaynaklar arasında Media Services hesabınızı bulun. Hesap bölmesini açın.
1. **Media Services** listesinden **varlıklar** ' ı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Sürekli video kaydı":::
1. SampleAssetFromEVR-LVAEdge-{DateTime} adıyla listelenmiş bir varlık bulacaksınız. Bu, RecordingStarted olayının outputLocation özelliğinde verilen addır. Topolojideki assetNamePattern, bu adın nasıl oluşturulduğunu belirler.
1. Varlığı seçin.
1. Varlık ayrıntıları sayfasında **akış URL 'si** metin kutusunda **Yeni oluştur** ' u seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Yeni varlık":::
1. Açılan sihirbazda, varsayılan seçenekleri kabul edin ve **Ekle**' yi seçin. Daha fazla bilgi için bkz. [video kayıttan yürütme](video-playback-concept.md).

    > [!TIP]
    > [Akış uç noktanızın çalıştığından](../latest/stream-streaming-endpoint-concept.md)emin olun.
1. Player Videoyu yüklemesi gerekir. Görüntülemek için **Yürüt** ' ü seçin.

> [!NOTE]
> Videonun kaynağı bir kamera akışını taklit eden bir kapsayıcı olduğundan, videodaki zaman damgaları grafik örneğini etkinleştirdiğinizde ve devre dışı bırakıldığında ile ilgilidir. [Multi-Day kayıt öğreticisinin kayıttan yürütülmesi](playback-multi-day-recordings-tutorial.md) içinde yerleşik olarak bulunan kayıttan yürütme denetimlerini kullanıyorsanız, videoda ekranda görüntülenen zaman damgalarını görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer öğreticileri denemek istiyorsanız, oluşturduğunuz kaynaklara açık tutun. Aksi takdirde, Azure portal gidin, Kaynak gruplarınızı inceleyin, bu öğreticiyi çalıştırdığınız kaynak grubunu seçin ve kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

* RTSP simülatörü kullanmak yerine RTSP desteğiyle bir [IP kamerası](https://en.wikipedia.org/wiki/IP_camera) kullanın. Profiller G, S veya T ile uyumlu cihazlar ' ı arayarak [ONVIF uyumlu ürünler sayfasında](https://www.onvif.org/conformant-products/) , RTSP desteğiyle IP kameralarını arayabilirsiniz.
* AMD64 veya x64 Linux cihazı kullanın (Azure Linux VM kullanarak). Bu cihaz, IP kamerası ile aynı ağda olmalıdır. [Linux üzerinde Azure IoT Edge çalışma zamanını Install](../../iot-edge/how-to-install-iot-edge.md)içindeki yönergeleri izleyin. Ardından, cihazı Azure IoT Hub 'a kaydetmek için [ilk IoT Edge modülünüzü bir sanal Linux cihaz](../../iot-edge/quickstart-linux.md) hızlı başlangıçlarına dağıtma ' daki yönergeleri izleyin.