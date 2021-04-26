---
title: Öğretici-IoT Edge cihaz hiyerarşisi oluşturma-Azure IoT Edge
description: Bu öğreticide, ağ geçitlerini kullanarak IoT Edge cihazların hiyerarşik yapısını nasıl oluşturacağınız gösterilmektedir.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728509"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Öğretici: IoT Edge cihaz hiyerarşisi oluşturma

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Hiyerarşik katmanlarda düzenlenmiş ağlarda Azure IoT Edge düğümleri dağıtın. Hiyerarşideki her katman, altındaki katmandaki cihazlardan gelen iletileri ve istekleri işleyen bir ağ geçidi aygıtıdır.

Yalnızca en üst katmanın buluta bağlantısı olması için bir cihaz hiyerarşisi oluşturabilir ve alt katmanlar yalnızca komşu Kuzey ve Güney katmanlarla iletişim kurabilir. Bu ağ katmanlama, [ISA-95 standardını](https://en.wikipedia.org/wiki/ANSI/ISA-95)izleyen en endüstriyel ağların temelidir.

Bu öğreticinin amacı, Basitleştirilmiş bir üretim ortamına benzetim yapan IoT Edge cihazları hiyerarşisi oluşturmaktır. Sonunda, kapsayıcı görüntülerini hiyerarşi aracılığıyla indirerek internet erişimi olmadan daha düşük bir katman cihazına [sanal sıcaklık algılayıcısı modülünü](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) dağıtacaksınız.

Bu öğreticiyi başarmak için, bu öğreticide IoT Edge cihaz hiyerarşisi oluşturma, cihazlarınıza IoT Edge çalışma zamanı kapsayıcıları dağıtma ve cihazlarınızı yerel olarak yapılandırma işlemleri adım adım açıklanmıştır. Bu öğreticide, aşağıdakileri yapmak için bir otomatik yapılandırma aracı kullanacaksınız:

> [!div class="checklist"]
>
> * IoT Edge cihazların hiyerarşisinde ilişkiler oluşturun ve tanımlayın.
> * Hiyerarşinizdeki cihazlarda IoT Edge çalışma zamanını yapılandırın.
> * Cihaz hiyerarşiniz genelinde tutarlı sertifikalar yükler.
> * Hiyerarşinizdeki cihazlara iş yükleri ekleyin.
> * Daha düşük katman cihazlarınızdaki tek bir bağlantı noktası üzerinden HTTP trafiğini güvenli bir şekilde yönlendirmek için [IoT Edge API ara modülünü](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) kullanın.

>[!TIP]
>Bu öğretici, iç içe IoT Edge özelliklerinin bir özetini sağlamak için el ile ve otomatikleştirilmiş adımların bir karışımını içerir.
>
>IoT Edge cihazların hiyerarşisini ayarlamak için tamamen otomatik bir görünüm isterseniz, [endüstriyel IoT örneği için](https://aka.ms/iotedge-nested-sample)betikleştirilmiş Azure IoT Edge izleyebilirsiniz. Bu komut dosyalı senaryo, bir fabrika ortamının benzetimini yapmak için Azure sanal makinelerini önceden yapılandırılmış cihazlar olarak dağıtır.
>
>IoT Edge cihaz hiyerarşisi oluşturma ve yönetme adımları hakkında ayrıntılı bilgi almak istiyorsanız, [IoT Edge cihaz ağ geçidi hiyerarşilerinde nasıl yapılır kılavuzuna](how-to-connect-downstream-iot-edge-device.md)bakın.

Bu öğreticide, aşağıdaki ağ katmanları tanımlanmıştır:

* **Üst katman**: bu katmandaki IoT Edge cihazlar doğrudan buluta bağlanabilir.

* **Alt katmanlar**: üst katmanın altındaki katmanlarda IoT Edge cihaz doğrudan buluta bağlanamaz. Veri göndermek ve almak için bir veya daha fazla ara IoT Edge cihazlarından birine gitmeleri gerekir.

Bu öğretici, aşağıda bulunan basitlik için iki cihaz hiyerarşisi kullanır. Bir cihaz, **üst düzey cihaz**, hiyerarşinin en üst katmanında, doğrudan buluta bağlanabilecek bir cihazı temsil eder. Bu cihaza **ana cihaz** da denir. Diğer cihaz, **alt katman cihazı**, hiyerarşinin alt katmanında bir cihazı temsil eder ve bu da doğrudan buluta bağlanamaz. Gerektiğinde üretim ortamınızı temsil etmek için daha düşük katman cihazları ekleyebilirsiniz. Alt katmanlarda yer alacak cihazlara de **alt cihaz** denir.

![İki cihaz içeren öğretici hiyerarşisinin yapısı: üst katman cihazı ve alt katman cihazı](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Önkoşullar

IoT Edge cihazlarının bir hiyerarşisini oluşturmak için şunlar gerekir:

* İnternet bağlantısı olan bir bilgisayar (Windows veya Linux).
* Geçerli aboneliği olan bir Azure hesabı. [Azure aboneliğiniz](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Azure 'da ücretsiz veya standart bir katman [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) .
* Azure IoT uzantısı v 0.10.6 veya üzeri yüklü olan Azure CLı v 2.3.1 kullanarak Azure Cloud Shell bash kabuğu. Bu öğretici [Azure Cloud Shell](../cloud-shell/overview.md)kullanır. Azure Cloud Shell hakkında bilgi sahibi değilseniz, [Ayrıntılar için hızlı başlangıç ' a bakın](./quickstart-linux.md#prerequisites).
  * Azure CLı modüllerinizin ve uzantılarının geçerli sürümlerini görmek için [az Version](/cli/azure/reference-index?#az_version)' ı çalıştırın.
* Hiyerarşinizdeki her cihaz için IoT Edge bir cihaz olarak yapılandırılacak bir Linux cihazı. Bu öğreticide iki cihaz kullanılmaktadır. Kullanılabilir cihazlar yoksa, aşağıdaki komutu kullanarak hiyerarşinizdeki her bir cihaz için Azure sanal makineleri oluşturabilirsiniz.

   Aşağıdaki komutta yer tutucu metnini değiştirin ve her sanal makine için bir kez olmak üzere iki kez çalıştırın. Her sanal makinenin benzersiz bir DNS öneki olması gerekir ve bu da adı olarak da kullanılır. DNS ön eki aşağıdaki normal ifadeye uymalıdır: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   Sanal makine, kullanıcıların kimliğini doğrulamak için SSH anahtarlarını kullanır. SSH anahtarlarını oluşturma ve kullanma konusunda bilgi sahibi değilseniz, [Azure 'Da Linux sanal makineleri IÇIN SSH genel-özel anahtar çiftleri yönergelerini](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)izleyebilirsiniz.

   IoT Edge sürüm 1,2, bu ARM şablonuyla önceden yüklenir ve varlıkları sanal makinelere el ile yüklemeniz gerekir. IoT Edge kendi cihazlarınıza yüklüyorsanız, bkz. [Linux için Azure IoT Edge yükleme (sürüm 1,2)](how-to-install-iot-edge.md) veya [güncelleştirme IoT Edge sürüm 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Bu ARM şablonunu kullanarak bir sanal makinenin başarılı bir şekilde oluşturulması, sanal makinenizin `SSH` tanıtıcı ve tam etki alanı adı () çıktısını oluşturacak `FQDN` . Sonraki adımlarda yapılandırma için SSH tanıtıcısını ve her bir sanal makinenin FQDN veya IP adresini kullanacaksınız, bu nedenle bu bilgileri izleyin. Örnek bir çıktı aşağıda verilmiştir.

   >[!TIP]
   >Azure portal IP adresini ve FQDN 'yi de bulabilirsiniz. IP adresi için sanal makineler listenize gidin ve **genel IP adresi alanını** aklınızda edin. FQDN için, her bir sanal makinenin genel bakış sayfasına gidin ve **DNS adı** alanını bulun.

   ![Sanal makine, SSH tanıtıcısını içeren oluşturma sonrasında bir JSON çıktısı alacak](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Aşağıdaki bağlantı noktalarının en düşük katman cihazı dışındaki tüm cihazlar için açık olduğundan emin olun: 8000, 443, 5671, 8883:
  * 8000: API proxy 'si aracılığıyla Docker kapsayıcı görüntülerini çekmek için kullanılır.
  * 443: REST API çağrıları için üst ve alt sınır hub 'ları arasında kullanılır.
  * 5671, 8883: AMQP ve MQTT için kullanılır.

  Daha fazla bilgi için bkz. [Azure Portal ile bağlantı noktalarını bir sanal makineye açma](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge cihaz hiyerarşinizi yapılandırma

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge cihaz hiyerarşisi oluşturma

IoT Edge cihazlar hiyerarşinizin katmanlarını yapar. Bu öğreticide, iki IoT Edge cihazın bir hiyerarşisi oluşturulur: **üst katman cihaz** ve alt **Katman cihazı**. Gerektiğinde ek alt aygıtlar oluşturabilirsiniz.

IoT Edge cihazları hiyerarşinizi oluşturmak ve yapılandırmak için `iotedge-config` aracını kullanacaksınız. Bu araç, çeşitli adımları iki şekilde otomatikleştirerek hiyerarşinin yapılandırmasını basitleştirir:

1. Bulut yapılandırmasını ayarlama ve aşağıdakiler dahil olmak üzere her cihaz yapılandırmasını hazırlama:

   * IoT Hub cihaz oluşturma
   * Cihazlar arasındaki iletişime yetki vermek için üst-alt ilişkilerini ayarlama
   * Aralarında güvenli iletişim kurmak için her bir cihaz için bir sertifika zinciri oluşturma
   * Her cihaz için yapılandırma dosyaları oluşturuluyor

1. Aşağıdakiler dahil olmak üzere her bir cihaz yapılandırmasını yükleme:

   * Her cihaza sertifika yükleme
   * Her cihaz için yapılandırma dosyaları uygulanıyor

`iotedge-config`Araç Ayrıca modül dağıtımlarını IoT Edge cihazınıza otomatik olarak yapar.

`iotedge-config`Hiyerarşinizi oluşturmak ve yapılandırmak üzere aracı kullanmak için, Azure CLI 'de aşağıdaki adımları izleyin:

1. [Azure Cloud Shell](https://shell.azure.com/), öğreticinin kaynakları için bir dizin oluşturun:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Yapılandırma aracının ve yapılandırma şablonlarının sürümünü indirin:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Bu işlem, `iotedge_config_cli_release` öğretici dizininizde klasörünü oluşturur.

   Cihaz hiyerarşinizi oluşturmak için kullanılan şablon dosyası, `iotedge_config.yaml` içinde bulunan dosyadır `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . Aynı dizinde, `deploymentLowerLayer.json` **alt katman cihazınıza** hangi modüllerin dağıtılacağı hakkında yönergeler içeren bir JSON dağıtım dosyasıdır. `deploymentTopLayer.json`Her cihaza dağıtılan modüller aynı olmadığından, **en üst katman cihazınız** için dosya aynıdır. `device_config.toml`Dosya, IoT Edge cihaz yapılandırmaları için bir şablondur ve hiyerarşinizdeki cihazların yapılandırma paketlerini otomatik olarak oluşturmak için kullanılır.

   Araç için kaynak koda ve betiklerine bakmak istiyorsanız `iotedge-config` [GitHub 'daki Azure-Samples deposuna](https://github.com/Azure-Samples/iotedge_config_cli)göz atın.

1. Öğretici yapılandırma şablonunu açın ve bilgilerinizi kullanarak düzenleyin:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   **Iothub** bölümünde, `iothub_hostname` ve `iothub_name` alanlarını bilgilerinizi ile doldurun. Bu bilgiler, Azure portal IoT Hub Genel Bakış sayfasında bulunabilir.

   İsteğe bağlı **Sertifikalar** bölümünde, alanları sertifikaya ve anahtarınıza mutlak yollarla doldurabilirsiniz. Bu alanları boş bırakırsanız, komut dosyası, kullanım için otomatik olarak imzalanan test sertifikaları oluşturur. Sertifikaların bir ağ geçidi senaryosunda nasıl kullanıldığı hakkında bilginiz yoksa, [nasıl yapılır kılavuzunun sertifikası bölümüne](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)bakın.

   **Yapılandırma** bölümünde, `template_config_path` `device_config.toml` cihaz yapılandırmalarını oluşturmak için kullanılan şablonun yoludur. `default_edge_agent`Alan, Edge Aracısı görüntüsü alt katman cihazlarının nereye ve nereden çekeceğini belirler.

   **Edgedevices** bölümünde, bir üretim senaryosu için, hiyerarşi ağacını istenen yapınızı yansıtacak şekilde düzenleyebilirsiniz. Bu öğreticinin amaçları doğrultusunda, varsayılan ağacı kabul edin. Her cihaz için `device_id` cihazlarınızın adını girebileceğiniz bir alan vardır. Ayrıca, `deployment` Bu cihaz için BIR JSON dağıtımı yolunu belirten alanı da vardır.

   Ayrıca, Azure portal veya Azure Cloud Shell aracılığıyla IoT Hub IoT Edge cihazları el ile kaydedebilirsiniz. Nasıl yapılacağını öğrenmek için [IoT Edge cihazının nasıl kaydedileceği hakkındaki kılavuza](how-to-register-device.md)bakın.

   Üst-alt ilişkilerini de el ile tanımlayabilirsiniz. Daha fazla bilgi edinmek için nasıl yapılır kılavuzunun [ağ geçidi hiyerarşisi oluşturma](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) bölümüne bakın.

   ![Yapılandırma dosyasının edgedevices bölümü hiyerarşinizi tanımlamanızı sağlar](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Dosyayı kaydedip kapatın:

   `CTRL + S`, `CTRL + Q`

1. Öğretici kaynakları dizininizde yapılandırma paketleri için çıkış dizini oluşturun:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. `iotedge_config_cli_release`Dizininize gidin ve IoT Edge cihazları hiyerarşinizi oluşturmak için aracı çalıştırın:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Bu `--output` bayrak ile araç, seçtiğiniz bir dizinde cihaz sertifikaları, sertifika paketleri ve günlük dosyası oluşturur. `-f`Bayrak ayarlandığında araç, IoT Hub var olan IoT Edge cihazları otomatik olarak arayacaktır ve hataları ortadan kaldırarak hub 'ınızı temiz tutmaya devam eder.

   Yapılandırma Aracı, IoT Edge cihazlarınızı oluşturur ve aralarında üst-alt ilişkileri ayarlar. İsteğe bağlı olarak, cihazlarınız için kullanılacak sertifikaları oluşturur. Dağıtım Jdönemleri için yollar sağlanmışsa, araç otomatik olarak bu dağıtımları cihazlarınıza oluşturur, ancak bu gerekli değildir. Son olarak, araç cihazlarınız için yapılandırma paketleri oluşturur ve bunları çıkış dizinine yerleştirir. Yapılandırma aracı tarafından alınan adımlara tam bakış için çıkış dizinindeki günlük dosyasına bakın.

   ![Betik, yürütme sonrasında hiyerarşinizin topolojisini gösterir](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Betikteki topoloji çıkışının doğru olup olmadığını iki kez kontrol edin. Hiyerarşinizin doğru şekilde yapılandırılmış olması durumunda, devam etmeye hazırlanın.

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yapılandırma

Yapılandırma adımları, cihazlarınızın sağlanmasına ek olarak, daha önce oluşturduğunuz sertifikaları kullanarak hiyerarşinizdeki cihazlar arasında güvenilen iletişim kurar. Adımlar, hiyerarşinizin ağ yapısını oluşturmaya da başlar. En üst katman cihaz internet bağlantısını koruyacak ve bu, alt katman cihazları bu görüntülere erişmek için üst katman cihazından yönlendirilecektir.

IoT Edge çalışma zamanını yapılandırmak için, kurulum betiği tarafından oluşturulan yapılandırma paketlerini cihazlarınıza uygulamanız gerekir. Yapılandırmalar **en üst katman cihazı** ve **daha düşük bir katman cihazı** arasında biraz farklılık gösterir, bu nedenle her cihaza hangi cihazın yapılandırma dosyasını uygulayacaksanız.

1. Her cihazda karşılık gelen yapılandırma paketi gerekir. Yapılandırma paketlerini her bir cihaza taşımak için bir USB sürücü veya [güvenli dosya kopyalama](https://www.ssh.com/ssh/scp/) kullanabilirsiniz.

   Her cihaza doğru yapılandırma paketini gönderdiğinizden emin olun.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   , `:~` Yapılandırma klasörünün sanal makinedeki giriş dizinine yerleştirileceği anlamına gelir.

1. Yapılandırma paketini cihaza uygulamak için sanal makinenizde oturum açın:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Her cihazda, yapılandırma paketinin sıkıştırmasını açın. Önce zip 'i yüklemeniz gerekir:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Her cihazda yapılandırma paketini cihaza uygulayın:

   ```bash
   sudo ./install.sh
   ```

   **Üst katman cihazında**, ana bilgisayar adını girmeniz için bir istem alacaksınız. **Alt katman cihazında**, ana bilgisayar adı ve üst öğenin ana bilgisayar adı istenir. Her istem için uygun IP veya FQDN sağlayın. Her birini kullanabilir, ancak istediğiniz cihazlarda tutarlı bir şekilde yararlanabilirsiniz. Install betiğinin çıktısı aşağıda verilmiştir.

   Cihazınızın yapılandırma dosyasında yapılan değişiklikler hakkında daha yakından bakmak isterseniz, [nasıl yapılır kılavuzundaki cihazlara IoT Edge yapılandırma bölümüne](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)bakın.

  ![Yapılandırma paketleri yüklendiğinde, cihazınızdaki config. TOML dosyaları güncelleştirilecek ve tüm IoT Edge Hizmetleri otomatik olarak yeniden başlatılacak](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Yukarıdaki adımları doğru şekilde tamamladıysanız, cihazlarınızın doğru şekilde yapılandırıldığını kontrol edebilirsiniz.

Cihazlarınızda yapılandırma ve bağlantı denetimlerini çalıştırın. **Üst katman cihaz** için:

   ```bash
   sudo iotedge check
   ```

**Alt katman cihazında**, tanılama görüntüsünün komutuna el ile geçirilmesi gerekir:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

**Üst katman cihazınızda**, birkaç geçirme değerlendirmesi içeren bir çıktı görmeniz beklenir. Günlük ilkeleri hakkında, ağınıza ve DNS ilkelerinize bağlı olarak bazı uyarılar görebilirsiniz.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Yapılandırmaların her cihazda doğru olduğundan emin olduktan sonra devam etmeye hazırlanın.

## <a name="deploy-modules-to-your-devices"></a>Cihazlarınızdan modüller dağıtma

Cihazlarınıza yönelik modül dağıtımları, cihazlar oluşturulduğunda otomatik olarak oluşturulmuştur. Araç, oluşturulduktan `iotedge-config-cli` sonra **üst ve alt katman cihazları** Için dağıtım jdönemleri beslenir. Her cihazda IoT Edge çalışma zamanı yapılandırılırken modül dağıtımı beklemede. Çalışma zamanını yapılandırdıktan sonra, **üst katman cihazına** olan dağıtımlar başlamıştır. Bu dağıtımlar tamamlandıktan sonra, **alt katman cihaz** , gerekli görüntülerini çekmek IÇIN **IoT Edge API ara sunucu** modülünü kullanabilir.

[Azure Cloud Shell](https://shell.azure.com/), cihazınıza hangi modüllerin dağıtıldığını anlamak için **en üst katman cihazının** Deployment JSON ' a göz atabilirsiniz:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Çalışma zamanı modüllerini aracı ve **IoT Edge Hub** **IoT Edge** Ayrıca, **üst katman cihaz** , **DOCKER kayıt defteri** modülünü ve **IoT Edge API ara sunucu** modülünü alır.

**Docker kayıt defteri** modülü mevcut bir Azure Container Registry gösterir. Bu durumda, `REGISTRY_PROXY_REMOTEURL` Microsoft Container Registry işaret eder. İçinde `createOptions` , 5000 bağlantı noktası üzerinden iletişim kurar.

**IoT Edge API proxy** modülü, daha düşük katman cihazların kapsayıcı görüntülerini çekmesini veya depolama birimine gönderim yapmasına izin vermek için http isteklerini diğer modüllere yönlendirir. Bu öğreticide bağlantı noktası 8000 ' de iletişim kurar ve Docker kapsayıcı görüntüsü çekme istekleri yönlendirmesi için bağlantı noktası 5000 ' de **Docker kayıt defteri** modülünüzün yolunu gönderecek şekilde yapılandırılmıştır. Ayrıca, herhangi bir blob Storage karşıya yükleme isteği 11002 numaralı bağlantı noktasındaki AzureBlobStorageonIoTEdge modülüne yönlendirir. **IoT Edge API proxy** modülü ve nasıl yapılandırılacağı hakkında daha fazla bilgi için modülün [nasıl yapılır kılavuzuna](how-to-configure-api-proxy-module.md)bakın.

Azure portal veya Azure Cloud Shell aracılığıyla bunun gibi bir dağıtım oluşturma hakkında daha fazla bilgi için bkz. [nasıl yapılır kılavuzunun en üst katman cihazı bölümü](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

[Azure Cloud Shell](https://shell.azure.com/), cihazınıza hangi modüllerin dağıtıldığını anlamak için, **alt katman cihazının** dağıtım JSON ' a göz atabilirsiniz:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

`systemModules` **Alt katman cihazının** çalışma zamanı modüllerinin, `$upstream:8000` `mcr.microsoft.com` **en üst katman cihaz** olduğu gibi, yerine üzerinden çekme olarak ayarlandığını görebilirsiniz. **Alt katman cihaz** , görüntüleri buluttan doğrudan çekmesine yönelik bağlantı noktası 8000 ' de **IoT Edge API proxy** modülünü gönderir. **Alt katman cihazına** dağıtılan diğer modül, **sanal sıcaklık algılayıcı** modülü de görüntü isteğini de yapar `$upstream:8000` .

Azure portal veya Azure Cloud Shell aracılığıyla bunun gibi bir dağıtım oluşturma hakkında daha fazla bilgi için bkz. [nasıl yapılır kılavuzunun alt katman cihazı bölümü](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Şu komutu kullanarak modüllerinizin durumunu görüntüleyebilirsiniz:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Bu komut, bildirilen tüm edgeAgent özelliklerini çıktı olarak alır. Cihazın durumunu izlemeye yönelik bazı yararlı bilgiler şunlardır: *çalışma zamanı durumu*, *çalışma zamanı başlangıç zamanı*, *çalışma zamanı son çıkış zamanı*, *çalışma zamanı yeniden başlatma sayısı*.

Ayrıca [Azure Portal](https://ms.portal.azure.com/)modüllerinizin durumunu da görebilirsiniz. Cihazlarınızı ve modüllerinizi görmek için IoT Hub **IoT Edge** bölümüne gidin.

Modül dağıtımlarınızla memnun olduktan sonra devam etmeye hazırlanın.

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Gönderdiğiniz **sanal sıcaklık algılayıcı** modülü örnek ortam verileri oluşturur. Çevresel sıcaklık ve nem, makine sıcaklığı ve basınç ve zaman damgası içeren iletileri gönderir.

Bu iletileri ayrıca [Azure Cloud Shell](https://shell.azure.com/)aracılığıyla da görüntüleyebilirsiniz:

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Sorun giderme

`iotedge check`Yapılandırmayı doğrulamak ve hataları gidermek için komutunu çalıştırın.

`iotedge check`Alt makinelerin doğrudan internet erişimi olmasa bile, iç içe bir hiyerarşide çalıştırabilirsiniz.

`iotedge check`Alt katmandan çalıştırdığınızda program, görüntüyü üst öğeden 443 numaralı bağlantı noktası üzerinden çekmenize çalışır.

Bu öğreticide bağlantı noktası 8000 ' i kullanıyoruz, bu nedenle Şunu belirtmemiz gerekir:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

`azureiotedge-diagnostics`Değer, kayıt defteri modülüyle bağlantılı kapsayıcı kayıt defterinden çekilir. Bu öğretici, varsayılan olarak şu şekilde ayarlanmıştır https://mcr.microsoft.com:

| Name | Değer |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Özel bir kapsayıcı kayıt defteri kullanıyorsanız, tüm görüntülerin (ıotedgeapiproxy, edgeAgent, edgeHub, sanal sıcaklık algılayıcısı ve Tanılamalar) kapsayıcı kayıt defterinde bulunduğundan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ücretlerden kaçınmak için bu makalede oluşturduğunuz yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

Kaynakları silmek için:

1. [Azure portalda](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

2. IoT Edge test kaynaklarınızı içeren kaynak grubunun adını seçin. 

3. Kaynak grubunuzda bulunan kaynak listesini gözden geçirin. Tümünü silmek isterseniz **Kaynak grubunu sil**'i seçebilirsiniz. Kaynakların yalnızca bazılarını silmek istiyorsanız tek tek tıklayarak silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, iki IoT Edge cihazı ağ geçidi olarak yapılandırdığınıza ve bir tane diğerinin üst aygıtı olarak ayarlayaöğreneceksiniz. Daha sonra, IoT Edge API ara modülünü kullanarak bir ağ geçidi aracılığıyla bir kapsayıcı görüntüsünü alt cihaza çekolursunuz. Daha fazla bilgi edinmek istiyorsanız [proxy modülünün kullanımıyla ilgili nasıl yapılır Kılavuzu '](how-to-configure-api-proxy-module.md) na bakın.

IoT Edge cihazların hiyerarşik katmanlarını oluşturmak için ağ geçitlerini kullanma hakkında daha fazla bilgi için bkz. yukarı [akış IoT Edge cihazlarını bağlama hakkında nasıl yapılır Kılavuzu](how-to-connect-downstream-iot-edge-device.md).

Azure IoT Edge sisteminin işletmeniz için oluşturabileceği ek çözümleri görmek için diğer öğreticilere geçin.

> [!div class="nextstepaction"]
> [Bir Azure Machine Learning modelini modül olarak dağıtma](tutorial-deploy-machine-learning.md)
