---
title: Öğretici Windows C# için modül geliştirme-Azure IoT Edge | Microsoft Docs
description: Bu öğreticide, C# kodla IoT Edge bir modül oluşturma ve bunu bir Windows IoT Edge cihazına dağıtma gösterilmektedir.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 88ff026ea56e5f41b3b124c2d5594822ffeb6830
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529260"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Öğretici: Windows cihazları C# için IoT Edge modülü geliştirme

Kod geliştirmek C# ve Azure IoT Edge çalıştıran bir Windows cihazına dağıtmak Için Visual Studio 'yu kullanın. 

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için Azure IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:    

> [!div class="checklist"]
> * C# SDK 'yı temel alan IoT Edge bir modül oluşturmak Için Visual Studio 'yu kullanın.
> * Visual Studio ve Docker kullanarak bir Docker görüntüsü oluşturun ve bunu Kayıt defterinize yayımlayın.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. İletileri yalnızca sıcaklık belirtilen bir eşiğin üzerindeyse yukarı yönde gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Çözüm kapsamı

Bu öğreticide, **C#** **Visual Studio 2019**kullanarak bir modülün nasıl geliştirileceği ve bir **Windows cihazına**nasıl dağıtılacağı gösterilmektedir. Linux cihazları için modüller geliştirirseniz bunun yerine [Linux cihazları için C# IoT Edge modülü geliştirme](tutorial-csharp-module.md) bölümüne gidin. 

Windows cihazlarına modül geliştirme ve dağıtma C# seçeneklerinizi anlamak için aşağıdaki tabloyu kullanın: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64 geliştirme** | ![VS Code C# 'de WinAMD64 için modüller geliştirin](./media/tutorial-c-module/green-check.png) | ![Visual C# Studio 'da WinAMD64 için modüller geliştirme](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 hata ayıklama** |   | ![Visual C# Studio 'da WinAMD64 için hata ayıklama modülleri](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, [bir Windows cihazı için IoT Edge modülü geliştirmede](tutorial-develop-for-windows.md)geliştirme ortamınızı ayarlamak için önceki öğreticiden çıkmalısınız. Bu Öğreticiyi tamamladıktan sonra, zaten aşağıdaki önkoşullara sahip olmanız gerekir: 

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge çalıştıran bir Windows cihazı](quickstart.md).
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)gibi bir kapsayıcı kayıt defteri.
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) uzantısıyla yapılandırıldı.
* Windows kapsayıcıları çalıştıracak şekilde yapılandırılmış [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) .

> [!TIP]
> Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, plreada Visual Studio marketi 'nden VS 2017 için [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) indirin ve yükleyin

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Aşağıdaki adımlarda, Visual Studio ve Azure IoT Edge araçları uzantısı kullanılarak bir IoT Edge modülü projesi oluşturulur. Bir proje şablonu oluşturduktan sonra, modülün iletileri kendilerine bildirilen özelliklerine göre filtreleyeceğini için yeni kod ekleyin. 

### <a name="create-a-new-project"></a>Yeni bir proje oluşturma

Azure IoT Edge araçları, Visual Studio 'da desteklenen tüm IoT Edge modül dilleri için proje şablonları sağlar. Bu şablonlar, IoT Edge test etmek için çalışan bir modül dağıtmak için ihtiyacınız olan tüm dosya ve koda sahiptir veya şablonu kendi iş mantığınızla özelleştirmek için bir başlangıç noktası sağlar. 

1. Visual Studio 2019 ' u başlatın ve **Yeni proje oluştur**' u seçin.

2. Yeni proje penceresinde **IoT Edge** proje arayın ve **Azure IoT Edge (Windows amd64)** projesi seçin. **İleri**’ye tıklayın. 

   ![Yeni bir Azure IoT Edge projesi oluştur](./media/tutorial-csharp-module-windows/new-project.png)

3. Yeni projeyi yapılandırın penceresinde, projeyi ve çözümü **CSharpTutorialApp**gibi açıklayıcı bir şekilde yeniden adlandırın. Projeyi oluşturmak için **Oluştur** ' a tıklayın. 

   ![Yeni bir Azure IoT Edge projesi yapılandırma](./media/tutorial-csharp-module-windows/configure-project.png)

4. IoT Edge uygulama ve modül penceresinde projenizi aşağıdaki değerlerle yapılandırın: 

   | Alan | Değer |
   | ----- | ----- |
   | Şablon seçin | **C# Modül**seçin. | 
   | Modül proje adı | Modülünüze **CSharpModule** adını verin. | 
   | Docker görüntü deposu | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz modül proje adı değerinden önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. Oturum açma sunucusunu Azure portalda kapsayıcı kayıt defterinizin Genel bakış sayfasından alabilirsiniz. <br><br> Son görüntü deposu \<registry name\>.azurecr.io/ckeskinmodulegibi görünüyor. |

   ![Projenizi hedef cihaz, modül türü ve kapsayıcı kayıt defteri için yapılandırma](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Projeyi oluşturmak için **Ekle** ' yi seçin. 

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Dağıtım bildirimi, IoT Edge çalışma zamanına sahip kapsayıcı kayıt defteriniz için kimlik bilgilerini paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar. Azure Container Registry 'nizin **erişim anahtarları** bölümünden kimlik bilgilerini kullanın. 

1. Visual Studio Çözüm Gezgini ' nde **Deployment. Template. JSON** dosyasını açın. 

2. $EdgeAgent istenen özelliklerde **Registrycredentials** özelliğini bulun. 

3. Bu biçimi izleyerek özelliği kimlik bilgilerinizle güncelleştirin: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Deployment. Template. json dosyasını kaydedin. 

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Varsayılan modül kodu bir giriş sırasındaki iletileri alır ve bunları bir çıkış kuyruğu aracılığıyla geçirir. Modülün iletileri IoT Hub iletmek için önce, daha fazla kod ekleyelim. Her iletideki sıcaklık verilerini analiz etmek için modülünü güncelleştirin ve yalnızca sıcaklığın belirli bir eşiği aşması durumunda iletiyi IoT Hub gönderir. 

1. Visual Studio 'da,  > **program.cs** **csharpmodule**' ü açın.

2. **CSharpModule** ad alanının en üst kısmına daha sonra kullanılan türler için üç **using** deyimi yazın:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. **TemperatureThreshold** değişkenini, sayaç değişkeninden sonra **Program** sınıfına ekleyin. TemperatureThreshold değişkeni, ölçülen sıcaklığın, verilerin IoT Hub 'ına gönderilmesi için aşmaması gereken değeri ayarlar. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Değişken bildirimlerinden sonra **Program** sınıfına **MessageBody**, **Machine**ve **Ambient** sınıfları ekleyin. Bu sınıflar gelen iletilerin gövdesi için beklenen şemayı tanımlar.

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

5. **Init** metodunu bulun. Bu yöntem, modülün ileti göndermek ve almak üzere yerel Azure IoT Edge çalışma zamanına bağlanmasına izin veren bir **Moduleclient** nesnesi oluşturur ve yapılandırır. Kod ayrıca, **input1** uç noktası aracılığıyla bir IoT Edge hub 'ından ileti almak için bir geri çağırma kaydeder.

   Init yönteminin tamamını aşağıdaki kodla değiştirin:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Bu güncelleştirilmiş Init yöntemi, ModuleClient ile IoT Edge çalışma zamanına bağlantıyı hala ayarlıyor, ancak yeni işlevsellik de ekliyor. **TemperatureThreshold** değerini almak için Module ikizi 'ın istenen özelliklerini okur. Daha sonra, modül ikizi istenen özellikleri için gelecekteki güncelleştirmeleri dinleyen bir geri çağırma oluşturur. Bu geri çağırmada, Module ikizi içindeki sıcaklık eşiğini uzaktan güncelleştirebilir ve değişiklikler modüle dahil edilir. 

   Güncelleştirilmiş Init yöntemi, var olan **Setınputmessagehandlerasync** metodunu da değiştirir. Örnek kodda, *input1* üzerindeki gelen Iletiler, *pipemessage* işleviyle işlenir, ancak bunu aşağıdaki adımlarda oluşturacağımız *filtermessages* işlevini kullanacak şekilde değiştirmek istiyoruz. 

6. **Program** sınıfına yeni bir **Ondesiredpropertiesupdate** yöntemi ekleyin. Bu yöntem modül ikizinin istenen özellikleri üzerinde yapılan güncelleştirmeleri alır ve **temperatureThreshold** değişkenini buna göre güncelleştirir. Tüm modüllerin, doğrudan buluttan bir modülün içinde çalışan kodu yapılandırmanıza izin veren kendi modül ikizi vardır.

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

7. Örnek **Pipemessage** metodunu kaldırın ve yeni bir **filtermessages** yöntemiyle değiştirin. Modül IoT Edge hub'ından bir ileti aldığında bu yöntem çağrılır. Modül ikizi aracılığıyla ayarlanan sıcaklık eşiğinin altındaki sıcaklıkları rapor eden iletileri filtreler. Ayrıca iletiye **MessageType** özelliğini ekleyip değerini **Alert** olarak ayarlar. 

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
                using(var filteredMessage = new Message(messageBytes))
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

9. IoT Edge çözümünüzde **Deployment. Template. JSON** dosyasını açın. Bu dosya, IoT Edge aracısına hangi modüllerin dağıtılacağını, bu örnekte **SimulatedTemperatureSensor** ve **csharpmodülünü**söyler ve IoT Edge hub 'ına iletileri aralarında nasıl yönlendirildiğini söyler.

10. Dağıtım bildirimine **CSharpModule** modül ikizini ekleyin. Aşağıdaki JSON içeriğini **modulesContent** bölümünün en altına, **$edgeHub** modül ikizinden sonra ekleyin: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dağıtım şablonuna modül ikizi Ekle](./media/tutorial-csharp-module-windows/module-twin.png)

11. Deployment. Template. json dosyasını kaydedin.


## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Bir önceki bölümde bir IoT Edge çözümü oluşturdunuz ve **CSharpModule** modülüne makine sıcaklığının kabul edilebilir eşiğin altında olduğunu bildiren iletileri filtreleyen kodu eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor. 

1. Geliştirme makinenizde Docker 'da oturum açmak için aşağıdaki komutu kullanın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusunu kullanın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   @No__t_0 kullanımını öneren bir güvenlik uyarısı alabilirsiniz. Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

2. Visual Studio Çözüm Gezgini ' nde, derlemek istediğiniz proje adına sağ tıklayın. Varsayılan ad **AzureIotEdgeApp1** ' dir ve bir Windows modülü oluştururken, uzantı **Windows. amd64**olmalıdır. 

3. **Derleme ve gönderme IoT Edge modüllerini**seçin. 

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkinci olarak, hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için `docker build` ' ı çalıştırır. Sonra, görüntü deposunu kapsayıcı Kayıt defterinize göndermek için `docker push` ' ı çalıştırır. 

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Cloud Explorer ve Azure IoT Edge araçları uzantısını kullanın. Zaten senaryonuz için hazırlanan bir dağıtım bildiriminiz var, yapılandırma klasöründeki **Deployment. JSON** dosyası. Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun. 

1. Visual Studio Cloud Explorer 'da IoT cihazlarınızın listesini görmek için kaynakları genişletin. 

2. Dağıtımı almak istediğiniz IoT Edge cihazının adına sağ tıklayın. 

3. **Dağıtım oluştur**' u seçin.

4. Dosya Gezgini 'nde, çözümünüzün yapılandırma klasöründe **Deployment. Windows-AMD64** dosyasını seçin. 

5. Cihazınızın altında listelenen dağıtılan modülleri görmek için Cloud Explorer 'ı yenileyin. 

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve yürütmeye başlar. Cihazda çalışan ve dağıtım bildiriminde bulunmayan modüller durdurulur. Cihazda eksik olan modüller başlatılır. 

İletileri IoT Hub geldikçe görüntülemek için IoT Edge araçları uzantısını kullanabilirsiniz. 

1. Visual Studio Cloud Explorer 'da IoT Edge cihazınızın adını seçin. 

2. **Eylemler** listesinde, **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin. 

3. IoT Hub gelen iletileri görüntüleyin. Bu işlem, iletilerin gelmesi biraz zaman alabilir, çünkü CSharpModule kodunda yaptığımız değişiklikler makine sıcaklığının iletileri göndermeden önce 25 dereceye ulaşmasını bekleyin. Ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler. 

   ![IoT Hub gelen iletileri görüntüle](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için ikizi CSharpModule modülünü kullandık. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio 'da **Deployment. Windows-AMD64. JSON** dosyasını açın. (Deployment. Template dosyası değil. Çözüm Gezgini 'nde yapılandırma dosyasında dağıtım bildirimini görmüyorsanız gezgin araç çubuğunda **tüm dosyaları göster** simgesini seçin.)

2. CSharpModule ikizi bulun ve **temperatureThreshold** parametresinin değerini, en son bildirilen sıcaklığa göre 10 derece daha yüksek olacak şekilde değiştirin. 

3. **Deployment. Windows-AMD64. JSON** dosyasını kaydedin.

4. Güncelleştirilmiş dağıtım bildirimini cihazınıza uygulamak için dağıtım adımlarını yeniden izleyin. 

5. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletilerin durulabileceğini görmeniz gerekir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz. 

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtreleme kodunu içeren bir IoT Edge modülü oluşturdunuz. Kendi modüllerinizi oluşturmaya hazırsanız, [kendi IoT Edge modüllerinizi geliştirme](module-development.md) veya [Visual Studio ile modül geliştirme](how-to-visual-studio-develop-module.md)hakkında daha fazla bilgi edinebilirsiniz. Azure IoT Edge bir sonraki öğreticilere devam ederek, verileri kenarda işlemek ve analiz etmek için Azure Cloud Services 'ı dağıtmanıza nasıl yardımcı olabileceğini öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [İşlevler](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [özel görüntü işleme hizmeti](tutorial-deploy-custom-vision.md)
