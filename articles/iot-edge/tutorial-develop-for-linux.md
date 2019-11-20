---
title: 'Öğretici: Linux cihazları için modül geliştirme-Azure IoT Edge'
description: Bu öğretici, Linux cihazları için Linux kapsayıcıları kullanarak IoT Edge modülleri geliştirmek üzere geliştirme makinenizi ve bulut kaynaklarınızı ayarlamayı adım adım göstermektedir
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 01ca118348b3a084c97182338bf656da83d52cb4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114045"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Öğretici: Linux cihazları için IoT Edge modülleri geliştirme

IoT Edge çalıştıran Linux cihazlara kod geliştirmek ve dağıtmak için Visual Studio Code kullanın. 

Hızlı başlangıç makalelerinde, Linux sanal makinesini kullanarak bir IoT Edge cihaz oluşturdunuz ve Azure Marketi 'nden önceden oluşturulmuş bir modül dağıtıldı. Bu öğretici, bir IoT Edge cihazına kendi kodunuzu geliştirme ve dağıtma konusunda size yol gösterir. Bu öğretici, belirli programlama dilleri veya Azure hizmetleri hakkında daha fazla ayrıntıya gidecek tüm diğer öğreticiler için faydalı bir önkoşuldur. 

Bu öğretici  **C# bir Linux cihazına Modül**dağıtma örneğini kullanır. Bu örnek, IoT Edge çözümleri için en yaygın geliştirici senaryosu olduğundan seçilmiştir. Farklı bir dil kullanmayı planlıyor veya bir Azure hizmeti dağıtsanız bile, bu öğretici geliştirme araçları ve kavramlar hakkında bilgi edinmek için hala yararlıdır. Geliştirme sürecine bu girişi tamamladıktan sonra, ayrıntıları incelemek için tercih ettiğiniz dili veya Azure hizmetini seçebilirsiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Geliştirme makinenizi ayarlayın.
> * Yeni bir proje oluşturmak için Visual Studio Code IoT Edge araçları kullanın.
> * Projenizi kapsayıcı olarak derleyin ve Azure Container Registry 'de saklayın.
> * Kodunuzu bir IoT Edge cihazına dağıtın. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Önemli kavramlar

Bu öğreticide IoT Edge modülünün geliştirilmesi gösterilmektedir. Bir *IoT Edge modülü*veya bazen yalnızca Short için bir *Modül* , yürütülebilir kod içeren bir kapsayıcıdır. Bir IoT Edge cihazına bir veya daha fazla modül dağıtabilirsiniz. Modüller, algılayıcılardan veri almak, veri analizi veya veri temizleme işlemleri gerçekleştirmek veya bir IoT Hub 'ına ileti göndermek gibi belirli görevleri gerçekleştirir. Daha fazla bilgi için bkz. [Azure IoT Edge modüllerini anlama](iot-edge-modules.md).

IoT Edge modüller geliştirirken, geliştirme makinesi ve modülün sonunda dağıtıldığı hedef IoT Edge cihaz arasındaki farkı anlamak önemlidir. Modül kodunuzu tutmak için oluşturduğunuz kapsayıcı, *hedef cihazın*işletim SISTEMI (OS) ile aynı olmalıdır. Örneğin, en yaygın senaryo, bir Windows bilgisayarda IoT Edge çalıştıran bir Linux cihazını hedefleyecek bir modül geliştiren kişidir. Bu durumda, kapsayıcı işletim sistemi Linux olur. Bu öğreticiye giderek, *geliştirme MAKINESI işletim* sistemi ve *kapsayıcı işletim sistemi*arasındaki farkı aklınızda bulundurun.

Bu öğretici, IoT Edge çalıştıran Linux cihazlarını hedefler. Geliştirme makineniz Linux kapsayıcıları çalıştırabilmeniz koşuluyla tercih ettiğiniz işletim sistemini kullanabilirsiniz. Bu öğreticinin kullanacağı, Linux cihazları için geliştirme için Visual Studio Code kullanmanızı öneririz. Visual Studio 'Yu da kullanabilirsiniz, ancak iki araç arasındaki DESTEKDE farklılıklar vardır.

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

## <a name="prerequisites"></a>Önkoşullar

Geliştirme makinesi:

* Geliştirme tercihlerinize bağlı olarak kendi bilgisayarınızı veya bir sanal makineyi kullanabilirsiniz.
  * Geliştirme makinenizin iç içe sanallaştırmayı desteklediğinden emin olun. Bu özellik, bir sonraki bölümde yüklediğiniz bir kapsayıcı altyapısını çalıştırmak için gereklidir.
* Bir kapsayıcı altyapısını çalıştırabilme ile ilgili çoğu işletim sistemi, Linux cihazları için IoT Edge modülleri geliştirmek üzere kullanılabilir. Bu öğretici bir Windows bilgisayarı kullanır, ancak MacOS veya Linux üzerinde bilinen farklılıkları gösterir. 
* Bu öğreticide daha sonra modül şablonu paketleri çekmek için [Git](https://git-scm.com/)'i yükler.  
* [Visual Studio Code için C# (OmniSharp tarafından desteklenen) uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK'sı](https://www.microsoft.com/net/download).

Linux üzerinde Azure IoT Edge bir cihaz:

* Geliştirme makinenizde IoT Edge çalıştırmanızı öneririz, bunun yerine ayrı bir cihaz kullanın. Geliştirme makinesi ve IoT Edge cihaz arasındaki bu ayrım, doğru bir dağıtım senaryosunu daha doğru yansıtmıştır ve farklı kavramları düz tutmaya yardımcı olur.
* Kullanılabilir ikinci bir cihazınız yoksa, Azure 'da [Linux sanal makinesiyle](quickstart-linux.md)IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalesini kullanın.

Bulut kaynakları:

* Azure 'da ücretsiz veya Standart katmanlı [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) . 

## <a name="install-container-engine"></a>Kapsayıcı altyapısını yükler

IoT Edge modüller kapsayıcı olarak paketlenir, bu nedenle kapsayıcıları derlemek ve yönetmek için geliştirme makinenizde bir kapsayıcı altyapısına ihtiyacınız vardır. Bir kapsayıcı altyapısı olarak çok sayıda özelliği ve popülerliği nedeniyle, Docker Desktop 'ı geliştirme için kullanmanızı öneririz. Windows cihazındaki Docker Desktop ile, farklı türlerde IoT Edge cihazları için kolayca modül geliştirebilmeniz için Linux kapsayıcıları ve Windows kapsayıcıları arasında geçiş yapabilirsiniz. 

Geliştirme makinenize yüklemek için Docker belgelerini kullanın: 

* [Windows için Docker Desktop 'ı yükler](https://docs.docker.com/docker-for-windows/install/)

  * Windows için Docker Desktop 'ı yüklediğinizde, Linux veya Windows kapsayıcıları kullanmak isteyip istemediğiniz sorulur. Bu karar, bir kolay anahtar kullanılarak herhangi bir zamanda değiştirilebilir. Bu öğreticide, modüllerimiz Linux cihazlarını hedeflediğinden, Linux kapsayıcıları kullanırız. Daha fazla bilgi için bkz. [Windows ve Linux kapsayıcıları arasında geçiş](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Mac için Docker Desktop 'ı yükler](https://docs.docker.com/docker-for-mac/install/)

* Birkaç Linux platformunda yükleme bilgileri için [Docker CE hakkında](https://docs.docker.com/install/) bilgi edinin.

## <a name="set-up-vs-code-and-tools"></a>VS Code ve araçları ayarlama

IoT Edge modülleri geliştirmek için Visual Studio Code için IoT uzantıları 'nı kullanın. Bu uzantılar proje şablonları sağlar, dağıtım bildiriminin oluşturulmasını otomatik hale getirir ve IoT Edge cihazları izlemenize ve yönetmenize olanak tanır. Bu bölümde Visual Studio Code ve IoT uzantısını yükleyip Visual Studio Code içinden IoT Hub kaynaklarını yönetmek üzere Azure hesabınızı ayarlarsınız. 

1. Geliştirme makinenize [Visual Studio Code](https://code.visualstudio.com/) 'yi yükler. 

2. Yükleme tamamlandıktan sonra, > **uzantıları** **görüntüle** ' yi seçin. 

3. Aslında IoT Hub ve IoT cihazlarıyla etkileşime başlamanıza yardımcı olan ve IoT Edge modülleri geliştiren bir uzantı koleksiyonu olan **Azure IoT araçları**' nı arayın. 

4. **Yükle**’yi seçin. Dahil edilen her uzantı tek tek yüklenir. 

5. Uzantılar yükleme tamamlandığında, komut paleti ' ni **görüntüle** > **komut paleti**' ni seçerek açın. 

6. Komut paletinde **Azure: oturum aç**' ı arayıp seçin. Azure hesabınızda oturum açmak için yönergeleri izleyin. 

7. Komut paletinde, **Azure IoT Hub arayıp seçin: IoT Hub ' i seçin**. Azure aboneliğinizi ve IoT Hub 'ınızı seçmek için istemleri izleyin. 

7. Sol taraftaki etkinlik çubuğundaki simgeyi seçerek veya **görünüm** > **Gezgini**' ni seçerek Visual Studio Code gezgin bölümünü açın. 

8. Gezgin bölümünün en altında, daraltılan **Azure IoT Hub cihazları** menüsünü genişletin. Komut paleti aracılığıyla seçtiğiniz IoT Hub 'ı ile ilişkili cihazları ve IoT Edge cihazları görmeniz gerekir. 

   ![IoT Hub 'ınızdaki cihazları görüntüleme](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Yeni bir modül projesi oluştur

Azure IoT araçları uzantısı, Visual Studio Code tüm desteklenen IoT Edge modülü dilleri için proje şablonları sağlar. Bu şablonlar, IoT Edge test etmek için çalışan bir modül dağıtmak için ihtiyacınız olan tüm dosya ve koda sahiptir veya şablonu kendi iş mantığınızla özelleştirmek için bir başlangıç noktası sağlar. 

Bu öğreticide, en sık kullanılan C# şablon olduğu için modül şablonunu kullanırız. 

### <a name="create-a-project-template"></a>Proje şablonu oluşturma

Visual Studio Code komut paletinde arama yapın ve **Azure IoT Edge: yeni IoT Edge çözüm**' i seçin. Çözümünüzü oluşturmak için istemleri izleyin ve aşağıdaki değerleri kullanın: 

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan değerleri kabul **EdgeSolution**. |
   | Modül şablonunu seçin | Seçin  **C# Modülü**. |
   | Modül adı sağlayın | Varsayılan **sampleModule**'ü kabul edin. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, son adımda verdiğiniz adından önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. Oturum açma sunucusunu Azure portalda kapsayıcı kayıt defterinizin Genel bakış sayfasından alabilirsiniz. <br><br> Son görüntü deposu, \<kayıt defteri adı\>. azurecr.io/samplemodule gibi görünüyor. |
 
   ![Docker görüntü deposunu sağlama](./media/tutorial-develop-for-linux/image-repository.png)

Yeni çözümünüz Visual Studio Code penceresine yüklendikten sonra, oluşturduğu dosyaları öğrenmek için biraz zaman ayırın: 

* **. Vscode** klasörü, hata ayıklama modülleri için kullanılan **Launch. JSON**adlı bir dosya içerir.
* **Modüller** klasörü, çözümünüzdeki her modül için bir klasör içerir. Şu anda yalnızca **sampleModule**veya modüle verdiğiniz herhangi bir ad olmalıdır. SampleModule klasörü ana program kodunu, modül meta verilerini ve birkaç Docker dosyasını içerir. 
* **. Env** dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini tutar. Bu kimlik bilgileri, kapsayıcı görüntülerini çekmek için erişim sağlamak üzere IoT Edge cihazınızdan paylaşılır. 
* Deployment. **Debug. Template. JSON** dosyası ve **Deployment. Template. JSON** dosyası, bir dağıtım bildirimi oluşturmanıza yardımcı olan şablonlardır. *Dağıtım bildirimi* , bir cihaza hangi modüllerin dağıtılmasını, bunların nasıl yapılandırılacağını ve bunların birbirleriyle ve buluttan nasıl iletişim kurabildiğini tanımlayan bir dosyadır. Şablon dosyaları bazı değerler için işaretçiler kullanır. Şablonu doğru bir dağıtım bildirimine dönüştürdüğünüzde, işaretçiler diğer çözüm dosyalarından alınan değerlerle değiştirilmiştir. Dağıtım şablonunuzda iki ortak yer tutucuyu bulun: 

  * Kayıt defteri bilgileri bölümünde, adres, çözümü oluşturduğunuzda verdiğiniz bilgilerden tekrar doldurulur. Ancak, Kullanıcı adı ve parola. env dosyasında depolanan değişkenlere başvurur. Bu,. env dosyası git yoksayıldı, ancak dağıtım şablonu olmadığı için güvenlik içindir. 
  * SampleModule bölümünde, çözümü oluştururken görüntü deposunu sağlasanız bile kapsayıcı görüntüsü doldurulmamış. Bu yer tutucu, SampleModule klasörünün içindeki **Module. JSON** dosyasını işaret eder. Bu dosyaya giderseniz, görüntü alanının depoyu içerdiğini, aynı zamanda kapsayıcının sürümünden ve platformundan oluşan bir etiket değerini görürsünüz. Geliştirme döngünüzün bir parçası olarak sürümü el ile yineleyebilirsiniz ve bu bölümde daha sonra tanıtıldığımız bir değiştirici kullanarak kapsayıcı platformunu seçebilirsiniz. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>IoT Edge aracısına kayıt defteri kimlik bilgilerinizi girin

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanının kapsayıcı görüntülerinizi IoT Edge cihaza çekmek için bu kimlik bilgilerine ihtiyacı vardır. 

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. Modül çözümünüzde **. env** dosyasını açın. 
2. Azure Container Registry 'nizden kopyaladığınız **Kullanıcı adı** ve **parola** değerlerini ekleyin.
3. Değişikliklerinizi. env dosyasına kaydedin. 

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 C# ve ARM32v7 cihazları için modüller geliştirebilir. Kapsayıcının nasıl oluşturulduğunu ve çalıştığını etkilediğinden, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Linux AMD64 varsayılandır. 

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla**veya pencerenin altındaki yan çubukta kısayol simgesini seçin. 

   ![Yan çubukta mimari simgesini seçin](./media/tutorial-develop-for-linux/select-architecture.png)

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız. 

### <a name="review-the-sample-code"></a>Örnek kodu gözden geçirin

Oluşturduğunuz çözüm şablonu, bir IoT Edge modülü için örnek kod içerir. Bu örnek modül yalnızca iletileri alır ve ardından üzerine geçirir. Ardışık düzen işlevselliği, modüllerin birbirleriyle iletişim kurduğu IoT Edge önemli bir kavramı gösterir.

Her modülün kodunda birden çok *giriş* ve *Çıkış* kuyruğu olabilir. Cihazda çalışan IoT Edge hub 'ı bir modülün çıktısından gelen iletileri bir veya daha fazla modülün girdisine yönlendirir. Giriş ve çıkışları bildirmek için dile özgü dil diller arasında farklılık gösterir, ancak kavram tüm modüller arasında aynıdır. Modüller arasında yönlendirme hakkında daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes).

Proje şablonuyla C# birlikte gelen örnek kod, .net için IoT Hub SDK 'Sının [moduleclient sınıfını](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) kullanır. 

1. **Modüller/SampleModule/** klasör içinde bulunan **program.cs** dosyasını açın. 

2. Program.cs ' de **Setınputmessagehandlerasync** metodunu bulun.

2. [Setınputmessagehandlerasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) yöntemi gelen iletileri almak için bir giriş kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **input1**adlı bir giriş kuyruğunu nasıl Başlatan hakkında bilgi alın. 

   ![Setınputmessagecallback oluşturucuda giriş adını bulma](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. Ardından **SendEventAsync** yöntemini bulun.

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) yöntemi alınan iletileri işler ve bunları iletmek için bir çıkış kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **output1**adlı bir çıkış kuyruğu başlattığında bkz. 

   ![SendEventToOutputAsync içinde çıkış adını bulma](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. **Deployment. Template. JSON** dosyasını açın.

7. $EdgeAgent istenen özelliklerin **modüller** özelliğini bulun. 

   Burada listelenen iki modül olmalıdır. İlk olarak, modüllerinizi test etmek için kullanabileceğiniz sanal sıcaklık verileri sağlamak üzere varsayılan olarak tüm şablonlara dahil olan **SimulatedTemperatureSensor**. İkincisi, bu çözümün bir parçası olarak oluşturduğunuz **sampleModule** modülüdür.

7. Dosyanın en altında, **$edgeHub** modülü için istenen özellikleri bulun. 

   IoT Edge hub modülünün işlevlerinden biri, iletileri bir dağıtımdaki tüm modüller arasında yönlendirmekte. **Rotalar** özelliğindeki değerleri gözden geçirin. İlk yol olan **SampleModuleToIoTHub**, sampleModule modülündeki herhangi bir çıkış kuyruğuna gelen herhangi bir iletiyi göstermek için bir joker karakter ( **\*** ) kullanır. Bu iletiler, IoT Hub belirten ayrılmış bir ad olan *$upstream*' a gider. İkinci yol olan sensorToSampleModule, SimulatedTemperatureSensor modülünden gelen iletileri alır ve bunları SampleModule kodunda gördüğünüz *input1* giriş kuyruğuna yönlendirir. 

   ![Dağıtım. Template. JSON içindeki yolları gözden geçirin](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Oluşturun ve çözümünüzü gönderin

Bazı önemli dağıtım kavramlarını anlamak için modül kodunu ve Dağıtım şablonunu gözden geçirdiniz. Şimdi SampleModule kapsayıcı görüntüsünü oluşturmak ve kapsayıcı Kayıt defterinize göndermek için hazır olursunuz. Visual Studio Code için IoT araçları uzantısı ile bu adım, şablon dosyasındaki bilgileri ve çözüm dosyalarından modül bilgilerini temel alan dağıtım bildirimini de oluşturur. 

### <a name="sign-in-to-docker"></a>Docker 'da oturum açın

Kapsayıcı görüntünüzü kayıt defterinde depolanacak şekilde gönderebilmesi için kapsayıcı kayıt defteri kimlik bilgilerinizi Docker 'a sağlayın. 

1. Visual Studio Code > **terminali**' i seçerek tümleşik Terminal ' i açın.

2. Kayıt defterini oluşturduktan sonra kaydettiğiniz Azure Container Registry kimlik bilgileriyle Docker 'da oturum açın. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin`kullanımını öneren bir güvenlik uyarısı alabilirsiniz. Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

### <a name="build-and-push"></a>Derleme ve gönderme 

Visual Studio Code artık kapsayıcı Kayıt defterinize erişebilir, bu nedenle çözüm kodunu bir kapsayıcı görüntüsüne dönüştürmek için zaman atalım. 

1. Visual Studio Code Gezgini ' nde **Deployment. Template. JSON** dosyasına sağ tıklayın ve **IoT Edge çözümü oluştur ve Gönder**' i seçin. 

   ![IoT Edge modülleri oluşturun ve gönderin](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkinci olarak, hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için `docker build` çalıştırır. Sonra, görüntü deposunu kapsayıcı Kayıt defterinize göndermek için `docker push` çalıştırır. 

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır. 

2. Yeni oluşturulan yapılandırma klasöründe **Deployment. AMD64. JSON** dosyasını açın. Dosya adı hedef mimariyi yansıtır, bu nedenle farklı bir mimari seçerseniz farklı olur.

3. Artık yer tutucuları olan iki parametrenin doğru değerleriyle doldurulduğunu unutmayın. **Registrycredentials** bölümünde kayıt defteri Kullanıcı adınız ve parolanız. env dosyasından alınır. **SampleModule** , Module. JSON dosyasındaki Name, Version ve Architecture etiketiyle tam görüntü deposuna sahiptir. 

4. SampleModule klasöründe **Module. JSON** dosyasını açın. 

5. Modül görüntüsünün sürüm numarasını değiştirin. ($Schema sürümü değil sürümü.) Örneğin, modül kodunda küçük bir düzeltme yaptığımız gibi, düzeltme eki sürüm numarasını **0.0.2** olarak artırın. 

   >[!TIP]
   >Modül sürümleri sürüm denetimini etkinleştirir ve güncelleştirmeleri üretime dağıtabilmeniz için küçük bir cihaz kümesinde değişiklikleri test etmeniz için izin verir. Derleme ve göndermeden önce modül sürümünü arttırmıyorsanız, depo kayıt defterinizde deponun üzerine yazarsınız. 

6. Değişikliklerinizi Module. JSON dosyasına kaydedin.

7. **Deployment. Template. JSON** dosyasına tekrar sağ tıklayın ve ardından **IoT Edge çözümü oluştur ve Gönder**' i seçin.

8. **Deployment. AMD64. JSON** dosyasını yeniden açın. Build ve push komutunu yeniden çalıştırdığınızda yeni bir dosyanın oluşturulduğuna dikkat edin. Bunun yerine, aynı dosya değişiklikleri yansıtacak şekilde güncelleştirildi. SampleModule görüntüsü artık kapsayıcının 0.0.2 sürümüne işaret ediyor. 

9. Build ve push komutunun ne yaptığını daha fazla doğrulamak için [Azure Portal](https://portal.azure.com) gidin ve kapsayıcı Kayıt defterinize gidin. 

10. Kapsayıcı kayıt defterinizde **depolar** ' ı ve **sampleModule**' ü seçin. Görüntünün her iki sürümünün de kayıt defterine itildiğini doğrulayın.

    ![Her iki görüntü sürümünü kapsayıcı kayıt defterinde görüntüle](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Sorun giderme

Modül görüntünüzü oluştururken ve gönderirken hatalarla karşılaşırsanız, genellikle geliştirme makinenizde Docker yapılandırması gerekir. Yapılandırmanızı gözden geçirmek için aşağıdaki denetimleri kullanın: 

* Kapsayıcı kayıt defterinden kopyaladığınız kimlik bilgilerini kullanarak `docker login` komutunu çalıştırdınız mı? Bu kimlik bilgileri, Azure 'da oturum açmak için kullandığınız olanlardan farklıdır. 
* Kapsayıcı deponuz doğru mu? Doğru kapsayıcı kayıt defteri adı ve doğru modülünüzün adı mı var? Denetlemek için SampleModule klasöründeki **Module. JSON** dosyasını açın. Depo değeri, **\<\>. azurecr.io/sampleModule kayıt defteri adı**gibi görünmelidir. 
* Modülünüzün **örnek modülünden** farklı bir ad kullandıysanız, bu ad çözüm genelinde tutarlıdır mi?
* Makineniz, oluşturmakta olduğunuz kapsayıcı türünü çalıştırıyor mu? Bu öğretici Linux IoT Edge cihazları için olduğundan Visual Studio Code, yan çubukta **AMD64** veya **Arm32v7** deyin ve Docker Desktop 'ın Linux kapsayıcılarını çalıştırması gerekir.  

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Oluşturulan kapsayıcı görüntülerinin kapsayıcı kayıt defterinizde depolandığını doğruladınız, bu nedenle bunları bir cihaza dağıtmaya zaman atalım. IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun. 

1. Visual Studio Code Gezgini ' nde, Azure IoT Hub cihazları bölümünü genişletin. 

2. Dağıtmak istediğiniz IoT Edge cihaza sağ tıklayın, ardından **tek cihaz Için dağıtım oluştur**' u seçin. 

   ![Tek bir cihaz için dağıtım oluşturma](./media/tutorial-develop-for-linux/create-deployment.png)

3. Dosya Gezgini 'nde, **yapılandırma** klasörüne gidin ve **Deployment. AMD64. JSON** dosyasını seçin. 

   Kapsayıcı kayıt defteri kimlik bilgilerine veya modül görüntü değerlerine sahip olmayan Deployment. Template. json dosyasını kullanmayın. Bir Linux ARM32 cihazını hedefliyorsanız dağıtım bildirimi Deployment. arm32v7. JSON olarak adlandırılır. 

4. IoT Edge cihazınızın ayrıntılarını genişletin ve cihazınızın **modüller** listesini genişletin. 

5. Cihazınızda çalışan SimulatedTemperatureSensor ve SampleModule modüllerini görene kadar cihaz görünümünü güncelleştirmek için Yenile düğmesini kullanın. 

   Her iki modülün de başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir. 

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

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, geliştirme makinenizde Visual Studio Code ayarlarsınız ve ilk IoT Edge modülünüzü buradan dağıttınız. Temel kavramları öğrenmiş olduğunuza göre, bir modüle işlev eklemeyi deneyin. böylece bu, üzerinden geçen verileri analiz edebilir. Tercih ettiğiniz dili seçin: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)