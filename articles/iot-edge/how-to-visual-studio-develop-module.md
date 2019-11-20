---
title: Visual Studio 'da modül geliştirme ve hata ayıklama-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge için modülleri geliştirmek ve hatalarını ayıklamak için Visual Studio 2019 kullanın
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ed668f744716b062fd70d2f63d89152f6fc8a902
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999133"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Azure IoT Edge için modülleri geliştirmek ve hatalarını ayıklamak için Visual Studio 2019 kullanın

İçin Azure IOT Edge modülleri, iş mantığınızı kapatabilirsiniz. Bu makalede, modülleri geliştirmek ve hatalarını ayıklamak için Visual Studio 2019 ' i ana araç olarak nasıl kullanacağınız gösterilmektedir.

Visual Studio için Azure IOT Edge araçları aşağıdaki avantajları sağlar:

- Yerel geliştirme bilgisayarınızda Azure IoT Edge çözümleri ve modülleri oluşturun, düzenleyin, derleyin, çalıştırın ve hata ayıklayın.
- Azure IOT Hub aracılığıyla Azure IOT Edge cihazı için Azure IOT Edge çözümünüzü dağıtın.
- Azure IoT modüllerinizi C 'de veya C# Visual Studio geliştirmenin tüm avantajlarına sahip olacak şekilde kodlayın.
- Azure IOT Edge cihazları ve kullanıcı Arabirimi modülleri yönetir.

Bu makalede, IoT Edge modüllerinizi geliştirmek için Visual Studio 2019 Azure IoT Edge araçlarının nasıl kullanılacağı gösterilmektedir. Ayrıca, projenizi Azure IOT Edge cihazınıza dağıtmayı öğrenin. Şu anda Visual Studio 2019, C ve C#içinde yazılmış modüller için destek sağlar. Desteklenen cihaz mimarileri Windows x64 ve Linux x64 veya ARM32. Desteklenen işletim sistemleri, diller ve mimariler hakkında daha fazla bilgi için bkz. [dil ve mimari desteği](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Önkoşullar

Bu makalede, bir bilgisayar veya geliştirme makinenize Windows çalıştıran sanal makine kullandığınızı varsayar. Windows bilgisayarlarda, Windows veya Linux modülleri geliştirebilirsiniz. Windows modülleri geliştirmek için 1809/Build 17763 veya daha yeni sürümünü çalıştıran bir Windows bilgisayarı kullanın. Linux modülleri geliştirmek için, [Docker Desktop gereksinimlerini](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)karşılayan bir Windows bilgisayarı kullanın. 

Bu makale, ana geliştirme aracı olarak Visual Studio 2019 ' i kullandığından, Visual Studio 'yu yükler. Visual Studio 2019 yüklemenizde **Azure geliştirme** ve **masaüstü geliştirme C++**  iş yüklerine dahil ettiğinizden emin olun. Gerekli iş yüklerini eklemek için [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) ' i değiştirebilirsiniz.

Visual Studio 2019 ' i kullanmaya başladıktan sonra, aşağıdaki araçlara ve bileşenlere de ihtiyacınız vardır:

- Visual Studio 2019 ' de bir IoT Edge projesi oluşturmak için Visual Studio marketi ' nden [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) indirin ve yükleyin.

> [!TIP]
> Visual Studio 2017 kullanıyorsanız, lütfen Visual Studio marketi 'nden VS 2017 için [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) indirip yükleyin

- Modül görüntülerinizi derlemek ve çalıştırmak için, geliştirme makinenize [Docker Community Edition](https://docs.docker.com/install/) indirin ve yükleyin. Docker CE 'yi Linux kapsayıcı modunda veya Windows kapsayıcı modunda çalışacak şekilde ayarlamanız gerekir.

- [Azure IoT EdgeHub geliştirme aracı](https://pypi.org/project/iotedgehubdev/)'nı yükleyerek IoT Edge çözümünüzü hata ayıklamak, çalıştırmak ve test etmek için yerel geliştirme ortamınızı ayarlayın. [Python (2.7/3.6 +) ve PIP](https://www.python.org/) 'yi yükleyip terminalinizde aşağıdaki komutu çalıştırarak **ıotedgehubdev** paketini yükledikten sonra. Azure IOT EdgeHub geliştirme aracı sürümünüz 0.3.0 büyük olduğundan emin olun.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Depoyu kopyalayın ve Vcpkg kitaplığı yöneticisini yükledikten sonra Windows için **Azure-IoT-SDK-c paketini** yükledikten sonra.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) veya [Docker Hub 'ı](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Prototip ve test etme amacıyla bir bulut kayıt defteri yerine yerel bir Docker kayıt defteri kullanabilirsiniz.

- Modülünüzü bir cihazda test etmek için en az bir IoT Edge cihazıyla etkin bir IoT Hub 'ınız olması gerekir. Bilgisayarınızı IoT Edge bir cihaz olarak kullanmak için, [Linux](quickstart-linux.md) veya [Windows](quickstart.md)Hızlı Başlangıç bölümündeki adımları izleyin. Geliştirme makinenizde IoT Edge Daemon çalıştırıyorsanız, Visual Studio 'da geliştirmeye başlamadan önce EdgeHub ve EdgeAgent 'ı durdurmanız gerekebilir.

### <a name="check-your-tools-version"></a>Araçlar sürümünüzü kontrol edin

1. **Araçlar** menüsünde **Uzantılar ve güncelleştirmeler**' i seçin. **Yüklü > araçları** ' nı genişletin ve **Visual Studio için** **Azure IoT Edge araçları** ve bulut Gezgini ' ni bulabilirsiniz.

1. Yüklü sürüm unutmayın. Bu sürüm Visual Studio Market'te en son sürümle karşılaştırabilirsiniz ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Sürümünüz Visual Studio Market ' den daha eskiyse, Visual Studio 'da aşağıdaki bölümde gösterildiği gibi araçlarınızı güncelleştirin.

### <a name="update-your-tools"></a>Sizin araçlarınız güncelleştir

1. **Uzantılar ve güncelleştirmeler** iletişim kutusunda **Visual Studio Market > güncelleştirmeler**' i genişletin, **Azure IoT Edge araçlar** ' ı veya **Visual Studio Için bulut Gezgini** ' ni seçin ve **Güncelleştir**' i seçin.

1. Araçları güncelleştirme yüklendikten sonra Visual Studio Araçları VSIX Yükleyicisi'ni kullanarak güncelleştirme tetikleyiciye kapatın.

1. Yükleyicide başlamak için **Tamam** ' ı seçin ve ardından araçları güncelleştirmek için **değiştirin** .

1. Güncelleştirme tamamlandıktan sonra, **Kapat** ' ı seçin ve Visual Studio 'yu yeniden başlatın.

### <a name="create-an-azure-iot-edge-project"></a>Azure IOT Edge projesi oluşturma

Visual Studio'da Azure IOT Edge proje şablonu, Azure IOT hub'ı Azure IOT Edge cihazlarına dağıtılabilir bir proje oluşturur. İlk olarak bir Azure IoT Edge çözümü oluşturun ve ardından bu çözümdeki ilk modülü oluşturun. Her IOT Edge çözüm, birden fazla modülü içerebilir.

> [!TIP]
> Visual Studio tarafından oluşturulan IoT Edge proje yapısı Visual Studio Code ile aynı değildir.

1. Visual Studio yeni proje iletişim kutusunda **Azure IoT Edge** proje ' yi arayıp seçin ve **İleri**' ye tıklayın. Proje yapılandırması penceresinde, projeniz için bir ad girin ve konumu belirtin ve ardından **Oluştur**' u seçin. Varsayılan proje adı **AzureIoTEdgeApp1**.

   ![Yeni proje oluştur](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. **IoT Edge uygulama ve Modül Ekle** penceresinde,  **C# Module** veya **C modülünü** seçin ve ardından modül adı ve modül görüntü deponuzu belirtin. Visual Studio, modül adını **localhost: 5000/\>< modüllü**olarak doldurur. Kayıt defteri kendi bilgilerinizle değiştirin. Test için yerel bir Docker kayıt defteri kullanıyorsanız, **localhost** sorunsuz olur. Azure Container Registry kullanırsanız, oturum açma sunucusu defterinizin ayarlarından'ni kullanın. Oturum açma sunucusu,   **_\<kayıt defteri adı\>_ . azurecr.io**gibi görünür. Son sonucun  **\< *kayıt defteri*adı\> _\<\>_** gibi görünmesi için dizenin **localhost: 5000** kısmını yalnızca değiştirin. azurecr.io/modül adı. Varsayılan modül adı **IotEdgeModule1**

   ![Uygulama ve Modül Ekle](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Ya da C# C kullanan bir modülle Azure IoT Edge çözümünü oluşturmak için Tamam ' ı seçin.

Artık bir **AzureIoTEdgeApp1. Linux. amd64** projeniz veya bir **AzureIoTEdgeApp1. Windows. amd64** projeniz ve ayrıca çözümünüzde bir **IotEdgeModule1** projesi vardır. Her bir **AzureIoTEdgeApp1** projesi, `deployment.template.json` IoT Edge çözümünüz için derlemek ve dağıtmak istediğiniz modülleri tanımlayan bir dosyasına sahiptir ve ayrıca modüller arasındaki yolları tanımlar. Varsayılan çözümde bir **SimulatedTemperatureSensor** modülü ve bir **IotEdgeModule1** modülü vardır. **SimulatedTemperatureSensor** modülü, **IotEdgeModule1** modülüne sanal verileri üretir, ancak **IotEdgeModule1** modülündeki varsayılan kod, Azure IoT Hub ileti alma işlemleri için doğrudan kanallar sağlar.

**IotEdgeModule1** projesi bir C# modülle bir .NET Core 2,1 konsol uygulamasıdır. Windows kapsayıcısı veya Linux kapsayıcısı ile çalışan IoT Edge cihazınız için gereken gerekli Docker dosyalarını içerir. `module.json` Dosya, bir modülün meta verilerini açıklar. Azure IoT cihaz SDK 'sını bağımlılık olarak alan gerçek modül kodu, `Program.cs` veya `main.c` dosyasında bulunur.

## <a name="develop-your-module"></a>Modülü geliştirme

Çözümle birlikte gelen varsayılan modül kodu **IotEdgeModule1** > **program.cs** (for C#) veya **Main. c** (c) konumundadır. Modül ve `deployment.template.json` dosya, çözümü derlemek, kapsayıcı Kayıt defterinize gönderebilmeniz ve herhangi bir koda dokunmadan teste başlamak için bir cihaza dağıtabilmeniz için ayarlanır. Modül bir kaynaktan giriş almak için oluşturulmuştur (Bu durumda, verileri taklit eden **SimulatedTemperatureSensor** modülü) ve Azure IoT Hub 'yi kanal oluşturarak.

Modül şablonunu kendi kodunuzla özelleştirmeye hazır olduğunuzda, güvenlik, cihaz yönetimi ve güvenilirlik gibi IoT çözümlerinin temel ihtiyaçlarını ele alan modüller oluşturmak için [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 'larını kullanın.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>IoT Edge cihaz bağlantı dizesiyle ıotedgehubdev başlatma

1. Herhangi bir IoT Edge cihazının bağlantı dizesini Visual Studio Cloud Explorer 'daki **birincil bağlantı dizesinden** kopyalayın. Bir IoT Edge cihazının simgesi Edge olmayan bir cihazın simgesinden farklı olduğundan, uç olmayan bir cihazın bağlantı dizesini kopyalamadığınızdan emin olun.

   ![Edge cihaz bağlantı dizesini kopyalayın](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. **Araçlar Azure IoT Edge** **Araçlar**kurulum IoT Edge simülatörü ' ne gidin, bağlantı dizesini yapıştırın ve Tamam ' a tıklayın. >  > 

   ![Küme kenar bağlantı dizesi penceresini açın](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. İlk adımdan bağlantı dizesini girip **Tamam**' ı seçin.

> [!NOTE]
> Sonuçlar, sonraki tüm Azure IoT Edge çözümlerine otomatik olarak uygulandığından, geliştirme bilgisayarınızda yalnızca bir kez bu adımları izlemeniz gerekir. Bu yordam, farklı bir bağlantı dizesine değiştirmeniz gerekiyorsa yeniden izlenebilir.

## <a name="build-and-debug-single-module"></a>Tek modülle derleme ve hata ayıklama

Genellikle, birden çok modülle bir çözümün tamamında çalıştırmadan önce her modülü test etmek ve hatalarını ayıklamak isteyeceksiniz.

1. **IotEdgeModule1** öğesine sağ tıklayın ve bağlam menüsünden **Başlangıç projesi olarak ayarla** ' yı seçin.

   ![Başlangıç projesi ayarlama](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Modülü çalıştırmak için **F5** tuşuna basın veya aşağıdaki düğmeye tıklayın; Bu işlem ilk defa&ndash;10 20 saniye sürebilir.

   ![Modül çalıştırın](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Modül başarıyla başlatılmışsa bir .NET Core konsol uygulaması başlatın.

   ![Çalışan Modülü](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. İçinde C#geliştirme yapıyorsanız, `PipeMessage()` **program.cs**işlevindeki bir kesme noktası ayarlayın; C kullanıyorsanız `InputQueue1Callback()` , **Main. c**içindeki işlevde bir kesme noktası ayarlayın. Ardından, **Git Bash** veya **WSL Bash** kabuğu 'nda aşağıdaki komutu çalıştırarak bir ileti göndererek test edebilirsiniz. ( `curl` Komutu bir PowerShell veya komut isteminden çalıştıramazsınız.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Tek bir modülün hatalarını ayıklamak](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Kesme noktasını tetikledi. Değişkenleri Visual Studio **Yereller** penceresinde izleyebilirsiniz.

   > [!TIP]
   > Bunun yerine `curl`Ileti göndermek için [POSTMAN](https://www.getpostman.com/) veya diğer API araçlarını da kullanabilirsiniz.

1. Tuşuna **Ctrl + F5 tuşlarına basarak** veya hata ayıklamayı durdurmak için Durdur düğmesini tıklatın.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Derleme ve IOT Edge çözüm birden çok modül ile hata ayıklama

Tek bir modülün geliştirilmesini tamamladıktan sonra, birden çok modülle bir çözümün tamamını çalıştırmak ve hatalarını ayıklamak isteyebilirsiniz.

1. **AzureIoTEdgeApp1** öğesine sağ tıklayıp**Yeni IoT Edge modülü** **Ekle** > ' yi seçerek çözüme ikinci bir modül ekleyin. İkinci modülün varsayılan adı **IotEdgeModule2** ' dür ve başka bir kanal modülü olarak görev görür.

1. Dosyayı `deployment.template.json` açtığınızda **modüller** bölümüne **IotEdgeModule2** eklendiğini görürsünüz. Değiştirin **yollar** aşağıdaki bölümü. Modül adlarınızı özelleştirdiyseniz, bu adları eşleşecek şekilde güncelleştirdiğinizden emin olun.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. **AzureIoTEdgeApp1** öğesine sağ tıklayın ve bağlam menüsünden **Başlangıç projesi olarak ayarla** ' yı seçin.

1. Kesme noktalarınızı oluşturun ve ardından aynı anda birden fazla modülü çalıştırmak ve hatalarını ayıklamak için **F5** 'e basın Farklı bir modülü temsil eden birden çok .NET Core konsol uygulaması penceresi görmeniz gerekir.

   ![Birden çok modül hata ayıklama](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Hata ayıklamayı durdurmak için **CTRL + F5** tuşlarına basın veya Durdur düğmesini seçin.

## <a name="build-and-push-images"></a>Derleme ve görüntüleri gönderme

1. **AzureIoTEdgeApp1** 'in başlangıç projesi olduğundan emin olun. Modül görüntüleriniz için derlemek üzere yapılandırma olarak **Hata Ayıkla** veya **Yayınla** ' yı seçin.

    > [!NOTE]
    > **Hata ayıklamayı**seçerken, Visual Studio Docker görüntülerini oluşturmak için kullanır `Dockerfile.(amd64|windows-amd64).debug` . Bu .NET Core komut satırı hata ayıklayıcı VSDBG kapsayıcı görüntünüzü oluşturma sırasında içerir. Üretime yönelik kullanıma yönelik IoT Edge modüller için, vsdbg olmadan kullanılan `Dockerfile.(amd64|windows-amd64)` **yayın** yapılandırmasını kullanmanızı öneririz.

1. Azure Container Registry gibi özel bir kayıt defteri kullanıyorsanız, oturum açmak için aşağıdaki Docker komutunu kullanın. Yerel kayıt defteri kullanıyorsanız [yerel bir kayıt defteri çalıştırabilirsiniz](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry gibi özel bir kayıt defteri kullanıyorsanız, kayıt defteri oturum açma bilgilerinizi dosyada `deployment.template.json`bulunan çalışma zamanı ayarlarına eklemeniz gerekir. Yer tutucuları gerçek ACR yönetici kullanıcı adınızla, parolanızla ve kayıt defteri adıyla değiştirin.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. **AzureIoTEdgeApp1** öğesine sağ tıklayın ve her modül Için Docker görüntüsünü derlemek ve göndermek üzere **Oluştur ve Gönder uç çözümünü** seçin.

   ![Derleme ve görüntüleri gönderme](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Çözümü dağıtma

IoT Edge cihazınızı ayarlamak için kullandığınız hızlı başlangıç makalesinde Azure portalı kullanarak bir modül dağıttınız. Modüller için Visual Studio Cloud Explorer'ı kullanarak da dağıtabilirsiniz. Senaryonuz için hazırlanmış bir dağıtım bildiriminiz zaten var, `deployment.json` dosya ve tüm yapmanız gereken, dağıtımı alacak bir cihaz seçin.

1. Açık **Cloud Explorer** tıklayarak **görünümü** > **Cloud Explorer**. Visual Studio 2019 ' de oturum açtığınızdan emin olun.

1. İçinde **Cloud Explorer**, aboneliğinizi ve Azure IOT Hub'ınıza ve dağıtmak istediğiniz Azure IOT Edge cihazı bulun.

1. Dağıtım oluşturmak için IoT Edge cihaza sağ tıklayın, altında `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`dağıtım bildirim dosyasını seçmeniz gerekir.

   > [!NOTE]
   > Seçmelisiniz değil `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. **SimulatedTemperatureSensor** modülüyle ve **$edgeAgent** ve **$edgeHub**birlikte çalışan yeni modülleri görmek için Yenile düğmesine tıklayın.

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

1. Belirli bir cihaz için D2C iletisini izlemek üzere, listeden cihazı seçin ve ardından **eylem** penceresinde **yerleşik olay uç noktasını izlemeye başla** ' ya tıklayın.

1. İzleme verilerini durdurmak için listeden cihazı seçin ve ardından **eylem** penceresinde **yerleşik olay uç noktasını İzlemeyi Durdur** ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge cihazlarınız için özel modüller geliştirmek üzere [Azure IoT Hub SDK 'Larını anlayın ve kullanın](../iot-hub/iot-hub-devguide-sdks.md).
