---
title: Aşağı akış IoT Edge cihazları bağlama-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihazı, Azure IoT Edge ağ geçidi cihazlarına bağlanacak şekilde yapılandırma.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484328"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Bir aşağı akış IoT Edge cihazını bir Azure IoT Edge ağ geçidine bağlama

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Bu makalede, bir IoT Edge Ağ Geçidi ile bir aşağı akış IoT Edge cihazı arasında güvenilir bir bağlantı kurmaya yönelik yönergeler sağlanmaktadır.

Bir ağ geçidi senaryosunda, IoT Edge bir cihaz hem ağ geçidi hem de bir aşağı akış cihaz olabilir. Bir cihaz hiyerarşisi oluşturmak için birden çok IoT Edge ağ geçidi katmanlanmış olabilir. Aşağı akış (veya alt) cihazlar, ağ geçidi (veya üst) cihazından ileti doğrulayabilir ve iletileri gönderebilir veya alabilir.

Bir ağ geçidi hiyerarşisinde IoT Edge cihazları için iki farklı yapılandırma vardır ve bu makalede her ikisi de ele alınır. Birincisi, **en üst katman** IoT Edge aygıtıdır. Birden çok IoT Edge cihaz birbirine bağlandığında, üst cihazına sahip olmayan ancak doğrudan IoT Hub ' a bağlanan herhangi bir cihaz en üst katmanda olduğu kabul edilir. Bu cihaz, altındaki tüm cihazlardan gelen istekleri işlemekten sorumludur. Diğer yapılandırma hiyerarşinin **daha düşük bir katmanında** tüm IoT Edge cihazları için geçerlidir. Bu cihazlar, diğer aşağı akış IoT ve IoT Edge cihazları için bir ağ geçidi olabilir, ancak aynı zamanda kendi üst cihazlarından iletişim yönlendirmelidir.

Bazı ağ mimarileri, bir hiyerarşideki yalnızca en iyi IoT Edge cihazının buluta bağlanmasını gerektirir. Bu yapılandırmada, bir hiyerarşinin alt katmanlarındaki tüm IoT Edge cihazlar yalnızca ağ geçidi (veya üst) cihazlarıyla ve herhangi bir aşağı akış (veya alt) cihazla iletişim kurabilir.

Bu makaledeki tüm adımlar, bir IoT Edge cihazını aşağı akış IoT cihazları için bir ağ geçidi olacak şekilde ayarlayan [saydam bir ağ geçidi görevi gören bir IoT Edge cihazını yapılandırma](how-to-create-transparent-gateway.md)' da oluşturur. Tüm Ağ Geçidi senaryoları için aynı temel adımlar geçerlidir:

* **Kimlik doğrulaması**: ağ geçidi hiyerarşisindeki tüm cihazlar için IoT Hub kimlikleri oluşturun.
* **Yetkilendirme**: alt cihazların IoT Hub bağlanacak şekilde üst cihazlarına bağlanmasını sağlamak üzere IoT Hub ' de üst/alt ilişkiyi ayarlayın.
* **Ağ geçidi bulma**: alt cihazın ana cihazını yerel ağda bulabağlanabildiğinden emin olun.
* **Güvenli bağlantı**: aynı zincirin parçası olan güvenilen sertifikalarla güvenli bir bağlantı kurun.

## <a name="prerequisites"></a>Önkoşullar

* Ücretsiz veya standart bir IoT Hub 'ı.
* En az iki **IoT Edge cihaz**, biri en üst katman cihazıdır ve bir veya daha fazla alt katman cihazıdır. Kullanılabilir IoT Edge cihazları yoksa [Ubuntu sanal makinelerinde Azure IoT Edge çalıştırabilirsiniz](how-to-install-iot-edge-ubuntuvm.md).
* Cihazları oluşturmak ve yönetmek için Azure CLı kullanıyorsanız, Azure IoT uzantısı v 0.10.6 veya üzeri yüklü olan Azure CLı v 2.3.1 ' yi kullanın.

Bu makalede, senaryonuza doğru ağ geçidi hiyerarşisini oluşturmanıza yardımcı olacak ayrıntılı adımlar ve seçenekler sağlanmaktadır. Kılavuzlu bir öğretici için bkz. [ağ geçitlerini kullanarak IoT Edge cihazları hiyerarşisi oluşturma](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Ağ Geçidi hiyerarşisi oluşturma

Senaryodaki IoT Edge cihazlar için üst/alt ilişkileri tanımlayarak IoT Edge bir ağ geçidi hiyerarşisi oluşturursunuz. Yeni bir cihaz kimliği oluşturduğunuzda bir üst cihaz ayarlayabilir veya var olan bir cihaz kimliğinin üst ve alt öğelerini yönetebilirsiniz.

Üst/alt öğe ilişkilerini ayarlama adımı, alt cihazların IoT Hub bağlandıkları gibi üst cihazlarına bağlanmasını sağlar.

Yalnızca IoT Edge cihazları üst cihaz olabilir, ancak IoT Edge cihazlar ve IoT cihazları alt öğe olabilir. Bir üst öğe birçok alt öğeye sahip olabilir, ancak bir alt öğe yalnızca bir üst öğeye sahip olabilir. Bir ağ geçidi hiyerarşisi üst/alt öğe kümelerinin birlikte gruplanarak oluşturulur, böylece bir cihaz alt öğesi diğerinin üst öğesidir.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, yeni cihaz kimlikleri oluştururken veya var olan cihazları düzenleyerek üst/alt ilişkiyi yönetebilirsiniz.

Yeni bir IoT Edge cihaz oluşturduğunuzda, bu hub 'daki mevcut IoT Edge cihazların listesinden üst ve alt cihazları seçme seçeneğiniz vardır.

1. [Azure Portal](https://portal.azure.com)IoT Hub 'ınıza gidin.
1. Gezinti menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge cihaz ekle**' yi seçin.
1. Cihaz KIMLIĞI ve kimlik doğrulama ayarlarını ayarlamaya birlikte, **bir üst cihaz ayarlayabilir** veya **alt cihazlar seçebilirsiniz**.
1. Üst veya alt öğe olarak istediğiniz cihaz veya cihazları seçin.

Ayrıca, var olan cihazlar için üst/alt ilişkileri oluşturabilir veya yönetebilirsiniz.

1. [Azure Portal](https://portal.azure.com)IoT Hub 'ınıza gidin.
1. Gezinti menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge cihaz** listesinden yönetmek istediğiniz cihazı seçin.
1. **Bir üst cihaz ayarla** veya **alt cihazları yönet**' i seçin.
1. Üst veya alt cihaz ekleyin veya kaldırın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı için [Azure IoT](/cli/azure/iot) uzantısı, IoT kaynaklarınızı yönetmek için komutlar sağlar. Yeni cihaz kimlikleri oluştururken veya mevcut cihazları düzenleyerek IoT ve IoT Edge cihazlarının üst/alt ilişkilerini yönetebilirsiniz.

[Az IoT Hub cihazı-kimlik](/cli/azure/iot/hub/device-identity) komut kümesi, belirli bir cihaz için üst/alt ilişkileri yönetmenizi sağlar.

Bu `create` komut, alt cihaz ekleme ve cihaz oluşturma sırasında bir üst cihazı ayarlama parametrelerini içerir.

,, Ve veya dahil olmak üzere ek cihaz kimliği komutları, `add-children` `list-children` `remove-children` `get-parent` `set-parent` var olan cihazlar için üst/alt ilişkileri yönetmenizi sağlar.

---

## <a name="prepare-certificates"></a>Sertifikaları hazırlama

Tutarlı bir sertifika zinciri, aralarında güvenli bir iletişim kurmak için aynı ağ geçidi hiyerarşisindeki cihazlar arasında yüklenmelidir. Hiyerarşideki her cihaz, bir IoT Edge cihazının veya IoT yaprak cihazının aynı kök CA sertifikasının bir kopyasına ihtiyacı olsun. Hiyerarşideki her bir IoT Edge cihaz, bu kök CA sertifikasını cihaz CA sertifikası için kök olarak kullanır.

Bu kurulumla, her bir aşağı akış IoT Edge cihaz veya IoT yaprak cihazı, bağlandıkları edgeHub 'ın paylaşılan kök CA sertifikası tarafından imzalanan bir sunucu sertifikasına sahip olduğunu doğrulayarak ebeveynlerinin kimliğini doğrulayabilirler.

<!-- TODO: certificate graphic -->

Aşağıdaki sertifikaları oluşturun:

* Belirli bir ağ geçidi hiyerarşisindeki tüm cihazların en üst paylaşılan sertifikası olan **kök CA sertifikası**. Bu sertifika tüm cihazlara yüklendi.
* Kök sertifika zincirine dahil etmek istediğiniz tüm **Ara sertifikalar** .
* Kök ve ara sertifikalar tarafından oluşturulan bir **CIHAZ CA sertifikası** ve **özel anahtarı**. Ağ Geçidi hiyerarşisindeki her bir IoT Edge cihaz için benzersiz bir cihaz CA sertifikasına ihtiyacınız vardır.

Kendinden imzalı bir sertifika yetkilisini kullanabilir veya Baltimore, Verisign, DigiCert veya GlobalSign gibi güvenilir bir ticari sertifika yetkilisinden satın alabilirsiniz.

Kullanmak üzere kendi sertifikanız yoksa, [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturabilirsiniz](how-to-create-test-certificates.md). Tek bir kök ve ara sertifika kümesi oluşturmak ve ardından cihazlarınızın her biri için IoT Edge cihaz CA sertifikaları oluşturmak için bu makaledeki adımları izleyin.

## <a name="configure-iot-edge-on-devices"></a>Cihazlarda IoT Edge yapılandırma

Bir ağ geçidi olarak IoT Edge ayarlama adımları, bir aşağı akış cihazı olarak IoT Edge ayarlamaya yönelik adımlara çok benzer.

Ağ Geçidi bulmayı etkinleştirmek için, her IoT Edge ağ geçidi cihazının alt cihazlarının yerel ağda bulmak için kullanacağı **ana bilgisayar adı** ile yapılandırılması gerekir. Her aşağı akış IoT Edge cihazının bağlanmak için bir **parent_hostname** yapılandırılması gerekir. Tek bir IoT Edge cihazının hem üst hem de bir alt cihaz olması halinde, her iki parametreye de ihtiyacı vardır.

Güvenli bağlantıları etkinleştirmek için, bir ağ geçidi senaryosunda her IoT Edge cihazının benzersiz bir cihaz CA sertifikasıyla yapılandırılması ve ağ geçidi hiyerarşisindeki tüm cihazlar tarafından paylaşılan kök CA sertifikasının bir kopyası olması gerekir.

Cihazınızda zaten IoT Edge yüklü olmalıdır. Aksi takdirde, [IoT Hub IoT Edge bir cihaz kaydetme](how-to-register-device.md) adımlarını izleyin ve ardından [Azure IoT Edge çalışma zamanını yüklemelisiniz](how-to-install-iot-edge.md).

Bu bölümdeki adımlarda, bu makalede daha önce açıklanan **kök CA sertifikasına** ve **cihaz CA sertifikasına ve özel anahtara** başvuru yapılır. Bu sertifikaları farklı bir cihazda oluşturduysanız, bunların bu cihazda kullanılabilir olmasını sağlayabilirsiniz. Dosyaları fiziksel olarak bir USB sürücüsüyle, [Azure Key Vault](../key-vault/general/overview.md)gibi bir hizmetle veya [güvenli dosya kopyalama](https://www.ssh.com/ssh/scp/)gibi bir işlevle aktarabilirsiniz.

Cihazınızda IoT Edge yapılandırmak için aşağıdaki adımları kullanın.

Kullanıcının **ıotedge** 'ın sertifikaları ve anahtarları tutan dizin için okuma izinlerine sahip olduğundan emin olun.

1. **Kök CA sertifikasını** bu IoT Edge cihaza yükler.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Sertifika deposunu güncelleştirin.

   ```bash
   sudo update-ca-certificates
   ```

   Bu komut,/etc/SSL/certsone öğesine bir sertifikanın eklendiği çıkış olmalıdır.

1. IoT Edge yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Yapılandırma dosyası cihazınızda yoksa, `/etc/aziot/config.toml.edge.template` bir şablon olarak kullanarak bir tane oluşturun.

1. Yapılandırma dosyasında **hostname** bölümünü bulun. Parametresini içeren satırın açıklamasını kaldırın `hostname` ve değeri tam etki alanı adı (FQDN) veya IoT Edge CIHAZıNıN IP adresi olacak şekilde güncelleştirin.

   Bu parametrenin değeri, bu ağ geçidine bağlanmak için hangi aşağı akış cihazlarının kullanılacağını kullanır. Ana bilgisayar adı varsayılan olarak makine adını alır, ancak aşağı akış cihazlarını bağlamak için FQDN veya IP adresi gereklidir.

   Sunucu sertifikası ortak adı için karakter sınırı olan 64 karakterden kısa bir ana bilgisayar adı kullanın.

   Bir ağ geçidi hiyerarşisinde konak adı düzeniyle tutarlı olun. FQDN 'leri veya IP adreslerini kullanın, ancak ikisini birden kullanmayın.

1. *Bu cihaz bir alt cihaz ise*, **ana konak adı** bölümünü bulun. Üst cihazın `parent_hostname` yapılandırma dosyasında ana bilgisayar adı olarak belirtilen şeyle eşleşen üst CIHAZıN FQDN veya IP adresi olacak şekilde parametreyi kaldırın ve güncelleştirin.

1. **Güven paketi sertifikası** bölümünü bulun. `trust_bundle_cert`Dosya URI 'si olan parametreyi, cihazınızdaki kök CA sertifikasına göre açıklama ve güncelleştirme.

1. IoT Edge cihazınızın, başlatıldığında IoT Edge aracısının doğru sürümünü kullanacağınızı doğrulayın.

   **Varsayılan Edge Aracısı** bölümünü bulun ve görüntü değerinin sürüm 1,2 IoT Edge olduğunu doğrulayın. Aksi takdirde, güncelleştirin:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Yapılandırma dosyasında **Edge CA sertifikası** bölümünü bulun. Bu bölümdeki satırların açıklamasını kaldırın ve IoT Edge cihazında sertifika ve anahtar dosyaları için dosya URI 'SI yollarını sağlayın.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. `Ctrl+O`Yapılandırma dosyasını kaydedin () ve `Ctrl+X` kapatın.

1. Daha önce IoT Edge için başka sertifikalar kullandıysanız, yeni sertifikalarınızın uygulandığından emin olmak için aşağıdaki iki dizindeki dosyaları silin:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Yaptığınız değişiklikleri uygulayın.

   ```bash
   sudo iotedge config apply
   ```

1. Yapılandırmadaki hataları denetleyin.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge Denetim Aracı, tanılama denetiminden bazılarını gerçekleştirmek için bir kapsayıcı kullanır. Bu aracı aşağı akış IoT Edge cihazlarda kullanmak istiyorsanız, `mcr.microsoft.com/azureiotedge-diagnostics:latest` özel kapsayıcı kayıt defterinizde kapsayıcı resmine erişebildiklerinden veya sahip olduklarından emin olun.

## <a name="network-isolate-downstream-devices"></a>Ağ yukarı akış cihazlarını yalıtma

Bu makaledeki adımlar, bir ağ geçidi veya bir aşağı akış cihazı olarak IoT Edge cihazları ayarlar ve aralarında güvenilen bir bağlantı oluşturur. Ağ geçidi cihazı, kimlik doğrulama ve ileti yönlendirme de dahil olmak üzere aşağı akış cihazı ve IoT Hub arasındaki etkileşimleri işler. Aşağı akış IoT Edge cihazlara dağıtılan modüller, bulut hizmetlerine kendi bağlantılarını oluşturmaya devam edebilir.

ISA-95 standardını izleenler gibi bazı ağ mimarileri, internet bağlantısı sayısını en aza indirmek için arama yapın. Bu senaryolarda, doğrudan internet bağlantısı olmadan aşağı akış IoT Edge cihazları yapılandırabilirsiniz. Ağ Geçidi cihazlarından yönlendirme IoT Hub iletişimleri ötesinde, aşağı akış IoT Edge cihazları tüm bulut bağlantıları için ağ geçidi cihazına bağlı olabilir.

Bu ağ yapılandırması, yalnızca bir ağ geçidi hiyerarşisinin en üst katmanında bulunan IoT Edge cihazının buluta doğrudan bağlantıları olmasını gerektirir. Daha düşük katmanlardaki IoT Edge cihazlar yalnızca üst cihazlarıyla veya herhangi bir alt cihazla iletişim kurabilir. Ağ Geçidi cihazlarındaki özel modüller, aşağıdakiler dahil olmak üzere bu senaryoyu etkinleştirir:

* **API proxy modülü** , altında başka bir IoT Edge cihazı olan herhangi bir IoT Edge ağ geçidinde gereklidir. Bu, alt katman dışında bir ağ geçidi hiyerarşisinin *her katmanında* olması gerektiği anlamına gelir. Bu modül, HTTP verilerini tek bir bağlantı noktası üzerinden ağ katmanları aracılığıyla yönlendirmek için [NGINX](https://nginx.org) ters proxy kullanır. Bu, kendi modül ikizi ve ortam değişkenleri aracılığıyla yüksek düzeyde yapılandırılabilir, bu nedenle ağ geçidi senaryo gereksinimlerinize uyacak şekilde ayarlanabilir.

* **Docker kayıt defteri modülü** , bir ağ geçidi hiyerarşisinin *en üst katmanında* IoT Edge ağ geçidinde dağıtılabilir. Bu modül, daha düşük katmanlardaki tüm IoT Edge cihazları adına kapsayıcı görüntülerini alma ve önbelleğe alma işleminden sorumludur. Bu modülün en üst katmanda dağıtılmasının alternatifi yerel bir kayıt defteri kullanmak veya kapsayıcı görüntülerini cihazlara el ile yüklemek ve modülün çekme ilkesini **hiçbir zaman** olarak ayarlamanıza olanak sağlar.

* **IoT Edge Azure Blob depolama alanı** , ağ geçidi hiyerarşisinin *en üst katmanında* IoT Edge ağ geçidinde dağıtılabilir. Bu modül, daha düşük katmanlardaki tüm IoT Edge cihazları adına Blobları karşıya yüklemeden sorumludur. Blob 'ları karşıya yükleme özelliği ayrıca modül günlüğü yükleme ve destek paketi karşıya yükleme gibi daha düşük katmanlardaki IoT Edge cihazlar için yararlı sorun giderme işlevleri sağlar.

### <a name="network-configuration"></a>Ağ yapılandırması

Üst katmandaki her ağ geçidi aygıtı için, ağ işleçleri şunlar gerekir:

* Statik bir IP adresi veya tam etki alanı adı (FQDN) sağlayın.
* Bu IP adresinden giden iletişimleri 443 (HTTPS) ve 5671 (AMQP) bağlantı noktaları üzerinden Azure IoT Hub ana bilgisayar adına yetkilendirin.
* Bu IP adresinden giden iletişimleri, bağlantı noktası 443 (HTTPS) üzerinden Azure Container Registry ana bilgisayar adına yetkilendirin.

  API proxy modülü tek seferde yalnızca bir kapsayıcı kayıt defterine yönelik bağlantıları işleyebilir. Özel kapsayıcı kayıt defterinizde saklanan Microsoft Container Registry (mcr.microsoft.com) tarafından sunulan genel görüntüler dahil olmak üzere tüm kapsayıcı görüntülerini kullanmanızı öneririz.

Daha düşük bir katmandaki her ağ geçidi aygıtı için, ağ işleçlerinin şunları yapması gerekir:

* Statik bir IP adresi sağlayın.
* Bu IP adresinden giden iletişimleri 443 (HTTPS) ve 5671 (AMQP) bağlantı noktaları üzerinden üst ağ geçidinin IP adresine yetkilendirin.

### <a name="deploy-modules-to-top-layer-devices"></a>Modülleri üst katman cihazlarına dağıtma

Bir ağ geçidi hiyerarşisinin üst katmanındaki IoT Edge cihaz, cihazda çalıştırdığınız tüm iş yükü modüllerine ek olarak, kendisine dağıtılması gereken gerekli modüller kümesine sahiptir.

API proxy modülü, en yaygın ağ geçidi senaryolarını işleyecek şekilde özelleştirilmek üzere tasarlanmıştır. Bu makale, bir temel yapılandırmada modülleri ayarlamak için ve örneği sağlar. Daha ayrıntılı bilgi ve örnekler için [ağ geçidi hiyerarşiniz IÇIN API proxy modülünü yapılandırma](how-to-configure-api-proxy-module.md) bölümüne bakın.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)IoT Hub 'ınıza gidin.
1. Gezinti menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge cihaz** listesinden yapılandırmakta olduğunuz en üst katman cihazını seçin.
1. **Modül ayarla**' yı seçin.
1. **IoT Edge modüller** bölümünde **Ekle** ' yi ve ardından **Market modülü**' nü seçin.
1. **IoT Edge API ara sunucu** modülünü arayın ve seçin.
1. Dağıtılan modüller listesinden API proxy modülünün adını seçin ve aşağıdaki modül ayarlarını güncelleştirin:
   1. **Ortam değişkenleri** sekmesinde **NGINX_DEFAULT_PORT** değerini olarak güncelleştirin `443` .
   1. **Kapsayıcı oluşturma seçenekleri** sekmesinde bağlantı noktası bağlamalarını 443 başvuru bağlantı noktasına güncelleştirin.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Bu değişiklikler API proxy modülünü 443 numaralı bağlantı noktasında dinlemek üzere yapılandırır. Bağlantı noktası bağlama çakışmalarını engellemek için, edgeHub modülünü 443 numaralı bağlantı noktasında dinlememek üzere yapılandırmanız gerekir. Bunun yerine, API proxy modülü 443 numaralı bağlantı noktasında herhangi bir edgeHub trafiğini yönlendirir.

1. **Çalışma zamanı ayarları** ' nı seçin ve edgeHub modülü oluşturma seçeneklerini bulun. 5671 ve 8883 bağlantı noktaları için bağlamaları bırakarak bağlantı noktası 443 için bağlantı noktası bağlamasını silin.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Çalışma zamanı ayarlarına yaptığınız değişiklikleri kaydetmek için **Kaydet** ' i seçin.
1. Yeniden **Ekle** ' yi ve ardından **IoT Edge modülü**' nü seçin.
1. Dağıtımınıza Docker kayıt defteri modülünü eklemek için aşağıdaki değerleri girin:
   1. **IoT Edge modül adı**: `registry`
   1. **Modül ayarları** sekmesinde, **görüntü URI 'si**:`registry:latest`
   1. **Ortam değişkenleri** sekmesinde, aşağıdaki ortam değişkenlerini ekleyin:

      * **Ad**: `REGISTRY_PROXY_REMOTEURL` **değer**: Bu kayıt defteri modülünün eşleşmesini istediğiniz kapsayıcı kayıt defterinin URL 'si. Örneğin, `https://myregistry.azurecr`.

        Kayıt modülü yalnızca bir kapsayıcı kayıt defterine eşlenir, bu nedenle tüm kapsayıcı görüntülerinin tek bir özel kapsayıcı kayıt defterinde olmasını öneririz.

      * **Ad**: `REGISTRY_PROXY_USERNAME` **değer**: kapsayıcı kayıt defterinin kimliğini doğrulamak için Kullanıcı adı.

      * **Ad**: `REGISTRY_PROXY_PASSWORD` **değer**: kapsayıcı kayıt defterinin kimliğini doğrulamak için parola.

   1. **Kapsayıcı oluşturma seçenekleri** sekmesinde, Yapıştır:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Modülü dağıtıma eklemek için **Ekle** ' yi seçin.
1. Ileri ' yi seçin: sonraki adıma gitmek için **yollar** .
1. Cihazdan buluta iletilerin aşağı akış aygıtlarından IoT Hub ulaşmasını sağlamak için, tüm iletileri IoT Hub ileten bir yol ekleyin. Örnek:
    1. **Ad**: `Route`
    1. **Değer**: `FROM /messages/* INTO $upstream`
1. Son adıma geçmek için **gözden geçir + oluştur** ' u seçin.
1. Cihazınıza dağıtmak için **Oluştur** ' u seçin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/), BIR dağıtım JSON dosyası oluşturun. Örnek:

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

   Bu dağıtım dosyası, 443 bağlantı noktasını dinlemek için API proxy modülünü yapılandırır. Bağlantı noktası bağlama çakışmalarını engellemek için, dosya edgeHub modülünü 443 numaralı bağlantı noktasında dinleme yapmayacak şekilde yapılandırır. Bunun yerine, API proxy modülü 443 numaralı bağlantı noktasında herhangi bir edgeHub trafiğini yönlendirir.

1. IoT Edge cihazına bir dağıtım oluşturmak için aşağıdaki komutu girin:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Modülleri alt katman cihazlarına dağıtın

Bir ağ geçidi hiyerarşisinin alt katmanlarındaki IoT Edge cihazların, cihazda çalıştırdığınız tüm iş yükü modüllerine ek olarak, bunlara dağıtılması gereken bir gerekli modülü vardır.

#### <a name="route-container-image-pulls"></a>Yol kapsayıcı görüntüsü çeker

Ağ Geçidi hiyerarşilerindeki IoT Edge cihazlar için gerekli proxy modülünü tartışmadan önce, daha düşük katmanlardaki cihazların modül görüntülerini nasıl IoT Edge anlamak önemlidir.

Alt katman cihazlarınız buluta bağlanamıyorsa, ancak her zamanki gibi modül görüntülerini çekmesini istiyorsanız, ağ geçidi hiyerarşisinin en üst katman cihazının bu istekleri işleyecek şekilde yapılandırılması gerekir. Üst katman cihazının, kapsayıcı Kayıt defterinize eşlenmiş bir Docker **kayıt defteri** modülünü çalıştırması gerekir. Ardından, kapsayıcı isteklerini bu sunucuya yönlendirmek için API proxy modülünü yapılandırın. Bu ayrıntılar, bu makalenin önceki bölümlerinde ele alınmıştır. Bu yapılandırmada, alt katman cihazların bulut kapsayıcısı kayıt defterlerine, ancak üst katmanda çalışan kayıt defterine işaret etmesi gerekir.

Örneğin, öğesini çağırmak yerine `mcr.microsoft.com/azureiotedge-api-proxy:1.0` alt katman cihazları çağırmalıdır `$upstream:443/azureiotedge-api-proxy:1.0` .

**$Upstream** parametresi, daha düşük bir katman cihazının üst öğesine işaret eder, bu nedenle istek kayıt modülüne bir ara ortam yönlendirme isteği istekleri olan üst katmana ulaşana kadar tüm katmanlarda dolaşacaktır. `:443`Bu örnekteki bağlantı noktası, üst CIHAZDAKI API proxy modülünün dinlediği bağlantı noktası ile değiştirilmelidir.

API proxy modülü yalnızca bir kayıt defteri modülüne yönlendirebilir ve her kayıt defteri modülü yalnızca bir kapsayıcı kayıt defteriyle eşleşmekte olabilir. Bu nedenle, alt katman cihazların çekme için gereken tüm görüntülerin tek bir kapsayıcı kayıt defterinde depolanması gerekir.

Düşük katman cihazların bir ağ geçidi hiyerarşisi aracılığıyla modül çekme istekleri yapmasını istemiyorsanız, başka bir seçenek de yerel kayıt defteri çözümünü yönetmeniz olur. Ya da, dağıtım oluşturmadan önce modül görüntülerini cihazlara gönderin ve ardından **ımagepullpolicy** öğesini **hiçbir** şekilde ayarlayın.

#### <a name="bootstrap-the-iot-edge-agent"></a>IoT Edge aracısını önyükleme

IoT Edge Aracısı, herhangi bir IoT Edge cihazında başlatılacak ilk çalışma zamanı bileşenidir. Tüm aşağı akış IoT Edge cihazların, başlatıldığında edgeAgent modül görüntüsüne erişebilecekleri ve ardından dağıtımlara erişip modül görüntülerinin geri kalanını başladıklarından emin olmanız gerekir.

Kimlik doğrulama bilgilerini, sertifikaları ve ana ana bilgisayar adını sağlamak için bir IoT Edge cihazında yapılandırma dosyasına gittiğinizde, edgeAgent kapsayıcı görüntüsünü de güncelleştirin.

Üst düzey ağ geçidi cihazı kapsayıcı görüntüsü isteklerini işleyecek şekilde yapılandırıldıysa, `mcr.microsoft.com` üst konak adı ve API proxy dinleme bağlantı noktasıyla değiştirin. Dağıtım bildiriminde, `$upstream` bir kısayol olarak kullanabilirsiniz, ancak bu, edgeHub modülünün yönlendirmeyi işlemesini ve bu modülün bu noktada başlatılmadığını gerektirir. Örnek:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Yerel bir kapsayıcı kayıt defteri kullanıyorsanız veya cihazda kapsayıcı görüntülerini el ile sağlıyorsanız, yapılandırma dosyasını uygun şekilde güncelleştirin.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Çalışma zamanını yapılandırma ve proxy modülünü dağıtma

Bulut ve tüm aşağı akış IoT Edge cihazları arasındaki tüm iletişimleri yönlendirmek için **API proxy modülü** gereklidir. Hiyerarşinin alt katmanında, aşağı akış IoT Edge cihazları bulunmayan IoT Edge bir cihaz, bu modüle gerek yoktur.

API proxy modülü, en yaygın ağ geçidi senaryolarını işleyecek şekilde özelleştirilmek üzere tasarlanmıştır. Bu makale, modülleri temel bir yapılandırmada ayarlamaya yönelik adımlara kısaca dokunur. Daha ayrıntılı bilgi ve örnekler için [ağ geçidi hiyerarşiniz IÇIN API proxy modülünü yapılandırma](how-to-configure-api-proxy-module.md) bölümüne bakın.

1. [Azure Portal](https://portal.azure.com)IoT Hub 'ınıza gidin.
1. Gezinti menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge cihaz** listesinden yapılandırmakta olduğunuz alt katman cihazını seçin.
1. **Modül ayarla**' yı seçin.
1. **IoT Edge modüller** bölümünde **Ekle** ' yi ve ardından **Market modülü**' nü seçin.
1. **IoT Edge API ara sunucu** modülünü arayın ve seçin.
1. Dağıtılan modüller listesinden API proxy modülünün adını seçin ve aşağıdaki modül ayarlarını güncelleştirin:
   1. **Modül ayarları** sekmesinde, **görüntü URI 'si** değerini güncelleştirin. `mcr.microsoft.com` yerine `$upstream:443` yazın.
   1. **Ortam değişkenleri** sekmesinde **NGINX_DEFAULT_PORT** değerini olarak güncelleştirin `443` .
   1. **Kapsayıcı oluşturma seçenekleri** sekmesinde bağlantı noktası bağlamalarını 443 başvuru bağlantı noktasına güncelleştirin.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Bu değişiklikler API proxy modülünü 443 numaralı bağlantı noktasında dinlemek üzere yapılandırır. Bağlantı noktası bağlama çakışmalarını engellemek için, edgeHub modülünü 443 numaralı bağlantı noktasında dinlememek üzere yapılandırmanız gerekir. Bunun yerine, API proxy modülü 443 numaralı bağlantı noktasında herhangi bir edgeHub trafiğini yönlendirir.

1. **Çalışma zamanı ayarları**' nı seçin.
1. EdgeHub modülü ayarlarını güncelleştirin:

   1. **Görüntü** alanında `mcr.microsoft.com` ile değiştirin `$upstream:443` .
   1. **Oluşturma seçenekleri** alanında, bağlantı noktası 443 için bağlantı noktası bağlamasını silerek, 5671 ve 8883 bağlantı noktaları için bağlamaları bırakın.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. EdgeAgent modülü ayarlarını güncelleştirin:
   1. **Görüntü** alanında `mcr.microsoft.com` ile değiştirin `$upstream:443` .

1. Çalışma zamanı ayarlarına yaptığınız değişiklikleri kaydetmek için **Kaydet** ' i seçin.
1. Ileri ' yi seçin: sonraki adıma gitmek için **yollar** .
1. Cihazdan buluta iletilerin aşağı akış aygıtlarından IoT Hub ulaşmasını sağlamak için, tüm iletileri öğesine ileten bir yol ekleyin `$upstream` . Yukarı akış parametresi, alt katman cihazları durumunda üst cihaza işaret eder. Örnek:
    1. **Ad**: `Route`
    1. **Değer**: `FROM /messages/* INTO $upstream`
1. Son adıma geçmek için **gözden geçir + oluştur** ' u seçin.
1. Cihazınıza dağıtmak için **Oluştur** ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge cihazını ağ geçidi olarak kullanma](iot-edge-as-gateway.md)

[Ağ Geçidi hiyerarşi senaryonuz için API proxy modülünü yapılandırın](how-to-configure-api-proxy-module.md)