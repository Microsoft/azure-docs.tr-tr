---
title: Öğretici-Azure IoT Edge kullanarak Windows için C# modülleri geliştirme
description: Bu öğreticide, C# kodu ile IoT Edge modülleri oluşturma ve bunları IoT Edge çalıştıran Windows cihazlarına dağıtma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 84e5bbff11d0a5ff5d47ca43a3da54d1f1fb5555
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218864"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Öğretici: Windows kapsayıcıları kullanarak C# IoT Edge modülleri geliştirme

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede C# kodu geliştirmek ve Azure IoT Edge çalıştıran bir Windows cihazına dağıtmak için Visual Studio 'Nun nasıl kullanılacağı gösterilmektedir.

>[!NOTE]
>IoT Edge 1,1 LTS, Windows kapsayıcılarını destekleyen son sürüm kanaldır. Sürüm 1,2 ' den başlayarak Windows kapsayıcıları desteklenmez. Windows cihazlarda IoT Edge çalıştırmak için [Windows 'Ta Linux için IoT Edge](iot-edge-for-linux-on-windows.md) kullanmayı veya taşımayı düşünün.

Azure IoT Edge modüllerini, iş mantığınızı doğrudan IoT Edge cihazlarınızda uygulayan kodu dağıtmak için kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * C# SDK 'SıNı temel alan bir IoT Edge modülü oluşturmak için Visual Studio 'Yu kullanın.
> * Visual Studio ve Docker kullanarak bir Docker görüntüsü oluşturun ve bunu Kayıt defterinize yayımlayın.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. Modül, yalnızca sıcaklık belirtilen eşiği aşarsa yukarı akış iletileri gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, Visual Studio 2019 kullanarak C# ' de bir modülün nasıl geliştirilmesi ve sonra bir Windows cihazına dağıtılması gösterilmektedir. Linux kapsayıcıları kullanarak modüller geliştirirseniz bunun yerine [Linux kapsayıcıları kullanarak C# IoT Edge modülleri geliştirme](tutorial-csharp-module.md) bölümüne gidin.

Windows kapsayıcıları kullanarak C# modülleri geliştirme ve dağıtmaya yönelik seçeneklerinizi anlamak için aşağıdaki tabloya bakın:

| C# | Visual &nbsp; Studio &nbsp; kodu | Visual Studio 2017 &nbsp; ve &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 geliştirme | ![Visual Studio Code 'de WinAMD64 için C# modülleri geliştirme](./media/tutorial-c-module/green-check.png) | ![Visual Studio 'da WinAMD64 için C# modülleri geliştirme](./media/tutorial-c-module/green-check.png) |
| Windows AMD64 hata ayıklama |   | ![Visual Studio 'da WinAMD64 için C# modüllerine hata ayıklama](./media/tutorial-c-module/green-check.png) |

Bu öğreticiye başlamadan önce, [Windows kapsayıcıları kullanarak IoT Edge modülleri](tutorial-develop-for-windows.md) geliştirme öğreticisindeki yönergeleri izleyerek geliştirme ortamınızı ayarlayın. Bu işlem tamamlandıktan sonra ortamınız aşağıdaki önkoşulları içerir:

* Azure 'da ücretsiz veya Standart katmanlı [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
* [Azure IoT Edge çalıştıran bir Windows cihazı](how-to-install-iot-edge-windows-on-windows.md).
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) uzantısıyla yapılandırıldı.
* Windows kapsayıcıları çalıştıracak şekilde yapılandırılmış [Docker Desktop](https://docs.docker.com/docker-for-windows/install/).

> [!TIP]
> Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)visual Studio 2017 Için Azure IoT Edge araçları indirin ve yükleyin.

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Bu bölümde, Visual Studio ve Azure IoT Edge araçları uzantısını kullanarak bir IoT Edge modülü projesi oluşturacaksınız. Bir proje şablonu oluşturduktan sonra modül, bildirilen özelliklerine göre iletileri filtreleyerek yeni kod ekleyeceksiniz.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

Azure IoT Edge araçları, Visual Studio 'da desteklenen tüm IoT Edge modül dilleri için proje şablonları sağlar. Bu şablonlar, test IoT Edge için çalışan bir modül dağıtmanız için ihtiyacınız olan tüm dosya ve kodları vardır. Bunlar, kendi iş mantığınızla özelleştirmek için size bir başlangıç noktası da verebilir.

1. Visual Studio 2019 ' i açın ve ardından **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur** bölmesinde **IoT Edge** araması yapın ve ardından sonuçlar listesinde **Azure IoT Edge (Windows amd64)** projesini seçin.

   !["Yeni proje oluştur" bölmesinin IoT Edge ekran görüntüsü.](./media/tutorial-csharp-module-windows/new-project.png)

1. **İleri**’yi seçin.

    **Yeni projenizi yapılandırın** bölmesi açılır.

   !["Yeni projenizi yapılandırma" bölmesinin ekran görüntüsü.](./media/tutorial-csharp-module-windows/configure-project.png)

1. **Yeni projenizi yapılandırın** bölmesinde, proje ve çözümü **CSharpTutorialApp** gibi daha açıklayıcı bir şekilde yeniden adlandırın. 

1. Projeyi oluşturmak için **Oluştur**'u seçin.

   **Modül Ekle** bölmesi açılır.

   ![Projenizi yapılandırmak için "Modül Ekle" bölmesinin ekran görüntüsü.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. **Yeni projenizi yapılandırın** sayfasında, aşağıdakileri yapın:

   a. Sol bölmede **C# modül** şablonunu seçin.  
   b. **Modül adı** kutusuna **csharpmodule** yazın.  
   c. **Depo URL 'si** kutusunda, **localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle aşağıdaki biçimde değiştirin:`<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, Module proje-adı değerinden önceden doldurulur.  Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz.

1. Projeyi oluşturmak için **Ekle** ' yi seçin.

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Dağıtım bildirimi, IoT Edge çalışma zamanına sahip kapsayıcı kayıt defteriniz için kimlik bilgilerini paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar. Azure Container Registry 'nizin **erişim anahtarları** bölümünden kimlik bilgilerini kullanın.

1. Visual Studio Çözüm Gezgini ' de dosya *üzerindedeployment.template.js* açın.

1. $EdgeAgent istenen özelliklerde **Registrycredentials** özelliğini arayın. Özelliğin kayıt defteri adresi, projeyi oluştururken verdiğiniz bilgilerle birlikte tekrar doldurulmalıdır. Kullanıcı adı ve parola alanları, değişken adlarını içermelidir. Örnek:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Modül çözümünüzde ortam (ENV) dosyasını açın. Varsayılan olarak, dosya Çözüm Gezgini gizlidir, bu nedenle görüntülemek için **tüm dosyaları göster** düğmesini seçmeniz gerekebilir. ENV dosyası, dosyasında *deployment.template.js* gördüğünüz Kullanıcı adı ve parola değişkenlerini içermelidir.

1. Azure Container Registry 'nizden **Kullanıcı adı** ve **parola** değerlerini ekleyin.

1. ENV dosyasında yaptığınız değişiklikleri kaydedin.

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Varsayılan modül kodu bir giriş sırasındaki iletileri alır ve bunları bir çıkış kuyruğu aracılığıyla geçirir. Modülün iletileri IoT Hub 'ınıza iletmeden önce, daha fazla kod ekleyelim. Her iletideki sıcaklık verilerini analiz etmek ve yalnızca sıcaklığın belirli bir eşiği aşması durumunda iletiyi IoT Hub 'ına göndermesi için modülü güncelleştirin.

1. Visual Studio 'da **csharpmodule**  >  **program. cs**' yi seçin.

1. **CSharpModule** ad alanının en üst kısmına daha sonra kullanılan türler için üç **using** deyimi yazın:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. **TemperatureThreshold** değişkenini, sayaç değişkeninden sonra **Program** sınıfına ekleyin. TemperatureThreshold değişkeni, ölçülen sıcaklığın, verilerin IoT Hub 'ına gönderilmesi için aşmaması gereken değeri ayarlar.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Değişken bildirimlerinden sonra **Program** sınıfına **MessageBody**, **Machine** ve **Ambient** sınıfları ekleyin. Bu sınıflar gelen iletilerin gövdesi için beklenen şemayı tanımlar.

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

1. **Init** metodunu bulun. Bu yöntem, modülün ileti göndermek ve almak üzere yerel Azure IoT Edge çalışma zamanına bağlanmasına izin veren bir **Moduleclient** nesnesi oluşturur ve yapılandırır. Kod ayrıca, **input1** uç noktası aracılığıyla bir IoT Edge hub 'ından ileti almak için bir geri çağırma kaydeder.

   Init yönteminin tamamını aşağıdaki kodla değiştirin:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Bu güncelleştirilmiş Init yöntemi, ModuleClient ile IoT Edge çalışma zamanına bağlantıyı hala ayarlıyor, ancak yeni işlevsellik de ekliyor. **TemperatureThreshold** değerini almak için Module ikizi 'ın istenen özelliklerini okur. Daha sonra modül ikizi istenen özellikleri için gelecekteki güncelleştirmeleri dinleyen bir geri çağırma oluşturur. Bu geri çağırmada, Module ikizi içindeki sıcaklık eşiğini uzaktan güncelleştirebilir ve değişiklikler modüle dahil edilir.

   Güncelleştirilmiş Init yöntemi, var olan **Setınputmessagehandlerasync** metodunu da değiştirir. Örnek kodda, *input1* üzerindeki gelen Iletiler, *pipemessage* işleviyle işlenir, ancak bunu aşağıdaki adımlarda oluşturacağımız *filtermessages* işlevini kullanacak şekilde değiştirmek istiyoruz.

1. **Program** sınıfına yeni bir **Ondesiredpropertiesupdate** yöntemi ekleyin. Bu yöntem, ikizi modülünden istenen özelliklerde yapılan güncelleştirmeleri alır ve **temperatureThreshold** değişkenini eşleşecek şekilde güncelleştirir. Tüm modüllerin, doğrudan buluttan bir modülün içinde çalışan kodu yapılandırmanıza izin veren kendi modül ikizi vardır.

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

1. Örnek **Pipemessage** metodunu kaldırın ve yeni bir **filtermessages** yöntemiyle değiştirin. Modül IoT Edge hub'ından bir ileti aldığında bu yöntem çağrılır. Bu, ikizi modülü aracılığıyla ayarlanan sıcaklık eşiğinin altına sıcaklıkları rapor eden iletileri filtreler. Yöntemi, **Uyarı** olarak ayarlanan değere sahip **mesajgetype** özelliğini iletiye de ekler.

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

1. *Program.cs* dosyasını kaydedin.

1. IoT Edge çözümünüzdeki dosyada *deployment.template.js* açın. Bu dosya IoT Edge aracısına hangi modüllerin dağıtılacağını söyler ve IoT Edge hub 'ına aralarında iletileri nasıl yönlendirildiğini söyler. Burada, dağıtılacak modüller **SimulatedTemperatureSensor** ve **csharpmodule ' dür**.

1. İkizi **csharpmodule** modülünü dağıtım bildirimine ekleyin. `modulesContent` **$EdgeHub** modülünden sonra, bölümünün sonuna aşağıdaki JSON içeriğini ekleyin: ikizi:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dağıtım şablonuna eklenmekte olan ikizi modülünü gösteren ekran görüntüsü.](./media/tutorial-csharp-module-windows/module-twin.png)

1. *deployment.template.json* dosyasını kaydedin.

## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir eşiğin altında olduğu iletileri filtrelemek için bir IoT Edge çözümü oluşturdunuz ve **Csharpmodule** 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

### <a name="sign-in-to-docker"></a>Docker 'da oturum açın

1. Geliştirme makinenizde Docker 'da oturum açmak için aşağıdaki komutu kullanın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusunu kullanın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu, üretim senaryoları için en iyi uygulama olarak önerdiğimiz halde Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma başvurusu](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Derleme ve gönderme

1. Visual Studio Çözüm Gezgini, derlemek istediğiniz projenin adına sağ tıklayın. Varsayılan ad **AzureIotEdgeApp1** ' dir ve bir Windows modülü oluştururken, uzantının **Windows. amd64** olması gerekir.

1. **Derleme ve gönderme IoT Edge modüllerini** seçin.

   Build ve push komutu üç işlem başlatır:
   * İlk olarak, *yapılandırma* adlı çözümde tam dağıtım bildirimini tutan yeni bir klasör oluşturur. Dağıtım şablonundaki bilgilerden ve diğer çözüm dosyalarında oluşturulmuştur. 
   * İkincisi, `docker build` hedef mimariniz için uygun Dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. 
   * Son olarak, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlı bir şekilde devam eder.

## <a name="deploy-modules-to-the-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Cloud Explorer ve Azure IoT Edge araçları uzantısını kullanın. Senaryonuz için *yapılandırma* klasöründeki *deployment.windows-amd64.js* bir dağıtım bildirimi hazırladınız. Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. Visual Studio Cloud Explorer 'da IoT cihazlarınızın listesini görüntülemek için kaynakları genişletin.

1. Dağıtımı almak istediğiniz IoT Edge cihazının adına sağ tıklayın.

1. **Dağıtım oluştur**' u seçin.

1. Visual Studio dosya Gezgini 'nde, çözümünüzün *yapılandırma* klasöründeki dosya *deployment.windows-amd64.js* seçin.

1. Cihazınızın altında listelenen dağıtılan modülleri görüntülemek için Cloud Explorer 'ı yenileyin.

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra, cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve üzerinde yürütmeye başlar. Cihazda çalışan ancak dağıtım bildirimine dahil olmayan tüm modüller durdurulur. Cihazda eksik olan modüller başlatılır.

IoT Hub 'ınıza geldikçe iletileri görüntülemek için IoT Edge araçları uzantısını kullanabilirsiniz.

1. Visual Studio Cloud Explorer 'da IoT Edge cihazınızın adını seçin.

1. **Eylemler** listesinde, **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

1. IoT Hub 'ınıza ulaşan iletileri görüntüleyin. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması gerektiğinden, iletilerin gelmesi biraz zaman alabilir. CSharpModule kodundaki değişiklikler, iletiler gönderilmeden önce makine sıcaklığının 25 dereceye ulaşması bitinceye kadar beklemeniz gerekir. Kod ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler.

   ![IoT Hub 'ına ulaşan iletileri görüntüleyen çıkış penceresinin ekran görüntüsü.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için ikizi CSharpModule modülünü kullandınız. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio 'da *deployment.windows-amd64.jsdosya üzerinde* açın. 

   *Deployment. Template* *dosyasını açmayın.* Çözüm Gezgini içindeki *yapılandırma* dosyasında dağıtım bildirimini görmüyorsanız, Çözüm Gezgini araç çubuğunda **tüm dosyaları göster** simgesini seçin.

1. CSharpModule ikizi bulun ve **temperatureThreshold** parametresinin değerini, en son bildirilen sıcaklığın üzerinde 5 ila 10 derece daha yüksek olan yeni bir sıcaklığa değiştirin.

1. *deployment.windows-amd64.js* dosyaya kaydedin.

1. Güncelleştirilmiş dağıtım bildirimini cihazınıza uygulamak için dağıtım yönergelerini yeniden izleyin.

1. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletiler durdurulmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önerilen bir sonraki makaleye devam etmeyi planlıyorsanız, bu öğreticide oluşturduğunuz kaynakları ve konfigürasyonları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Aksi takdirde, ücretlendirmesini önlemek için, burada kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtreleme kodunu içeren bir IoT Edge modülü oluşturdunuz.

Azure IoT Edge, verileri işlemek ve analiz etmek için Azure bulut hizmetleri 'ni dağıtmanıza nasıl yardımcı olabileceğini öğrenmek için, sonraki öğreticilere devam edin.

> [!div class="nextstepaction"]
> [Azure işlevleri](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Özel görüntü işleme hizmeti](tutorial-deploy-custom-vision.md)
