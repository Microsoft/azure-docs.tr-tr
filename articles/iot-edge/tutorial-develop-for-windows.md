---
title: 'Öğretici: Windows cihazları için modül geliştirme-Azure IoT Edge'
description: Bu öğretici, Windows cihazları için Windows kapsayıcıları kullanarak IoT Edge modülleri geliştirmek üzere geliştirme makinenizi ve bulut kaynaklarınızı ayarlamayı adım adım göstermektedir
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 67a80178c5cd800ea08d9f5493efd0f37422fe36
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113989"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Öğretici: Windows cihazları için IoT Edge modülleri geliştirme

IoT Edge çalıştıran Windows cihazlarına kod geliştirmek ve dağıtmak için Visual Studio 'Yu kullanın.

Hızlı başlangıçta, bir Windows sanal makinesini kullanarak IoT Edge bir cihaz oluşturdunuz ve Azure Marketi 'nden önceden oluşturulmuş bir modül dağıtıldı. Bu öğretici, bir IoT Edge cihazına kendi kodunuzu geliştirme ve dağıtma konusunda size yol gösterir. Bu öğretici, belirli programlama dilleri veya Azure hizmetleri hakkında daha ayrıntılı bilgi sağlayan diğer öğreticiler için faydalı bir önkoşuldur. 

Bu öğretici bir  **C# Windows cihazına Modül**dağıtma örneğini kullanır. En yaygın geliştirme senaryosu olduğundan bu örnek seçildi. Farklı bir dilde geliştirme veya Azure hizmetlerini modül olarak dağıtmaya çalışıyorsanız, bu öğretici geliştirme araçları hakkında bilgi edinmek için yine de yararlı olacaktır. Geliştirme kavramlarını anladıktan sonra, ayrıntıları görmek için tercih ettiğiniz dili veya Azure hizmetini seçebilirsiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Geliştirme makinenizi ayarlayın.
> * Visual Studio için IoT Edge araçları 'nı kullanarak yeni bir proje oluşturun.
> * Projenizi kapsayıcı olarak derleyin ve Azure Container Registry 'de saklayın.
> * Kodunuzu bir IoT Edge cihazına dağıtın. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Önemli kavramlar

Bu öğreticide IoT Edge modülünün geliştirilmesi gösterilmektedir. Bir *IoT Edge modülü*veya bazen yalnızca Short için bir *Modül* , yürütülebilir kod içeren bir kapsayıcıdır. Bir IoT Edge cihazına bir veya daha fazla modül dağıtabilirsiniz. Modüller, algılayıcılardan veri almak, veri analizi veya veri temizleme işlemleri gerçekleştirmek veya bir IoT Hub 'ına ileti göndermek gibi belirli görevleri gerçekleştirir. Daha fazla bilgi için bkz. [Azure IoT Edge modüllerini anlama](iot-edge-modules.md).

IoT Edge modüller geliştirirken, geliştirme makinesi ve modülün sonunda dağıtıldığı hedef IoT Edge cihaz arasındaki farkı anlamak önemlidir. Modül kodunuzu tutmak için oluşturduğunuz kapsayıcı, *hedef cihazın*işletim SISTEMI (OS) ile aynı olmalıdır. Windows kapsayıcı geliştirmesi için, Windows kapsayıcıları yalnızca Windows işletim sistemlerinde çalıştığı için bu kavram daha basittir. Ancak örneğin, Windows geliştirme makinenizi Linux IoT Edge cihazları için modüller oluşturmak üzere kullanabilirsiniz. Bu senaryoda, geliştirme makinenizin Linux kapsayıcıları çalıştırdığından emin olmanız gerekir. Bu öğreticiye giderek, *geliştirme MAKINESI işletim* sistemi ve *kapsayıcı işletim sistemi*arasındaki farkı aklınızda bulundurun.

Bu öğretici, IoT Edge çalıştıran Windows cihazlarını hedefler. Windows IoT Edge cihazları Windows kapsayıcıları kullanır. Bu öğreticinin kullanacağı, Windows cihazları için geliştirme için Visual Studio kullanmanızı öneririz. İki araç arasındaki DESTEKDE farklılıklar olsa da Visual Studio Code de kullanabilirsiniz.

Aşağıdaki tabloda, Visual Studio Code ve Visual Studio 'da **Windows kapsayıcıları** için desteklenen geliştirme senaryoları listelenmiştir.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure hizmetleri** | Azure İşlevleri <br> Azure Stream Analytics |   |
| **Diller** | C#(hata ayıklama desteklenmiyor) | C <br> C# |
| **Daha fazla bilgi** | [Visual Studio Code için Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Visual Studio 2017 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Visual Studio 2019 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Önkoşullar

Geliştirme makinesi:

* Windows 10, 1809 güncelleştirmesiyle veya daha yeni bir sürüm.
* Geliştirme tercihlerinize bağlı olarak kendi bilgisayarınızı veya bir sanal makineyi kullanabilirsiniz.
  * Geliştirme makinenizin iç içe sanallaştırmayı desteklediğinden emin olun. Bu özellik, bir sonraki bölümde yüklediğiniz bir kapsayıcı altyapısını çalıştırmak için gereklidir.
* [Git](https://git-scm.com/)'i yükleyin. 

Windows 'da Azure IoT Edge cihaz:

* Geliştirme makinenizde IoT Edge çalıştırmanızı öneririz, bunun yerine ayrı bir cihaz kullanın. Geliştirme makinesi ve IoT Edge cihaz arasındaki bu ayrım, doğru bir dağıtım senaryosunu daha doğru yansıtmıştır ve farklı kavramları düz tutmaya yardımcı olur.
* Kullanılabilir ikinci bir cihazınız yoksa, Azure 'da bir [Windows sanal makinesiyle](quickstart.md)IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalesini kullanın.

Bulut kaynakları:

* Azure 'da ücretsiz veya Standart katmanlı [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) . 

## <a name="install-container-engine"></a>Kapsayıcı altyapısını yükler

IoT Edge modüller kapsayıcı olarak paketlenir, bu nedenle kapsayıcıları derlemek ve yönetmek için geliştirme makinenizde bir kapsayıcı altyapısına ihtiyacınız vardır. Bir kapsayıcı altyapısı olarak çok sayıda özelliği ve popülerliği nedeniyle, Docker Desktop 'ı geliştirme için kullanmanızı öneririz. Windows bilgisayarda Docker Desktop ile, farklı türlerde IoT Edge cihazları için kolayca modül geliştirebilmeniz için Linux kapsayıcıları ve Windows kapsayıcıları arasında geçiş yapabilirsiniz. 

Geliştirme makinenize yüklemek için Docker belgelerini kullanın: 

* [Windows için Docker Desktop 'ı yükler](https://docs.docker.com/docker-for-windows/install/)

  * Windows için Docker Desktop 'ı yüklediğinizde, Linux veya Windows kapsayıcıları kullanmak isteyip istemediğiniz sorulur. Bu öğretici için **Windows kapsayıcıları**' nı kullanın. Daha fazla bilgi için bkz. [Windows ve Linux kapsayıcıları arasında geçiş](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Visual Studio ve araçları ayarlama

Visual Studio için IoT uzantıları IoT Edge modüller geliştirmenize yardımcı olur. Bu uzantılar proje şablonları sağlar, dağıtım bildiriminin oluşturulmasını otomatik hale getirir ve IoT Edge cihazları izlemenize ve yönetmenize olanak tanır. Bu bölümde, Visual Studio 'Yu ve IoT Edge uzantısını yüklersiniz ve sonra Azure hesabınızı, IoT Hub kaynaklarını Visual Studio içinden yönetecek şekilde ayarlarsınız. 

Bu öğretici, Visual Studio 2019 için geliştirme adımlarını öğretir. Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, adımlar çok benzerdir. Visual Studio Code kullanmayı tercih ediyorsanız, [Azure IoT Edge için modülleri geliştirmek ve hata ayıklamak için Visual Studio Code kullanma](how-to-vs-code-develop-module.md)bölümündeki yönergelere bakın. 

1. Geliştirme makinenizde Visual Studio 2019 ' i hazırlayın. 

   * Geliştirme makinenizde zaten Visual Studio yoksa, aşağıdaki iş yükleriyle [Visual studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) ' i yükleyebilirsiniz: 

      * Azure geliştirme
      * C++ ile masaüstü geliştirme
      * .NET Core çoklu platform geliştirme

   * Geliştirme makinenizde Visual Studio 2019 zaten varsa, gerekli iş yüklerini eklemek için [Visual Studio 'Yu değiştirme](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) bölümündeki adımları izleyin.

2. Visual Studio 2019 için [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) uzantısını indirip yükleyin. 

   Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, [Visual studio 2017 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)' nı indirip yükleyin.

3. Yüklemelerinizi tamamladığınızda, Visual Studio 2019 ' i açın ve **kod olmadan devam**' ı seçin.

4.  > **bulut Gezginini** **görüntüle** ' yi seçin. 

5. Bulut Gezgini 'nde profil simgesini seçin ve henüz oturum açmadıysanız Azure hesabınızda oturum açın. 

6. Oturum açtıktan sonra Azure abonelikleriniz listelenir. IoT Hub 'ınızı içeren aboneliği genişletin. 

7. Aboneliğiniz altında **IoT** hub 'larınızı ve IoT Hub 'ınızı genişletin. IoT cihazlarınızın bir listesini görmeniz ve bu Gezgini kullanarak bunları yönetebilmeniz gerekir. 

   ![Bulut Gezgininde IoT Hub kaynaklarına erişin](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Yeni bir modül projesi oluştur

Azure IoT Edge araçları uzantısı, Visual Studio 'da desteklenen tüm IoT Edge modül dilleri için proje şablonları sağlar. Bu şablonlar, IoT Edge test etmek için çalışan bir modül dağıtmak için ihtiyacınız olan tüm dosya ve koda sahiptir veya şablonu kendi iş mantığınızla özelleştirmek için bir başlangıç noktası sağlar. 

1. **Dosya** > **Yeni** > **projesi seçin...**

2. Yeni proje penceresinde, **IoT Edge** arayın ve **Azure IoT Edge (Windows amd64)** projesi seçin. **İleri**’ye tıklayın. 

   ![Yeni bir Azure IoT Edge projesi oluştur](./media/tutorial-develop-for-windows/new-project.png)

3. Yeni projeyi yapılandırın penceresinde, projeyi ve çözümü **CSharpTutorialApp**gibi açıklayıcı bir şekilde yeniden adlandırın. Projeyi oluşturmak için **Oluştur** ' a tıklayın.

   ![Yeni bir Azure IoT Edge projesi yapılandırma](./media/tutorial-develop-for-windows/configure-project.png)

4. Modül Ekle penceresinde, projenizi aşağıdaki değerlerle yapılandırın: 

   | Alan | Değer |
   | ----- | ----- |
   | Visual Studio şablonu | **C# Modül**seçin. | 
   | Modül adı | Varsayılan **IotEdgeModule1**kabul edin. | 
   | Depo URL 'Si | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz modül proje adı değerinden önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. **Oturum açma sunucusu** değerini, Azure Portal kapsayıcı kayıt defterinizin **genel bakış** sayfasından alabilirsiniz. <br><br> Son görüntü deposu, \<kayıt defteri adı\>. azurecr.io/iotedgemodule1 gibi görünüyor. |

      ![Projenizi hedef cihaz, modül türü ve kapsayıcı kayıt defteri için yapılandırma](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Modülü oluşturmak için **Ekle** ' yi seçin. 

Yeni projeniz Visual Studio penceresinde yüklendikten sonra, oluşturduğu dosyaları öğrenmek için biraz zaman ayırın: 

* **CSharpTutorialApp**adlı bir IoT Edge projesi.
  * **Modüller** klasörü, projeye dahil olan modüllerin işaretçilerini içerir. Bu durumda, yalnızca IotEdgeModule1 olmalıdır. 
  * Gizli **. env** dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini tutar. Bu kimlik bilgileri, kapsayıcı görüntülerini çekmek için erişim sağlamak üzere IoT Edge cihazınızdan paylaşılır.
  * Deployment **. Template. JSON** dosyası bir dağıtım bildirimi oluşturmanıza yardımcı olacak bir şablondur. *Dağıtım bildirimi* , bir cihaza hangi modüllerin dağıtılmasını, bunların nasıl yapılandırılacağını ve bunların birbirleriyle ve buluttan nasıl iletişim kurabildiğini tanımlayan bir dosyadır.
    > [!TIP]
    > Kayıt defteri bilgileri bölümünde, adres, çözümü oluşturduğunuzda verdiğiniz bilgilerden tekrar doldurulur. Ancak,. env dosyasında depolanan Kullanıcı adı ve parola başvuru değişkenleri. Bu,. env dosyası git yoksayıldı, ancak dağıtım şablonu olmadığı için güvenlik içindir.
* **IotEdgeModule1**adlı bir IoT Edge modülü projesi.
  * **Program.cs** dosyası, proje şablonuyla birlikte C# gelen varsayılan modül kodunu içerir. Varsayılan modül bir kaynaktan giriş alır ve IoT Hub buna karşı geçirir. 
  * **Module. JSON** dosyası, tam görüntü deposu, görüntü sürümü ve desteklenen her platformda kullanılacak Dockerfile dahil olmak üzere modülle ilgili ayrıntıları tutar.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>IoT Edge aracısına kayıt defteri kimlik bilgilerinizi girin

IoT Edge çalışma zamanının kapsayıcı görüntülerinizi IoT Edge cihaza çekmek için kayıt defteri kimlik bilgileriniz gerekir. IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri bilgilerinizi çekmeyi ve dağıtım şablonunda doldurmaya çalışır.

1. Modül çözümünüzde **Deployment. Template. JSON** dosyasını açın.

1. $EdgeAgent istenen özelliklerde **Registrycredentials** özelliğini bulun ve doğru bilgileri içerdiğinden emin olun.

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Modül çözümünüzde **. env** dosyasını açın. (Çözüm Gezgini varsayılan olarak gizlidir, bu nedenle görüntülemek için **tüm dosyaları göster** düğmesini seçmeniz gerekebilir.)

1. Azure Container Registry 'nizden kopyaladığınız **Kullanıcı adı** ve **parola** değerlerini ekleyin.

1. Değişikliklerinizi. env dosyasına kaydedin.

### <a name="review-the-sample-code"></a>Örnek kodu gözden geçirin

Oluşturduğunuz çözüm şablonu, bir IoT Edge modülü için örnek kod içerir. Bu örnek modül yalnızca iletileri alır ve ardından üzerine geçirir. Ardışık düzen işlevselliği, modüllerin birbirleriyle iletişim kurduğu IoT Edge önemli bir kavramı gösterir.

Her modülün kodunda birden çok *giriş* ve *Çıkış* kuyruğu olabilir. Cihazda çalışan IoT Edge hub 'ı bir modülün çıktısından gelen iletileri bir veya daha fazla modülün girdisine yönlendirir. Giriş ve çıkışları bildirmek için dile özgü dil diller arasında farklılık gösterir, ancak kavram tüm modüller arasında aynıdır. Modüller arasında yönlendirme hakkında daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes).

Proje şablonuyla C# birlikte gelen örnek kod, .net için IoT Hub SDK 'Sının [moduleclient sınıfını](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) kullanır. 

1. **Program.cs** dosyasında **Setınputmessagehandlerasync** metodunu bulun.

2. [Setınputmessagehandlerasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) yöntemi gelen iletileri almak için bir giriş kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **input1**adlı bir giriş kuyruğunu nasıl Başlatan hakkında bilgi alın. 

   ![Setınputmessagehandlserasync oluşturucusunda giriş adını bulur](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Ardından **SendEventAsync** yöntemini bulun.

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) yöntemi alınan iletileri işler ve bunları iletmek için bir çıkış kuyruğu ayarlar. Bu yöntemi gözden geçirin ve **output1**adlı bir çıkış kuyruğu başlattığında bkz. 

   ![SendEventAsync oluşturucusunda çıkış adını bulma](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. **Deployment. Template. JSON** dosyasını açın.

6. $EdgeAgent istenen özelliklerin **modüller** özelliğini bulun. 

   Burada listelenen iki modül olmalıdır. İlk olarak, modüllerinizi test etmek için kullanabileceğiniz sanal sıcaklık verileri sağlamak üzere varsayılan olarak tüm şablonlara dahil olan **SimulatedTemperatureSensor**. İkincisi, bu projenin bir parçası olarak oluşturduğunuz **IotEdgeModule1** modülüdür.

   Bu modüller özelliği, cihazınıza veya cihazlarınıza dağıtımda hangi modüllerin dahil edileceğini bildirir. 

7. $EdgeHub istenen özelliklerin **rotalar** özelliğini bulun. 

   IoT Edge hub modülünün işlevlerinden biri, iletileri bir dağıtımdaki tüm modüller arasında yönlendirmekte. Rotalar özelliğindeki değerleri gözden geçirin. İlk yol olan **IotEdgeModule1ToIoTHub**, IotEdgeModule1 modülündeki herhangi bir çıkış sırasından gelen herhangi bir iletiyi dahil etmek için bir joker karakter ( **\*** ) kullanır. Bu iletiler, IoT Hub belirten ayrılmış bir ad olan *$upstream*' a gider. İkinci yol, **sensorToIotEdgeModule1**, SimulatedTemperatureSensor modülünden gelen iletileri alır ve IotEdgeModule1 modülünün *input1* giriş kuyruğuna yönlendirir. 

   ![Dağıtım. Template. JSON içindeki yolları gözden geçirin](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Oluşturun ve çözümünüzü gönderin

Bazı önemli dağıtım kavramlarını anlamak için modül kodunu ve Dağıtım şablonunu gözden geçirdiniz. Şimdi IotEdgeModule1 kapsayıcı görüntüsünü oluşturmak ve kapsayıcı Kayıt defterinize göndermek için hazır olursunuz. Visual Studio için IoT araçları uzantısı ile bu adım, şablon dosyasındaki bilgileri ve çözüm dosyalarından modül bilgilerini temel alan dağıtım bildirimini de oluşturur. 

### <a name="sign-in-to-docker"></a>Docker 'da oturum açın

Kapsayıcı görüntünüzü kayıt defterinde depolanacak şekilde gönderebilmesi için, kapsayıcı kayıt defteri kimlik bilgilerinizi geliştirme makinenizde Docker 'a sağlayın. 

1. PowerShell 'i veya bir komut istemi 'ni açın.

2. Kayıt defterini oluşturduktan sonra kaydettiğiniz Azure Container Registry kimlik bilgileriyle Docker 'da oturum açın. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin`kullanımını öneren bir güvenlik uyarısı alabilirsiniz. Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

### <a name="build-and-push"></a>Derleme ve gönderme

Geliştirme makinenizin artık kapsayıcı Kayıt defterinize erişimi vardır ve IoT Edge cihazlarınız da çok fazla olur. Proje kodunu bir kapsayıcı görüntüsüne dönüştürmek zaman. 

1. **CSharpTutorialApp** proje klasörüne sağ tıklayın ve **IoT Edge modülleri oluştur ve Gönder**' i seçin. 

   ![IoT Edge modülleri oluşturun ve gönderin](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi dışında, tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkinci olarak, hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için `docker build` çalıştırır. Sonra, görüntü deposunu kapsayıcı Kayıt defterinize göndermek için `docker push` çalıştırır. 

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır. 

2. Yeni oluşturulan yapılandırma klasöründe **Deployment. Windows-AMD64. JSON** dosyasını açın. (Yapılandırma klasörü, Visual Studio 'da Çözüm Gezgini görünmeyebilir. Bu durumda, Çözüm Gezgini görev çubuğundaki **tüm dosyaları göster** simgesini seçin.)

3. IotEdgeModule1 bölümünün **image** parametresini bulun. Görüntüde, Module. JSON dosyasındaki Name, Version ve Architecture etiketiyle tam görüntü deposu bulunduğundan emin olun.

4. IotEdgeModule1 klasöründeki **Module. JSON** dosyasını açın. 

5. Modül görüntüsünün sürüm numarasını değiştirin. ($Schema sürümü değil sürümü.) Örneğin, modül kodunda küçük bir düzeltme yaptığımız gibi, düzeltme eki sürüm numarasını **0.0.2** olarak artırın. 

   >[!TIP]
   >Modül sürümleri sürüm denetimini etkinleştirir ve güncelleştirmeleri üretime dağıtabilmeniz için küçük bir cihaz kümesinde değişiklikleri test etmeniz için izin verir. Derleme ve göndermeden önce modül sürümünü arttırmıyorsanız, depo kayıt defterinizde deponun üzerine yazarsınız. 

6. Değişikliklerinizi Module. JSON dosyasına kaydedin.

7. **CSharpTutorialApp** proje klasörünü yeniden sağ tıklatın ve **IoT Edge modüllerini yeniden oluştur ve Gönder** ' i seçin. 

8. **Deployment. Windows-AMD64. JSON** dosyasını yeniden açın. Build ve push komutunu yeniden çalıştırdığınızda yeni bir dosyanın oluşturulduğuna dikkat edin. Bunun yerine, aynı dosya değişiklikleri yansıtacak şekilde güncelleştirildi. IotEdgeModule1 görüntüsü artık kapsayıcının 0.0.2 sürümüne işaret ediyor. Dağıtım bildiriminde bu değişiklik, IoT Edge cihaza, çekme için bir modülün yeni bir sürümü olduğunu nasıl söyleirsiniz. 

9. Build ve push komutunun ne yaptığını daha fazla doğrulamak için [Azure Portal](https://portal.azure.com) gidin ve kapsayıcı Kayıt defterinize gidin. 

10. Kapsayıcı kayıt defterinizde **depolar** ' ı ve sonra **iotedgemodule1**' i seçin. Görüntünün her iki sürümünün de kayıt defterine itildiğini doğrulayın.

    ![Her iki görüntü sürümünü kapsayıcı kayıt defterinde görüntüle](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Sorun giderme

Modül görüntünüzü oluştururken ve gönderirken hatalarla karşılaşırsanız, genellikle geliştirme makinenizde Docker yapılandırması gerekir. Yapılandırmanızı gözden geçirmek için aşağıdaki denetimleri kullanın: 

* Kapsayıcı kayıt defterinden kopyaladığınız kimlik bilgilerini kullanarak `docker login` komutunu çalıştırdınız mı? Bu kimlik bilgileri, Azure 'da oturum açmak için kullandığınız olanlardan farklıdır. 
* Kapsayıcı deponuz doğru mu? Doğru kapsayıcı kayıt defteri adı ve doğru modülünüzün adı mı var? Denetlemek için IotEdgeModule1 klasöründeki **Module. JSON** dosyasını açın. Depo değeri, **\<\>. azurecr.io/iotedgemodule1 kayıt defteri adı**gibi görünmelidir. 
* Modülünüzün **IotEdgeModule1** 'den farklı bir ad kullandıysanız, bu ad çözüm genelinde tutarlıdır mi?
* Makineniz, oluşturmakta olduğunuz kapsayıcı türünü çalıştırıyor mu? Bu öğretici Windows IoT Edge cihazları için olduğundan Visual Studio dosyalarınızda **Windows-AMD64** uzantısı olmalıdır ve Docker Desktop Windows kapsayıcıları çalıştırıyor olmalıdır. 

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Oluşturulan kapsayıcı görüntülerinin kapsayıcı kayıt defterinizde depolandığını doğruladınız, bu nedenle bunları bir cihaza dağıtmaya zaman atalım. IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun. 

1. Visual Studio 'da bulut Gezginini açın ve IoT Hub 'ınız için ayrıntıları genişletin. 

2. Dağıtmak istediğiniz cihazın adını seçin. **Eylemler** listesinde, **dağıtım oluştur**' u seçin.

   ![Tek bir cihaz için dağıtım oluşturma](./media/tutorial-develop-for-windows/create-deployment.png)


3. Dosya Gezgini 'nde projenizin yapılandırma klasörüne gidin ve **Deployment. Windows-AMD64. JSON** dosyasını seçin. Bu dosya genellikle `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json` konumunda bulunur

   İçindeki tam modül görüntü değerlerine sahip olmayan Deployment. Template. json dosyasını kullanmayın. 

4. Cihazınızdaki modülleri görmek için, Cloud Explorer 'da IoT Edge cihazınızın ayrıntılarını genişletin.

5. SimulatedTemperatureSensor ve IotEdgeModule1 modüllerinin cihazınızı dağıttığından bakmak üzere cihaz durumunu güncelleştirmek için **Yenile** düğmesini kullanın. 


   ![IoT Edge cihazınızda çalışan modülleri görüntüleme](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Cihazdan iletileri görüntüle

IotEdgeModule1 kodu iletileri giriş kuyruğu aracılığıyla alır ve bunları çıkış kuyruğu boyunca geçirir. Dağıtım bildirimi, SimulatedTemperatureSensor adresinden IotEdgeModule1 'e ileti geçtiğini ve sonra IotEdgeModule1 'den IoT Hub 'e iletmekte olan yolları bildirdi. Visual Studio için Azure IoT Edge araçları, tek tek cihazlarınızdan IoT Hub geldikçe iletileri görmenizi sağlar. 

1. Visual Studio Cloud Explorer 'da, dağıttığınız IoT Edge cihazın adını seçin. 

2. **Eylemler** menüsünde, **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

3. IoT Hub 'ınıza ulaşan iletileri görmek için Visual Studio 'daki **Çıkış** bölümünü izleyin. 

   Her iki modülün de başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir. 

   ![Gelen cihazı bulut iletilerine görüntüleme](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Cihazdaki değişiklikleri görüntüle

Cihazınızda neler olduğunu görmek isterseniz, cihazınızda çalışan IoT Edge çalışma zamanını ve modülleri incelemek için bu bölümdeki komutları kullanın. 

Bu bölümdeki komutlar, geliştirme makineniz değil IoT Edge cihazınıza yöneliktir. IoT Edge cihazınız için bir sanal makine kullanıyorsanız, şimdi bu sunucuya bağlanın. Azure 'da, sanal makinenin genel bakış sayfasına gidin ve uzak masaüstü bağlantısına erişmek için **Bağlan** ' ı seçin. Cihazda, `iotedge` komutlarını çalıştırmak için bir komut veya PowerShell penceresi açın.

* Cihazınıza dağıtılan tüm modülleri görüntüleyin ve durumlarını denetleyin:

   ```cmd
   iotedge list
   ```

   Dört modül görmeniz gerekir: iki IoT Edge çalışma zamanı modülü, SimulatedTemperatureSensor ve IotEdgeModule1. Dört tümü çalışıyor olarak listelenmelidir.

* Belirli bir modülün günlüklerini inceleyin:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge modüller büyük/küçük harfe duyarlıdır. 

   SimulatedTemperatureSensor ve IotEdgeModule1 günlükleri işlendikleri iletileri göstermelidir. EdgeAgent modülü, diğer modüllerin başlatılmasından sorumludur, bu nedenle Günlükler dağıtım bildirimini uygulamayla ilgili bilgiler alacak. Herhangi bir modül listelenmemişse veya çalışmıyorsa, edgeAgent günlükleri muhtemelen hatalara sahip olur. EdgeHub modülü, modüller ve IoT Hub arasındaki iletişimlerden sorumludur. Modüller çalışır duruma geliyor ancak iletiler IoT Hub 'ınıza inmemişse, edgeHub günlükleri muhtemelen hatalara sahip olur. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, geliştirme makinenizde Visual Studio 2019 kurulumunu ve ilk IoT Edge modülünüzü buradan dağıttığınızı görürsünüz. Temel kavramları öğrenmiş olduğunuza göre, bir modüle işlev eklemeyi deneyin. böylece bu, üzerinden geçen verileri analiz edebilir. Tercih ettiğiniz dili seçin: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
