---
title: Cihaz sertifikalarını yönetme-Azure IoT Edge | Microsoft Docs
description: Üretim dağıtımına hazırlanmak için test sertifikaları oluşturun, bunları bir Azure IoT Edge cihazında yükleyip yönetin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3b6bd19d47658e5ad079f0b731cbafc866bb333
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045782"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT Edge cihazda sertifikaları yönetme

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Tüm IoT Edge cihazları çalışma zamanı ile cihazda çalışan modüller arasında güvenli bağlantılar oluşturmak için sertifikaları kullanır. Ağ geçitleri olarak çalışan IoT Edge cihazların, bu sertifikaları da kendi aşağı akış cihazlarına bağlamak için kullanır.

## <a name="install-production-certificates"></a>Üretim sertifikalarını yükleme

IoT Edge ilk yüklediğinizde ve cihazınızı sağladığınızda cihaz, hizmeti test edebilmeniz için geçici sertifikalarla ayarlanır.
Bu geçici sertifikaların süreleri 90 gün içinde doluyor veya makineniz yeniden başlatılarak sıfırlanabilir.
Bir üretim senaryosuna geçtiğinizde veya bir ağ geçidi cihazı oluşturmak istiyorsanız kendi sertifikalarınızı sağlamanız gerekir.
Bu makalede, IoT Edge cihazlarınıza sertifika yüklemek için gereken adımlar gösterilir.

Farklı sertifika türleri ve rolleri hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge sertifikaları nasıl kullandığını anlayın](iot-edge-certs.md).

>[!NOTE]
>Bu makale boyunca kullanılan "kök CA" terimi, IoT çözümünüz için Sertifika zincirinin en üst yetkili ortak sertifikasına başvurur. Bir dağıtılmış sertifika yetkilisinin sertifika kökünü veya kuruluşunuzun sertifika yetkilisinin kökünü kullanmanız gerekmez. Çoğu durumda, aslında bir ara CA genel sertifikasıdır.

### <a name="prerequisites"></a>Önkoşullar

* IoT Edge bir cihaz.

  Ayarlanmış bir IoT Edge cihazınız yoksa bir Azure sanal makinesinde bir tane oluşturabilirsiniz. [Bir sanal Linux cihazı oluşturmak](quickstart-linux.md) veya [bir sanal Windows cihazı oluşturmak](quickstart.md)için hızlı başlangıç makalelerinden birindeki adımları izleyin.

* Bir kök sertifika yetkilisi (CA) sertifikasına sahip veya Baltimore, Verisign, DigiCert veya GlobalSign gibi güvenilir bir ticari sertifika yetkilisinden satın alınmış.

  Henüz bir kök sertifika yetkiliniz yoksa ve üretim sertifikaları gerektiren IoT Edge özellikleri denemek istiyorsanız (Ağ Geçidi senaryoları gibi), [IoT Edge cihaz özelliklerini sınamak için tanıtım sertifikaları oluşturabilirsiniz](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Üretim sertifikaları oluşturma

Aşağıdaki dosyaları oluşturmak için kendi sertifika yetkilinizi kullanmanız gerekir:

* Kök CA
* Cihaz CA sertifikası
* Cihaz CA özel anahtarı

Bu makalede *kök CA 'sı* olarak adlandırdığımız, bir kuruluşun en üst sertifika yetkilisi değil. Bu, IoT Edge hub modülünün, Kullanıcı modüllerinin ve herhangi bir aşağı akış aygıtının birbirleriyle güven sağlamak için kullanacağı IoT Edge senaryoya yönelik en üst sertifika yetkilissudur.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Şu anda libiothsm içindeki bir sınırlama 1 Ocak 2038 tarihinde veya sonrasında sona ermekte olan sertifikaların kullanılmasını engelliyor.

:::moniker-end

Bu sertifikalara bir örnek görmek için, [örnekler ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)bölümünde tanıtım Sertifikaları oluşturan betikleri gözden geçirin.

### <a name="install-certificates-on-the-device"></a>Cihaza sertifika yükler

Sertifika zincirinizi IoT Edge cihaza yükleyip IoT Edge çalışma zamanını yeni sertifikalara başvuracak şekilde yapılandırın.

IoT Edge cihazınıza üç sertifika ve anahtar dosyasını kopyalayın. Sertifika dosyalarını taşımak için [Azure Key Vault](../key-vault/index.yml) veya [Güvenli kopya Protokolü](https://www.ssh.com/ssh/scp/) gibi bir işlev gibi bir hizmet kullanabilirsiniz. Sertifikaları IoT Edge cihazında oluşturduysanız, bu adımı atlayabilir ve çalışma dizininin yolunu kullanabilirsiniz.

Windows üzerinde Linux için IoT Edge kullanıyorsanız, `id_rsa` ana bilgisayar işletim sistemi ve Linux sanal makinesi arasında dosya aktarımlarının kimliğini doğrulamak için Azure IoT Edge dosyasında bulunan SSH anahtarını kullanmanız gerekir. Kimliği doğrulanmış bir SCP 'YI aşağıdaki komutu kullanarak yapabilirsiniz:

   ```powershell-interactive
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

   >[!NOTE]
   >Linux sanal makinesinin IP adresi komut aracılığıyla sorgulanabilir `Get-EflowVmAddr` .

[Tanıtım sertifikaları oluşturmak](how-to-create-test-certificates.md)için örnek betikleri kullandıysanız, aşağıdaki dosyaları IoT-Edge cihazınıza kopyalayın:

* Cihaz CA sertifikası: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Cihaz CA özel anahtarı: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Kök CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge güvenliği Daemon yapılandırma dosyasını açın.

   * Linux ve Windows üzerinde Linux için IoT Edge: `/etc/iotedge/config.yaml`

   * Windows kapsayıcıları kullanan pencereler: `C:\ProgramData\iotedge\config.yaml`

1. Config. YAML içindeki **sertifika** özelliklerini IoT Edge cihazdaki sertifika ve anahtar dosyaları IÇIN dosya URI yolu olarak ayarlayın. `#`Dört satırın açıklamasını kaldırmak için, sertifika özelliklerinden önceki karakteri kaldırın. **Sertifikalarda:** Line 'ın önünde boşluk olmadığından ve iç içe yerleştirilmiş öğelerin iki boşlukla girintilendiğinden emin olun. Örnek:

   * Linux ve Windows üzerinde Linux için IoT Edge:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

   * Windows kapsayıcıları kullanan pencereler:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

1. Linux cihazlarda, kullanıcının **ıotedge** 'in sertifikaları tutan dizin için okuma izinlerine sahip olduğundan emin olun.

1. Daha önce cihazda IoT Edge için başka bir sertifika kullandıysanız, IoT Edge başlatmadan veya yeniden başlatmadan önce aşağıdaki iki dizindeki dosyaları silin:

   * Linux ve Windows üzerinde Linux için IoT Edge: `/var/lib/iotedge/hsm/certs` ve `/var/lib/iotedge/hsm/cert_keys`

   * Windows kapsayıcıları kullanan pencereler: `C:\ProgramData\iotedge\hsm\certs` ve `C:\ProgramData\iotedge\hsm\cert_keys`

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge güvenliği Daemon yapılandırma dosyasını açın: `/etc/aziot/config.toml`

1. `trust_bundle_cert`Dosyanın başlangıcında parametresini bulun. Bu satırın açıklamasını kaldırın ve cihazınızdaki kök CA sertifikasına dosya URI 'SI sağlayın.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. `[edge_ca]`Config. TOML dosyasındaki bölümünü bulun. Bu bölümdeki satırların açıklamasını kaldırın ve IoT Edge cihazında sertifika ve anahtar dosyaları için dosya URI 'SI yollarını sağlayın.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Kullanıcının **ıotedge** 'in sertifikaları tutan dizin için okuma izinlerine sahip olduğundan emin olun.

1. Daha önce cihazda IoT Edge için başka bir sertifika kullandıysanız, IoT Edge başlatmadan veya yeniden başlatmadan önce aşağıdaki iki dizindeki dosyaları silin:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Sertifika ömrünü özelleştirme

IoT Edge, cihaz üzerinde aşağıdakiler dahil olmak üzere çeşitli durumlarda otomatik olarak sertifika üretir:

* IoT Edge yüklerken ve sağladığınızda kendi üretim sertifikalarınızı sağlamazsanız, IoT Edge Güvenlik Yöneticisi otomatik olarak bir **CIHAZ CA sertifikası** oluşturur. Bu otomatik olarak imzalanan sertifika yalnızca geliştirme ve test senaryoları için tasarlanmıştır, üretim değildir. Bu sertifikanın süresi 90 gün sonra doluyor.
* IoT Edge Güvenlik Yöneticisi Ayrıca cihaz CA sertifikası tarafından imzalanan bir **iş yükü CA sertifikası** oluşturur

IoT Edge aygıttaki farklı sertifikaların işlevi hakkında daha fazla bilgi için bkz. [Azure IoT Edge sertifikaları nasıl kullandığını anlama](iot-edge-certs.md).

Bu iki otomatik oluşturulan sertifika için, sertifikaların kullanım ömrü için gün sayısını yapılandırmak üzere yapılandırma dosyasında **auto_generated_ca_lifetime_days** bayrağını ayarlama seçeneğiniz vardır.

>[!NOTE]
>IoT Edge Güvenlik Yöneticisi 'nin oluşturduğu, **IoT Edge merkezi sunucu sertifikası** olan üçüncü bir otomatik oluşturulan sertifika vardır. Bu sertifikanın her zaman 90 gün ömrü vardır, ancak süresi dolmadan önce otomatik olarak yenilenir. **Auto_generated_ca_lifetime_days** değeri bu sertifikayı etkilemez.

Süre dolduktan sonra, belirtilen gün sayısından sonra, cihaz CA sertifikasını yeniden oluşturmak için IoT Edge yeniden başlatılması gerekir. Cihaz CA sertifikası otomatik olarak yenilenmez.

1. Sertifika süre sonunu varsayılan 90 gün dışında bir şeye göre yapılandırmak için, değeri, yapılandırma dosyasının **Sertifikalar** bölümüne gün olarak ekleyin.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Şu anda libiothsm içindeki bir sınırlama 1 Ocak 2038 tarihinde veya sonrasında sona ermekte olan sertifikaların kullanılmasını engelliyor.

1. `hsm`Önceden oluşturulmuş tüm sertifikaları kaldırmak için klasörün içeriğini silin.

   * Linux ve Windows üzerinde Linux için IoT Edge: `/var/lib/iotedge/hsm/certs` ve `/var/lib/iotedge/hsm/cert_keys`

   * Windows kapsayıcıları kullanan pencereler: `C:\ProgramData\iotedge\hsm\certs` ve `C:\ProgramData\iotedge\hsm\cert_keys`

1. IoT Edge hizmetini yeniden başlatın.

   * Linux ve Windows üzerinde Linux için IoT Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

   * Windows kapsayıcıları kullanan pencereler:

   ```powershell
   Restart-Service iotedge
   ```

1. Ömür ayarını onaylayın.

   * Linux ve Windows üzerinde Linux için IoT Edge:

   ```bash
   sudo iotedge check --verbose
   ```

   * Windows kapsayıcıları kullanan pencereler:

   ```powershell
   iotedge check --verbose
   ```

   Otomatik olarak oluşturulan cihaz CA sertifikalarının sona erene kadar geçen gün sayısını listeleyen **Üretim hazırlığı: sertifika** denetimi çıktısını denetleyin.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge cihaza sertifika yükleme, çözümünüzü üretime dağıtmak için gereken bir adımdır. [IoT Edge çözümünüzü üretime dağıtmaya hazırlanma](production-checklist.md)hakkında daha fazla bilgi edinin.
