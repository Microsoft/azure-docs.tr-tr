---
title: 'Öğretici: Azure Işlevlerini modül olarak dağıtma-Azure IoT Edge'
description: Bu öğreticide, bir Azure Işlevini IoT Edge modülü olarak geliştirirsiniz ve bir uç cihaza dağıtırsınız.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 30abc4a5a1431800cef2bcbda6f5eeedf9a216a3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874648"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Öğretici: Azure Işlevlerini IoT Edge modülleri olarak dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

İş mantığınızı doğrudan Azure IoT Edge cihazlarınıza uygulayan kodu dağıtmak için Azure İşlevleri'ni kullanabilirsiniz. Bu öğretici, sanal IoT Edge cihazında algılayıcı verilerini filtreleyen bir Azure Işlevi oluşturma ve dağıtma konusunda size yol gösterir. Hızlı başlangıçlarda oluşturduğunuz sanal IoT Edge cihazını kullanırsınız. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Işlevi oluşturmak için Visual Studio Code kullanın.
> * VS Code ve Docker kullanarak Docker görüntüsü oluşturma ve bunu kapsayıcı kayıt defterinde yayımlama.
> * Kapsayıcı kayıt defterindeki modülü IoT Edge cihazınıza dağıtma.
> * Filtrelenmiş verileri görüntüleme.

<center>

![Diyagram-öğretici mimarisi: işlev modülünü hazırlama ve dağıtma](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Bu öğreticide oluşturduğunuz Azure Işlevi, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. Bu Işlev yalnızca sıcaklık belirtilen eşiğin üstünde olduğunda Azure IoT Hub yukarı akış iletileri gönderir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, Linux kapsayıcı geliştirmesi için geliştirme ortamınızı ayarlamak üzere önceki öğreticiden çıkmalısınız: [Linux kapsayıcıları kullanarak IoT Edge modülleri](tutorial-develop-for-linux.md)geliştirme. Bu öğreticiyi tamamlayarak aşağıdaki önkoşulların yerine gelmelidir:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Linux kapsayıcılarıyla Azure IoT Edge çalıştıran bir cihaz. Hızlı başlangıçlarını bir [Linux cihazı](quickstart-linux.md) veya [Windows cihazı](quickstart.md)ayarlamak için kullanabilirsiniz.
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Azure Işlevleri ile ' de bir IoT Edge modülü geliştirmek için, aşağıdaki ek önkoşulları geliştirme makinenize yüklersiniz:

* [Visual Studio Code için C# (OmniSharp tarafından desteklenen) uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK'sı](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-function-project"></a>İşlev projesi oluşturma

Önkoşullara yüklediğiniz Visual Studio Code için Azure IoT araçları, bazı kod şablonlarının yanı sıra yönetim özellikleri sağlar. Bu bölümde, Azure Işlevi içeren bir IoT Edge çözümü oluşturmak için Visual Studio Code kullanırsınız.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

Kendi kodunuzla özelleştirebileceğiniz bir C# işlev çözüm şablonu oluşturun.

1. Geliştirme makinenizde Visual Studio Code'u açın.

2.   >  **Komut paleti** göster ' i seçerek vs Code komut paleti ' ni açın.

3. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın. Çözümünüzü oluşturmak için komut paletindeki yönergeleri izleyin.

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için **Functionsolution** gibi açıklayıcı bir ad girin veya varsayılan değeri kabul edin. |
   | Modül şablonunu seçin | **Azure işlevleri-C#**' ı seçin. |
   | Modül adı sağlayın | Modülünüze **CSharpFunction** adını verin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı görüntünüz bir önceki adımdaki değerle önceden doldurulur. **Localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle değiştirin. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz. Son dize \<registry name\> . azurecr.io/CSharpFunction gibi görünür. |

   ![Docker görüntü deposunu sağlama](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials&quot;></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar.

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. VS Code gezgininde .env dosyasını açın.
2. Alanları Azure kapsayıcı kayıt defterinizden kopyaladığınız **kullanıcı adı** ve **parola** değerleriyle güncelleştirin.
3. Bu dosyayı kaydedin.

### <a name=&quot;select-your-target-architecture&quot;></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 ve Linux ARM32v7 cihazları için C modülleri geliştirebilir. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır.

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla** veya pencerenin altındaki yan çubukta kısayol simgesini seçin.

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız.

### <a name=&quot;update-the-module-with-custom-code&quot;></a>Modülü özel kodla güncelleştirme

Modülün iletileri IoT Hub iletmek için önce, daha fazla kod ekleyelim.

1. Visual Studio Code ' de, **modüller**  >  **csharpfunction**  >  **csharpfunction. cs**' yi açın.

1. **Csharpfunction. cs** dosyasının içeriğini aşağıdaki kodla değiştirin. Bu kod, çevresel ve makine sıcaklığı hakkında telemetri alır ve makine sıcaklığı tanımlı bir eşiğin üzerinde ise IoT Hub ileti yalnızca ' a iletir.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName(&quot;CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Dosyayı kaydedin.

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge çözümünüzü derleyin ve gönderin

Önceki bölümde, bir IoT Edge çözümü oluşturdunuz ve, kabul edilen eşiğin altında bildirilen makine sıcaklıkları olan iletileri filtrelemek için **Csharpişlevini** değiştirmiş olursunuz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

1. **Görünüm** terminali ' i seçerek vs Code tümleşik Terminal ' i açın  >  .

2. Terminalde aşağıdaki komutu girerek Docker 'da oturum açın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusu ile oturum açın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

3. VS Code Gezgini 'nde, dosya **üzerindedeployment.template.js** sağ tıklayın ve **Build ve push IoT Edge çözümünü** seçin.

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi oluşturulan tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkincisi, `docker build` hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. Ardından, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır.

## <a name="view-your-container-image"></a>Kapsayıcı görüntünüzü açma

Kapsayıcı görüntünüz, kapsayıcı kayıt defterinize gönderildiğinde Visual Studio Code işlemin başarılı olduğunu belirten bir ileti görüntüler. İşlemin başarılı olduğunu kendiniz onaylamak isterseniz kayıt defterindeki görüntüye bakabilirsiniz.

1. Azure portalında Azure kapsayıcı kayıt defterinize göz atın.
2. **Depoları** seçin.
3. Listede **csharpfunction** deposunu görüyor olmalısınız. Diğer ayrıntıları görmek için bu depoyu seçin.
4. **Etiketler** bölümünde **0.0.1-amd64** etiketini görmeniz gerekir. Bu etiket, derlediğiniz görüntünün sürümünü ve platformunu gösterir. Bu değerler CSharpFunction klasöründeki module.json dosyasında belirlenir.

## <a name="deploy-and-run-the-solution"></a>Çözümü dağıtma ve çalıştırma

Işlev modülünüzü hızlı başlangıçlarda yaptığınız gibi bir IoT Edge cihazına dağıtmak için Azure portal kullanabilirsiniz. Ayrıca modülleri Visual Studio Code'un içinden de dağıtabilir ve izleyebilirsiniz. Aşağıdaki bölümlerde, önkoşullarda listelenen VS Code için Azure IoT araçları kullanılır. Uzantıyı henüz yüklemediyseniz, şimdi yükleyin.

1. Visual Studio Code Gezgini ' nde, **Azure IoT Hub** bölümünde **aygıtlar** ' ı genişleterek IoT cihazları listesini görüntüleyin.

2. IoT Edge cihazınızın adına sağ tıklayın ve ardından **tek cihaz Için dağıtım oluştur**' u seçin.

3. **CSharpFunction** modülünü içeren çözüm klasörüne göz atın. Yapılandırma klasörünü açın, dosya **üzerindedeployment.amd64.js** seçin ve ardından **kenar dağıtım bildirimini Seç**' i seçin.

4. Cihazınızın altında, dağıtılan ve çalışan modüllerin listesini görmek için **modüller** ' i genişletin. Yenile düğmesine tıklayın. **SimulatedTemperatureSensor** modülü ve **$edgeAgent** ve **$EdgeHub** birlikte çalışan yeni **csharpişlevini** görmeniz gerekir.

    Yeni modüllerin gösterilmesi birkaç dakika sürebilir. IoT Edge cihazınızın yeni dağıtım bilgilerini IoT Hub alma, yeni kapsayıcıları başlatma ve sonra durumu IoT Hub olarak bildirme.

   ![Dağıtılan modülleri VS Code'da görüntüleme](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Oluşturulan verileri görüntüleme

Azure IoT Hub çalıştırarak IoT Hub 'ınıza gelen tüm iletileri görebilirsiniz: komut paletinde **yerleşik olay uç noktasını Izlemeye başlayın** .

IoT hub'ınıza belirli bir cihazdan gelen iletilerin gösterilmesi için görünüme filtre de uygulayabilirsiniz. **Azure IoT Hub cihazları** bölümünde cihaza sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

İzleme iletilerini durdurmak için, komut paletinde **Azure IoT Hub: yerleşik olay uç noktasını Izlemeyi durdur** komutunu çalıştırın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Geçmeyecekseniz ücret kesilmesini önlemek için yerel yapılandırmalarınızı ve bu makalede oluşturulan Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, IoT Edge cihazınız tarafından oluşturulan ham verileri filtrelemek için kod içeren bir Azure Işlev modülü oluşturdunuz.

Azure IoT Edge'in verileri iş içgörüsüne çevirmenize yardımcı olabilecek diğer yolları öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Stream Analytics'te kayan pencere kullanarak ortalamaları bulma](tutorial-deploy-stream-analytics.md)
