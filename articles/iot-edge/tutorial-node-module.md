---
title: Öğretici Linux için Node.js modülü geliştirme-Azure IoT Edge | Microsoft Docs
description: Bu öğreticide Node.js koduyla IoT Edge modülü oluşturma ve bir Edge cihazına dağıtma adımları gösterilmektedir
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 77e7edfba94c7b428e59dadfd9f0824608941d7a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223260"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-using-linux-containers"></a>Öğretici: Linux kapsayıcıları kullanarak Node.js IoT Edge modülü geliştirme ve dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Node.js kodu geliştirmek ve Azure IoT Edge çalıştıran bir cihaza dağıtmak için Visual Studio Code kullanın.

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır. Hızlı başlangıçlarda oluşturduğunuz IoT Edge cihazını kullanacaksınız. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Visual Studio Code kullanarak IoT Edge Node.js modülü oluşturma
> * Visual Studio Code ve Docker kullanarak bir Docker görüntüsü oluşturma ve bunu kayıt defterinizde yayımlama
> * Modüle IoT Edge cihazınıza dağıtma
> * Oluşturulan verileri görüntüleme

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. İletileri yalnızca sıcaklık belirtilen bir eşiğin üzerindeyse yukarı yönde gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, **Visual Studio Code** kullanarak **Node.js** bir modülün nasıl geliştirileceği ve bir IoT Edge cihazına nasıl dağıtılacağı gösterilmektedir.

IoT Edge, Windows kapsayıcıları kullanılarak Node.js modülleri desteklemez.

Node.js modülleri geliştirme ve dağıtmaya yönelik seçeneklerinizi anlamak için aşağıdaki tabloyu kullanın:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64 üzerinde Node.js modülleri için VS Code kullanma](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32 'de Node.js modülleri için VS Code kullanma](./media/tutorial-c-module/green-check.png) |  |

Bu öğreticiye başlamadan önce, Linux kapsayıcı geliştirmesi için geliştirme ortamınızı ayarlamak üzere önceki öğreticiden çıkmalısınız: [Linux kapsayıcıları kullanarak IoT Edge modülleri](tutorial-develop-for-linux.md)geliştirme. Bu öğreticiyi tamamlayarak aşağıdaki önkoşulların yerine gelmelidir:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT Edge çalıştıran bir cihaz. Hızlı başlangıçlarını bir [Linux cihazı](quickstart-linux.md) veya [Windows cihazı](quickstart.md)ayarlamak için kullanabilirsiniz.
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Node.js IoT Edge bir modül geliştirmek için, aşağıdaki ek önkoşulları geliştirme makinenize yüklersiniz:

* [Node.js ve npm](https://nodejs.org). npm paketi, Node.js ile birlikte dağıtılır. Başka bir deyişle Node.js'yi indirdiğinizde npm de bilgisayarınıza otomatik olarak yüklenir.

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Aşağıdaki adımlarda Visual Studio Code ve Azure IoT araçlarını kullanarak bir IoT Edge Node.js modülünün nasıl oluşturulacağı gösterilmektedir.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

Temel olarak kullanabileceğiniz bir Node.js çözüm şablonu oluşturmak için **npm** kullanın.

1. Visual Studio Code ' de, tümleşik terminali **görüntüle**' yi seçerek  >   vs Code tümleşik Terminal ' i açın.

2. Tümleşik terminalde **yeoman** ve Node.js Azure IoT Edge modülü oluşturucuyu yüklemek için aşağıdaki komutu girin:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3.   >  Vs Code komut paletini açmak için **komut paleti** görüntüle ' yi seçin.

4. Komut paletinde **Azure: Sign in** komutunu yazıp çalıştırdıktan sonra yönergeleri izleyerek Azure hesabınızda oturum açın. Oturumu önceden açtıysanız bu adımı atlayabilirsiniz.

5. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu yazıp çalıştırın. Çözümünüzü oluşturmak için komut paletindeki yönergeleri izleyin.

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan **EdgeSolution** kabul edin. |
   | Modül şablonunu seçin | **Node.js modülünü** seçin. |
   | Modül adı sağlayın | Modülünüze **NodeModule** adını verin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, son adımda verdiğiniz adından önceden doldurulur. **Localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle değiştirin. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz. <br><br>Son görüntü deposu \<registry name\> . azurecr.io/nodemodule gibi görünüyor. |

   ![Docker görüntü deposunu sağlama](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı deponuzun kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar.

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. VS Code gezgininde **.env** dosyasını açın.
2. Alanları Azure kapsayıcı kayıt defterinizden kopyaladığınız **kullanıcı adı** ve **parola** değerleriyle güncelleştirin.
3. Bu dosyayı kaydedin.

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code, Linux AMD64 ve Linux ARM32v7 cihazları için Node.js modülleri geliştirebilirler. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır.

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla** veya pencerenin altındaki yan çubukta kısayol simgesini seçin.

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız.

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Her şablon, **SimulatedTemperatureSensor** modülünden sanal algılayıcı verileri alan ve IoT Hub yönlendiren örnek kod içerir. Bu bölümde NodeModule modülünün verileri göndermeden önce analiz etmesi için gerekli kodu ekleyin.

1. VS Code Gezgini 'nde, **modüller**  >  **nodemodule**  >  **app.js** açın.

2. Gerekli düğüm modülleri altına bir sıcaklık eşik değeri girin. Sıcaklık eşiği, verilerin IoT Hub’a gönderilmesi için, ölçülen sıcaklığın aşması gereken değeri ayarlar.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. `PipeMessage` işlevinin tamamını `FilterMessage` işleviyle değiştirin.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. `pipeMessage` işlev adını `client.on()` işlevindeki `filterMessage` ile değiştirin.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Aşağıdaki kod parçacığını `client.open()` geri çağırma işlevinin içine, `else` deyiminin içindeki `client.on()` sonrasına kopyalayın. İstenen özellikler güncelleştirildiğinde bu işlev çağrılır.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. app.js dosyasını kaydedin.

7. VS Code gezgininde IoT Edge çözüm çalışma alanınızdaki **deployment.template.json** dosyasını açın.

8. Dağıtım bildirimine NodeModule modül ikizini ekleyin. Aşağıdaki JSON içeriğini `moduleContent` bölümünün en altına, `$edgeHub` modül ikizinin arkasına ekleyin:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dağıtım şablonuna modül ikizi Ekle](./media/tutorial-node-module/module-twin.png)

9. deployment.template.json dosyasını kaydedin.

## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir sınırlar içinde olduğu iletileri filtreleyecek bir IoT Edge çözümü oluşturdunuz ve NodeModule 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

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

4. Cihazınızın altında, dağıtılan ve çalışan modüllerin listesini görmek için **modüller** ' i genişletin. Yenile düğmesine tıklayın. **SimulatedTemperatureSensor** modülü ve **$edgeAgent** ve **$EdgeHub** birlikte çalışan yeni **nodemodule** görmeniz gerekir.

    Modüllerin başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir.

## <a name="view-the-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve yürütmeye başlar. Cihazda çalışan ve dağıtım bildiriminde bulunmayan modüller durdurulur. Cihazda eksik olan modüller başlatılır.

IoT Edge cihazınızın durumunu görüntülemek için Visual Studio Code gezgininin **Azure IoT Hub Cihazları** bölümünü kullanabilirsiniz. Dağıtılan ve çalışan modüllerin listesini görmek için cihazınızın ayrıntılarını genişletin.

1. Visual Studio Code Gezgini ' nde, IoT Edge cihazınızın adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

2. IoT Hub gelen iletileri görüntüleyin. İletilerin gelmesi biraz zaman alabilir. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması vardır. Ardından, NodeModule kodunda yaptığımız değişiklikler, makine sıcaklığının İleti göndermeden önce 25 dereceye ulaşmasını bekleyin. Ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler.

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için dağıtım bildiriminde ikizi NodeModule modülünü kullandık. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio Code, çalışan modülleri görmek için IoT Edge cihazınızın altındaki ayrıntıları genişletin.

2. **Nodemodule** öğesine sağ tıklayın ve **Modül ikizi Düzenle**' yi seçin.

3. İstenen özelliklerde **TemperatureThreshold** bulun. Değerini, en son bildirilen sıcakdan daha yüksek olan yeni bir sıcaklık 5 derece ile 10 derece arasında değiştirin.

4. Module ikizi dosyasını kaydedin.

5. Modül ikizi Düzenle bölmesinde herhangi bir yere sağ tıklayın ve **Modül Ikizi Güncelleştir**' i seçin.

6. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletilerin durulabileceğini görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Geçmeyecekseniz ücret kesilmesini önlemek için yerel yapılandırmalarınızı ve bu makalede oluşturulan Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtrelemek için kod içeren bir IoT Edge modülü oluşturdunuz.

Azure IoT Edge bir sonraki öğreticilere devam ederek, verileri kenarda işlemek ve analiz etmek için Azure Cloud Services 'ı dağıtmanıza nasıl yardımcı olabileceğini öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [İşlevler](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Özel görüntü işleme hizmeti](tutorial-deploy-custom-vision.md)