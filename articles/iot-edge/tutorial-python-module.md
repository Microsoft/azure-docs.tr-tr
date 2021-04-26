---
title: Öğretici-Azure IoT Edge kullanarak özel Python modülü öğreticisi
description: Bu öğreticide Python koduyla IoT Edge modülü oluşturma ve bir Edge cihazına dağıtma adımları gösterilmektedir.
services: iot-edge
author: kgremban
manager: philmea
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d6a95dae91ef3e6aa7d39cf8af51c355a87ea73a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462737"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>Öğretici: Linux kapsayıcıları kullanarak Python IoT Edge modülü geliştirme ve dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Python kodu geliştirmek ve Azure IoT Edge çalıştıran bir cihaza dağıtmak için Visual Studio Code kullanın.

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için Azure IoT Edge modüllerini kullanabilirsiniz. Bu öğretici, hızlı başlangıçta ayarladığınız IoT Edge cihazdaki algılayıcı verilerini filtreleyen bir IoT Edge modülünü oluşturma ve dağıtma konusunda size yol gösterir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Visual Studio Code kullanarak IoT Edge Python modülü oluşturma.
> * Visual Studio Code ve Docker kullanarak bir Docker görüntüsü oluşturma ve bunu kayıt defterinizde yayımlama.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. İletileri yalnızca sıcaklık belirtilen bir eşiğin üzerindeyse yukarı yönde gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, **Visual Studio Code** kullanarak **Python** 'da bir modülün nasıl geliştirileceği ve bir IoT Edge cihazına nasıl dağıtılacağı gösterilmektedir.

IoT Edge, Windows kapsayıcıları kullanılarak Python modüllerini desteklemez.

Linux kapsayıcıları kullanarak Python modülleri geliştirme ve dağıtma seçeneklerinizi anlamak için aşağıdaki tabloyu kullanın:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64 üzerinde Python modülleri için VS Code kullanma](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32 'de Python modülleri için VS Code kullanma](./media/tutorial-c-module/green-check.png) |  |

Bu öğreticiye başlamadan önce, Linux kapsayıcı geliştirmesi için geliştirme ortamınızı ayarlamak üzere önceki öğreticiden çıkmalısınız: [Linux kapsayıcıları kullanarak IoT Edge modülleri](tutorial-develop-for-linux.md)geliştirme. Bu öğreticiyi tamamlayarak aşağıdaki önkoşulların yerine gelmelidir:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT Edge çalıştıran bir cihaz. Hızlı başlangıçlarını bir [Linux cihazı](quickstart-linux.md) veya [Windows cihazı](quickstart.md)ayarlamak için kullanabilirsiniz.
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Python 'da bir IoT Edge modülü geliştirmek için aşağıdaki ek önkoşulları geliştirme makinenize yüklersiniz:

* Visual Studio Code için [Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
* [Python](https://www.python.org/downloads/).
* Python paketlerini (genellikle Python yüklemenize dahildir) yüklemek için [PIP](https://pip.pypa.io/en/stable/installing/#installation).

>[!Note]
>`bin` klasörünüzün platformunuzun yolunda olduğundan emin olun. Bu yol genelde UNIX ve macOS için `~/.local/`, Windows için `%APPDATA%\Python` olacaktır.

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Aşağıdaki adımlarda Visual Studio Code ve Azure IoT araçlarını kullanarak IoT Edge Python modülü oluşturulur.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

Kendi kodunuzla özelleştirebileceğiniz bir Python çözüm şablonu oluşturun.

1. Visual Studio Code,   >  vs Code komut paletini açmak için **komut paletini** görüntüle ' yi seçin.

2. Komut paletinde **Azure: Sign in** komutunu girip çalıştırdıktan sonra yönergeleri izleyerek Azure hesabınızda oturum açın. Oturumu önceden açtıysanız bu adımı atlayabilirsiniz.

3. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın. Çözümünüzü oluşturmak için istemleri izleyin ve aşağıdaki bilgileri sağlayın:

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan **EdgeSolution** kabul edin. |
   | Modül şablonunu seçin | **Python Modülü**'nü seçin. |
   | Modül adı sağlayın | Modülünüze **PythonModule** adını verin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, son adımda verdiğiniz adından önceden doldurulur. **Localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle değiştirin. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz. <br><br>Son görüntü deposu \<registry name\> . azurecr.io/pythonmodule gibi görünüyor. |

   ![Docker görüntü deposunu sağlama](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı deponuzun kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar.

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. VS Code gezgininde **.env** dosyasını açın.
2. Alanları Azure kapsayıcı kayıt defterinizden kopyaladığınız **kullanıcı adı** ve **parola** değerleriyle güncelleştirin.
3. .env dosyasını kaydedin.

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 ve Linux ARM32v7 cihazları için Python modülleri geliştirebilir. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır.

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla** veya pencerenin altındaki yan çubukta kısayol simgesini seçin.

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız.

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Her şablon, **SimulatedTemperatureSensor** modülünden sanal algılayıcı verileri alan ve IoT Hub 'ına yönlendiren örnek kod içerir. Bu bölümde, iletileri göndermeden önce çözümlemek için **PythonModule** 'i genişleten kodu ekleyin.

1. VS Code Gezgini 'nde,   >  **PythonModule**  >  **Main.py** modüllerini açın.

2. **main.py** dosyasının en üst kısmından **json** kitaplığını içeri aktarın:

    ```python
    import json
    ```

3. **TEMPERATURE_THRESHOLD** ve **TWIN_CALLBACKS** değişkenlerini global sayaçlarının altına ekleyin. Sıcaklık eşiği, verilerin IoT Hub’a gönderilmesi için, ölçülen makine sıcaklığının aşması gereken değeri ayarlar.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. **İnput1_listener** işlevini aşağıdaki kodla değiştirin:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. İkizi güncelleştirmelerini dinlemek için **dinleyicileri** de güncelleştirin.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. main.py dosyasını kaydedin.

7. VS Code gezgininde IoT Edge çözüm çalışma alanınızdaki **deployment.template.json** dosyasını açın.

8. Dağıtım bildirimine ikizi **PythonModule** modülünü ekleyin. Aşağıdaki JSON içeriğini **moduleContent** bölümünün en altına, **$edgeHub** modül ikizinin arkasına ekleyin:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dağıtım şablonuna modül ikizi Ekle](./media/tutorial-python-module/module-twin.png)

9. deployment.template.json dosyasını kaydedin.

## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir sınırlar içinde olduğu iletileri filtreleyecek bir IoT Edge çözümü oluşturdunuz ve kodu PythonModule eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

1. **Görünüm** terminali ' i seçerek vs Code tümleşik Terminal ' i açın  >  .

2. Terminalde aşağıdaki komutu girerek Docker 'da oturum açın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusu ile oturum açın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

3. VS Code Gezgini 'nde, dosya **üzerindedeployment.template.js** sağ tıklayın ve **Build ve push IoT Edge çözümünü** seçin.

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkincisi, `docker build` hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. Ardından, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır.

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Code Gezginini ve Azure IoT araçları uzantısını kullanın. Senaryonuz için hazırlanan bir dağıtım bildiriminiz zaten var, config klasöründeki dosya **deployment.amd64.js** . Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. Visual Studio Code Gezgini ' nde, **Azure IoT Hub** bölümünde **aygıtlar** ' ı genişleterek IoT cihazları listesini görüntüleyin.

2. IoT Edge cihazınızın adına sağ tıklayıp **Create Deployment for Single Device** (Tek bir cihaz için dağıtım oluştur) öğesini seçin.

3. **Config** klasöründeki dosya **deployment.amd64.js** seçin ve ardından **kenar dağıtım bildirimini Seç**' e tıklayın. deployment.template.json dosyasını kullanmayın.

4. Cihazınızın altında, dağıtılan ve çalışan modüllerin listesini görmek için **modüller** ' i genişletin. Yenile düğmesine tıklayın. **SimulatedTemperatureSensor** modülü ve **$edgeAgent** ve **$edgeHub** birlikte çalışan yeni **PythonModule** görmeniz gerekir.

    Modüllerin başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir.

## <a name="view-the-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve yürütmeye başlar. Cihazda çalışan ve dağıtım bildiriminde bulunmayan modüller durdurulur. Cihazda eksik olan modüller başlatılır.

IoT Edge cihazınızın durumunu görüntülemek için Visual Studio Code gezgininin **Azure IoT Hub Cihazları** bölümünü kullanabilirsiniz. Dağıtılan ve çalışan modüllerin listesini görmek için cihazınızın ayrıntılarını genişletin.

1. Visual Studio Code Gezgini ' nde, IoT Edge cihazınızın adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

2. IoT Hub gelen iletileri görüntüleyin. İletilerin gelmesi biraz zaman alabilir. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması vardır. Daha sonra, PythonModule kodunda yaptığımız değişiklikler, makine sıcaklığının İleti göndermeden önce 25 dereceye ulaşması bitinceye kadar bekler. Ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler.

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Dağıtım bildiriminde ikizi PythonModule modülünü, sıcaklık eşiğini 25 derece olarak ayarlamak için kullandık. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio Code, çalışan modülleri görmek için IoT Edge cihazınızın altındaki ayrıntıları genişletin.

2. **PythonModule** öğesine sağ tıklayın ve **modülü Düzenle ikizi**' yi seçin.

3. İstenen özelliklerde **TemperatureThreshold** bulun. Değerini, en son bildirilen sıcakdan daha yüksek olan yeni bir sıcaklık 5 derece ile 10 derece arasında değiştirin.

4. Module ikizi dosyasını kaydedin.

5. Modül ikizi Düzenle bölmesinde herhangi bir yere sağ tıklayın ve **Modül Ikizi Güncelleştir**' i seçin.

6. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletilerin durulabileceğini görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtrelemek için kod içeren bir IoT Edge modülü oluşturdunuz.

Azure IoT Edge bir sonraki öğreticilere devam ederek, verileri kenarda işlemek ve analiz etmek için Azure Cloud Services 'ı dağıtmanıza nasıl yardımcı olabileceğini öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [İşlevler](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Özel görüntü işleme hizmeti](tutorial-deploy-custom-vision.md)