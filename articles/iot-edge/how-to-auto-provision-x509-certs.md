---
title: X. 509.440 sertifikalarını kullanarak DPS cihazları otomatik olarak Sağlama-Azure IoT Edge | Microsoft Docs
description: Cihaz sağlama hizmeti ile Azure IoT Edge için otomatik cihaz sağlamayı test etmek üzere X. 509.440 sertifikalarını kullanın
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481965"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X. 509.440 sertifikalarını kullanarak bir IoT Edge cihazı oluşturma ve sağlama

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

[Azure IoT Hub cihaz sağlama hizmeti (DPS)](../iot-dps/index.yml)Ile, X. 509.440 sertifikalarını kullanarak IoT Edge cihazları otomatik olarak sağlayabilirsiniz. Otomatik sağlama işlemini tanımıyorsanız, devam etmeden önce [sağlamaya](../iot-dps/about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin.

Bu makalede, aşağıdaki adımlarla bir IoT Edge cihazında X. 509.440 sertifikalarını kullanarak bir cihaz sağlama hizmeti kaydı oluşturma işlemi gösterilmektedir:

* Sertifikalar ve anahtarlar oluşturun.
* Bir cihaz için tek bir kayıt veya bir dizi cihaz için Grup kaydı oluşturun.
* IoT Edge çalışma zamanını yükleyip cihazı IoT Hub kaydedin.

Bir kanıtlama mekanizması olarak X. 509.440 sertifikalarını kullanmak, üretimi ölçeklendirmek ve cihaz sağlamayı basitleştirmek için mükemmel bir yoldur. Genellikle, X. 509.440 sertifikaları bir sertifika güven zincirinde düzenlenir. Otomatik olarak imzalanan veya güvenilen bir kök sertifikayla başlayarak, zincirdeki her bir sertifika sonraki alt sertifikayı imzalar. Bu model, bir cihazda yüklü olan son "yaprak" sertifikaya, her ara sertifika aracılığıyla kök sertifikadan bir güven zinciri oluşturur.

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir IoT Hub.
* IoT Edge cihaz olması için fiziksel veya sanal bir cihaz.
* [Git](https://git-scm.com/download/) 'in en son sürümü yüklendi.
* Azure 'da IoT Hub 'ınıza bağlı IoT Hub cihaz sağlama hizmeti örneği.
  * Cihaz sağlama hizmeti örneğiniz yoksa [IoT Hub DPS 'Yi ayarlama](../iot-dps/quick-setup-auto-provision.md)bölümündeki yönergeleri izleyin.
  * Cihaz sağlama hizmetini çalıştırdıktan sonra, genel bakış sayfasından **kimlik kapsamının** değerini kopyalayın. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız.

## <a name="generate-device-identity-certificates"></a>Cihaz kimlik sertifikaları oluşturma

Cihaz kimlik sertifikası, en üstteki X. 509.440 sertifika yetkilisi (CA) sertifikasına bir sertifika güven zinciri üzerinden bağlanan bir yaprak sertifikadır. Cihaz kimliği sertifikası, cihazın IoT Hub 'ınızda olmasını istediğiniz cihaz KIMLIĞINE ayarlanmış olan ortak adı (CN) olmalıdır.

Cihaz kimlik sertifikaları yalnızca IoT Edge cihazı sağlamak ve cihazın Azure IoT Hub kimlik doğrulamasını yapmak için kullanılır. IoT Edge cihazın, doğrulama için modüllerde veya yaprak cihazlarda sundukları CA sertifikalarının aksine, sertifikaları imzalarlar. Daha fazla bilgi için bkz. [Azure IoT Edge sertifika kullanımı ayrıntısı](iot-edge-certs.md).

Cihaz kimliği sertifikasını oluşturduktan sonra iki dosyanız olmalıdır: sertifikanın ortak bölümünü içeren bir. cer veya. ped dosyası ve sertifikanın özel anahtarıyla bir. cer veya. PEA dosyası. DPS 'de grup kaydı kullanmayı planlıyorsanız, aynı sertifika güven zincirindeki bir ara veya kök CA sertifikasının genel bölümüne de ihtiyacınız vardır.

X. 509.952 ile otomatik sağlamayı ayarlamak için aşağıdaki dosyalara ihtiyacınız vardır:

* Cihaz kimliği sertifikası ve onun özel anahtar sertifikası. Tek bir kayıt oluşturursanız cihaz kimliği sertifikası, DPS 'e yüklenir. Özel anahtar IoT Edge çalışma zamanına geçirilir.
* En azından cihaz kimliği ve ara sertifikaları olması gereken tam bir zincir sertifikası. Tam zincir sertifikası IoT Edge çalışma zamanına geçirilir.
* Sertifika güven zincirindeki bir ara veya kök CA sertifikası. Bu sertifika, bir grup kaydı oluşturursanız, DPS 'e yüklenir.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Şu anda libiothsm içindeki bir sınırlama 1 Ocak 2038 tarihinde veya sonrasında sona ermekte olan sertifikaların kullanılmasını engelliyor.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Test sertifikalarını kullan (isteğe bağlı)

Yeni kimlik sertifikaları oluşturmak için kullanabileceğiniz bir sertifika yetkiliniz yoksa ve bu senaryoyu denemek istiyorsanız, Azure IoT Edge git deposu, test sertifikaları oluşturmak için kullanabileceğiniz betikleri içerir. Bu sertifikalar yalnızca geliştirme testi için tasarlanmıştır ve üretimde kullanılmamalıdır.

Test sertifikaları oluşturmak için, [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturma](how-to-create-test-certificates.md)bölümündeki adımları izleyin. Sertifika oluşturma betikleri ayarlamak ve bir kök CA sertifikası oluşturmak için gereken iki bölümü doldurun. Ardından, bir cihaz kimlik sertifikası oluşturma adımlarını izleyin. İşiniz bittiğinde, aşağıdaki sertifika zinciri ve anahtar çiftine sahip olmanız gerekir:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge cihazında bu sertifikaların her ikisi de gereklidir. DPS 'de tek kayıt kullanacaksanız,. cert. ped dosyasını karşıya yüklersiniz. DPS 'de grup kaydı 'nı kullanacaksanız, karşıya yüklemek için aynı sertifika güven zincirindeki bir ara veya kök CA sertifikasına de ihtiyacınız vardır. Tanıtım sertifikaları kullanıyorsanız, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` Grup kaydı için sertifikayı kullanın.

## <a name="create-a-dps-individual-enrollment"></a>DPS bireysel kaydı oluşturma

Tek bir IoT Edge cihaz için DPS 'de tek bir kayıt oluşturmak üzere oluşturulan sertifikalarınızı ve anahtarlarınızı kullanın. Bireysel kayıtlar bir cihazın kimlik sertifikasının genel bölümünü alır ve cihazdaki sertifikayla eşleşir.

Birden çok IoT Edge cihaz sağlamak istiyorsanız, bir sonraki bölümdeki adımları izleyin, [BIR DPS grup kaydı oluşturun](#create-a-dps-group-enrollment).

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu** bildirme fırsatına sahip olursunuz. Device ikizi 'da, bir cihaz için gereken bölge, ortam, konum veya cihaz türü gibi herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-at-scale.md)oluşturmak için kullanılır.

Cihaz sağlama hizmetindeki kayıtlar hakkında daha fazla bilgi için bkz. cihaz kayıtlarını [yönetme](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > Azure CLı 'da bir [kayıt](/cli/azure/iot/dps/enrollment) veya [kayıt grubu](/cli/azure/iot/dps/enrollment-group) oluşturabilir ve **kenar özellikli** bayrağını kullanarak bir cihazın veya cihaz grubunun IoT Edge bir cihaz olduğunu belirtebilirsiniz.

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin.

1. **Ayarlar** altında kayıtları **Yönet**' i seçin.

1. **Bireysel kayıt Ekle** ' yi seçin, ardından kaydı yapılandırmak için aşağıdaki adımları izleyin:  

   * **Mekanizma**: **X. 509.440** öğesini seçin.

   * **Birincil sertifika. pek veya. cer dosyası**: ortak dosyayı cihaz kimliği sertifikasından karşıya yükleyin. Bir test sertifikası oluşturmak için betikleri kullandıysanız aşağıdaki dosyayı seçin:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub CIHAZ kimliği**: isterseniz cihazınız IÇIN bir kimlik sağlayın. Modül dağıtımı için tek bir cihazı hedeflemek üzere cihaz kimliklerini kullanabilirsiniz. Bir cihaz KIMLIĞI sağlamazsanız, X. 509.440 sertifikası içindeki ortak ad (CN) kullanılır.

   * **IoT Edge cihaz**: kaydın IoT Edge bir cihaz için olduğunu bildirmek için **true** seçeneğini belirleyin.

   * **Bu cihazın atanabileceği IoT Hub 'Larını seçin**: Cihazınızı bağlamak Istediğiniz bağlantılı IoT Hub 'ını seçin. Birden çok hub seçebilirsiniz ve bu cihaz, seçilen ayırma ilkesine göre bu cihazdan birine atanır.

   * **Ilk cihaz Ikizi durumu**: isterseniz, cihaza ikizi eklenecek bir etiket değeri ekleyin. Otomatik dağıtım için cihaz gruplarını hedeflemek üzere etiketleri kullanabilirsiniz. Örnek:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **Kaydet**’i seçin.

Bu cihaz için bir kayıt mevcut olduğuna göre, IoT Edge çalışma zamanı cihazı yükleme sırasında otomatik olarak sağlayabilir. IoT Edge cihazınızı ayarlamak için [IoT Edge çalışma zamanı 'Nı yüklemeye](#install-the-iot-edge-runtime) devam edin.

## <a name="create-a-dps-group-enrollment"></a>Bir DPS grup kaydı oluşturma

Birden çok IoT Edge cihaz için DPS 'de bir grup kaydı oluşturmak için oluşturulan sertifikalarınızı ve anahtarlarınızı kullanın. Grup kayıtları, tek tek cihaz kimlik sertifikalarını oluşturmak için kullanılan sertifika güven zincirindeki bir ara veya kök CA sertifikası kullanır.

Bunun yerine tek bir IoT Edge cihaz sağlamak istiyorsanız, önceki bölümdeki adımları izleyin, [BIR DPS bireysel kaydı oluşturun](#create-a-dps-individual-enrollment).

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu** bildirme fırsatına sahip olursunuz. Device ikizi 'da, bir cihaz için gereken bölge, ortam, konum veya cihaz türü gibi herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-at-scale.md)oluşturmak için kullanılır.

### <a name="verify-your-root-certificate"></a>Kök sertifikanızı doğrulama

Bir kayıt grubu oluşturduğunuzda, doğrulanmış bir sertifika kullanma seçeneğiniz vardır. Kök sertifikaya sahip olduğunuzu belirterek, bir sertifikayı DPS ile doğrulayabilirsiniz. Daha fazla bilgi için bkz. [X. 509.440 CA sertifikaları Için nasıl sahip olma](../iot-dps/how-to-verify-certificates.md).

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin.

1. Sol taraftaki menüden **Sertifikalar** ' ı seçin.

1. Yeni bir sertifika eklemek için **Ekle** ' yi seçin.

1. Sertifikanız için bir kolay ad girin, sonra X. 509.440 sertifikanızın ortak bölümünü temsil eden. cer veya. ped dosyasına gidin.

   Tanıtım sertifikalarını kullanıyorsanız, sertifikayı karşıya yükleyin `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` .

1. **Kaydet**’i seçin.

1. Sertifikanızın artık **Sertifikalar** sayfasında listelenmesi gerekir. Sertifika ayrıntılarını açmak için seçin.

1. **Doğrulama kodu oluştur** ' u seçin, sonra oluşturulan kodu kopyalayın.

1. Kendi CA sertifikanızı mi getirdiğinizi, ister tanıtım sertifikalarını mı kullandığınızı bağımsız olarak, IoT Edge deposunda sunulan doğrulama aracını kullanarak sahip olma kanıtını doğrulayabilirsiniz. Doğrulama Aracı, konu adı olarak belirtilen doğrulama koduna sahip yeni bir sertifikayı imzalamak için CA sertifikanızı kullanır.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Azure portal sertifika ayrıntıları sayfasında, yeni oluşturulan doğrulama sertifikasını karşıya yükleyin.

1. **Doğrula** seçeneğini belirleyin.

### <a name="create-enrollment-group"></a>Kayıt grubu oluştur

Cihaz sağlama hizmetindeki kayıtlar hakkında daha fazla bilgi için bkz. cihaz kayıtlarını [yönetme](../iot-dps/how-to-manage-enrollments.md).

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin.

1. **Ayarlar** altında kayıtları **Yönet**' i seçin.

1. Kayıt **grubu Ekle** ' yi seçin, ardından kaydı yapılandırmak için aşağıdaki adımları izleyin:

   * **Grup adı**: Bu grup kaydı için hatırlayabileceğiniz bir ad sağlayın.

   * **Kanıtlama türü**: **sertifika** seçin.

   * **IoT Edge cihaz**: **doğru** öğesini seçin. Bir grup kaydı için tüm cihazların IoT Edge cihaz olması veya hiçbirinin olmaması gerekir.

   * **Sertifika türü**: yalnızca bu kayıt için yeni bir dosya yüklemek ISTIYORSANıZ, DPS ile depolanan DOĞRULANMıŞ bir CA sertifikanız varsa **CA Sertifikası** ' nı veya **Ara Sertifika** ' yı seçin.

   * **Birincil sertifika**: son bölümde CA sertifikası ' nı seçerseniz, açılan listeden sertifikanızı seçin. Ara sertifika ' yı seçerseniz, ortak dosyayı cihaz kimlik sertifikalarını oluşturmak için kullanılan sertifika güven zincirindeki bir CA sertifikasından karşıya yükleyin.

   * **Bu cihazın atanabileceği IoT Hub 'Larını seçin**: Cihazınızı bağlamak Istediğiniz bağlantılı IoT Hub 'ını seçin. Birden çok hub seçebilirsiniz ve bu cihaz, seçilen ayırma ilkesine göre bu cihazdan birine atanır.

   * **Ilk cihaz Ikizi durumu**: isterseniz, cihaza ikizi eklenecek bir etiket değeri ekleyin. Otomatik dağıtım için cihaz gruplarını hedeflemek üzere etiketleri kullanabilirsiniz. Örnek:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **Kaydet**’i seçin.

Bu cihaz için bir kayıt mevcut olduğuna göre, IoT Edge çalışma zamanı cihazı yükleme sırasında otomatik olarak sağlayabilir. IoT Edge cihazınızı ayarlamak için sonraki bölüme geçin.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükleme

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Bileşenleri kapsayıcılarda çalıştırılır ve kenarda kod çalıştırabilmeniz için cihaza ek kapsayıcılar dağıtmanıza izin verir.

[Azure IoT Edge çalışma zamanını yüklemek](how-to-install-iot-edge.md)için bu adımları izleyin ve sonra cihazı sağlamak için bu makaleye geri dönün.

DPS ile X. 509.440 sağlama yalnızca IoT Edge Version 1.0.9 veya daha yeni sürümlerde desteklenir.

## <a name="configure-the-device-with-provisioning-information"></a>Cihazı sağlama bilgileriyle yapılandırma

Çalışma zamanı cihazınıza yüklendikten sonra, cihazı cihaz sağlama hizmetine bağlanmak için kullandığı bilgilerle yapılandırın ve IoT Hub.

Aşağıdaki bilgileri hazırlayın:

* DPS **kimlik kapsamı** değeri. Bu değeri, Azure portal DPS örneğinizin genel bakış sayfasından elde edebilirsiniz.
* Cihazdaki cihaz kimliği sertifika zinciri dosyası.
* Cihazdaki cihaz kimliği anahtar dosyası.
* İsteğe bağlı kayıt KIMLIĞI. Sağlanmazsa, KIMLIK, cihaz kimlik sertifikasındaki ortak ad üzerinden alınır.

### <a name="linux-device"></a>Linux cihazı

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge cihazında yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Dosyanın sağlama yapılandırması bölümünü bulun. DPS X. 509.952 sertifika sağlaması için satırların açıklamasını kaldırın ve diğer sağlama satırlarının açıklama olarak belirlendiğinden emin olun.

   `provisioning:`Satırda önünde boşluk olmaması ve iç içe geçmiş öğelerin iki boşluk olması gerekir.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. , Ve değerlerini, `scope_id` `identity_cert` `identity_pk` DPS ve cihaz bilgileriniz ile güncelleştirin.

   X. 509.952 sertifikasını ve anahtar bilgilerini config. YAML dosyasına eklediğinizde, yolların dosya URI 'Leri olarak sağlanması gerekir. Örnek:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. İsteğe bağlı olarak, `registration_id` cihaz için bir sağlar. Aksi takdirde, cihazı kimlik sertifikasının CN adı ile kaydetmek için bu satırı açıklama olarak bırakın.

1. İsteğe bağlı olarak, `always_reprovision_on_startup` `dynamic_reprovisioning` cihazınızın yeniden sağlama davranışını yapılandırmak için veya satırını kullanın. Bir cihaz başlangıçta yeniden sağlamak üzere ayarlandıysa, her zaman önce DPS ile sağlamayı dener ve ardından bu başarısız olursa sağlama yedeklemesine geri dönecektir. Bir cihaz kendisini dinamik olarak yeniden sağlamak üzere ayarlandıysa, yeniden sağlama olayı algılandığında IoT Edge yeniden başlatılır ve yeniden hazırlar. Daha fazla bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Config. YAML dosyasını kaydedin ve kapatın.

1. Cihazda yaptığınız tüm yapılandırma değişikliklerini alması için IoT Edge çalışma zamanını yeniden başlatın.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge yüklemesinin bir parçası olarak sağlanmış bir şablon dosyasını temel alan cihazınız için bir yapılandırma dosyası oluşturun.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge cihazında yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Dosyanın **sağlama** bölümünü bulun. X. 509.440 sertifikasıyla DPS sağlama satırlarının açıklamalarını kaldırın ve diğer sağlama satırlarının açıklama olarak belirlendiğinden emin olun.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. , Ve değerlerini, `id_scope` `identity_cert` `identity_pk` DPS ve cihaz bilgileriniz ile güncelleştirin.

   Kimlik sertifikası değeri bir dosya URI 'SI olarak verilebilir veya EST veya yerel bir sertifika yetkilisi kullanılarak dinamik olarak verilebilir. Kullanmayı seçtiğiniz biçime göre yalnızca bir satırın açıklamasını kaldırın.

   Kimlik özel anahtar değeri bir dosya URI 'SI veya PKCS # 11 URI 'SI olarak belirtilebilir. Kullanmayı seçtiğiniz biçime göre yalnızca bir satırın açıklamasını kaldırın.

   Herhangi bir PKCS # 11 URI kullanırsanız, yapılandırma dosyasında **PKCS # 11** bölümünü bulun ve PKCS # 11 yapılandırmanız hakkında bilgi sağlayın.

1. İsteğe bağlı olarak, `registration_id` cihaz için bir sağlar. Aksi takdirde, kimlik sertifikasının ortak adı ile cihazı kaydetmek için bu satırı açıklama olarak bırakın.

1. Dosyayı kaydedin ve kapatın.

1. IoT Edge yaptığınız yapılandırma değişikliklerini uygulayın.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows cihazı

1. Yönetici modunda bir PowerShell penceresi açın. PowerShell (x86) değil IoT Edge yüklerken PowerShell 'in AMD64 oturumunu kullandığınızdan emin olun.

1. **Initialize-ıotedge** komutu, makinenizde IoT Edge çalışma zamanını yapılandırır. Komut, Windows kapsayıcılarıyla el ile sağlama yapmak için varsayılan olarak, `-DpsX509` X. 509.952 sertifika kimlik doğrulamasıyla otomatik sağlamayı kullanmak için bayrağını kullanın.

   ,, Ve için yer tutucu değerlerini, `{scope_id}` `{identity cert chain path}` `{identity key path}` DPS örneğinizin ve cihazınızdaki dosya yollarından uygun değerlerle değiştirin.

   `-RegistrationId {registration_id}`CIHAZ kimliğini kimlik SERTIFIKASıNıN CN adı dışında bir şey olarak ayarlamak istiyorsanız öğesini ekleyin.

   `-ContainerOs Linux`Windows üzerinde Linux kapsayıcıları kullanıyorsanız, parametresini ekleyin.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Yapılandırma dosyası, sertifikanızı ve anahtar bilgilerinizi dosya URI 'Leri olarak depolar. Ancak, Initialize-IoTEdge komutu bu biçimlendirme adımını sizin için işler, böylece cihazınızdaki sertifikaya ve anahtar dosyalarına mutlak yol sağlayabilirsiniz.

## <a name="verify-successful-installation"></a>Yüklemenin başarılı olduğunu doğrulama

Çalışma zamanı başarıyla başlatıldıysa, IoT Hub ve cihazınıza IoT Edge modülleri dağıtmaya başlayabilirsiniz.

Cihaz sağlama hizmeti 'nde oluşturduğunuz bireysel kaydın kullanıldığını doğrulayabilirsiniz. Azure portal cihaz sağlama hizmeti örneğinize gidin. Oluşturduğunuz bireysel kayıt için kayıt ayrıntılarını açın. Kayıt durumunun **atandığını** ve cihaz kimliğinin listelendiğini unutmayın.

Çalışma zamanının başarıyla yüklendiğini ve başlatıldığını doğrulamak için cihazınızda aşağıdaki komutları kullanın.

### <a name="linux-device"></a>Linux cihazı

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge hizmetinin durumunu kontrol edin.

```cmd/sh
systemctl status iotedge
```

Hizmet günlüklerini inceleyin.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Çalışan modülleri listeleyin.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge hizmetinin durumunu kontrol edin.

```cmd/sh
sudo iotedge system status
```

Hizmet günlüklerini inceleyin.

```cmd/sh
sudo iotedge system logs
```

Çalışan modülleri listeleyin.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Windows cihazı

IoT Edge hizmetinin durumunu kontrol edin.

```powershell
Get-Service iotedge
```

Hizmet günlüklerini inceleyin.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Çalışan modülleri listeleyin.

```powershell
iotedge list
```

## <a name="next-steps"></a>Sonraki adımlar

Cihaz sağlama hizmeti kayıt işlemi, yeni cihazı sağladığınız anda cihaz KIMLIĞI ve cihaz ikizi etiketlerini ayarlamanıza olanak sağlar. Bu değerleri, otomatik cihaz yönetimi kullanarak ayrı cihazları veya cihaz gruplarını hedeflemek için kullanabilirsiniz. Azure portal veya [Azure CLI kullanarak](how-to-deploy-cli-at-scale.md) [IoT Edge modüllerini ölçekte nasıl dağıtacağınızı ve izleyeceğinizi](how-to-deploy-at-scale.md) öğrenin.
