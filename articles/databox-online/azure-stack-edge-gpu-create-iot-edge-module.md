---
title: GPU ile Azure Stack Edge Pro için C# IoT Edge modülü | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazınıza dağıtılabilecek bir C# IoT Edge modülünü geliştirmeyi öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1aab6fa7a2ea659b489a2e65e2a6a79070edc6b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873774"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>Dosyaları Azure Stack Edge Pro 'ya taşımak için C# IoT Edge modülünü geliştirme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure Stack Edge Pro cihazınızdan dağıtım için IoT Edge modülü oluşturma adımları anlatılmaktadır. Azure Stack Edge Pro, verileri işleyebilmeniz ve ağ üzerinden Azure 'a göndermenizi sağlayan bir depolama çözümüdür.

Azure Stack Edge Pro ile Azure IoT Edge modüllerini kullanarak verileri Azure 'a taşındığını sağlayabilirsiniz. Bu makalede kullanılan modül, bir dosyayı yerel bir paylaşımdan Azure Stack Edge Pro cihazınızdaki bir bulut paylaşımında kopyalama mantığını uygular.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Modüllerinizi depolamak ve yönetmek için bir kapsayıcı kayıt defteri oluşturun (Docker görüntüleri).
> * Azure Stack Edge Pro cihazınızda dağıtmak için bir IoT Edge modülü oluşturun.


## <a name="about-the-iot-edge-module"></a>IoT Edge modülü hakkında

Azure Stack Edge Pro cihazınız, IoT Edge modülleri dağıtabilir ve çalıştırabilir. Edge modülleri, bir cihazdan ileti alma, ileti dönüştürme veya bir IoT Hub ileti gönderme gibi belirli bir görevi gerçekleştiren Docker kapsayıcılarıdır. Bu makalede, dosyaları yerel bir paylaşımdan Azure Stack Edge Pro cihazınızdaki bir bulut paylaşımıyla kopyalayan bir modül oluşturacaksınız.

1. Dosyalar, Azure Stack Edge Pro cihazınızda yerel paylaşıma yazılır.
2. Dosya olay Oluşturucusu, yerel paylaşıma yazılan her dosya için bir dosya olayı oluşturur. Dosya olayları, bir dosya değiştirildiğinde de oluşturulur. Dosya olayları daha sonra IoT Edge hub 'ına gönderilir (IoT Edge çalışma zamanında).
3. IoT Edge özel modül dosya olayını, dosya için göreli yol da içeren bir dosya olay nesnesi oluşturacak şekilde işler. Modül göreli dosya yolunu kullanarak mutlak bir yol oluşturur ve dosyayı yerel paylaşımdan bulut paylaşımıyla kopyalar. Modül daha sonra dosyayı yerel paylaşımdan siler.

![Azure IoT Edge modülünün Azure Stack Edge Pro 'da çalışması](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

Dosya bulut paylaşımından olduktan sonra otomatik olarak Azure depolama hesabınıza yüklenir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- Çalıştıran bir Azure Stack Edge Pro cihazı.

    - Cihazda ayrıca ilişkili bir IoT Hub kaynağı vardır.
    - Cihazda sınır hesaplama rolü yapılandırıldı.
    Daha fazla bilgi için Azure Stack Edge Pro için [Işlem yapılandırma](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute) bölümüne gidin.<!--Update link?-->

- Aşağıdaki geliştirme kaynakları:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Visual Studio Code için C# (OmniSharp tarafından desteklenen) uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Visual Studio Code için Azure IoT Edge uzantısı](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK'sı](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Yazılımı indirmek ve yüklemek için bir hesap oluşturmanız gerekebilir.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Azure kapsayıcı kayıt defteri, Azure’da özel Docker kapsayıcısı görüntülerinizi depolayıp yönetebileceğiniz özel bir Docker kayıt defteridir. Bulutta bulunan iki popüler Docker kayıt defteri hizmeti Azure Container Registry ve Docker Hub 'lardır. Bu makale Container Registry kullanır.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. **Container Registry > kaynak > kapsayıcıları oluştur**' u seçin. **Oluştur**’a tıklayın.
3. Girmelisiniz

   1. Azure 'da 5 ile 50 alfasayısal karakter içeren benzersiz bir **kayıt defteri adı** .
   2. Bir **abonelik** seçin.
   3. Yeni oluşturun veya var olan bir **kaynak grubunu** seçin.
   4. Bir **konum** seçin. Bu konumun Azure Stack Edge kaynağıyla ilişkili ile aynı olmasını öneririz.
   5. **Yönetici kullanıcı** ayarını **Etkinleştir**'e getirin.
   6. SKU 'YU **temel** olarak ayarlayın.

      ![Kapsayıcı kayıt defteri oluşturma](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. **Oluştur**’u seçin.
5. Kapsayıcı kayıt defteriniz oluşturulduktan sonra, bu kayıt defterine gidin ve **Erişim anahtarları**'nı seçin.

    ![Erişim anahtarları al](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. **Oturum açma sunucusu**, **Kullanıcı adı** ve **Parola** değerlerini kopyalayın. Bu değerleri daha sonra Docker görüntüsünü Kayıt defterinize yayımlamak ve Azure IoT Edge çalışma zamanına kayıt defteri kimlik bilgilerini eklemek için kullanın.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge modülü projesi oluşturma

Aşağıdaki adımlarda, .NET Core 2,1 SDK temel alınarak bir IoT Edge modülü projesi oluşturulur. Proje Visual Studio Code ve Azure IoT Edge uzantısını kullanır.

### <a name="create-a-new-solution"></a>Yeni çözüm oluşturma

Kendi yazacağınız kodla özelleştirebileceğiniz bir C# çözüm şablonu oluşturun.

1. Visual Studio Code ' de, VS Code komut paletini açmak için **> komut paleti görüntüle** ' yi seçin.
2. Komut paletinde **Azure: Sign in** komutunu girip çalıştırdıktan sonra yönergeleri izleyerek Azure hesabınızda oturum açın. Oturumu önceden açtıysanız bu adımı atlayabilirsiniz.
3. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın. Komut paletinde çözümünüzü oluşturmak için aşağıdaki bilgileri girin:

    1. Çözümü oluşturmak istediğiniz klasörü seçin.
    2. Çözümünüz için bir ad girin veya varsayılan **EdgeSolution** adını kabul edin.
    
        ![Yeni çözüm oluştur 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. Modül şablonu olarak **C# Module** girişini seçin.
    4. Varsayılan modül adını atamak istediğiniz adla değiştirin, bu durumda **Filecopymodule** olur.
    
        ![Yeni çözüm oluştur 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. Önceki bölümde, ilk modülünüzün görüntü deposu olarak oluşturduğunuz kapsayıcı kayıt defterini belirtin. **localhost:5000** yerine kopyaladığınız oturum açma sunucusu değerini yazın.

        Son dize gibi görünür `<Login server name>/<Module name>` . Bu örnekte, dize: `mycontreg2.azurecr.io/filecopymodule` .

        ![Yeni çözüm oluştur 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. **Dosya > klasörü aç**' a gidin.

    ![Yeni çözüm oluştur 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. Daha önce oluşturduğunuz **EdgeSolution** klasörüne gidin ve bu klasöre gelin. VS Code pencere, IoT Edge çözüm çalışma alanınızı beş üst düzey bileşeni ile yükler. `.vscode`Bu makaledeki klasörü, **. gitignore** dosyasını, **. env** dosyasını ve * * üzerindeki deployment.template.jsdüzenleyemezsiniz.
    
    Değiştirdiğiniz tek bileşen modüller klasörüdür. Bu klasörde modülünüzü kapsayıcı görüntüsü olarak derlemek için modülünüzün ve Docker dosyalarınızın C# kodu bulunur.

    ![Yeni çözüm oluştur 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

1. VS Code Gezgini 'nde, **> FileCopyModule > program. cs adlı modülleri** açın.
2. **Filecopymodule ad alanının** en üstünde, daha sonra kullanılan türler için aşağıdaki using deyimlerini ekleyin. **Microsoft. Azure. Devices. Client. Transport. MQTT** , IoT Edge hub 'ına ileti göndermek için bir protokoldür.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. **Inputfolderpath** ve **Outputfolderpath** değişkenini program sınıfına ekleyin.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Önceki adımdan hemen sonra, ileti gövdesini tanımlamak için **Fileevent** sınıfını ekleyin.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. **Init yönteminde**, kod bir **moduleclient** nesnesi oluşturur ve yapılandırır. Bu nesne, modülün ileti göndermek ve almak için MQTT protokolünü kullanarak yerel Azure IoT Edge çalışma zamanına bağlanmasını sağlar. Init yönteminde kullanılan bağlantı dizesi, modüle IoT Edge çalışma zamanı tarafından sağlanır. Kod, **input1** uç noktası aracılığıyla bir IoT Edge hub 'ından ileti almak Için bir dosya kopyası geri çağırması kaydeder. **Init metodunu** aşağıdaki kodla değiştirin.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. **Pipemessage yöntemi** için kodu kaldırın ve onun yerine, **FileCopy** için kodu ekleyin.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Bu dosyayı kaydedin.
8. Ayrıca, bu proje için [varolan bir kod örneğini indirebilirsiniz](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) . Daha sonra, kaydettiğiniz dosyayı bu örnekteki **program. cs** dosyasına göre doğrulayabilirsiniz.

## <a name="build-your-iot-edge-solution"></a>IoT Edge çözümünüzü derleyin

Önceki bölümde, dosyaları yerel paylaşımdan bulut paylaşımıyla kopyalamak için bir IoT Edge çözümü oluşturdunuz ve FileCopyModule 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

1. VSCode 'da, yeni bir Visual Studio Code tümleşik Terminal açmak için Terminal > yeni Terminal ' a gidin.
2. Tümleşik terminalde aşağıdaki komutu girerek Docker 'da oturum açın.

    `docker login <ACR login server> -u <ACR username>`

    Kapsayıcı Kayıt defterinizden kopyaladığınız oturum açma sunucusunu ve Kullanıcı adını kullanın.

    ![IoT Edge çözümü oluşturun ve gönderin](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Parola istendiğinde parolayı girin. Ayrıca, oturum açma sunucusu, Kullanıcı adı ve parola değerlerini, Azure portal kapsayıcı kayıt defterinizde **erişim tuşlarından** alabilirsiniz.
 
3. Kimlik bilgileri sağlandığında modül görüntünüzü Azure Container Registry 'nize gönderebilirsiniz. VS Code Gezgini 'nde, dosya **üzerindemodule.js** sağ tıklayın ve **Build ve push IoT Edge çözümünü** seçin.

    ![Derleme ve gönderme IoT Edge çözüm 2](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Çözümünüzü derlemek için Visual Studio Code söylemeniz durumunda, tümleşik terminalde iki komut çalıştırılır: Docker Build ve Docker Push. Bu iki komut kodunuzu derler, CSharpModule.dll ile kapsayıcı oluşturur ve ardından kodu, çözümü başlatırken belirttiğiniz kapsayıcı kayıt defterine gönderir.

    Modül platformunu seçmeniz istenir. Linux 'a karşılık gelen *AMD64* öğesini seçin.

    ![Platform seçin](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Yalnızca Linux modülleri desteklenir.

    Yoksayabilirsiniz aşağıdaki uyarıyı görebilirsiniz:

    *Program. cs (77, 44): uyarı CS1998: Bu zaman uyumsuz yöntemde ' await ' işleçleri yok ve zaman uyumlu olarak çalışacak. Engelleyici olmayan API çağrılarını beklemek için ' await ' işlecini veya bir arka plan iş parçacığında CPU 'ya bağlı çalışma yapmak için ' await Task. Run (...) ' kullanmayı düşünün.*

4. VS Code tümleşik terminalinde etiketle tam kapsayıcı görüntü adresini görebilirsiniz. Görüntü adresi, dosyadaki module.jsbiçimdeki bilgilerden oluşturulur `<repository>:<version>-<platform>` . Bu makalede, şöyle görünmelidir `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` .

## <a name="next-steps"></a>Sonraki adımlar

Bu modülü Azure Stack Edge Pro 'da dağıtmak ve çalıştırmak için, [modül ekleme](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module)içindeki adımlara bakın.<!--Update link?-->
