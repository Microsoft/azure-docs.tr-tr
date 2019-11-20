---
title: Uzaktan Izleme çözümünü yerel olarak dağıtma-IntelliJ IDE-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, test ve geliştirme için IntelliJ kullanarak uzaktan Izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888822"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Uzaktan Izleme çözüm Hızlandırıcısını yerel olarak dağıtma-IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Bu makalede, test ve geliştirme için uzaktan Izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir. IntelliJ 'de mikro hizmetleri çalıştırmayı öğreneceksiniz. Yerel bir mikro hizmet dağıtımı şu bulut hizmetlerini kullanır: IoT Hub, Azure Cosmos DB, Azure Akış Analizi ve Azure Time Series Insights.

Yerel makinenizde Docker 'da uzaktan Izleme çözüm hızlandırıcıyı çalıştırmak istiyorsanız, bkz. [Uzaktan izleme çözüm hızlandırıcıyı yerel olarak dağıtma-Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Ön koşullar

Uzaktan Izleme çözümü Hızlandırıcısı tarafından kullanılan Azure hizmetlerini dağıtmak için etkin bir Azure aboneliğine ihtiyacınız vardır.

Hesabınız yoksa yalnızca birkaç dakika içinde ücretsiz bir deneme sürümü hesabı oluşturabilirsiniz. Ayrıntılar için bkz. [Azure Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Makine Kurulumu

Yerel dağıtımı tamamlamaya yönelik olarak, yerel geliştirme makinenizde aşağıdaki araçların yüklü olması gerekir:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala eklentisi](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT eklentisi](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT yürütücü eklentisi](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [NGINX](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/)

Node. js V8, betiklerin Azure kaynakları oluşturmak için kullandığı bılgısayar CLı için bir önkoşuldur. Node. js ile v10 arasındaki kullanmayın.

> [!NOTE]
> IntelliJ IDE, Windows ve Mac için kullanılabilir.

## <a name="download-the-source-code"></a>Kaynak kodunu indirin

Uzaktan Izleme kaynak kodu depoları, mikro hizmetler Docker görüntülerini çalıştırmak için gereken kaynak kodu ve Docker yapılandırma dosyalarını içerir.

Deponun yerel bir sürümünü kopyalayıp oluşturmak için, komut satırı ortamınızı kullanarak yerel makinenizde uygun bir klasöre gidin. Ardından, Java deposunu kopyalamak için aşağıdaki komut kümelerinden birini çalıştırın:

* Java mikro hizmet uygulamalarının en son sürümünü indirmek için aşağıdaki komutu çalıştırın:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* En son alt modülleri almak için aşağıdaki komutları çalıştırın:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Bu komutlar, mikro hizmetleri yerel olarak çalıştırmak için kullandığınız betiklerin yanı sıra tüm mikro hizmetler için kaynak kodu indirir. Mikro hizmetleri Docker 'da çalıştırmak için kaynak koda ihtiyacınız yoktur. Ancak, daha sonra çözüm hızlandırıcıyı değiştirmeyi ve değişikliklerinizi yerel olarak test etmek için planlıyorsanız, kaynak kodu yararlı olur.

## <a name="deploy-the-azure-services"></a>Azure hizmetlerini dağıtma

Bu makalede, mikro hizmetlerin yerel olarak nasıl çalıştırılacağı gösterilmekle birlikte, bulutta çalışan Azure hizmetlerine bağımlıdır. Azure hizmetlerini dağıtmak için aşağıdaki betiği kullanın. Betik örnekleri, bir Windows makinesinde Java deposunu kullandığınızı varsayar. Başka bir ortamda çalışıyorsanız, yollar, dosya uzantıları ve yol ayırıcıları uygun şekilde ayarlayın.

### <a name="create-new-azure-resources"></a>Yeni Azure kaynakları oluşturma

Gerekli Azure kaynaklarını henüz oluşturmadıysanız, şu adımları izleyin:

1. Komut satırı ortamınızda, deponun kopyalanmış kopyasında **\services\scripts\local\launch** klasörüne gidin.

1. **PC** CLI aracını yüklemek ve Azure hesabınızda oturum açmak için aşağıdaki komutları çalıştırın:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. **Start. cmd** betiğini çalıştırın. Komut dosyası aşağıdaki bilgileri ister:

   * Bir çözüm adı.
   * Kullanılacak Azure aboneliği.
   * Kullanılacak Azure veri merkezinin konumu.

   Betik, Azure 'da çözüm adınızı içeren bir kaynak grubu oluşturur. Bu kaynak grubu, çözüm hızlandırıcının kullandığı Azure kaynaklarını içerir. Bu kaynak grubunu, ilgili kaynaklara artık gerek kalmadığında silebilirsiniz.

   Betik Ayrıca yerel makinenize bir ortam değişkenleri kümesi de ekler. Her değişken adının önek **bilgisayarları**vardır. Bu ortam değişkenleri, uzaktan Izlemenin bir Azure Key Vault kaynağından yapılandırma değerlerini okumasına izin veren ayrıntılar sağlar.

   > [!TIP]
   > Komut dosyası tamamlandığında, ortam değişkenlerini **\<giriş klasörünüzün\>\\. pcs\\\<çözüm adı\>. env**adlı bir dosyaya kaydeder. Bunları gelecekteki çözüm hızlandırıcılarına yönelik olarak kullanabilirsiniz. Yerel makinenizde ayarlanan tüm ortam değişkenlerinin, **Docker-Compose**çalıştırdığınızda **Yerel\\. env dosyası\\hizmetler\\betiklerdeki** değerleri geçersiz kılmasını unutmayın.

1. Komut satırı ortamınızı kapatın.

### <a name="use-existing-azure-resources"></a>Mevcut Azure kaynaklarını kullanma

Gerekli Azure kaynaklarını zaten oluşturduysanız, yerel makinenizde karşılık gelen ortam değişkenlerini ayarlayın:
* **PCS_KEYVAULT_NAME**: Key Vault kaynağının adı.
* **PCS_AAD_APPID**: Azure Active Directory (Azure AD) uygulama kimliği.
* **PCS_AAD_APPSECRET**: Azure AD uygulama gizli anahtarı.

Yapılandırma değerleri bu Key Vault kaynağından okunacaktır. Bu ortam değişkenleri, **\\. pcs\\\<çözüm adı\>. env dosyası\<\>giriş klasörünüze** kaydedilebilir. Yerel makinenizde ayarlanan ortam değişkenlerinin, **Docker-Compose**çalıştırdığınızda **Yerel\\. env dosyası\\hizmetler\\betiklerdeki** değerleri geçersiz kılmasını unutmayın.

Mikro hizmet tarafından gereken bazı yapılandırmalar ilk dağıtımda oluşturulmuş bir Key Vault örneğine depolanır. Anahtar kasasındaki ilgili değişkenler gerektiği şekilde değiştirilmelidir.

## <a name="run-the-microservices"></a>Mikro hizmetleri çalıştırma

Bu bölümde, uzaktan Izleme mikro hizmetlerini çalıştırırsınız. Şunu çalıştırırsınız:

* Web Kullanıcı arabirimi yerel olarak.
* Docker 'da Azure IoT cihaz benzetimi, kimlik doğrulama ve Azure Stream Analytics Manager hizmetleri.
* IntelliJ 'deki mikro hizmetler.

### <a name="run-the-device-simulation-service"></a>Cihaz simülasyon hizmetini çalıştırma

Yeni bir komut Istemi penceresi açın. Önceki bölümde **Start. cmd** betiği tarafından ayarlanan ortam değişkenlerine erişiminiz olup olmadığını denetleyin.

Cihaz benzetimi hizmeti için Docker kapsayıcısını açmak üzere aşağıdaki komutu çalıştırın. Hizmet, uzaktan Izleme çözümü için cihazların benzetimini yapar.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Auth hizmetini çalıştırma

Yeni bir komut Istemi penceresi açın ve ardından kimlik doğrulama hizmeti için Docker kapsayıcısını açmak üzere aşağıdaki komutu çalıştırın. Bu hizmeti kullanarak, Azure IoT çözümlerine erişme yetkisine sahip kullanıcıları yönetebilirsiniz.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Stream Analytics Manager hizmetini çalıştırma

Yeni bir komut Istemi penceresi açın ve sonra Stream Analytics Manager hizmeti için Docker kapsayıcısını açmak üzere aşağıdaki komutu çalıştırın. Bu hizmetle Stream Analytics işleri yönetebilirsiniz. Bu tür bir yönetim, iş yapılandırması ayarlamayı ve iş durumunu başlatmayı, durdurmayı ve izlemeyi içerir.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Tüm diğer mikro hizmetleri yerel makinenize dağıtma

Aşağıdaki adımlarda, IntelliJ 'de uzaktan Izleme mikro hizmetlerinin nasıl çalıştırılacağı gösterilmektedir.

#### <a name="import-a-project"></a>Projeyi içeri aktar

1. IntelliJ IDE 'yi açın.
1. **Projeyi Içeri aktar**' ı seçin.
1. **Azure-iot-PCs-Remote-Monitoring-java\services\build.SBT**seçin.

#### <a name="create-run-configurations"></a>Çalıştırma yapılandırması oluşturma

1. **Çalıştır** > **yapılandırmayı Düzenle**' yi seçin.
1. **Yeni yapılandırma** > **SBT görevi**' ni seçin.
1. **Ad**girin ve ardından **görevleri** **çalıştırma**olarak girin.
1. Çalıştırmak istediğiniz hizmete göre **çalışma dizinini** seçin.
1. Seçimlerinizi kaydetmek için **uygula** > **Tamam ' ı** seçin.
1. Aşağıdaki Web Hizmetleri için çalıştırma yapılandırması oluşturun:
    * WebService (services\config)
    * WebService (services\device-telemetri)
    * WebService (services\iothub-Manager)
    * WebService (services\storage-Adapter)

Örnek olarak, aşağıdaki görüntüde bir hizmet için nasıl yapılandırma ekleneceği gösterilmektedir:

[Sol bölmedeki SBT görevleri listesinde vurgulanan storageAdapter seçeneğini ve sağ bölmedeki ad, görevler, çalışma dizini ve VM parametreleri kutularındaki girdileri gösteren, IntelliJ IDE Run/Debug Configurations penceresinin ekran görüntüsünü ![.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Bileşik yapılandırma oluşturma

1. Tüm Hizmetleri birlikte çalıştırmak için **bileşik** > **Yeni yapılandırma Ekle** ' yi seçin.
1. **Ad**girin ve ardından **SBT görevleri ekle**' yi seçin.
1. Seçimlerinizi kaydetmek için **uygula** > **Tamam ' ı** seçin.

Örnek olarak, aşağıdaki görüntüde tüm SBT görevlerinin tek bir yapılandırmaya nasıl ekleneceği gösterilmektedir:

[Sol bölmedeki Birleşik listede ve sağ bölmede vurgulanan SBT görevi ' Devicetelemetri ' seçeneğinde bulunan AllServices seçeneğini gösteren IntelliJ IDE Run/Debug Configurations penceresinin ekran görüntüsünü ![.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Yerel makinede Web hizmetlerini derlemek ve çalıştırmak için **Çalıştır** ' ı seçin.

Her Web hizmeti bir komut Istemi penceresi ve Web tarayıcısı penceresi açar. Komut isteminde, çalışan hizmetten alınan çıktıyı görürsünüz. Tarayıcı penceresi, durumu izlemenize olanak sağlar. Komut Istemi pencerelerini veya Web sayfalarını kapatmayın, çünkü bu eylemler Web hizmetini durdurur.

Hizmetlerin durumuna erişmek için aşağıdaki URL 'Lere gidin:

* IoT-Hub Yöneticisi: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Cihaz telemetrisi: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Yapılandırma: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* depolama-bağdaştırıcı: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Stream Analytics işini Başlat

Stream Analytics işini başlatmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) gidin.
1. Çözümünüz için oluşturulan **kaynak grubuna** gidin. Kaynak grubunun adı, **Start. cmd** betiğini çalıştırdığınızda çözümünüz için seçtiğiniz addır.
1. Kaynak listesinden **Stream Analytics işi** seçin.
1. Stream Analytics iş **genel bakış** sayfasında **Başlat** düğmesini seçin ve ardından işi başlatmak için **Başlat** ' ı seçin.

### <a name="run-the-web-ui"></a>Web Kullanıcı arabirimini çalıştırma

Bu adımda, Web Kullanıcı arabirimini başlatın. Yeni bir komut Istemi penceresi açın. **Start. cmd** betiği tarafından ayarlanan ortam değişkenlerine erişiminiz olup olmadığını denetleyin. Deponun yerel kopyasında **webui** klasörüne gidin ve aşağıdaki komutları çalıştırın:

```cmd
npm install
npm start
```

**Başlat** komutu tamamlandığında, tarayıcınız sayfayı [http://localhost:3000/dashboard](http://localhost:3000/dashboard)adreste görüntüler. Bu sayfadaki hatalar beklenmektedir. Uygulamayı hata olmadan görüntülemek için aşağıdaki adımları izleyin.

### <a name="configure-and-run-nginx"></a>NGINX 'i yapılandırma ve çalıştırma

Web uygulamasını yerel makinenizde çalışan mikro hizmetlere bağlayan bir ters proxy sunucusu ayarlayın:

1. **NGINX. conf** dosyasını deponun yerel kopyasında bulunan **webui\scripts\localhost** klasöründen **nginx\conf** yükleme dizinine kopyalayın.
1. NGINX 'i çalıştırın.

NGINX çalıştırma hakkında daha fazla bilgi için bkz. [Windows için NGINX](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Panoya Bağlan

Uzaktan Izleme çözümü panosuna erişmek için tarayıcınızda http://localhost:9000 gidin.

## <a name="clean-up"></a>Temizleme

Gereksiz ücretlerden kaçınmak için, testinizi tamamladıktan sonra bulut hizmetlerini Azure aboneliğinizden kaldırın. Hizmetleri kaldırmak için [Azure Portal](https://ms.portal.azure.com)gidin ve **Start. cmd** betiğinin oluşturulduğu kaynak grubunu silin.

Ayrıca, kaynak kodu GitHub 'dan Klonladığınız sırada oluşturulan uzaktan Izleme deposunun yerel kopyasını da silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uzaktan Izleme çözümünü dağıttığınıza göre, bir sonraki adım [çözüm panosunun yeteneklerini araştırmakta](quickstart-remote-monitoring-deploy.md).
