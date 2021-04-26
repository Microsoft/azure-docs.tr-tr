---
title: GRPC aracılığıyla Intel OpenVINO™ DL Streamer – Edge AI uzantısı kullanarak canlı videoyu çözümleyin
description: Bu öğreticide, (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için Intel OpenVINO™ DL Streamer – Edge AI uzantısının nasıl kullanılacağı gösterilmektedir.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 07a7daf6363f0e528f84635ed6713ac462f89ca5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562860"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Öğretici: Intel OpenVINO™ DL Streamer – Edge AI uzantısı kullanarak canlı videoyu çözümleme 

Bu öğreticide, (benzetimli) bir IP kamerasından canlı video akışını çözümlemek için Intel OpenVINO™ DL Streamer – Edge AI uzantısının nasıl kullanılacağı gösterilmektedir. Bu çıkarım sunucusunun nesneleri (bir kişi, araç veya bisiklet), nesne sınıflandırması (araç attributions) ve nesne izlemeye yönelik bir modeli (kişi, araç ve bisiklet) tespit etmek için farklı modellere erişmenizi nasıl sağlayacağını göreceksiniz. GRPC modülüyle tümleştirme, video çerçevelerini AI çıkarımı sunucusuna göndermenizi sağlar. Sonuçlar daha sonra IoT Edge hub 'ına gönderilir. Bu çıkarım hizmetini canlı video analizi olarak aynı işlem düğümünde çalıştırdığınızda, paylaşılan bellek aracılığıyla video verisi göndermenin avantajlarından yararlanabilirsiniz. Bu, canlı video akışı kare hızında (ör. 30 kare/sn). 

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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Bu hızlı başlangıçta, IoT Edge üzerinde canlı video analizlerini kullanarak, Araçlar, kişi veya bisiklet izlemek üzere Araçlar ' dan Intel OpenVINO™ DL Streamer – Edge AI Uzantısı ile birlikte, bu arada bir oyun gibi nesneleri tespit edin. Ortaya çıkan çıkarım olaylarını IoT Edge hub 'a yayımlayacaksınız.

## <a name="overview"></a>Genel Bakış

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="LVA MediaGraph 'e genel bakış":::

Bu diyagramda, sinyallerin bu hızlı başlangıçta nasıl akagösterdiği gösterilmektedir. [Edge modülü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , Real-Time Akış Protokolü (RTSP) sunucusunu BARıNDıRAN bir IP kamerasına benzetir. Bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü, bu sunucudan video akışını çeker ve [GRPC uzantısı işlemci](media-graph-concept.md#grpc-extension-processor) düğümüne video çerçeveleri gönderir. 

GRPC uzantısı işlemci düğümü, kodu çözülmüş video çerçevelerini giriş olarak alır ve bu çerçeveleri gRPC sunucusu tarafından sunulan bir [GRPC](terminology.md#grpc) uç noktasına geçirir. Düğüm, [paylaşılan bellek](https://en.wikipedia.org/wiki/Shared_memory) kullanarak verilerin aktarımını veya doğrudan GRPC iletilerinin gövdesine içerik katıştırmayı destekler. Ayrıca, düğüm, gRPC uç noktasına geçmeden önce video çerçevelerini ölçeklendirmeye ve kodlamaya yönelik yerleşik bir görüntü biçimlendirici içerir. Scaler, görüntü en boy oranının korunmasıyla, doldurulmuş veya esneme yönelik seçeneklere sahiptir. Görüntü Kodlayıcısı JPEG, PNG veya BMP biçimlerini destekler. [İşlemci hakkında](media-graph-extension-concept.md#grpc-extension-processor)daha fazla bilgi edinin.

Bu öğreticide şunları yapacaksınız:

1. Medya grafiğini dağıtın.
1. Sonuçları yorumlayın.
1. Kaynakları temizleyin.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Intel OpenVINO™ DL Streamer – Edge AI uzantı modülü hakkında


OpenVINO™ DL Streamer-Edge AI uzantı modülü, OpenVINO™ DL Streamer ile derlenen video analizi işlem hattına hizmet eden Intel video analizine (VA sunma) dayalı bir mikro hizmettir. Geliştiriciler, algılama, sınıflandırma veya izleme gerçekleştiren ve sonuçları döndüren AI uzantısı modülüne kodu çözülmüş video çerçeveleri gönderebilir. AI uzantısı modülü, Microsoft 'tan IoT Edge canlı video analizi gibi video analizi platformlarıyla uyumlu gRPC API 'Lerini kullanıma sunar. 

Karmaşık, yüksek performanslı canlı video analizi çözümleri oluşturmak için IoT Edge modüldeki canlı video analizinin, kenardaki ölçeğe uygun bir güçlü çıkarım altyapısı ile eşleştirilmesi gerekir. Bu öğreticide, IoT Edge üzerinde canlı video analizi ile çalışmak üzere tasarlanan bir Edge modülü olan [Intel OpenVINO™ DL Streamer – Edge AI uzantısına]()gönderilir. 

Bu çıkarım sunucusunun ilk sürümünde aşağıdaki [modellere](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options)erişebilirsiniz:

- ![araç için person_vehicle_bike_detection nesne algılamayı object_detection](./media/use-intel-openvino-tutorial/object-detection.png)

- ![araç için vehicle_attributes_recognition nesne sınıflandırması object_classification](./media/use-intel-openvino-tutorial/object-classification.png)

- ![kişi araç için person_vehicle_bike_tracking nesne izlemeye yönelik object_tracking](./media/use-intel-openvino-tutorial/object-tracking.png)

Hızlıca kullanmaya başlamak için önceden yüklenmiş nesne algılama, nesne sınıflandırması ve nesne Izleme işlem hatlarını kullanır. Buna ek olarak, önceden yüklenmiş [kişi-araç-Bisiklet-algılama-Crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) ve [araç-öznitelikler-tanıma-engeli-0039 modelleriyle](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md)birlikte gelir.

> [!NOTE]
> Uç modülünü indirerek ve kullanarak: OpenVINO™ DL Streamer – Edge AI Uzantısı ile Intel ve dahil edilen yazılım, [Lisans sözleşmesinin](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)altındaki hüküm ve koşulları kabul etmiş olursunuz.
> Intel, insan haklarını önceden göstermek ve insan hakları ayollarının complicity kaçınması için kararlıdır. Bkz. [Intel 'In küresel Insan hakları ilkeleri](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Intel ürünleri ve yazılımları, yalnızca uluslararası olarak tanınan bir insan hakkı ihlaline neden olmayan veya bunlara katkıda bulunmayan uygulamalarda kullanılmak üzere tasarlanmıştır.

Yalnızca dağıtım şablonınızdaki işlem hattı ortam değişkenlerini değiştirerek, belirli kullanım durumu için farklı işlem hatları esnekliğini kullanabilirsiniz. Böylece, işlem hattı modeli ve canlı video analiziyle birlikte kullanıldığında medya ardışık düzen ve çıkarım modelinin değiştirilmesi birkaç saniye önemlidir.  

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Altında `GraphInstanceSet` , grafik topolojisinin adını önceki bağlantıdaki değerle eşleşecek şekilde düzenleyin:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Altında `GraphTopologyDelete` , adı düzenleyin:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma

1. Dosyasında *src/Edge/deployment.openvino.grpc.cpu.template.js* öğesine sağ tıklayın ve ardından **IoT Edge dağıtım bildirimi oluştur**' u seçin.

    ![IoT Edge dağıtım bildirimi oluştur](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Bildirim dosyasındaki *deployment.openvino.grpc.cpu.amd64.js* *src/Edge/config* klasöründe oluşturulur.

> [!NOTE]
> Ayrıca, bir şablon *üzerinde* , Intel OpenVINO DL Streamer-Edge AI uzantı modülü için GPU desteğini sağlayan birdeployment.openvino.grpc.gpu.template.jsde sunuyoruz. Bu şablonlar Intel Docker Hub görüntüsüne işaret.

Yukarıda bahsedilen şablonlar Intel Docker Hub görüntüsüne işaret. Kendi Azure Container Registry bir kopyasını barındırmak istiyorsanız, aşağıdaki 1. ve 2. adımı izleyebilirsiniz:
1. Docker CLı araçları yüklü bir cihaza (Edge cihazınız) ve çekme/etiketleme/kapsayıcıyı şu adımlarla gönderin:
    * Docker Hub 'dan Intel 'in görüntüsünü çekin:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Intel görüntüsünü kendi Azure Container Registry adınızla etiketleyin. {ACR NAME} değerini,. env dosyasında bulabileceğiniz ACR adınızla değiştirin:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Etiketli görüntünüzü Azure Container Registry gönderin:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Artık Azure Container Registry barındırılan yeni yansımanıza başvurmak için şablonları düzenlemeniz gerekir.
    * *Üzerindedeployment.openvino.grpc.cpu.template.js* sağ tıklayın ve *lavextension* modül bölümüne gidin ve değiştirin:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        Yeni değer:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * *deployment.openvino.grpc.gpu.template.js* için 2. adımı yineleyin


3. [Hareket ve yayma olaylarını Algıla](detect-motion-emit-events-quickstart.md) hızlı başlangıcı ' nı tamamlayıp bu adımı atlayın. 

    Aksi halde, sol alt köşedeki **Azure ıOT hub** bölmesinin yakınında, **diğer eylemler** simgesini ve ardından **IoT Hub bağlantı dizesi ayarla**' yı seçin. Dizeyi dosyadaki *appsettings.js* kopyalayabilirsiniz. Ya da Visual Studio Code içinde doğru IoT Hub 'ı yapılandırdığınızdan emin olmak için, [IoT Hub 'ı Seç komutunu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)kullanın.
    
    ![IoT Hub bağlantı dizesi ayarla](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. *Kaynak/kenar/yapılandırma/deployment.openvino.grpc.cpu.template.jsüzerinde* sağ tıklayın ve **tek cihaz için dağıtım oluştur**' u seçin. 

    ![Tek cihaz için dağıtım oluştur](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. IoT Hub bir cihaz seçmeniz istendiğinde, **LVA-örnek-cihaz**' ı seçin.
1. 30 saniye sonra, pencerenin sol alt köşesinde Azure IoT Hub ' yi yenileyin. Edge cihazında artık aşağıdaki dağıtılan modüller gösterilmektedir:

    * **Lvaedge** adlı canlı video analizi modülü
    * Bir RTSP sunucusuna benzetir ve canlı video akışı kaynağı görevi gören **rtspsim** modülü
    * Intel OpenVINO™ DL Streamer – Edge AI uzantısı olan **Lvaextension** modülü 

### <a name="prepare-to-monitor-events"></a>Olayları izlemeye hazırlanma

Canlı video analizi cihazına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin. Visual Studio Code **Çıkış** penceresinde IoT Hub olaylarını izlemek için bu adıma ihtiyacınız vardır. 

![İzlemeyi Başlat](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Araçlar, kişiler veya bisiklet algılamak için örnek programı çalıştırın
Bu öğreticinin [grafik topolojisini](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) bir tarayıcıda açarsanız, değerinin, `grpcExtensionAddress` `tcp://lvaExtension:5001` *httpextensionopenvino* örneğine kıyasla, GRPC sunucusuna yönelik URL 'yi değiştirmeniz gerekmez. Bunun yerine, modülün belirli bir işlem hattını, daha önce belirtildiği gibi ortam değişkenleriyle çalıştırmasını söyleyin. Varsayılan şablonda şunu olarak belirledik: "person_vehicle_bike_detection" için "object_detection". Desteklenen diğer ardışık düzenleri deneyebilirsiniz. 

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
             "topologyName": "InferencingWithOpenVINOgRPC",
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
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Bu iletide, bu ayrıntılara dikkat edin:

* İleti bir tanılama olayıdır. `MediaSessionEstablished` RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlandığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* `applicationProperties`' De, `subject` iletinin medya grafiğindeki RTSP kaynak düğümünden oluşturulduğunu gösterir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* , `eventTime` Olayın gerçekleştiği saati gösterir.
* `body`Tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="inference-event"></a>Çıkarım olayı

GRPC uzantısı işlemci düğümü, Intel OpenVINO™ DL Streamer – Edge AI uzantısının çıkarım sonuçlarını alır. Ardından sonuçları, çıkarım olayları olarak IoT Hub havuz düğümü aracılığıyla yayar. 

Bu olaylarda, türü, `entity` bir otomobil ya da kamyon gibi bir varlık olduğunu belirtmek için olarak ayarlanır. `eventTime`Değer, nesnenin ALGıLANDıĞı UTC zamandır. 

Aşağıdaki örnekte, bir araç, araç türü (Van) ve renk (beyaz), her biri 0,9 üzerinde bir güven düzeyiyle, nesne izleme modelini kullanırken varlığa bir KIMLIK atayan şekilde olduğunu görürsünüz.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id&quot;: &quot;1"
      }
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
Farklı bir model kullanmak için Dağıtım şablonunu değiştirmeniz gerekir. Desteklenen modeller arasında geçiş yapmak için, lvaExtenstion modülünde bulunan ortam değişkenlerini değiştirebilirsiniz. Modeller için desteklenen değerler ve birleşimler için [GitHub 'da bu belgeye](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) bakın.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Şablonu kopyalayın ve olası her bir işlem hattı için yeni bir ad altında saklayın. Bu şekilde, bu şablonlara dayalı yeni bir dağıtım oluşturarak modeller arasında geçiş yapabilirsiniz.

Değişkenleri değiştirdikten sonra, şablonu cihaza yeniden dağıtabilirsiniz. Şimdi, örnek programı yeniden çalıştırmak için yukarıdaki adımları tekrarlayarak yeni işlem hattı ile yineleyebilirsiniz. Çıkarım sonuçları benzer olacaktır (şemada), ancak seçtiğiniz işlem hattı modeline göre daha fazla veya daha az bilgi gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer hızlı başlangıç ve öğreticiler denemek istiyorsanız, oluşturduğunuz kaynakları saklayın. Aksi takdirde, Azure portal gidin, kaynak gruplarınıza gidin, bu öğreticiyi çalıştırdığınız kaynak grubunu seçin ve tüm kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Gelişmiş kullanıcılar için ek güçlükleri gözden geçirin:

* RTSP simülatörü kullanmak yerine RTSP desteği olan bir [IP kamerası](https://en.wikipedia.org/wiki/IP_camera) kullanın. [ONVIF uyumlu](https://www.onvif.org/conformant-products/) ürünler sayfasında RTSP 'YI destekleyen IP kameralarını arayabilirsiniz. Profiller G, S veya T ile uyumlu olan cihazları arayın.
* Azure Linux VM yerine bir Intel x64 Linux cihazı kullanın. Bu cihaz, IP kamerası ile aynı ağda olmalıdır. [Linux üzerinde Azure IoT Edge çalışma zamanını Install](../../iot-edge/how-to-install-iot-edge.md)bölümündeki yönergeleri izleyebilirsiniz. Ardından, [ilk IoT Edge modülünüzü bir sanal Linux cihazına dağıtma](../../iot-edge/quickstart-linux.md)konusundaki yönergeleri Izleyerek cihazı Azure IoT Hub kaydettirin.