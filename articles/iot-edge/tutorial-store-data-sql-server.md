---
title: Öğretici deposu veri SQL modülü - Azure IOT Edge ile | Microsoft Docs
description: Bir SQL Server modülüyle IoT Edge cihazınızda yerel veri depolamayı öğrenin
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: c03b0dcf6a99611a0261fad7c4ba673c3a8932c9
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122844"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Öğretici: SQL Server veritabanları ile verileri kenarda depolayın

Azure IoT Edge çalıştıran bir Linux cihazında veri depolamak için SQL Server modülünü dağıtın.

Uç cihazlarda veri depolamak ve sorgulamak için Azure IoT Edge ve SQL Server işlevlerini kullanın. Azure IOT Edge cihaz çevrimdışı olduğunda iletileri önbelleğe alınması ve bağlantı yeniden kurulduğunda bunları iletmek için temel depolama özellikleri vardır. Ancak sorgu verilerini yerel ortamda sorgulayabilme gibi daha gelişmiş depolama özelliklerine ihtiyaç duyabilirsiniz. IoT Edge cihazlarınız, IoT Hub bir bağlantı sürdürmenize gerek kalmadan daha karmaşık bilgi işlem gerçekleştirmek için yerel veritabanlarını kullanabilir. 

Bu makalede bir IoT Edge cihazına SQL Server veritabanı dağıtma yönergeleri yer almaktadır. IoT Edge cihazında çalışan Azure İşlevleri, gelen verileri yapılandırıp veritabanına gönderir. Bu makaledeki adımlar MySQL veya PostgreSQL gibi kapsayıcılarda çalışan diğer veritabanlarına da uygulanabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz: 

> [!div class="checklist"]
> * Visual Studio Code kullanarak Azure İşlevi oluşturma
> * IoT Edge cihazınıza bir SQL veritabanı dağıtma
> * Visual Studio Code uygulamasını kullanarak modülleri derleme ve IoT Edge cihazınıza dağıtma
> * Oluşturulan verileri görüntüleme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, Linux kapsayıcı geliştirmesi için geliştirme ortamınızı ayarlamak üzere önceki öğreticiden çıkmalısınız: [Linux cihazları için IoT Edge modülleri geliştirin](tutorial-develop-for-linux.md). Bu öğreticiyi tamamlayarak aşağıdaki önkoşulların yerine gelmelidir: 

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge çalıştıran bir Linux cihazı](quickstart-linux.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Bu öğretici, SQL Server veri göndermek için bir Azure Işlevleri modülü kullanır. Azure Işlevleri ile IoT Edge modülü geliştirmek için, aşağıdaki ek önkoşulları geliştirme makinenize yüklersiniz: 

* [C#Visual Studio Code (OmniSharp tarafından desteklenen) uzantısı için Visual Studio Code için](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK'sı](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>İşlev projesi oluşturma

Bir veritabanına veri göndermek için verileri doğru şekilde yapılandırıp tabloda kaydeden bir modüle ihtiyacınız vardır. 

### <a name="create-a-new-project"></a>Yeni bir proje oluşturma

Aşağıdaki adımlarda, Visual Studio Code ve Azure IoT araçlarını kullanarak bir IoT Edge işlevinin nasıl oluşturulacağı gösterilmektedir.

1. Visual Studio Code'u açın.

2. **View (Görünüm)**  > **Command palette (Komut paleti)** öğesini seçerek VS Code komut paletini açın.

3. Komut paletinde komutunu **yazın ve Azure IoT Edge çalıştırın: Yeni IoT Edge çözümü**. Komut paletinde çözümünüzü oluşturmak için aşağıdaki bilgileri girin: 

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Gibi çözümünüz için açıklayıcı bir ad girin **SqlSolution**, veya varsayılan değeri kabul edin. |
   | Modül şablonunu seçin | Seçin **Azure işlevleri - C#** . |
   | Modül adı sağlayın | Modülünüze **sqlFunction** adını verin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı görüntünüz bir önceki adımdaki değerle önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. Oturum açma sunucusunu Azure portalda kapsayıcı kayıt defterinizin Genel bakış sayfasından alabilirsiniz. <br><br>Son dize, kayıt defteri \<adı\>. azurecr.io/SqlFunction gibi görünür. |

   VS Code penceresi IoT Edge çözümü çalışma alanınızı yükler. 
   
### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar.

1. VS Code gezgininde .env dosyasını açın.
2. Alanları Azure kapsayıcı kayıt defterinizden kopyaladığınız **kullanıcı adı** ve **parola** değerleriyle güncelleştirin.
3. Bu dosyayı kaydedin.

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 ve Linux ARM32v7 cihazları için C modülleri geliştirebilir. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır. 

1. Komut paletini açın ve **Azure IoT Edge arayın: Edge çözümü**için varsayılan hedef platformunu ayarlayın veya pencerenin altındaki yan çubukta kısayol simgesini seçin. 

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız. 

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

1. VS Code Gezgininde açın **modülleri** > **sqlFunction** > **sqlFunction.cs**.

2. Dosyanın tüm içeriğini aşağıdaki kodla değiştirin:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
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

3. Dize 35 satırında değiştirin **\<sql bağlantı dizesi\>** aşağıdaki dizeyi. **Veri kaynağı** özelliği, henüz mevcut olmayan SQL Server kapsayıcısına başvurur ancak sonraki bölümde **SQL** adı ile oluşturacaksınız. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Kaydet **sqlFunction.cs** dosya. 

5. Açık **sqlFunction.csproj** dosya.

6. Paket başvuruları grubunu bulun ve SqlClient dahil olmak üzere yeni bir tane ekleyin. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Kaydet **sqlFunction.csproj** dosya.

## <a name="add-the-sql-server-container"></a>SQL Server kapsayıcısını ekleme

[Dağıtım bildirimi](module-composition.md), IoT Edge çalışma zamanının hangi modüllerinin IoT Edge cihazınıza yükleneceğini bildirir. Önceki bölümde özelleştirilmiş bir Işlev modülü oluşturmak için kodu sağladınız, ancak SQL Server modülü zaten Azure Marketi 'nde oluşturulmuş ve kullanılabilir. Tek yapmanız gereken IoT Edge çalışma zamanına bunu dahil etmek ve cihazınızda yapılandırmaktır. 

1. Visual Studio Code ' de, komut paletini **görüntüle** > ' yi seçerek komut paleti ' ni**açın.**

2. Komut paletinde komutunu **yazın ve Azure IoT Edge çalıştırın: IoT Edge modülünü**ekleyin. Komut paletinde, yeni bir modül eklemek için aşağıdaki bilgileri sağlayın: 

   | Alan | Value | 
   | ----- | ----- |
   | Dağıtım şablonu dosyasını seçin | Komut paleti geçerli çözüm klasörünüzdeki Deployment. Template. json dosyasını vurgular. Bu dosyayı seçin.  |
   | Modül şablonunu seçin | **Azure Marketi 'Nden modül**seçin. |

3. Azure IoT Edge Module marketi ' nde, **SQL Server modülünü**arayıp seçin. 

4. Modül adını, tümü küçük harfle **SQL**olarak değiştirin. Bu ad, sqlFunction.cs dosyasındaki bağlantı dizesinde belirtilen kapsayıcı adıyla eşleşir. 

5. Modülü çözümünüze eklemek için **Içeri aktar** ' ı seçin. 

6. Çözüm klasörünüzde **Deployment. Template. JSON** dosyasını açın. 

7. Bulma **modülleri** bölümü. Üç modül görmeniz gerekir. *SimulatedTemperatureSensor* modülü, yeni çözümlerde varsayılan olarak dahil edilir ve diğer modüllerinizle kullanılacak test verileri sağlar. *SqlFunction* modülü, başlangıçta oluşturduğunuz ve yeni kodla güncelleştirdiğiniz modüldür. Son olarak, *SQL* modülü Azure Marketi 'nden içeri aktarıldı. 

   >[!Tip]
   >SQL Server modülü, dağıtım bildiriminin ortam değişkenlerinde varsayılan parola kümesiyle birlikte gelir. Üretim ortamında bir SQL Server kapsayıcısı oluşturduğunuzda [varsayılan sistem yöneticisi parolasını değiştirmeniz gerekir](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. **Deployment. Template. JSON** dosyasını kapatın.

## <a name="build-your-iot-edge-solution"></a>IoT Edge çözümünüzü derleyin

Önceki bölümlerde tek modüle sahip bir çözüm oluşturdunuz ve dağıtım bildirimi şablonuna başka bir modül daha eklediniz. SQL Server modülü Microsoft tarafından genel olarak barındırılır, ancak Işlevler modülündeki kodu kapsayıcılara kapsayılabilmeniz gerekir. Bu bölümde, çözümü derler, sqlFunction modülünün kapsayıcı görüntülerini oluşturursunuz ve görüntüyü kapsayıcı Kayıt defterinize gönderirsiniz. 

1. Visual Studio Code'da **Görünüm** > **Terminal**'i seçerek tümleşik terminali açın.  

1. Görüntüleri kayıt defterinize gönderebilmeniz için kapsayıcı kayıt defterinizi Visual Studio Code'a ekleyin. . Env dosyasına eklediğiniz Azure Container Registry (ACR) kimlik bilgilerini kullanın. Tümleşik terminale aşağıdaki komutu girin:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    --Password-stdin parametresinin kullanımını öneren bir güvenlik uyarısı görebilirsiniz. Bunun kullanımı bu makalenin kapsamında olmasa da bu en iyi yöntemin izlenmesi önerilir. Daha fazla bilgi için [Docker Login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) komut başvurusuna bakın. 

2. VS Code gezgininde **deployment.template.json** dosyasına sağ tıklayıp **Build and Push IoT Edge solution** (IoT Edge Çözümü Oluştur ve Gönder) öğesini seçin. 

Visual Studio Code uygulamasına çözümünüzü derleme komutu verdiğinizde dağıtım şablonundaki bilgileri alır ve **config** adlı yeni bir klasörde deployment.json dosyası oluşturur. Ardından tümleşik terminalde `docker build` ve `docker push` komutlarını çalıştırır. Bu iki komut, kodunuzu derlemek, modül kapsayıcılı hale getirme ve ardından kod çözümü hazırlarken belirttiğiniz kapsayıcı kayıt defterine gönderin. 

SqlFunction modülünün kapsayıcı Kayıt defterinize başarıyla gönderildiği doğrulayabilirsiniz. Azure portal, kapsayıcı Kayıt defterinize gidin. **Depolar** ' ı seçin ve **SqlFunction**araması yapın. Diğer iki modül olan SimulatedTemperatureSensor ve SQL, Microsoft kayıt defterlerine ait depolarından zaten işaret ettiğiniz için kapsayıcı Kayıt defterinize itilmez.

## <a name="deploy-the-solution-to-a-device"></a>Çözümü bir cihaza dağıtma

IoT Hub üzerinden bir cihazda modül ayarlayabilirsiniz ancak IoT Hub ve cihazlara Visual Studio Code aracılığıyla da erişebilirsiniz. Bu bölümde IoT Hub'ınıza erişim ayarlarını yapacak ve ardından çözümünüzü IoT Edge cihazınıza dağıtmak için VS Code uygulamasını kullanacaksınız. 

1. VS Code gezgininde **Azure IoT Hub Devices** (Azure IoT Hub Cihazları) bölümünü seçin. 

2. Dağıtımınızla hedeflemek istediğiniz cihaza sağ tıklayıp **Create deployment for single device** (Tek cihaz için dağıtım oluştur) öğesini seçin. 

3. Dosya Gezgini'nde gidin **config** klasörün içinde çözümünüzün ve **deployment.amd64**. **Select Edge deployment manifest** (Edge dağıtım bildirimini seç) öğesine tıklayın. 

   Dağıtım bildirimi olarak Deployment. Template. json dosyasını kullanmayın.

Dağıtım başarılı olursa VS Code çıkışında onay iletisi yazdırılır. 

Cihazınızı Azure IOT Hub cihazları bölümünde VS Code durumunu yenileyin. Yeni modüller listelenir ve kapsayıcılar yüklendiği ve başlatıldığı sürece bir sonraki birkaç dakikada çalışıyor olarak rapor alınacaktır. Tüm modüllerin cihazınızda çalışıp çalışmadığını da kontrol edebilirsiniz. IoT Edge cihazında modüllerin durumunu görmek için aşağıdaki komutu çalıştırın. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>SQL veritabanını oluşturma

Dağıtım bildirimini cihazınıza uyguladığınızda üç modül çalışmaya başlar. SimulatedTemperatureSensor modülü, sanal ortam verileri oluşturur. sqlFunction modülü verileri alır ve veritabanı için biçimlendirir. Bu bölüm, sıcaklık verilerini depolamak için kullanılacak SQL veritabanını ayarlama sürecinde size yardımcı olacaktır. 

IOT Edge Cihazınızda aşağıdaki komutları çalıştırın. Bu komutlar, cihazınızda çalışan **SQL** modülüne bağlanır ve kendisine gönderilen sıcaklık verilerini tutmak için bir veritabanı ve tablo oluşturur. 

1. IOT Edge Cihazınızda bir komut satırı aracı veritabanınıza bağlanın. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. SQL komut aracını açın.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Veritabanınızı oluşturun: 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Tablonuzu tanımlayın.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server Docker dosyanızı özelleştirerek SQL Server örneğinizin birden fazla IoT Edge cihazına otomatik olarak dağıtılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Microsoft SQL Server kapsayıcısı demo proje](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Yerel verileri görüntüleme

Tablonuz oluşturulduktan sonra sqlFunction modülü verileri IoT Edge cihazınızdaki yerel bir SQL Server 2017 veritabanında depolamaya başlar. 

SQL komut aracında biçimlendirilmiş tablo verilerinizi görüntülemek için aşağıdaki komutu çalıştırın: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Yerel veritabanı içeriğini görüntüleyin](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz. 

Geçmeyecekseniz ücret kesilmesini önlemek için yerel yapılandırmalarınızı ve bu makalede oluşturulan Azure kaynaklarını silebilirsiniz. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, IoT Edge cihazınız tarafından oluşturulan ham verileri filtreleme kodunu içeren bir Azure İşlevleri modülü oluşturdunuz. Kendi modüllerinizi oluşturmaya hazır olduğunuzda [Visual Studio Code için Azure IoT Edge ile Azure İşlevleri Geliştirme](how-to-develop-csharp-function.md) hakkında daha fazla bilgi edinebilirsiniz. 

Kenarda başka bir depolama yöntemi denemek istiyorsanız, IoT Edge Azure Blob Storage kullanma hakkında bilgi edinin. 

> [!div class="nextstepaction"]
> [IoT Edge'de Azure Blob Depolama ile verileri kenarda depolama](how-to-store-data-blob.md)
