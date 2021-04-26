---
title: Azure IoT Edge için modüller geliştirin ve hata ayıklayın | Microsoft Docs
description: C#, Python, Node.js, Java veya C kullanarak Azure IoT Edge için modül geliştirmek, derlemek ve hata ayıklamak için Visual Studio Code kullanın
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 7cc5061911ffc2cbc91dfa8c0d2fd1a6e59ff1a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875674"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Azure IoT Edge için modülleri geliştirmek ve hatalarını ayıklamak için Visual Studio Code kullanın

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

İş mantığınızı Azure IoT Edge için modüllere dönüştürebilirsiniz. Bu makalede, modülleri geliştirme ve hata ayıklama için ana araç olarak Visual Studio Code nasıl kullanacağınız gösterilmektedir.

Visual Studio Code ' de C#, Node.js veya Java 'da yazılan modüllerde hata ayıklamanın iki yolu vardır: bir işlemi bir modül kapsayıcısına iliştirebilir veya hata ayıklama modunda modül kodunu başlatabilirsiniz. Python veya C 'de yazılan modüllerde hata ayıklamak için yalnızca Linux AMD64 kapsayıcılarındaki bir işleme ekleyebilirsiniz.

Visual Studio Code hata ayıklama yeteneklerini bilmiyorsanız, [hata ayıklama](https://code.visualstudio.com/Docs/editor/debugging)hakkında bilgi edinin.

Bu makalede birden çok mimaride birden çok dilde modül geliştirme ve hata ayıklama için yönergeler sağlanmaktadır. Şu anda Visual Studio Code C#, C, Python, Node.js ve Java 'da yazılan modüller için destek sağlar. Desteklenen cihaz mimarileri x64 ve ARM32. Desteklenen işletim sistemleri, diller ve mimariler hakkında daha fazla bilgi için bkz. [dil ve mimari desteği](module-development.md#language-and-architecture-support).

>[!NOTE]
>Linux ARM64 cihazları için geliştirme ve hata ayıklama desteği [genel önizlemeye](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)sunuldu. Daha fazla bilgi için bkz. [Visual Studio Code IoT Edge modüllerini geliştirme ve hata ayıklama (Önizleme)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Önkoşullar

Geliştirme makineniz olarak Windows, macOS veya Linux çalıştıran bir bilgisayar veya sanal makine kullanabilirsiniz. Windows bilgisayarlarda, Windows veya Linux modülleri geliştirebilirsiniz. Windows modülleri geliştirmek için 1809/Build 17763 veya daha yeni sürümünü çalıştıran bir Windows bilgisayarı kullanın. Linux modülleri geliştirmek için, [Docker Desktop gereksinimlerini](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)karşılayan bir Windows bilgisayarı kullanın.

Önce [Visual Studio Code](https://code.visualstudio.com/) yükleyip aşağıdaki uzantıları ekleyin:

- [Azure IoT Araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker uzantısı](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Geliştirmekte olduğunuz dile özel Visual Studio uzantıları:
  - Azure Işlevleri dahil olmak üzere c#: [C# uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Visual Studio Code Için Java Uzantı paketi](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [c/C++ uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Ayrıca modülünüzü geliştirmek için dile özgü bazı ek araçlar yüklemeniz gerekir:

- Azure Işlevleri dahil olmak üzere C#: [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: Python paketlerini yüklemek için [Python](https://www.python.org/downloads/) ve [PIP](https://pip.pypa.io/en/stable/installing/#installation) (genellikle Python yüklemenize dahildir).

- Node.js: [Node.js](https://nodejs.org). Ayrıca [Yeumman](https://www.npmjs.com/package/yo) ve [Azure IoT Edge Node.js modül oluşturucusunu](https://www.npmjs.com/package/generator-azure-iot-edge-module)yüklemek isteyeceksiniz.

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) ve [Maven](https://maven.apache.org/). [ `JAVA_HOME` Ortam değişkenini](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) JDK yüklemenize işaret etmek için ayarlamanız gerekir.

Modül görüntünüzü derlemek ve dağıtmak için, modül görüntüsünü ve modül görüntüsünü tutacak bir kapsayıcı kayıt defteri oluşturmak üzere Docker gereklidir:

- Geliştirme makinenizde [Docker Community Edition](https://docs.docker.com/install/) .

- [Azure Container Registry](../container-registry/index.yml) veya [Docker Hub 'ı](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Yerel bir Docker kayıt defteri 'ni, bir bulut kayıt defteri yerine prototip ve test amacıyla kullanabilirsiniz.

Modülünüzü C 'de geliştirmediğiniz takdirde, IoT Edge çözümünüzü hata ayıklamak, çalıştırmak ve test etmek üzere yerel geliştirme ortamınızı ayarlamak için Python tabanlı [Azure IoT EdgeHub geliştirme aracına](https://pypi.org/project/iotedgehubdev/) da ihtiyacınız vardır. Daha önce yapmadıysanız, [Python (2.7/3.6/3.7) ve PIP](https://www.python.org/) 'yi yükleyip bu komutu terminalinizde çalıştırarak **ıotedgehubdev** ' i yükleyebilirsiniz.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Şu anda, ıotedgehubdev, Python 3,8 ile uyumlu olmayan bir Docker-çi kitaplığı kullanıyor.
>
> Önceden yüklenmiş Python 2,7 (örneğin, Ubuntu veya macOS) dahil birden çok Python varsa, `pip` `pip3` **ıotedgehubdev** 'ı yüklemek için doğru veya yanlış kullandığınızdan emin olun

Modülünüzü bir cihazda test etmek için en az bir IoT Edge cihazıyla etkin bir IoT Hub 'ınız olması gerekir. Bilgisayarınızı IoT Edge bir cihaz olarak kullanmak için, [Linux](quickstart-linux.md) veya [Windows](quickstart.md)Hızlı Başlangıç bölümündeki adımları izleyin. Geliştirme makinenizde IoT Edge Daemon çalıştırıyorsanız, bir sonraki adıma geçmeden önce EdgeHub ve EdgeAgent 'ı durdurmanız gerekebilir.

## <a name="create-a-new-solution-template"></a>Yeni bir çözüm şablonu oluşturun

Aşağıdaki adımlarda, Visual Studio Code ve Azure IoT araçlarını kullanarak tercih ettiğiniz geliştirme dilinizde (C# dilinde yazılmış Azure Işlevleri dahil) IoT Edge bir modülün nasıl oluşturulacağı gösterilmektedir. Bir çözüm oluşturup sonra bu çözümdeki ilk modülü oluşturarak başlayın. Her çözüm birden çok modül içerebilir.

1. **Görünüm**  >  **komut paleti**' ni seçin.

1. Komut paletinde, **Azure IoT Edge: yeni IoT Edge çözümü** komutunu girin ve çalıştırın.

   ![Yeni IoT Edge çözüm Çalıştır](./media/how-to-develop-csharp-module/new-solution.png)

1. Yeni çözümü oluşturmak istediğiniz klasöre gidin ve ardından **Klasör Seç**' i seçin.

1. Çözümünüz için bir ad girin.

1. Tercih ettiğiniz geliştirme diliniz için çözümdeki ilk modül olacak bir modül şablonu seçin.

1. Modülünüzün adını girin. Kapsayıcı kayıt defteriniz dahilinde benzersiz olan bir ad seçin.

1. Modülün görüntü deposunun adını belirtin. Visual Studio Code, modül adını **localhost: 5000/<modülünüzün adıyla \>** doldurur. Kendi kayıt defteri bilgileriniz ile değiştirin. Test için yerel bir Docker kayıt defteri kullanıyorsanız, **localhost** sorunsuz olur. Azure Container Registry kullanırsanız, kayıt defterinizin ayarlarından oturum açma sunucusunu kullanın. Oturum açma sunucusu **_\<registry name\>_ . azurecr.io** gibi görünüyor. Son sonucun **\<*registry name*\> . azurecr.io/ _\<your module name\>_** gibi görünmesi için dizenin **localhost: 5000** kısmını yalnızca değiştirin.

   ![Docker görüntü deposunu sağlama](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code verdiğiniz bilgileri alır, bir IoT Edge çözümü oluşturur ve sonra yeni bir pencerede yükler.

Çözüm içinde dört öğe vardır:

- Bir **. vscode** klasörü hata ayıklama yapılandırması içerir.

- **Modüller** klasörü her modül için alt klasörlere sahiptir.  Her modülün klasörü içinde, modüllerin nasıl oluşturulup dağıtıldığını denetleyen **module.js** bir dosya vardır.  Bu dosyanın modül dağıtım kapsayıcısı kayıt defteri 'ni localhost 'dan uzak bir kayıt defterine değiştirmek için değiştirilmesi gerekir. Bu noktada yalnızca bir modüldür.  Ancak, komut paletinde **Azure IoT Edge: add IoT Edge Module** komutuyla daha fazla ek ekleyebilirsiniz.

- Bir **. env** dosyası ortam değişkenlerinizi listeler. Kayıt defteriniz Azure Container Registry, içinde bir Azure Container Registry Kullanıcı adı ve parola görürsünüz.

  > [!NOTE]
  > Ortam dosyası yalnızca modül için bir görüntü deposu sağlarsanız oluşturulur. Yerel olarak test etme ve hata ayıklama için localhost varsayılan değerlerini kabul ettiyseniz, ortam değişkenlerini bildirmeniz gerekmez.

- Dosya **deployment.template.js** , yeni modülünüzü test için kullanabileceğiniz verileri taklit eden örnek bir **SimulatedTemperatureSensor** modülüyle birlikte listeler. Dağıtım bildirimlerinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [dağıtım bildirimlerini kullanarak modül dağıtma ve rotalar oluşturma](module-composition.md)hakkında bilgi edinin.

Sanal sıcaklık modülünün nasıl çalıştığını görmek için [SimulatedTemperatureSensor. csproj kaynak kodunu](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)görüntüleyin.

## <a name="add-additional-modules"></a>Ek modüller ekleme

Çözümünüze ek modüller eklemek için, komut paletinden **Azure IoT Edge: IoT Edge modülü Ekle** komutunu çalıştırın. Ayrıca **modüller** klasörünü veya `deployment.template.json` Visual Studio Code Explorer görünümündeki dosyayı sağ tıklatıp **IoT Edge modülü Ekle**' yi seçin.

## <a name="develop-your-module"></a>Modülünüzü geliştirme

Çözümle birlikte gelen varsayılan modül kodu şu konumda bulunur:

- Azure işlevi (C#): ***&lt; modüllerinizin &gt;*  >  *&lt; &gt;* adı >** modülünüzün adı. cs
- C#: **modüller > program. cs > *&lt; &gt; Modül adınızı***
- Python: **modüller > *&lt; modülünüzün adını &gt;* > Main.py**
- Node.js: ***&lt; modül adınızın &gt;* > > modüller app.js**
- Java: ***&lt; Modül adınızı &gt;* > src > ana > Java > com > Edgemodulemodules > App. Java ' > modüller**
- C: **modüller > ana. c > *&lt; &gt; Modül adınızı***

Modül ve dosya deployment.template.js, çözümü derlemenize, kapsayıcı Kayıt defterinize gönderebilmeniz ve herhangi bir koda dokunmadan teste başlamak için bir cihaza dağıtabilmeniz için ayarlanır. Modül, bir kaynaktan giriş yapmak için oluşturulmuştur (Bu durumda, verileri taklit eden SimulatedTemperatureSensor modülü) ve IoT Hub için kanal oluşturma.

Şablonu kendi kodunuzla özelleştirmeye hazır olduğunuzda, güvenlik, cihaz yönetimi ve güvenilirlik gibi IoT çözümlerinin temel ihtiyaçlarını ele alan modüller oluşturmak için [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 'larını kullanın.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Kapsayıcı olmadan bir modülün hatalarını ayıklama (C#, Node.js, Java)

C#, Node.js veya Java 'da geliştiriyorsanız, modülünüzün iletileri başlatabilmesi, çalıştırmaları ve yönlendirebilmesi için varsayılan modül kodunda bir **Moduleclient** nesnesinin kullanılması gerekir. Ayrıca, modül iletileri aldığında işlem yapmak için varsayılan **input1** giriş kanalını da kullanacaksınız.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge çözümü için IoT Edge simülatör ayarlama

Geliştirme makinenizde, IoT Edge çözümünüzü çalıştırabilmeniz için IoT Edge güvenlik cini yüklemek yerine IoT Edge simülatörü başlatabilirsiniz.

1. Cihaz Gezgini ' nde, sol taraftaki IoT Edge cihaz KIMLIĞINIZ ' ne sağ tıklayın ve ardından cihaz bağlantı dizesiyle simülatör 'yi başlatmak için **IoT Edge simülatör kurulum** ' u seçin.
1. Tümleşik terminalde ilerleme ayrıntısı ' nı okuyarak IoT Edge Benzeticisinin başarıyla ayarlandığını görebilirsiniz.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Tek modüllü uygulama için IoT Edge simülatör ayarlama

Simülatörü ayarlamak ve başlatmak için, Visual Studio Code komut paletinden **tek bir modül için Azure IoT Edge: IoT Edge hub simülatörü başlatma** komutunu çalıştırın. İstendiğinde, uygulamanızın giriş adı olarak varsayılan modül kodundan (veya kodunuzda eşdeğer değerden) **input1** değerini kullanın. Komut, **ıotedgehubdev** CLI öğesini tetikler ve ardından IoT Edge simülatörü ve bir test yardımcı programı modül kapsayıcısını başlatır. Simülatör Tek modüllü modda başarıyla başlatılmışsa, tümleşik terminalde aşağıdaki çıktıları görebilirsiniz. Ayrıca, `curl` ile ileti gönderilmesine yardımcı olacak bir komut da görebilirsiniz. Daha sonra bu adı kullanacaksınız.

   ![Tek modüllü uygulama için IoT Edge simülatör ayarlama](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Modülün çalışır durumda olduğunu görmek için Visual Studio Code Docker Gezgin görünümünü kullanabilirsiniz.

   ![Simülatör modülü durumu](media/how-to-develop-csharp-module/simulator-status.png)

   **Edgehubdev** kapsayıcısı, yerel IoT Edge simülatör 'in temel sayısıdır. Geliştirme makinenizde IoT Edge güvenlik cini olmadan çalışabilir ve yerel modül uygulamanız veya modül kapsayıcılarınız için ortam ayarları sağlar. **Giriş** kapsayıcısı, iletilerinizi modüldeki hedef giriş kanalına KÖPRÜLEMEK Için REST API 'lerini kullanıma sunar.

### <a name="debug-module-in-launch-mode"></a>Başlatma modunda hata ayıklama modülü

1. Geliştirme dilinizin gereksinimlerine göre ortamınızı hata ayıklama için hazırlayın, modülünüzün bir kesme noktası ayarlayın ve kullanılacak hata ayıklama yapılandırmasını seçin:
   - **C#**
     - Visual Studio Code tümleşik terminalde, dizini ***&lt; Modül adı &gt;*** klasörünüz olarak değiştirin ve ardından .NET Core uygulaması derlemek için aşağıdaki komutu çalıştırın.

       ```cmd
       dotnet build
       ```

     - Dosyayı açın `Program.cs` ve bir kesme noktası ekleyin.

     - Visual Studio Code hata ayıklama görünümüne giderek **görünüm > hata ayıkla**' yı seçin. Modülünüzün hata ayıklama yapılandırmasını, açılan menüden **yerel hata ayıklama (.NET Core) _&lt; adını &gt;_** seçin.

        > [!NOTE]
        > .NET Core ' `TargetFramework` daki program yolunuza uygun değilse `launch.json` , ' deki program yolunu,. csproj dosyanızdaki ile eşleşecek şekilde el ile güncelleştirmeniz gerekir, `launch.json` `TargetFramework` böylece Visual Studio Code bu programı başarıyla başlatabilir.

   - **Node.js**
     - Visual Studio Code tümleşik terminalde, dizini ***&lt; Modül adı &gt;*** klasörünüze değiştirin ve düğüm paketlerini yüklemek için aşağıdaki komutu çalıştırın

       ```cmd
       npm install
       ```

     - Dosyayı açın `app.js` ve bir kesme noktası ekleyin.

     - Visual Studio Code hata ayıklama görünümüne giderek **görünüm > hata ayıkla**' yı seçin. Modülünüzün hata ayıklama yapılandırmasını, açılan listeden **yerel hata ayıklama (Node.js) _&lt; adını &gt;_** seçin.
   - **Java**
     - Dosyayı açın `App.java` ve bir kesme noktası ekleyin.

     - Visual Studio Code hata ayıklama görünümüne giderek **görünüm > hata ayıkla**' yı seçin. Modülünüzün hata ayıklama yapılandırmasını, açılan listeden **yerel hata ayıklama (Java) _&lt; adını &gt;_** seçin.

1. Hata **ayıklamayı Başlat** ' a tıklayın veya hata ayıklama oturumu başlatmak için **F5** 'e basın.

1. Visual Studio Code tümleşik terminalde, modülünüzü bir **Merhaba Dünya** iletisi göndermek için aşağıdaki komutu çalıştırın. Bu, önceki adımlarda IoT Edge simülatör ' i ayarlarken gösterilen komuttur.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows kullanıyorsanız, Visual Studio Code tümleşik terminalinin kabuğun **Git Bash** veya **WSL Bash** olduğundan emin olun. `curl`Komutu bir PowerShell veya komut isteminden çalıştıramazsınız.
   > [!TIP]
   > Ayrıca, yerine iletileri göndermek için [Postman](https://www.getpostman.com/) veya diğer API Araçları ' nı kullanabilirsiniz `curl` .

1. Visual Studio Code hata ayıklama görünümünde, sol panelde değişkenleri görürsünüz.

1. Hata ayıklama oturumunuzu durdurmak için Durdur düğmesini seçin veya **SHIFT + F5** tuşlarına basın ve ardından Azure IoT Edge çalıştırın: benzeticiyi durdurmak ve temizlemek için komut paletinde **IoT Edge simülatörü durdur** .

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>IoT Edge simülatör ile iliştirme modunda hata ayıklama (C#, Node.js, Java, Azure Işlevleri)

Varsayılan çözümünüz iki modül içerir, biri sanal bir sıcaklık algılayıcısı modülüdür ve diğeri kanal modülüdür. Sanal sıcaklık algılayıcısı, kanal modülüne ileti gönderir ve ardından iletilerin IoT Hub gönderilir. Oluşturduğunuz modül klasöründe, farklı kapsayıcı türleri için birkaç Docker dosyası vardır. Modülle biten dosyaları kullanın. test için modülünüzü derlemek için **hata ayıklayın** .

Şu anda iliştirme modunda hata ayıklama yalnızca aşağıdaki gibi desteklenir:

- Azure Işlevleri de dahil olmak üzere C# modülleri, Linux AMD64 kapsayıcılarında hata ayıklamayı destekler
- Node.js modüller, Linux amd64 ve arm32v7 kapsayıcılarında ve Windows AMD64 kapsayıcılarında hata ayıklamayı destekler
- Java modülleri, Linux amd64 ve arm32v7 kapsayıcılarında hata ayıklamayı destekler

> [!TIP]
> Visual Studio Code durum çubuğundaki öğeye tıklayarak IoT Edge çözümünüz için varsayılan platformun seçenekleri arasında geçiş yapabilirsiniz.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge çözümü için IoT Edge simülatör ayarlama

Geliştirme makinenizde, IoT Edge çözümünüzü çalıştırabilmeniz için IoT Edge güvenlik cini yüklemek yerine IoT Edge simülatörü başlatabilirsiniz.

1. Cihaz Gezgini ' nde, sol taraftaki IoT Edge cihaz KIMLIĞINIZ ' ne sağ tıklayın ve ardından cihaz bağlantı dizesiyle simülatör 'yi başlatmak için **IoT Edge simülatör kurulum** ' u seçin.

1. Tümleşik terminalde ilerleme ayrıntısı ' nı okuyarak IoT Edge Benzeticisinin başarıyla ayarlandığını görebilirsiniz.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Hata ayıklama ve hata ayıklama için kapsayıcıyı iliştirme modunda oluştur ve Çalıştır

1. Modül dosyanızı açın ( `Program.cs` , `app.js` , `App.java` veya `<your module name>.cs` ) ve bir kesme noktası ekleyin.

1. Visual Studio Code gezgin görünümünde, `deployment.debug.template.json` çözümünüz için dosyaya sağ tıklayın ve ardından **derleme ve IoT Edge çözümü oluştur ve simülatör**' ı seçin. Tüm modül kapsayıcısı günlüklerini aynı pencerede izleyebilirsiniz. Ayrıca, kapsayıcı durumunu izlemek için Docker görünümüne gidebilirsiniz.

   ![Değişkenleri izle](media/how-to-vs-code-develop-module/view-log.png)

1. Visual Studio Code hata ayıklama görünümüne gidin ve modülünüzün hata ayıklama yapılandırma dosyasını seçin. Hata ayıklama seçeneği adı ***&lt; Modül adı &gt;* uzaktan hata ayıklama ile aynı olmalıdır**

1. **Hata ayıklamayı Başlat** ' ı seçin veya **F5** tuşuna basın. İliştirilecek işlemi seçin.

1. Visual Studio Code hata ayıklama görünümünde, sol panelde değişkenleri görürsünüz.

1. Hata ayıklama oturumunu durdurmak için öncelikle Durdur düğmesini seçin veya **SHIFT + F5** tuşlarına basın ve ardından **Azure IoT Edge: komut paletinden IoT Edge simülatörünü durdur** ' u seçin.

> [!NOTE]
> Yukarıdaki örnekte, kapsayıcılarda IoT Edge modüllerinin nasıl ayıklanacağı gösterilmektedir. Modülün kapsayıcı ayarlarına açık bağlantı noktaları ekledi `createOptions` . Modüllerinizin hatalarını ayıklamayı bitirdikten sonra, bu açık bağlantı noktalarını üretime yönelik IoT Edge modüller için kaldırmanızı öneririz.
>
> Azure Işlevleri dahil olmak üzere C# dilinde yazılan modüller için bu örnek, `Dockerfile.amd64.debug` derleme sırasında kapsayıcı görüntünde .NET Core komut satırı hata ayıklayıcısını (VSDBG) içeren hata ayıklama sürümünü temel alır. C# modüllerinizi hata ayıkladıktan sonra, üretim için hazır IoT Edge modüller için Dockerfile 'ı VSDBG olmadan doğrudan kullanmanızı öneririz.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>IoT Edge çalışma zamanına sahip bir modülün hatalarını ayıklama

Her modül klasöründe, farklı kapsayıcı türleri için birkaç Docker dosyası vardır. Modülle biten dosyaları kullanın. test için modülünüzü derlemek için **hata ayıklayın** .

Bu yöntemi kullanarak modüller hata ayıklarken, modülleriniz IoT Edge çalışma zamanının üstünde çalışmaktadır. IoT Edge cihaz ve Visual Studio Code aynı makinede olabilir veya daha fazla genellikle geliştirme makinesinde Visual Studio Code ve IoT Edge çalışma zamanı ve modüller başka bir fiziksel makinede çalışıyor. Visual Studio Code hata ayıklamak için şunları yapmanız gerekir:

- IoT Edge cihazınızı ayarlayın, IoT Edge modüllerinizi **. Debug** dockerfile ile derleyin ve ardından IoT Edge cihaza dağıtın.
- Hata ayıklayıcının iliştirilebileceği şekilde modülün IP ve bağlantı noktasını kullanıma sunun.
- Visual Studio Code, `launch.json` uzak makinedeki kapsayıcıda işleme eklenebilmesi için güncelleştirin. Bu dosya, `.vscode` çalışma alanınızdaki klasörde bulunur ve hata ayıklamayı destekleyen yeni bir modül eklediğiniz her seferinde güncellenir.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Modülünüzü IoT Edge cihaza derleyin ve dağıtın

1. Visual Studio Code ' de, `deployment.debug.template.json` doğru değerler ayarlanmış modül görüntülerinin hata ayıklama sürümünü içeren dosyayı açın `createOptions` .

1. Modülünüzü Python 'da geliştiriyorsanız devam etmeden önce şu adımları izleyin:
   - Dosyayı açın `main.py` ve içeri aktarma bölümünden sonra bu kodu ekleyin:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Hata ayıklamak istediğiniz geri aramaya aşağıdaki tek satırlık kod satırını ekleyin:

      ```python
      ptvsd.break_into_debugger()
      ```

     Örneğin, işlevde hata ayıklamak istiyorsanız `receive_message_listener` , bu kod satırını aşağıda gösterildiği gibi ekleyebilirsiniz:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Visual Studio Code komut paletinde:
   1. Komutu çalıştırın **Azure IoT Edge: IoT Edge çözüm oluşturun ve gönderin**.

   1. `deployment.debug.template.json`Çözümünüz için dosyayı seçin.

1. Visual Studio Code Gezgini görünümündeki **Azure IoT Hub cihazları** bölümünde:
   1. IoT Edge cihaz KIMLIĞINE sağ tıkladıktan sonra **tek cihaz Için dağıtım oluştur**' u seçin.

      > [!TIP]
      > Seçtiğiniz cihazın IoT Edge bir cihaz olduğunu doğrulamak için, modül listesini genişletmek ve **$edgeHub** ve **$edgeAgent** varlığını doğrulamak için seçin. Her IoT Edge cihaz bu iki modülü içerir.

   1. Çözümünüzün **yapılandırma** klasörüne gidin, `deployment.debug.amd64.json` dosyayı seçin ve ardından **kenar dağıtım bildirimini Seç**' i seçin.

Dağıtımı, tümleşik terminalde bir dağıtım KIMLIĞIYLE başarıyla oluşturulduğunu görürsünüz.

Terminalde komutu çalıştırarak kapsayıcı durumunuzu kontrol edebilirsiniz `docker ps` . Visual Studio Code ve IoT Edge çalışma zamanı aynı makinede çalışıyorsa, Visual Studio Code Docker görünümündeki durumu da denetleyebilirsiniz.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Hata ayıklayıcı için modülün IP ve bağlantı noktasını kullanıma sunma

Modülleriniz Visual Studio Code ile aynı makinede çalışıyorsa, kapsayıcıya eklemek için localhost 'u kullanırken ve **. Debug** dockerfile, modülünün kapsayıcı `createOptions` ayarları ve dosyasında zaten doğru bağlantı noktası ayarları varsa, bu bölümü atlayabilirsiniz `launch.json` . Modülleriniz ve Visual Studio Code ayrı makinelerde çalışıyorsa, geliştirme diliniz için adımları izleyin.

- **Azure Işlevleri dahil olmak üzere C#**

  [Geliştirme makinenizde ve cihaz IoT Edge SSH kanalını yapılandırın](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) ve sonra `launch.json` iliştirilecek dosyayı düzenleyin.

- **Node.js**

  - Hata ayıklamakta olan makinedeki modülün çalışır durumda olduğundan ve hata ayıklayıcılarına eklemek için, bağlantı noktası 9229 ' nin dışarıdan erişilebilir olduğundan emin olun. Bunu `http://<target-machine-IP>:9229/json` hata ayıklayıcı makinesinde açarak doğrulayabilirsiniz. Bu URL, ayıklanabilecek Node.js modülüyle ilgili bilgileri göstermelidir.
  
  - Geliştirme makinenizde Visual Studio Code açın ve ardından, modül `launch.json` ***&lt; adı &gt;* uzaktan hata ayıklama (Node.js)** profili (veya modülünüzün bir Windows kapsayıcısı olarak çalışıyorsa, modül **_&lt; adı &gt;_ uzaktan hata ayıklama (Windows kapsayıcısında Node.js)** profilinde, hata ayıklamakta olan makinenin IP 'si olması için düzenleyin.

- **Java**

  - ' İ çalıştırarak hata ayıklaması yapılacak makineye bir SSH tüneli oluşturun `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Geliştirme makinenizde Visual Studio Code açın ve hedef makineye iliştirebilmeniz için ***&lt; modül &gt; adı* uzaktan hata ayıklama (Java)** profilinizi düzenleyin `launch.json` . `launch.json`Visual Studio Code Ile Java 'yı Düzenle ve hata ayıklama hakkında daha fazla bilgi için, [hata ayıklayıcıyı yapılandırma](https://code.visualstudio.com/docs/java/java-debugging#_configuration)konusundaki bölümüne bakın.

- **Python**

  - Hata ayıklamakta olan makinedeki 5678 numaralı bağlantı noktasının açık ve erişilebilir olduğundan emin olun.

  - `ptvsd.enable_attach(('0.0.0.0', 5678))`Daha önce eklediğiniz kodda `main.py` , **0.0.0.0** olarak ayıklanabilecek makinenin IP adresini değiştirin. IoT Edge modülünüzü yeniden derleyin, gönderin ve dağıtın.

  - Geliştirme makinenizde Visual Studio Code açın ve ardından `launch.json` `host` , ***&lt; Modül adı &gt;* uzaktan hata ayıklama (Python)** profilinizin değeri yerine hedef makinenin IP adresini kullandığından düzenleme yapın `localhost` .

### <a name="debug-your-module"></a>Modülünüzün hatalarını ayıklama

1. Visual Studio Code hata ayıklama görünümünde modülünüzün hata ayıklama yapılandırma dosyasını seçin. Hata ayıklama seçeneği adı ***&lt; Modül adı &gt;* uzaktan hata ayıklama ile aynı olmalıdır**

1. Geliştirme diliniz için modül dosyasını açın ve bir kesme noktası ekleyin:

   - **Azure işlevi (C#)**: kesme noktasını dosyaya ekleyin `<your module name>.cs` .
   - **C#**: kesme noktasını dosyaya ekleyin `Program.cs` .
   - **Node.js**: kesme noktasını dosyaya ekleyin `app.js` .
   - **Java**: kesme noktasını dosyaya ekleyin `App.java` .
   - **Python**: `main.py` satırı eklediğiniz geri çağırma yönteminde dosyasına kesme noktası ekleyin `ptvsd.break_into_debugger()` .
   - **C**: kesme noktasını dosyaya ekleyin `main.c` .

1. **Hata ayıklamayı Başlat** ' ı veya **F5**' i seçin. İliştirilecek işlemi seçin.

1. Visual Studio Code hata ayıklama görünümünde, sol panelde değişkenleri görürsünüz.

> [!NOTE]
> Yukarıdaki örnekte, kapsayıcılarda IoT Edge modüllerinin nasıl ayıklanacağı gösterilmektedir. Modülün kapsayıcı ayarlarına açık bağlantı noktaları ekledi `createOptions` . Modüllerinizin hatalarını ayıklamayı bitirdikten sonra, bu açık bağlantı noktalarını üretime yönelik IoT Edge modüller için kaldırmanızı öneririz.

## <a name="build-and-debug-a-module-remotely"></a>Modülde uzaktan derleme ve hata ayıklama

Hem Docker hem de Moby altyapılarında SSH bağlantılarını destekleyen son değişiklikler ve Azure IoT araçları 'nda ortam ayarlarının Visual Studio Code komut paleti ve Azure IoT Edge terminallere eklenmesine olanak tanıyan yeni bir ayar ile, artık uzak cihazlarda modüller oluşturabilir ve hata ayıklayabilirsiniz.

Daha fazla bilgi ve adım adım yönergeler için bu [IoT Geliştirici blogu girişine](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Modülünüzü oluşturduktan sonra [Visual Studio Code Azure IoT Edge modüllerin nasıl dağıtılacağını](how-to-deploy-modules-vscode.md)öğrenin.

IoT Edge cihazlarınız için modüller geliştirmek üzere [Azure IoT Hub SDK 'Larını anlayın ve kullanın](../iot-hub/iot-hub-devguide-sdks.md).