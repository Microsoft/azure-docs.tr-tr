---
title: Eğitim Linux C# için modül geliştirme-Azure IoT Edge | Microsoft Docs
description: Bu öğreticide, C# kodla IoT Edge bir modül oluşturma ve bunu bir Linux IoT Edge cihazına dağıtma gösterilmektedir.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7714d065d8ac449d10d9b022005e7799f8280bda
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104447"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Öğretici: Linux cihazları C# için IoT Edge modülü geliştirme

Kodu geliştirmek C# ve Azure IoT Edge çalıştıran bir Linux cihazına dağıtmak için Visual Studio Code kullanın. 

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için Azure IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır. [Windows](quickstart.md)'da veya [Linux](quickstart-linux.md)'ta sanal bir cihaza Azure IoT Edge dağıtma hızlı başlangıçlarında oluşturduğunuz sanal IoT Edge cihazınızı kullanacaksınız. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:    

> [!div class="checklist"]
> * Visual Studio Code, üzerinde .NET Core 2.1 SDK dayalı bir IOT Edge modülünüzü oluşturmak için kullanın.
> * Visual Studio Code ve Docker kullanarak bir Docker görüntüsü oluşturma ve bunu kayıt defterinizde yayımlama.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. İletileri yalnızca sıcaklık belirtilen bir eşiğin üzerindeyse yukarı yönde gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Çözüm kapsamı

Bu öğreticide, **C#** **Visual Studio Code**kullanarak bir modülün nasıl geliştirileceği ve bir **Linux cihazına**nasıl dağıtılacağı gösterilmektedir. Windows cihazları için modüller geliştirirseniz bunun yerine [Windows cihazları için C# IoT Edge modülü geliştirme](tutorial-csharp-module-windows.md) bölümüne gidin.

Linux 'ta modül geliştirme ve dağıtmaya C# yönelik seçeneklerinizi anlamak için aşağıdaki tabloyu kullanın: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#VS Code 'de LinuxAMD64 için modüller](./media/tutorial-c-module/green-check.png) | ![C#Visual Studio 'da LinuxAMD64 için modüller](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#VS Code 'de LinuxARM32 için modüller](./media/tutorial-c-module/green-check.png) | ![C#Visual Studio 'da LinuxARM64 için modüller](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Linux ARM64 cihazları için destek [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)kullanılabilir. Daha fazla bilgi için bkz. [Visual Studio Code IoT Edge modüllerini geliştirme ve hata ayıklama (Önizleme)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, [bir Linux cihazı için IoT Edge modülü geliştirmede](tutorial-develop-for-linux.md)geliştirme ortamınızı ayarlamak için önceki öğreticiden çıkmalısınız. Bu Öğreticiyi tamamladıktan sonra, zaten aşağıdaki önkoşullara sahip olmanız gerekir: 

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge çalıştıran bir Linux cihazı](quickstart-linux.md).
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Bu öğreticileri tamamlayabilmeniz için geliştirme makinenizde aşağıdaki ek önkoşulları hazırlayın: 

* [Visual Studio Code için C# (OmniSharp tarafından desteklenen) uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK'sı](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Aşağıdaki adımlar, Visual Studio Code ve Azure IoT araçları uzantısı C# kullanılarak için bir IoT Edge modülü projesi oluşturur. Bir proje şablonu oluşturduktan sonra, modülün iletileri kendilerine bildirilen özelliklerine göre filtreleyeceğini için yeni kod ekleyin. 

### <a name="create-a-new-project"></a>Yeni bir proje oluşturma

Kendi yazacağınız kodla özelleştirebileceğiniz bir C# çözüm şablonu oluşturun. 

1. Visual Studio Code'da VS Code komut paletini açmak için **View (Görünüm)**  > **Command Palette (Komut Paleti)** öğesini seçin. 

2. Komut paletinde Azure komutunu **girip çalıştırın: Oturum açın** ve Azure hesabınızda oturum açmak için yönergeleri izleyin. Oturumu önceden açtıysanız bu adımı atlayabilirsiniz.

3. Komut paletinde, komutunu **girin ve Azure IoT Edge çalıştırın: Yeni IoT Edge çözümü**. Çözümünüzü oluşturmak için komut paletindeki yönergeleri izleyin.

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan değerleri kabul **EdgeSolution**. |
   | Modül şablonunu seçin | Seçin  **C# Modülü**. |
   | Modül adı sağlayın | Modülünüze **CSharpModule** adını verin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, son adımda verdiğiniz adından önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. Oturum açma sunucusunu Azure portalda kapsayıcı kayıt defterinizin Genel bakış sayfasından alabilirsiniz. <br><br>Son görüntü deposu, kayıt defteri \<adı\>. azurecr.io/csharpmodule gibi görünür. |
 
   ![Docker görüntü deposunu sağlama](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar. Azure Container Registry 'nizin **erişim anahtarları** bölümünden kimlik bilgilerini kullanın. 

1. VS Code gezgininde **.env** dosyasını açın. 
2. Azure Container Registry 'nizden **Kullanıcı adı** ve **parola** değerleriyle alanları güncelleştirin. 
3. Bu dosyayı kaydedin. 

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 C# ve Linux ARM32v7 cihazları için modüller geliştirebilir. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır. 

1. Komut paletini açın ve **Azure IoT Edge arayın: Edge çözümü**için varsayılan hedef platformunu ayarlayın veya pencerenin altındaki yan çubukta kısayol simgesini seçin. 

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız. 

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

1. VS Code gezgininde **modules** > **CSharpModule** > **main.py** girişini açın.

2. **CSharpModule** ad alanının en üst kısmına daha sonra kullanılan türler için üç **using** deyimi yazın:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. **Program** sınıfına **temperatureThreshold** değişkenini ekleyin. Bu değişken, IoT Hub'a verilerin gönderilmesi için ölçülen sıcaklığın aşması gereken değeri ayarlar. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. **Program** sınıfına **MessageBody**, **Machine** ve **Ambient** sınıflarını ekleyin. Bu sınıflar gelen iletilerin gövdesi için beklenen şemayı tanımlar.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. **Init** işlevini bulun. Bu işlev, modülün ileti göndermek ve almak üzere yerel Azure IoT Edge çalışma zamanına bağlanmasına izin veren bir **Moduleclient** nesnesi oluşturur ve yapılandırır. **ModuleClient** nesnesi oluşturulduktan sonra, kod modül ikizinin istenen özelliklerinden **temperatureThreshold** değerini okur. Kod, **input1** uç noktası yoluyla IoT Edge hub'ından iletileri almak için bir geri arama kaydeder. **SetInputMessageHandlerAsync** yöntemini yeni bir yöntemle değiştirin ve istenen özelliklerdeki güncelleştirmeler için bir **SetDesiredPropertyUpdateCallbackAsync** yöntemi ekleyin. Bu değişikliği yapmak için, **Init** yönteminin son satırının aşağıdaki kod ile değiştirin:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. **Program** sınıfına **onDesiredPropertiesUpdate** yöntemini ekleyin. Bu yöntem modül ikizinin istenen özellikleri üzerinde yapılan güncelleştirmeleri alır ve **temperatureThreshold** değişkenini buna göre güncelleştirir. Tüm modüllerin, doğrudan buluttan bir modülün içinde çalışan kodu yapılandırmanıza izin veren kendi modül ikizi vardır.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. **PipeMessage** yöntemini **FilterMessages** yöntemiyle değiştirin. Modül IoT Edge hub'ından bir ileti aldığında bu yöntem çağrılır. Modül ikizi aracılığıyla ayarlanan sıcaklık eşiğinin altındaki sıcaklıkları rapor eden iletileri filtreler. Ayrıca iletiye **MessageType** özelliğini ekleyip değerini **Alert** olarak ayarlar. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Program.cs dosyasını kaydedin.

9. VS Code gezgininde IoT Edge çözüm çalışma alanınızdaki **deployment.template.json** dosyasını açın. 

10. Dağıtım bildirimine **CSharpModule** modül ikizini ekleyin. Aşağıdaki JSON içeriğini **modulesContent** bölümünün en altına, **$edgeHub** modül ikizinden sonra ekleyin: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Modül ikizi için dağıtım şablonu Ekle](./media/tutorial-csharp-module/module-twin.png)

11. Deployment. Template. json dosyasını kaydedin.


## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir sınırlar içinde olduğu iletileri filtreleyecek bir IoT Edge çözümü oluşturdunuz ve CSharpModule 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

1. **Görünüm** > **Terminal**'i seçerek VS Code tümleşik terminalini açın.

1. Terminalde aşağıdaki komutu girerek Docker 'da oturum açın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusu ile oturum açın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin`. Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

2. VS Code gezgininde **deployment.template.json** dosyasına sağ tıklayıp **Build and Push IoT Edge solution** (IoT Edge Çözümü Oluştur ve Gönder) öğesini seçin.

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkincisi, hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır `docker build` . Ardından, görüntü deposunu `docker push` kapsayıcı Kayıt defterinize göndermek için çalışır.

## <a name="deploy-and-run-the-solution"></a>Çözümü dağıtma ve çalıştırma

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Code Gezginini ve Azure IoT araçları uzantısını kullanın. Zaten senaryonuz için hazırlanan bir dağıtım bildiriminiz var, yapılandırma klasöründeki **Deployment. JSON** dosyası. Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. IoT cihazlarınızın listesini görmek için Visual Studio Code Gezgini ' nde **Azure IoT Hub cihazlar** bölümünü genişletin.

2. IoT Edge cihazınızın adına sağ tıklayıp **Create Deployment for Single Device** (Tek bir cihaz için dağıtım oluştur) öğesini seçin. 

5. **config** klasöründeki **deployment.json** dosyasını seçin ve ardından **Select Edge Deployment Manifest** (Edge Dağıtım Bildirimini Seç) öğesine tıklayın. deployment.template.json dosyasını kullanmayın. 

6. Yenile düğmesine tıklayın. **SimulatedTemperatureSensor** modülü ve **$edgeAgent** ve **$EdgeHub**birlikte çalışan yeni **csharpmodule** öğesini görmeniz gerekir.  

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve yürütmeye başlar. Cihazda çalışan ve dağıtım bildiriminde bulunmayan modüller durdurulur. Cihazda eksik olan modüller başlatılır.

IoT Edge cihazınızın durumunu görüntülemek için Visual Studio Code gezgininin **Azure IoT Hub Cihazları** bölümünü kullanabilirsiniz. Dağıtılan ve çalışan modüllerin listesini görmek için cihazınızın ayrıntılarını genişletin.

1. Visual Studio Code Gezgini ' nde, IoT Edge cihazınızın adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

2. IoT Hub gelen iletileri görüntüleyin. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması gerektiğinden, iletilerin gelmesi biraz zaman alabilir. Ardından, CModule kodunda yaptığımız değişiklikler, makine sıcaklığının İleti göndermeden önce 25 dereceye ulaşması bitinceye kadar bekler. Ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler. 

   ![IoT Hub gelen iletileri görüntüle](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için dağıtım bildiriminde ikizi CSharpModule modülünü kullandık. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio Code, çalışan modülleri görmek için IoT Edge cihazınızın altındaki ayrıntıları genişletin. 

2. **Csharpmodule** öğesine sağ tıklayın ve **Modül ikizi Düzenle**' yi seçin. 

3. İstenen özelliklerde **TemperatureThreshold** bulun. Değerini, en son bildirilen sıcakdan daha yüksek olan yeni bir sıcaklık 5 derece ile 10 derece arasında değiştirin. 

4. Module ikizi dosyasını kaydedin.

5. Modül ikizi Düzenle bölmesinde herhangi bir yere sağ tıklayın ve **Modül Ikizi Güncelleştir**' i seçin. 

5. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletilerin durulabileceğini görmeniz gerekir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz. 

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtrelemek için kod içeren bir IoT Edge modülü oluşturdunuz. Kendi modüllerinizi oluşturmaya hazırsanız, [kendi IoT Edge modüllerinizi geliştirme](module-development.md) veya [Visual Studio Code ile modüller geliştirme](how-to-vs-code-develop-module.md)hakkında daha fazla bilgi edinebilirsiniz. Azure IoT Edge bir sonraki öğreticilere devam ederek, verileri kenarda işlemek ve analiz etmek için Azure Cloud Services 'ı dağıtmanıza nasıl yardımcı olabileceğini öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
