---
title: Öğretici-Azure IoT Edge kullanarak Linux cihazları için modül geliştirme
description: Bu öğretici, Linux cihazları için Linux kapsayıcıları kullanarak IoT Edge modülleri geliştirmek üzere geliştirme makinenizi ve bulut kaynaklarınızı ayarlamayı adım adım göstermektedir
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 10742ec277f5742067c432a2823cbb7592e6a752
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874709"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Öğretici: Linux kapsayıcılarıyla IoT Edge modülleri geliştirme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge çalıştıran cihazlara kod geliştirmek ve dağıtmak için Visual Studio Code kullanın.

Hızlı başlangıçta bir IoT Edge cihaz oluşturdunuz ve Azure Marketi 'nden bir modül dağıttınız. Bu öğreticide, bir IoT Edge cihazına kendi kodunuzu geliştirme ve dağıtma işlemi adım adım açıklanmaktadır. Bu makale, belirli programlama dilleri veya Azure hizmetleri hakkında daha ayrıntılı bilgi sağlayan diğer öğreticiler için kullanışlı bir önkoşuldur.

Bu öğretici, **bir Linux cihazına C# modülünü** dağıtma örneğini kullanır. Bu örnek, IoT Edge çözümleri için en yaygın geliştirici senaryosu olduğundan seçilmiştir. Farklı bir dil kullanmayı planlıyor veya bir Azure hizmeti dağıtsanız bile, bu öğretici geliştirme araçları ve kavramlar hakkında bilgi edinmek için hala yararlıdır. Geliştirme sürecine bu girişi tamamladıktan sonra ayrıntıları incelemek için tercih ettiğiniz dili veya Azure hizmetini seçin.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Geliştirme makinenizi ayarlayın.
> * Yeni bir proje oluşturmak için Visual Studio Code IoT Edge araçları kullanın.
> * Projenizi kapsayıcı olarak derleyin ve Azure Container Registry 'de saklayın.
> * Kodunuzu bir IoT Edge cihazına dağıtın.

## <a name="prerequisites"></a>Önkoşullar

Geliştirme makinesi:

* Geliştirme tercihlerinize bağlı olarak kendi bilgisayarınızı veya bir sanal makineyi kullanabilirsiniz.
  * Geliştirme makinenizin iç içe sanallaştırmayı desteklediğinden emin olun. Bu özellik, bir sonraki bölümde yüklediğiniz bir kapsayıcı altyapısını çalıştırmak için gereklidir.
* Bir kapsayıcı altyapısını çalıştırabilme ile ilgili çoğu işletim sistemi, Linux cihazları için IoT Edge modülleri geliştirmek üzere kullanılabilir. Bu öğretici bir Windows bilgisayarı kullanır, ancak macOS veya Linux üzerinde bilinen farklılıkları gösterir.
* Bu öğreticide daha sonra modül şablonu paketleri çekmek için [Git](https://git-scm.com/)'i yükler.  
* [Visual Studio Code için C# (OmniSharp tarafından desteklenen) uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK'sı](https://dotnet.microsoft.com/download/dotnet/2.1).

Bir Azure IoT Edge cihazı:

* Geliştirme makinenizde IoT Edge çalıştırmanızı öneririz, bunun yerine ayrı bir cihaz kullanın. Geliştirme makinesi ve IoT Edge cihaz arasındaki bu ayrım, doğru bir dağıtım senaryosunu daha doğru yansıtmıştır ve farklı kavramları düz tutmaya yardımcı olur.
* Kullanılabilir ikinci bir cihazınız yoksa, Azure 'da [Linux sanal makinesiyle](quickstart-linux.md)IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalesini kullanın.

Bulut kaynakları:

* Azure 'da ücretsiz veya Standart katmanlı [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Önemli kavramlar

Bu öğreticide IoT Edge modülünün geliştirilmesi gösterilmektedir. Bir *IoT Edge modülü* veya bazen yalnızca Short için bir *Modül* çalıştırılabilir kod içeren bir kapsayıcıdır. Bir IoT Edge cihazına bir veya daha fazla modül dağıtabilirsiniz. Modüller sensörlerden veri almak, verileri temizlemek ve analiz etmek ya da bir IoT Hub 'ına ileti göndermek gibi belirli görevleri gerçekleştirir. Daha fazla bilgi için bkz. [Azure IoT Edge modüllerini anlama](iot-edge-modules.md).

IoT Edge modüller geliştirirken, geliştirme makinesi ve modülün sonunda dağıtıldığı hedef IoT Edge cihaz arasındaki farkı anlamak önemlidir. Modül kodunuzu tutmak için oluşturduğunuz kapsayıcı, *hedef cihazın* işletim SISTEMI (OS) ile aynı olmalıdır. Örneğin, en yaygın senaryo, bir Windows bilgisayarda IoT Edge çalıştıran bir Linux cihazını hedefleyecek bir modül geliştiren kişidir. Bu durumda, kapsayıcı işletim sistemi Linux olur. Bu öğreticiye giderek, *geliştirme MAKINESI işletim* sistemi ve *kapsayıcı işletim sistemi* arasındaki farkı aklınızda bulundurun.

>[!TIP]
>[Windows üzerinde Linux için IoT Edge](iot-edge-for-linux-on-windows.md)kullanıyorsanız, senaryonuza ait *hedef cihaz* Windows ana bilgisayarı değil, Linux sanal makinedir.

Bu öğretici, Linux kapsayıcılarıyla IoT Edge çalıştıran cihazları hedefler. Geliştirme makineniz Linux kapsayıcıları çalıştırdığı sürece tercih ettiğiniz işletim sistemini kullanabilirsiniz. Bu öğreticinin kullanacağı, Linux kapsayıcılarıyla geliştirme için Visual Studio Code kullanmanızı öneririz. Visual Studio 'Yu da kullanabilirsiniz, ancak iki araç arasındaki DESTEKDE farklılıklar vardır.

Aşağıdaki tabloda Visual Studio Code ve Visual Studio 'da **Linux kapsayıcıları** için desteklenen geliştirme senaryoları listelenmektedir.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux cihaz mimarisi** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure hizmetleri** | Azure İşlevleri <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Diller** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Daha fazla bilgi** | [Visual Studio Code için Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Visual Studio 2017 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Visual Studio 2019 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Linux ARM64 cihazları için destek [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)kullanılabilir. Daha fazla bilgi için bkz. [Visual Studio Code IoT Edge modüllerini geliştirme ve hata ayıklama (Önizleme)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Bu öğreticide Visual Studio Code için geliştirme adımları öğretilir. Visual Studio 'Yu kullanmayı tercih ediyorsanız, [Azure IoT Edge için modülleri geliştirmek ve hatalarını ayıklamak Için Visual studio 2019 kullanma](how-to-visual-studio-develop-module.md)bölümündeki yönergelere bakın.
## <a name="install-container-engine"></a>Kapsayıcı altyapısını yükler

IoT Edge modüller kapsayıcı olarak paketlenir, bu nedenle bunları derlemek ve yönetmek için geliştirme makinenizde bir kapsayıcı altyapısına ihtiyacınız vardır. Özellik desteği ve popülerliği nedeniyle Docker Desktop 'ı geliştirmeye önerilir. Windows üzerinde Docker Desktop, farklı türlerde IoT Edge cihazları için kolayca modül geliştirebilmeniz için Linux kapsayıcıları ve Windows kapsayıcıları arasında geçiş yapmanızı sağlar.

Geliştirme makinenize yüklemek için Docker belgelerini kullanın:

* [Docker Desktop for Windows'u yükleme](https://docs.docker.com/docker-for-windows/install/)

  * Windows için Docker Desktop 'ı yüklediğinizde, Linux veya Windows kapsayıcıları kullanmak isteyip istemediğiniz sorulur. Bu karar, bir kolay anahtar kullanılarak herhangi bir zamanda değiştirilebilir. Bu öğreticide, modüllerimiz Linux cihazlarını hedeflediğinden, Linux kapsayıcıları kullanırız. Daha fazla bilgi için bkz. [Windows ve Linux kapsayıcıları arasında geçiş](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Mac için Docker Desktop 'ı yükler](https://docs.docker.com/docker-for-mac/install/)

* Birkaç Linux platformunda yükleme bilgileri için [Docker CE hakkında](https://docs.docker.com/install/) bilgi edinin.
  * Linux için Windows alt sistemi (WSL) için, Windows için Docker Desktop ' ı yüklersiniz.

## <a name="set-up-vs-code-and-tools"></a>VS Code ve araçları ayarlama

IoT Edge modülleri geliştirmek için Visual Studio Code için IoT uzantıları 'nı kullanın. Bu uzantılar proje şablonları sağlar, dağıtım bildiriminin oluşturulmasını otomatik hale getirir ve IoT Edge cihazları izlemenize ve yönetmenize olanak tanır. Bu bölümde Visual Studio Code ve IoT uzantısını yükleyip Visual Studio Code içinden IoT Hub kaynaklarını yönetmek üzere Azure hesabınızı ayarlarsınız.

1. Geliştirme makinenize [Visual Studio Code](https://code.visualstudio.com/) 'yi yükler.

2. Yükleme tamamlandıktan sonra uzantıları **görüntüle**' yi seçin  >  .

3. Aslında IoT Hub ve IoT cihazlarıyla etkileşime başlamanıza yardımcı olan ve IoT Edge modülleri geliştiren bir uzantı koleksiyonu olan **Azure IoT araçları**' nı arayın.

4. **Yükle**'yi seçin. Dahil edilen her uzantı tek tek yüklenir.

5. Uzantılar yükleme tamamlandığında, komut paletini **görüntüle**' yi seçerek komut paleti ' ni açın  >  .

6. Komut paletinde **Azure: oturum aç**' ı arayıp seçin. Azure hesabınızda oturum açmak için yönergeleri izleyin.

7. Komut paletinde, **Azure IoT Hub arayıp seçin: IoT Hub ' i seçin**. Azure aboneliğinizi ve IoT Hub 'ınızı seçmek için istemleri izleyin.

8. Sol taraftaki etkinlik çubuğundaki simgeyi seçerek veya **Görünüm** Gezgini ' ni seçerek Visual Studio Code gezgin bölümünü açın  >  .

9. Gezgin bölümünün en altında, daraltılan **Azure IoT Hub/cihazları** menüsünü genişletin. Komut paleti aracılığıyla seçtiğiniz IoT Hub 'ı ile ilişkili cihazları ve IoT Edge cihazları görmeniz gerekir.

   ![IoT Hub 'ınızdaki cihazları görüntüleme](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Yeni bir modül projesi oluştur

Azure IoT araçları uzantısı, Visual Studio Code tüm desteklenen IoT Edge modülü dilleri için proje şablonları sağlar. Bu şablonlar, IoT Edge test etmek için çalışan bir modül dağıtmak için ihtiyacınız olan tüm dosya ve koda sahiptir veya şablonu kendi iş mantığınızla özelleştirmek için bir başlangıç noktası sağlar.

Bu öğreticide, en sık kullanılan şablon olduğu için C# modül şablonunu kullanırız.

### <a name="create-a-project-template"></a>Proje şablonu oluşturma

Visual Studio Code komut paletinde arama yapın ve **Azure IoT Edge: yeni IoT Edge çözüm**' i seçin. Çözümünüzü oluşturmak için istemleri izleyin ve aşağıdaki değerleri kullanın:

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan **EdgeSolution** kabul edin. |
   | Modül şablonunu seçin | **C# modülünü** seçin. |
   | Modül adı sağlayın | Varsayılan **sampleModule**'ü kabul edin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, son adımda verdiğiniz adından önceden doldurulur. **Localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle değiştirin. Oturum açma sunucusu değerini, Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz. <br><br> Son görüntü deposu \<registry name\> . azurecr.io/sampleModule gibi görünüyor. |

   ![Docker görüntü deposunu sağlama](./media/tutorial-develop-for-linux/image-repository.png)

Yeni çözümünüz Visual Studio Code penceresine yüklendikten sonra, oluşturduğu dosyaları öğrenmek için biraz zaman ayırın:

* **. Vscode** klasörü, modülleri hata ayıklama için kullanılan **launch.json** adlı bir dosya içerir.
* **Modüller** klasörü, çözümünüzdeki her modül için bir klasör içerir. Şu anda yalnızca **sampleModule** veya modüle verdiğiniz herhangi bir ad olmalıdır. SampleModule klasörü ana program kodunu, modül meta verilerini ve birkaç Docker dosyasını içerir.
* **. Env** dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini tutar. Bu kimlik bilgileri, kapsayıcı görüntülerini çekmek için erişim sağlamak üzere IoT Edge cihazınızdan paylaşılır.
* Dosya ve **deployment.template.jsüzerindeki** **deployment.debug.template.js** , bir dağıtım bildirimi oluşturmanıza yardımcı olan şablonlardır. *Dağıtım bildirimi* , bir cihaza hangi modüllerin dağıtılmasını, bunların nasıl yapılandırılacağını ve bunların birbirleriyle ve buluttan nasıl iletişim kurabildiğini tanımlayan bir dosyadır. Şablon dosyaları bazı değerler için işaretçiler kullanır. Şablonu doğru bir dağıtım bildirimine dönüştürdüğünüzde, işaretçiler diğer çözüm dosyalarından alınan değerlerle değiştirilmiştir. Dağıtım şablonunuzda iki ortak yer tutucuyu bulun:

  * Kayıt defteri bilgileri bölümünde, adres, çözümü oluşturduğunuzda verdiğiniz bilgilerden tekrar doldurulur. Ancak, Kullanıcı adı ve parola. env dosyasında depolanan değişkenlere başvurur. Bu yapılandırma,. env dosyası git yoksayıldı, ancak dağıtım şablonu olmadığı için güvenlik içindir.
  * SampleModule bölümünde, çözümü oluştururken görüntü deposunu sağlasanız bile kapsayıcı görüntüsü doldurulmamış. Bu yer tutucu, SampleModule klasörünün içindeki **module.js** dosyasına işaret eder. Bu dosyaya giderseniz, görüntü alanının depoyu içerdiğini, aynı zamanda kapsayıcının sürümünden ve platformundan oluşan bir etiket değerini görürsünüz. Geliştirme döngünüzün bir parçası olarak sürümü el ile yineleyebilirsiniz ve bu bölümde daha sonra tanıtıldığımız bir değiştirici kullanarak kapsayıcı platformunu seçebilirsiniz.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>IoT Edge aracısına kayıt defteri kimlik bilgilerinizi girin

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanının kapsayıcı görüntülerinizi IoT Edge cihaza çekmek için bu kimlik bilgilerine ihtiyacı vardır.

>[!NOTE]
>**Localhost: 5000** değerini Azure Container kayıt defterinizin oturum açma sunucusu değeriyle değiştirmediyseniz, [**proje şablonu oluştur**](#create-a-project-template) adımında, dağıtım bildiriminin **. env** dosyası ve registrycredentials bölümü eksik olur. 

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. Modül çözümünüzde **. env** dosyasını açın.
2. Azure Container Registry 'nizden kopyaladığınız **Kullanıcı adı** ve **parola** değerlerini ekleyin.
3. Değişikliklerinizi. env dosyasına kaydedin.

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 ve ARM32v7 cihazları için C# modülleri geliştirebilir. Kapsayıcının nasıl oluşturulduğunu ve çalıştığını etkilediğinden, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır.

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla** veya pencerenin altındaki yan çubukta kısayol simgesini seçin.

   ![Yan çubukta mimari simgesini seçin](./media/tutorial-develop-for-linux/select-architecture.png)

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız.

### <a name="review-the-sample-code"></a>Örnek kodu gözden geçirme

Oluşturduğunuz çözüm şablonu, bir IoT Edge modülü için örnek kod içerir. Bu örnek modül yalnızca iletileri alır ve ardından üzerine geçirir. Ardışık düzen işlevselliği, modüllerin birbirleriyle iletişim kurduğu IoT Edge önemli bir kavramı gösterir.

Her modülün kodunda birden çok *giriş* ve *Çıkış* kuyruğu olabilir. Cihazda çalışan IoT Edge hub 'ı bir modülün çıktısından gelen iletileri bir veya daha fazla modülün girdisine yönlendirir. Giriş ve çıkışları bildirmek için özel kod diller arasında farklılık gösterir, ancak kavram tüm modüller arasında aynıdır. Modüller arasında yönlendirme hakkında daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes).

Proje şablonuyla birlikte gelen örnek C# kodu, .NET için IoT Hub SDK 'sının [Moduleclient sınıfını](/dotnet/api/microsoft.azure.devices.client.moduleclient) kullanır.

1. **Modüller/SampleModule/** klasörünün içindeki **program. cs** dosyasını açın.

2. Program. cs ' de **Setınputmessagehandlerasync** metodunu bulun.

3. [Setınputmessagehandlerasync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) yöntemi gelen iletileri almak için bir giriş kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **input1** adlı bir giriş kuyruğunu nasıl Başlatan hakkında bilgi alın.

   ![Setınputmessagecallback oluşturucuda giriş adını bulma](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Ardından **SendEventAsync** yöntemini bulun.

5. [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) yöntemi alınan iletileri işler ve bunları iletmek için bir çıkış kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **output1** adlı bir çıkış kuyruğu başlattığında bkz.

   ![SendEventToOutputAsync içinde çıkış adını bulma](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. deployment.template.jsdosya **üzerinde** açın.

7. $EdgeAgent istenen özelliklerin **modüller** özelliğini bulun.

   Burada listelenen iki modül olmalıdır. Bunlardan biri, modüllerinizi test etmek için kullanabileceğiniz sanal sıcaklık verileri sağlamak üzere varsayılan olarak tüm şablonlara dahil olan **SimulatedTemperatureSensor** modülüdür. Diğeri, bu çözümün bir parçası olarak oluşturduğunuz **sampleModule** modülüdür.

8. Dosyanın en altında, **$edgeHub** modülü için istenen özellikleri bulun.

   IoT Edge hub modülünün işlevlerinden biri, iletileri bir dağıtımdaki tüm modüller arasında yönlendirmekte. **Rotalar** özelliğindeki değerleri gözden geçirin. Bir yol, **SampleModuleToIoTHub**, *\** sampleModule modülündeki herhangi bir çıkış kuyruğuna gelen herhangi bir iletiyi göstermek için bir joker karakter (* _) kullanır. Bu iletiler, IoT Hub belirten ayrılmış bir ad olan _ $ yukarı akış * ' a gider. Diğer yol olan **Sensortosamplemodule**, SimulatedTemperatureSensor modülünden gelen iletileri alır ve bunları sampleModule kodunda gördüğünüz *input1* giriş kuyruğuna yönlendirir.

   ![deployment.template.jsiçindeki yolları gözden geçirin](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Çözümünüzü derleyin ve gönderin

Bazı önemli dağıtım kavramlarını anlamak için modül kodunu ve Dağıtım şablonunu gözden geçirdiniz. Şimdi SampleModule kapsayıcı görüntüsünü oluşturmak ve kapsayıcı Kayıt defterinize göndermek için hazır olursunuz. Visual Studio Code için IoT araçları uzantısı ile bu adım, şablon dosyasındaki bilgileri ve çözüm dosyalarından modül bilgilerini temel alan dağıtım bildirimini de oluşturur.

### <a name="sign-in-to-docker"></a>Docker 'da oturum açın

Kapsayıcı görüntünüzü kayıt defterinde depolanacak şekilde gönderebilmesi için kapsayıcı kayıt defteri kimlik bilgilerinizi Docker 'a sağlayın.

1. **Görünüm** terminali ' i seçerek Visual Studio Code tümleşik Terminal ' i açın  >  .

2. Kayıt defterini oluşturduktan sonra kaydettiğiniz Azure Container Registry kimlik bilgileriyle Docker 'da oturum açın.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.
   
3. Azure Container Registry oturumu açma

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Derleme ve gönderme

Visual Studio Code artık kapsayıcı Kayıt defterinize erişebilir, bu nedenle çözüm kodunu bir kapsayıcı görüntüsüne dönüştürmek için zaman atalım.

1. Visual Studio Code Gezgini 'nde, dosya **üzerindedeployment.template.js** sağ tıklayın ve **Build ve push IoT Edge çözümünü** seçin.

   ![IoT Edge modülleri oluşturun ve gönderin](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkincisi, `docker build` hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. Ardından, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır.

2. Dosyayı yeni oluşturulan yapılandırma klasöründe **deployment.amd64.js** açın. Dosya adı hedef mimariyi yansıtır, bu nedenle farklı bir mimari seçerseniz farklı olur.

3. Artık yer tutucuları olan iki parametrenin doğru değerleriyle doldurulduğunu unutmayın. **Registrycredentials** bölümünde kayıt defteri Kullanıcı adınız ve parolanız. env dosyasından alınır. **SampleModule** , dosyadaki module.js, sürüm ve mimari etiketine sahip tam görüntü deposuna sahiptir.

4. SampleModule klasöründeki dosya **module.js** açın.

5. Modül görüntüsünün sürüm numarasını değiştirin. ($Schema sürümü değil sürümü.) Örneğin, modül kodunda küçük bir düzeltme yaptığımız gibi, düzeltme eki sürüm numarasını **0.0.2** olarak artırın.

   >[!TIP]
   >Modül sürümleri sürüm denetimini etkinleştirir ve güncelleştirmeleri üretime dağıtabilmeniz için küçük bir cihaz kümesinde değişiklikleri test etmeniz için izin verir. Derleme ve göndermeden önce modül sürümünü arttırmıyorsanız, depo kayıt defterinizde deponun üzerine yazarsınız.

6. Değişikliklerinizi dosyadaki module.jskaydedin.

7. Dosyadaki **deployment.template.js** tekrar sağ tıklayın ve ardından **IoT Edge çözümü oluştur ve Gönder**' i seçin.

8. Dosyayı yeniden **deployment.amd64.js** açın. Build ve push komutunu yeniden çalıştırdığınızda yeni bir dosyanın oluşturulduğuna dikkat edin. Bunun yerine, aynı dosya değişiklikleri yansıtacak şekilde güncelleştirildi. SampleModule görüntüsü artık kapsayıcının 0.0.2 sürümüne işaret ediyor.

9. Build ve push komutunun ne yaptığını daha fazla doğrulamak için [Azure Portal](https://portal.azure.com) gidin ve kapsayıcı Kayıt defterinize gidin.

10. Kapsayıcı kayıt defterinizde **depolar** ' ı ve **sampleModule**' ü seçin. Görüntünün her iki sürümünün de kayıt defterine itildiğini doğrulayın.

    ![Her iki görüntü sürümünü kapsayıcı kayıt defterinde görüntüle](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Sorun giderme

Modül görüntünüzü oluştururken ve gönderirken hatalarla karşılaşırsanız, genellikle geliştirme makinenizde Docker yapılandırması gerekir. Yapılandırmanızı gözden geçirmek için aşağıdaki denetimleri kullanın:

* `docker login`Komutu, kapsayıcı Kayıt defterinizden kopyaladığınız kimlik bilgilerini kullanarak çalıştırdınız mı? Bu kimlik bilgileri, Azure 'da oturum açmak için kullandığınız olanlardan farklıdır.
* Kapsayıcı deponuz doğru mu? Doğru kapsayıcı kayıt defteri adı ve doğru modülünüzün adı mı var? Denetlemek için SampleModule klasöründeki dosya **module.js** açın. Depo değeri **\<registry name\> . azurecr.io/sampleModule** gibi görünmelidir.
* Modülünüzün **örnek modülünden** farklı bir ad kullandıysanız, bu ad çözüm genelinde tutarlıdır mi?
* Makineniz, oluşturmakta olduğunuz kapsayıcı türünü çalıştırıyor mu? Bu öğretici Linux IoT Edge cihazları için olduğundan Visual Studio Code, yan çubukta **AMD64** veya **Arm32v7** deyin ve Docker Desktop 'ın Linux kapsayıcılarını çalıştırması gerekir.  

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Oluşturulan kapsayıcı görüntülerinin kapsayıcı kayıt defterinizde depolandığını doğruladınız, bu nedenle bunları bir cihaza dağıtmaya zaman atalım. IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. Visual Studio Code Gezgini ' nde, **Azure IoT Hub** bölümünde **aygıtlar** ' ı genişleterek IoT cihazları listesini görüntüleyin.

2. Dağıtmak istediğiniz IoT Edge cihaza sağ tıklayın, ardından **tek cihaz Için dağıtım oluştur**' u seçin.

   ![Tek bir cihaz için dağıtım oluşturma](./media/tutorial-develop-for-linux/create-deployment.png)

3. Dosya Gezgini 'nde, **yapılandırma** klasörüne gidin ve **deployment.amd64.jsdosya '** yı seçin.

   Dosyada kapsayıcı kayıt defteri kimlik bilgileri veya modül görüntü değerlerini içermeyen deployment.template.jskullanmayın. Bir Linux ARM32 cihazını hedefliyorsanız, dağıtım bildirimi üzerinde deployment.arm32v7.jsolarak adlandırılır.

4. Cihazınızın altında, dağıtılan ve çalışan modüllerin listesini görmek için **modüller** ' i genişletin. Yenile düğmesine tıklayın. Cihazınızda çalışan yeni SimulatedTemperatureSensor ve SampleModule modüllerini görmeniz gerekir.

   Modüllerin başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir.

   ![IoT Edge cihazınızda çalışan modülleri görüntüleme](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Cihazdan iletileri görüntüle

SampleModule kodu iletileri giriş kuyruğu aracılığıyla alır ve bunları çıkış kuyruğu boyunca geçirir. Dağıtım bildirimi, iletileri SimulatedTemperatureSensor adresinden SampleModule 'e geçen yolları ve SampleModule 'tan IoT Hub iletilen iletileri bildirdi. Visual Studio Code için Azure IoT araçları, her bir cihazınızdan IoT Hub geldikçe iletileri görmenizi sağlar.

1. Visual Studio Code Gezgini ' nde, izlemek istediğiniz IoT Edge cihaza sağ tıklayın, sonra **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

2. IoT Hub 'ınıza ulaşan iletileri görmek için Visual Studio Code çıkış penceresini izleyin.

   ![Gelen cihazı bulut iletilerine görüntüleme](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Cihazdaki değişiklikleri görüntüle

Cihazınızda neler olduğunu görmek isterseniz, cihazınızda çalışan IoT Edge çalışma zamanını ve modülleri incelemek için bu bölümdeki komutları kullanın.

Bu bölümdeki komutlar, geliştirme makineniz değil IoT Edge cihazınıza yöneliktir. IoT Edge cihazınız için bir sanal makine kullanıyorsanız, şimdi bu sunucuya bağlanın. Azure 'da, sanal makinenin genel bakış sayfasına gidin ve güvenli kabuk bağlantısına erişmek için **Bağlan** ' ı seçin.

* Cihazınıza dağıtılan tüm modülleri görüntüleyin ve durumlarını denetleyin:

   ```bash
   iotedge list
   ```

   Dört modül görmeniz gerekir: iki IoT Edge çalışma zamanı modülü, SimulatedTemperatureSensor ve SampleModule. Dört tümü çalışıyor olarak listelenmelidir.

* Belirli bir modülün günlüklerini inceleyin:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge modüller büyük/küçük harfe duyarlıdır.

   SimulatedTemperatureSensor ve SampleModule günlükleri işledikleri iletileri göstermelidir. EdgeAgent modülü, diğer modüllerin başlatılmasından sorumludur, bu nedenle Günlükler dağıtım bildirimini uygulamayla ilgili bilgiler alacak. Herhangi bir modül listelenmemişse veya çalışmıyorsa, edgeAgent günlükleri muhtemelen hatalara sahip olur. EdgeHub modülü, modüller ve IoT Hub arasındaki iletişimlerden sorumludur. Modüller çalışır duruma geliyor ancak iletiler IoT Hub 'ınıza inmemişse, edgeHub günlükleri muhtemelen hatalara sahip olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, geliştirme makinenizde Visual Studio Code ayarlarsınız ve ilk IoT Edge modülünüzü buradan dağıttınız. Temel kavramları öğrenmiş olduğunuza göre, bir modüle işlev eklemeyi deneyin. böylece bu, üzerinden geçen verileri analiz edebilir. Tercih ettiğiniz dili seçin:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
