---
title: OpenVINO™ model sunucusu – AI Uzantısı ile Intel arasında canlı videoyu çözümleme
description: Bu öğreticide, (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için Intel tarafından sunulan bir AI modeli sunucusu kullanacaksınız.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 5751184493fffeeaf647507e9e9b00834f63ab5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557267"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Öğretici: OpenVINO™ model sunucusunu kullanarak canlı videoyu çözümleyin – Intel 'ten AI uzantısı 

Bu öğreticide, (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için, OpenVINO™ model sunucusu – AI uzantısının Intel 'ten nasıl kullanılacağı gösterilmektedir. Bu çıkarım sunucusunun nesneleri tespit etmek için modeller (bir kişi, araç veya bisiklet) ve araçlar sınıflandırma modeli için nasıl erişim sunacağını görürsünüz. Canlı video akışındaki çerçevelerin bir alt kümesi bu çıkarım sunucusuna gönderilir ve sonuçlar IoT Edge hub 'ına gönderilir.

Bu öğretici bir Azure VM 'yi IoT Edge bir cihaz olarak kullanır ve sanal bir canlı video akışı kullanır. C# dilinde yazılmış örnek koda dayalıdır ve [hareket ve yayma olaylarını Algıla](detect-motion-emit-events-quickstart.md) hızlı başlangıç bölümünde oluşturulur.

> [!NOTE]
> Bu öğreticide, Edge cihazınız olarak bir x86-64 makinenin kullanılması gerekir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir abonelik içeren bir Azure hesabı. Henüz bir [hesabınız yoksa ücretsiz olarak bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
  > [!NOTE]
  > Hizmet sorumluları oluşturma izinlerine sahip bir Azure aboneliğine ihtiyacınız olacak (**sahip rolü** bunu sağlar). Doğru izinleriniz yoksa, size doğru izinleri vermek için lütfen hesap yöneticinize ulaşın. 
* [Visual Studio Code](https://code.visualstudio.com/), aşağıdaki uzantılara sahip:
    * [Azure IoT Araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Hareket algılama ve olayları](detect-motion-emit-events-quickstart.md) gösterme hızlı başlangıcı ' nı tamamlamadıysanız, [Azure kaynaklarını ayarlama](detect-motion-emit-events-quickstart.md#set-up-azure-resources)adımlarını tamamlamayı unutmayın.

> [!TIP]
> Azure IoT araçları 'nı yüklerken Docker 'ı yüklemeniz istenebilir. İstemi yoksayabilirsiniz.

## <a name="review-the-sample-video"></a>Örnek videoyu gözden geçirin

Azure kaynaklarını ayarlarken, bir park lotunun kısa bir videosu, Azure 'da IoT Edge cihaz olarak kullandığınız Linux VM 'sine kopyalanır. Bu hızlı başlangıçta canlı bir akışın benzetimini yapmak için video dosyası kullanılmaktadır.

[VLC medya oynatıcı](https://www.videolan.org/vlc/)gibi bir uygulama açın. CTRL + N ' ı seçin ve ardından kayıttan yürütmeyi başlatmak için [videoya](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) bir bağlantı yapıştırın. Her bir park partisi, bunların Park yeri ve tek bir hareket halinde araçlar görürsünüz.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Bu hızlı başlangıçta, IoT Edge üzerinde canlı video analizlerini kullanarak, araçlar gibi nesneleri tespit etmek ya da bunları sınıflandırmak için, Intel 'den OpenVINO™ model sunucusu – AI uzantısı ' nı kullanacaksınız. Ortaya çıkan çıkarım olaylarını IoT Edge hub 'a yayımlayacaksınız.

## <a name="overview"></a>Genel Bakış

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Genel Bakış":::

Bu diyagramda, sinyallerin bu hızlı başlangıçta nasıl akagösterdiği gösterilmektedir. [Edge modülü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , Real-Time Akış Protokolü (RTSP) sunucusunu BARıNDıRAN bir IP kamerasına benzetir. Bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü, bu sunucudan video akışını çeker ve [http uzantısı işlemci](media-graph-concept.md#http-extension-processor) düğümüne video çerçeveleri gönderir. 

HTTP uzantısı düğümü bir ara sunucu rolünü yürütür. Bu BT, sizin tarafınızdan ayarlanan gelen video çerçevelerini örneklerle `samplingOptions` aynı zamanda video çerçevelerini belirtilen görüntü türüne dönüştürür. Daha sonra görüntüyü REST üzerinden bir HTTP uç noktası arkasında bulunan AI modellerini çalıştıran başka bir uç modülüne geçirir. Bu örnekte, bu Edge modülü, Intel 'teki OpenVINO™ model sunucusu – AI uzantısıdır. HTTP uzantısı işlemci düğümü, algılama sonuçlarını toplar ve olayları [IoT Hub havuz](media-graph-concept.md#iot-hub-message-sink) düğümüne yayımlar. Düğüm daha sonra bu olayları [IoT Edge hub 'ına](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)gönderir.

Bu öğreticide şunları yapacaksınız:

1. Medya grafiğini oluşturun ve dağıtın.
1. Sonuçları yorumlayın.
1. Kaynakları temizleyin.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>OpenVINO™ model sunucusu – Intel 'den AI uzantısı hakkında

[Openvino™ Toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) 'in Intel® dağıtımı (açık görsel çıkarım ve sinir ağ iyileştirmesi), geliştiricilerin ve veri bilimcilerinin bilgisayar görme iş yüklerini hızlandırmasına, derin öğrenime ve dağıtımlara sahip olmasına ve gelişmiş, hafif bir şekilde yürütmeyi uçtan buluta kadar kolay ve heterojen bir şekilde® yürütmeyi etkinleştirmesine yardımcı olan ücretsiz bir yazılım setidir. Model iyileştirici ve çıkarım altyapısı ile Intel® derin öğrenme dağıtım araç setini ve 40 'den fazla iyileştirilmiş önceden eğitilen [modeli Içeren açık model Zoo](https://github.com/openvinotoolkit/open_model_zoo) deposunu içerir.

Karmaşık, yüksek performanslı canlı video analizi çözümleri oluşturmak için IoT Edge modüldeki canlı video analizinin, kenardaki ölçeğe uygun bir güçlü çıkarım altyapısı ile eşleştirilmesi gerekir. Bu öğreticide, çıkarım istekleri, IoT Edge üzerinde canlı video analizi ile çalışmak üzere tasarlanan bir uç modülü olan [Intel, Openvino™ model sunucusuna – AI uzantısına](https://aka.ms/lva-intel-ovms)gönderilir. Bu çıkarım sunucu modülü, bilgisayar görme iş yükleri için çok iyileştirilmiş ve Intel® mimarileri için geliştirilen, OpenVINO™ araç seti tarafından desteklenen bir çıkarım sunucusu olan OpenVINO™ model sunucusunu (OVM 'ler) içerir. OVM 'lere, video çerçevelerinin ve canlı video analizine ilişkin IoT Edge modüldeki bir uzantı eklenmiştir ve bu sayede herhangi bir OpenVINO™ Toolkit desteklenen modeli ( [kodu](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)değiştirerek çıkarım sunucu modülünü özelleştirebilirsiniz) kullanabilirsiniz. Intel® donanımı tarafından sağlanan çok çeşitli hızlandırma mekanizmalarından daha fazla seçim yapabilirsiniz. Bunlara CPU 'Lar (Atom, çekirdek, Xeon), FPGAs, VPUs dahildir.

Bu çıkarım sunucusunun ilk sürümünde aşağıdaki [modellere](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)erişebilirsiniz:

- Araç algılama (çıkarım URL 'SI: http://{modül-adı}: 4000/vehicleDetection)
- Kişi/araç/Bisiklet algılama (çıkarım URL 'SI: http://{Module-Name}: 4000/personVehicleBikeDetection)
- Araç sınıflandırması (çıkarım URL 'SI: http://{modül-adı}: 4000/vehicleClassification)
- Yüz Algılama (çıkarım URL 'SI: http://{modül-adı}: 4000/faceDetection)

> [!NOTE]
> Uç modülünü indirerek ve kullanarak: OpenVINO™ model sunucusu – AI uzantısı ve dahil olan yazılım, [Lisans sözleşmesinin](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)altındaki hüküm ve koşulları kabul etmiş olursunuz.
> Intel, insan haklarını önceden göstermek ve insan hakları ayollarının complicity kaçınması için kararlıdır. Bkz. [Intel 'In küresel Insan hakları ilkeleri](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Intel ürünleri ve yazılımları, yalnızca uluslararası olarak tanınan bir insan hakkı ihlaline neden olmayan veya bunlara katkıda bulunmayan uygulamalarda kullanılmak üzere tasarlanmıştır.

## <a name="create-and-deploy-the-media-graph"></a>Medya grafiğini oluşturma ve dağıtma

### <a name="examine-and-edit-the-sample-files"></a>Örnek dosyaları İnceleme ve düzenleme

Önkoşulların bir parçası olarak, örnek kodu bir klasöre indirdiniz. Örnek dosyaları incelemek ve düzenlemek için bu adımları izleyin.

1. Visual Studio Code, *src/Edge* bölümüne gidin. *. Env* dosyanızı ve birkaç dağıtım şablonu dosyasını görürsünüz.

    Dağıtım şablonu, Edge cihazının dağıtım bildirimini ifade eder. Bazı yer tutucu değerleri içerir. *. Env* dosyası bu değişkenlerin değerlerini içerir.

1. *Src/buluttan cihaza-Console-App* klasörüne gidin. Burada dosya ve diğer birkaç dosya *appsettings.js* görürsünüz:

    * ***C2D-Console-App. csproj*** -Visual Studio Code için proje dosyası.
    * ***operations.js*** , programın çalıştırmasını istediğiniz işlemlerin bir listesi.
    * ***Program. cs*** -örnek program kodu. Bu kod:

        * Uygulama ayarlarını yükler.
        * IoT Edge modülündeki canlı video analizinin sunduğu doğrudan yöntemleri çağırır. [Doğrudan yöntemlerini](direct-methods.md)çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz.
        * Program çıkışını **TERMINAL** penceresinde Incelemenize ve **Çıkış** penceresinde modül tarafından oluşturulan olayları incelemenize olanak tanıyan duraklar.
        * Kaynakları temizlemek için doğrudan yöntemleri çağırır.


1. Dosyadaki *operations.js* düzenleyin:
    * Grafik topolojisine olan bağlantıyı değiştirin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * Altında `GraphInstanceSet` , grafik topolojisinin adını önceki bağlantıdaki değerle eşleşecek şekilde düzenleyin:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Altında `GraphTopologyDelete` , adı düzenleyin:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma

1. Dosyasında *src/Edge/deployment.openvino.template.js* öğesine sağ tıklayın ve ardından **IoT Edge dağıtım bildirimi oluştur**' u seçin.

    ![IoT Edge dağıtım bildirimi oluştur](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Bildirim dosyasındaki *deployment.openvino.amd64.js* *src/Edge/config* klasöründe oluşturulur.

1. [Hareket ve yayma olaylarını Algıla](detect-motion-emit-events-quickstart.md) hızlı başlangıcı ' nı tamamlayıp bu adımı atlayın. 

    Aksi halde, sol alt köşedeki **Azure ıOT hub** bölmesinin yakınında, **diğer eylemler** simgesini ve ardından **IoT Hub bağlantı dizesi ayarla**' yı seçin. Dizeyi dosyadaki *appsettings.js* kopyalayabilirsiniz. Ya da Visual Studio Code içinde doğru IoT Hub 'ı yapılandırdığınızdan emin olmak için, [IoT Hub 'ı Seç komutunu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)kullanın.
    
    ![IoT Hub bağlantı dizesi ayarla](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. *Kaynak/kenar/yapılandırma/deployment.openvino.amd64.jsüzerinde* sağ tıklayın ve **tek cihaz için dağıtım oluştur**' u seçin. 

    ![Tek cihaz için dağıtım oluştur](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. IoT Hub bir cihaz seçmeniz istendiğinde, **LVA-örnek-cihaz**' ı seçin.
1. 30 saniye sonra, pencerenin sol alt köşesinde Azure IoT Hub ' yi yenileyin. Edge cihazında artık aşağıdaki dağıtılan modüller gösterilmektedir:

    * **Lvaedge** adlı canlı video analizi modülü
    * Bir RTSP sunucusuna benzetir ve canlı video akışı kaynağı görevi gören **rtspsim** modülü
    * OpenVINO™ model sunucusu olan **openvino** modülü-ıNTEL 'Den AI uzantı modülü 

### <a name="prepare-to-monitor-events"></a>Olayları izlemeye hazırlanma

Canlı video analizi cihazına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin. Visual Studio Code **Çıkış** penceresinde IoT Hub olaylarını izlemek için bu adıma ihtiyacınız vardır. 

![İzlemeyi Başlat](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Araçlar 'ı algılamak için örnek programı çalıştırın
Bu öğreticinin [grafik topolojisini](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) bir tarayıcıda açarsanız, değerinin `inferencingUrl` olarak ayarlandığını görürsünüz. Bu, `http://openvino:4000/vehicleDetection` çıkarım sunucusunun canlı videoda, varsa, araçları algıladıktan sonra sonuçlar döndürmesini sağlar.

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. Bir hata ayıklama oturumu başlatmak için F5 tuşunu seçin. **TERMINAL** penceresinde yazdırılan iletileri görürsünüz.
1. Kod *üzerindekioperations.js* doğrudan yöntemlere ve çağrılarıyla başlatılır `GraphTopologyList` `GraphInstanceList` . Önceki hızlı başlangıçlarını tamamladıktan sonra kaynakları temizledikten sonra bu işlem boş listeleri döndürür ve ardından duraklatılır. Devam etmek için Enter tuşunu seçin.

    **TERMINAL** penceresinde, bir sonraki doğrudan yöntem çağrısı kümesi gösterilir:

     * Daha önce kullanılan bir çağrısı `GraphTopologySet``topologyUrl`
     * Aşağıdaki gövdesini kullanan öğesine yapılan bir çağrı `GraphInstanceSet` :

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * `GraphInstanceActivate`Grafik örneğini ve videonun akışını başlatan öğesine yönelik bir çağrı
     * `GraphInstanceList`Grafik örneğinin çalışır durumda olduğunu gösteren ikinci bir çağrı
1. **TERMINAL** penceresindeki çıktı bir istem sırasında duraklatılır `Press Enter to continue` . Henüz ENTER ' ı seçmeyin. Doğrudan çağrdığınız yöntemler için JSON yanıtı yüklerini görmek üzere yukarı kaydırın.
1. Visual Studio Code **Çıkış** penceresine geçin. IoT Edge modülündeki canlı video analizinin IoT Hub 'ına gönderdiğini iletiler görürsünüz. Bu hızlı başlangıçta aşağıdaki bölümde bu iletiler ele alınmaktadır.
1. Medya grafiği çalışmaya devam eder ve sonuçları yazdırır. RTSP simülatörü kaynak videoyu döngüye sokmaya devam eder. Medya grafiğini durdurmak için, **TERMINAL** penceresine dönün ve ENTER ' u seçin. 

    Sonraki çağrı dizisi kaynakları temizler:
      * `GraphInstanceDeactivate`Grafik örneğini devre dışı bırakmak için bir çağrı.
      * `GraphInstanceDelete`Örneği silme çağrısı.
      * İçin bir çağrı `GraphTopologyDelete` , topolojiyi siler.
      * İçin nihai bir çağrı `GraphTopologyList` , listenin boş olduğunu gösterir.

## <a name="interpret-results"></a>Sonuçları yorumlama

Medya grafiğini çalıştırdığınızda, HTTP uzantısı işlemci düğümünün sonuçları IoT Hub 'ına IoT Hub havuz düğümünden geçer. **Çıktı** penceresinde gördüğünüz iletiler bir `body` bölümü ve bir `applicationProperties` bölümü içerir. Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, canlı video analizi modülü, uygulama özelliklerini ve gövdenin içeriğini tanımlar. 

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü, rtspsim-live555 kapsayıcısında çalışan RTSP sunucusuna bağlanmaya çalışır. Bağlantı başarılı olursa, aşağıdaki olay yazdırılır. Olay türü **Microsoft. Media. MediaGraph. Diagnostics. Mediasessionsetup**' tur.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

Bu iletide, bu ayrıntılara dikkat edin:

* İleti bir tanılama olayıdır. `MediaSessionEstablished` RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlandığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* `applicationProperties`' De, `subject` iletinin medya grafiğindeki RTSP kaynak düğümünden oluşturulduğunu gösterir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* , `eventTime` Olayın gerçekleştiği saati gösterir.
* `body`Tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="inference-event"></a>Çıkarım olayı

HTTP uzantısı işlemci düğümü, OpenVINO™ model sunucusu – AI uzantı modülünden çıkarım sonuçlarını alır. Ardından sonuçları, çıkarım olayları olarak IoT Hub havuz düğümü aracılığıyla yayar. 

Bu olaylarda, türü, `entity` bir otomobil ya da kamyon gibi bir varlık olduğunu belirtmek için olarak ayarlanır. `eventTime`Değer, nesnenin ALGıLANDıĞı UTC zamandır. 

Aşağıdaki örnekte, 0,9 üzerinde güvenirlik değeri olan iki araçlar algılandı.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:43:18.1280000Z"
  }
}
```

İletilerde aşağıdaki ayrıntılara dikkat edin:

* `applicationProperties`' De, `subject` iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. 
* `applicationProperties`' De, `eventType` Bu olayın bir analiz olayı olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Bölüm, analiz olayı hakkındaki verileri içerir. Bu durumda, olay bir çıkarım olayıdır, bu nedenle gövde verileri içerir `inferences` .
* Bölümü, olduğunu `inferences` gösterir `type` `entity` . Bu bölüm, varlıkla ilgili ek verileri içerir.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Kişi veya Araçlar ya da bisiklet algılamak için örnek programı çalıştırın
Farklı bir model kullanmak için, grafik topolojisini ve ayrıca dosyasını değiştirmeniz gerekir `operations.json` .

[Grafik topolojisini](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) yerel bir dosyaya kopyalayın, söyleyin `C:\TEMP\topology.json` . Bu kopyayı açın ve değerini `inferencingUrl` olarak değiştirin `http://openvino:4000/personVehicleBikeDetection` .

Sonra, Visual Studio Code ' de *src/buluttan cihaza-Console-App* klasörüne gidin ve `operations.json` dosya açın. Satırı şu şekilde düzenleyin `topologyUrl` :

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Şimdi, örnek programı yeni topolojiyle birlikte çalıştırmak için yukarıdaki adımları yineleyebilirsiniz. Çıkarım sonuçları, yalnızca olarak ayarlanmış olan araç algılama modelinde benzer (şemada) `subtype` `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>Araçlar sınıflandırmak için örnek programı çalıştırma
Visual Studio Code ' de, önceki adımdan yerel kopyasını açın `topology.json` ve değerini `inferencingUrl` olarak düzenleyin `http://openvino:4000/vehicleClassification` . Önceki örneği kişileri veya taşıtlar ya da bisiklet algılamak için çalıştırırsanız, `operations.json` dosyayı yeniden değiştirmeniz gerekmez.

Şimdi, örnek programı yeni topolojiyle birlikte çalıştırmak için yukarıdaki adımları yineleyebilirsiniz. Örnek bir sınıflandırma sonucu aşağıdaki gibidir.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Yüzeyleri algılamak için örnek programı çalıştırma
Visual Studio Code ' de, önceki adımdan yerel kopyasını açın `topology.json` ve değerini `inferencingUrl` olarak düzenleyin `http://openvino:4000/faceDetection` . Önceki örneği kişileri veya taşıtlar ya da bisiklet algılamak için çalıştırırsanız, `operations.json` dosyayı yeniden değiştirmeniz gerekmez.

Şimdi, örnek programı yeni topolojiyle birlikte çalıştırmak için yukarıdaki adımları yineleyebilirsiniz. Örnek algılama sonucu aşağıdaki gibidir (Not: yukarıda kullanılan Park partisi videosu herhangi bir algılanabilir yüz içermiyorsa, bu modeli denemek için başka bir video yapmanız gerekir).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer hızlı başlangıç ve öğreticiler denemek istiyorsanız, oluşturduğunuz kaynakları saklayın. Aksi takdirde, Azure portal gidin, kaynak gruplarınıza gidin, bu öğreticiyi çalıştırdığınız kaynak grubunu seçin ve tüm kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Gelişmiş kullanıcılar için ek güçlükleri gözden geçirin:

* RTSP simülatörü kullanmak yerine RTSP desteği olan bir [IP kamerası](https://en.wikipedia.org/wiki/IP_camera) kullanın. [ONVIF uyumlu](https://www.onvif.org/conformant-products/) ürünler sayfasında RTSP 'YI destekleyen IP kameralarını arayabilirsiniz. Profiller G, S veya T ile uyumlu olan cihazları arayın.
* Azure Linux VM yerine AMD64 veya x64 Linux cihazı kullanın. Bu cihaz, IP kamerası ile aynı ağda olmalıdır. [Linux üzerinde Azure IoT Edge çalışma zamanını Install](../../iot-edge/how-to-install-iot-edge.md)bölümündeki yönergeleri izleyebilirsiniz. Ardından, [ilk IoT Edge modülünüzü bir sanal Linux cihazına dağıtma](../../iot-edge/quickstart-linux.md)konusundaki yönergeleri Izleyerek cihazı Azure IoT Hub kaydettirin.