---
ms.openlocfilehash: cd3c615a296934d8e06a062125a72c322d05dd7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750512"
---
### <a name="examine-and-edit-the-sample-files"></a>Örnek dosyaları İnceleme ve düzenleme

Önkoşulların bir parçası olarak, örnek kodu bir klasöre indirdiniz. Örnek dosyaları incelemek ve düzenlemek için bu adımları izleyin.

1. Visual Studio Code, *src/Edge* bölümüne gidin. *. Env* dosyanızı ve birkaç dağıtım şablonu dosyasını görürsünüz.

    Dağıtım şablonu, Edge cihazının dağıtım bildirimini ifade eder. Bazı yer tutucu değerleri içerir. *. Env* dosyası bu değişkenlerin değerlerini içerir.

1. *Src/buluttan cihaza-Console-App* klasörüne gidin. Burada dosya ve diğer birkaç dosya *appsettings.js* görürsünüz:

    * operations.js, programın çalıştırmasını istediğiniz işlemlerin bir listesi.
    * main.py-örnek program kodu. Bu kod:

        * Uygulama ayarlarını yükler.
        * IoT Edge modülündeki canlı video analizinin sunduğu doğrudan yöntemleri çağırır. Doğrudan yöntemlerini çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz.
        * Program çıkışını **TERMINAL** penceresinde Incelemenize ve **Çıkış** penceresinde modül tarafından oluşturulan olayları incelemenize olanak tanıyan duraklar.
        * Kaynakları temizlemek için doğrudan yöntemleri çağırır.  
1. Dosyadaki *operations.js* düzenleyin:
    * Grafik topolojisine olan bağlantıyı değiştirin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * Altında `GraphInstanceSet` , grafik topolojisinin adını önceki bağlantıdaki değerle eşleşecek şekilde düzenleyin:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Altında `GraphTopologyDelete` , adı düzenleyin:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma

1. Dosyasında *src/Edge/deployment.yolov3.template.js* öğesine sağ tıklayın ve ardından **IoT Edge dağıtım bildirimi oluştur**' u seçin.

    ![IoT Edge dağıtım bildirimi oluştur](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Bildirim dosyasındaki *deployment.yolov3.amd64.js* *src/Edge/config* klasöründe oluşturulur.

1. [Hareket ve yayma olaylarını Algıla](../../../detect-motion-emit-events-quickstart.md) hızlı başlangıcı ' nı tamamlayıp bu adımı atlayın. 

    Aksi halde, sol alt köşedeki **Azure ıOT hub** bölmesinin yakınında, **diğer eylemler** simgesini ve ardından **IoT Hub bağlantı dizesi ayarla**' yı seçin. Dizeyi dosyadaki *appsettings.js* kopyalayabilirsiniz. Ya da Visual Studio Code içinde doğru IoT Hub 'ı yapılandırdığınızdan emin olmak için, [IoT Hub 'ı Seç komutunu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)kullanın.
    
    ![IoT Hub bağlantı dizesi ayarla](../../../media/quickstarts/set-iotconnection-string.png)

    > [!NOTE]
    > IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. *Kaynak/kenar/yapılandırma/deployment.yolov3.amd64.jsüzerinde* sağ tıklayın ve **tek cihaz için dağıtım oluştur**' u seçin. 

    ![Tek cihaz için dağıtım oluştur](../../../media/quickstarts/create-deployment-single-device.png)

1. IoT Hub bir cihaz seçmeniz istendiğinde, **LVA-örnek-cihaz**' ı seçin.
1. 30 saniye sonra, pencerenin sol alt köşesinde Azure IoT Hub ' yi yenileyin. Edge cihazında artık aşağıdaki dağıtılan modüller gösterilmektedir:

    * **Lvaedge** adlı canlı video analizi modülü
    * Bir RTSP sunucusuna benzetir ve canlı video akışı kaynağı görevi gören **rtspsim** modülü
        > [!NOTE]
        > Yukarıdaki adımlar, kurulum betiği tarafından oluşturulan sanal makineyi kullandığınızı kabul ediyor. Bunun yerine kendi Edge cihazınızı kullanıyorsanız, bu hızlı başlangıç için kullanılan örnek video dosyasını çekmek ve depolamak için Edge cihazınıza gidin ve **yönetici haklarıyla** aşağıdaki komutları çalıştırın:  
        
        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
    * Görüntülere görüntü getiren ve birden çok nesne türü sınıfını döndüren YOLOv3 nesne algılama modeli olan **yolov3** modülü.
 
      ![Sınır cihazında dağıtılan modüller](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Olayları izlemeye hazırlanma

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. Canlı video analizi cihazına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin. Visual Studio Code **Çıkış** penceresinde IoT Hub olaylarını izlemek için bu adıma ihtiyacınız vardır. 

   ![İzlemeyi Başlat](../../../media/quickstarts/start-monitoring-iothub-events.png) 

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
### <a name="run-the-sample-program"></a>Örnek programı çalıştırma

1. Bir hata ayıklama oturumu başlatmak için F5 tuşunu seçin. **TERMINAL** penceresinde yazdırılan iletileri görürsünüz.
1. Kod *üzerindekioperations.js* doğrudan yöntemlere ve çağrılarıyla başlatılır `GraphTopologyList` `GraphInstanceList` . Önceki hızlı başlangıçlarını tamamladıktan sonra kaynakları temizledikten sonra bu işlem boş listeleri döndürür ve ardından duraklatılır. Devam etmek için Enter tuşunu seçin.

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

    **TERMINAL** penceresinde, bir sonraki doğrudan yöntem çağrısı kümesi gösterilir:

     * Daha önce kullanılan bir çağrısı `GraphTopologySet``topologyUrl`
     * Aşağıdaki gövdesini kullanan öğesine yapılan bir çağrı `GraphInstanceSet` :

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
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
