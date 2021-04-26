---
title: Linux VM 'de sanal TPM ile cihaz Sağlama-Azure IoT Edge
description: Azure IoT Edge için Azure cihaz sağlama hizmeti 'ni sınamak için bir Linux sanal makinesinde sanal bir TPM kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481999"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Linux 'ta TPM ile IoT Edge cihaz oluşturma ve sağlama

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Bu makalede, bir Güvenilir Platform Modülü (TPM) kullanarak bir Linux IoT Edge cihazında otomatik sağlamayı test etme işlemi gösterilmektedir. [Cihaz sağlama hizmeti](../iot-dps/index.yml)ile Azure IoT Edge cihazları otomatik olarak sağlayabilirsiniz. Otomatik sağlama işlemini tanımıyorsanız, devam etmeden önce [sağlamaya](../iot-dps/about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin.

Görevler aşağıdaki gibidir:

1. Donanım güvenliği için benzetimli Güvenilir Platform Modülü (TPM) ile Hyper-V ' d a Linux sanal makinesi (VM) oluşturun.
1. IoT Hub cihaz sağlama hizmeti 'nin (DPS) bir örneğini oluşturun.
1. Cihaz için tek bir kayıt oluşturun.
1. IoT Edge çalışma zamanını yükleyip cihazı IoT Hub bağlayın.

> [!TIP]
> Bu makalede, bir TPM simülatörü kullanılarak DPS sağlama işlemi nasıl test edileceğini açıklanmaktadır, ancak bunun çoğu, IoT için Azure Sertifikalı bir cihaz olan [Infineon OPTIMIZE GA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)gibi fiziksel TPM donanımları için geçerlidir.
>
> Fiziksel bir cihaz kullanıyorsanız, bu makaledeki [sağlama bilgilerini bir fiziksel cihazdan alma](#retrieve-provisioning-information-from-a-physical-device) bölümüne geçebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)' y i etkin bir Windows geliştirme makinesi. Bu makalede bir Ubuntu sunucu VM 'sini çalıştıran Windows 10 kullanılır.
* Etkin bir IoT Hub.

> [!NOTE]
> TPM kanıtlaması ile TPM kanıtlama kullanılırken TPM 2,0 gereklidir ve yalnızca bireysel, grup değil kayıtları oluşturmak için kullanılabilir.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Sanal TPM ile Linux sanal makinesi oluşturma

Bu bölümde, Hyper-V üzerinde yeni bir Linux sanal makinesi oluşturacaksınız. Otomatik sağlamanın IoT Edge ile nasıl çalıştığını test etmek için bu sanal makineyi sanal bir TPM ile yapılandırırsınız.

### <a name="create-a-virtual-switch"></a>Sanal anahtar oluştur

Sanal bir anahtar, sanal makinenizin bir fiziksel ağa bağlanmasını sağlar.

1. Windows makinenizde Hyper-V Yöneticisi 'Ni açın.

2. **Eylemler** menüsünde **sanal anahtar Yöneticisi**' ni seçin.

3. Bir **dış** sanal anahtar seçin, sonra **sanal anahtar oluştur**' u seçin.

4. Yeni sanal anahtarınızda bir ad verin, örneğin **EdgeSwitch**. Bağlantı türünün **dış ağ** olarak ayarlandığından emin olun ve ardından **Tamam**' ı seçin.

5. Bir açılır pencere, ağ bağlantısının kesintiye uğrayabileceği konusunda sizi uyarır. Devam etmek için **Evet**’i seçin.

Yeni sanal anahtarı oluştururken hata görürseniz, başka hiçbir anahtarın Ethernet bağdaştırıcısını kullandığından ve başka hiçbir anahtarın aynı adı kullankullanılmadığından emin olun.

### <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Sanal makineniz için kullanmak üzere bir disk görüntüsü dosyası indirin ve yerel olarak kaydedin. Örneğin, [Ubuntu server 18,04](http://releases.ubuntu.com/18.04/). IoT Edge cihazlar için desteklenen işletim sistemleri hakkında bilgi için bkz. [Azure IoT Edge desteklenen sistemler](support.md).

2. Hyper-V Yöneticisi 'nde,   >    >  **Eylemler** menüsünde Yeni **sanal makine** eylem ' i seçin.

3. Aşağıdaki belirli yapılandırmalara sahip **Yeni sanal makine Sihirbazı 'nı** doldurun:

   1. **Üretimi belirtin**: **2. nesil** seçeneğini belirleyin. 2. nesil sanal makinelerde, bir sanal makinede IoT Edge çalıştırmak için gereken iç içe sanallaştırma etkinleştirilmiş.
   2. **Ağı yapılandırma**: önceki bölümde oluşturduğunuz sanal anahtarla **bağlantı** değerini ayarlayın.
   3. **Yükleme seçenekleri**: **önyüklenebilir bir görüntü dosyasından işletim sistemi yükleme ' yi** seçin ve yerel olarak kaydettiğiniz disk görüntü dosyasına gidin.

4. Sanal makineyi oluşturmak için sihirbazda **son** ' u seçin.

Yeni VM 'nin oluşturulması birkaç dakika sürebilir.

### <a name="enable-virtual-tpm"></a>Sanal TPM 'YI etkinleştirme

VM 'niz oluşturulduktan sonra, cihazı otomatik olarak sağlamanıza olanak tanıyan sanal Güvenilir Platform Modülü 'nü (TPM) etkinleştirmek için ayarlarını açın.

1. Hyper-V Yöneticisi 'nde sanal makineye sağ tıklayın ve **Ayarlar**' ı seçin.

2. **Güvenlik**' e gidin.

3. **Güvenli önyüklemeyi etkinleştir** seçeneğinin işaretini kaldırın.

4. **Güvenilir Platform Modülü etkinleştir**' i işaretleyin.

5. **Tamam**'a tıklayın.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Sanal makineyi başlatın ve TPM verilerini toplayın

Sanal makinede, cihazın **kayıt kimliği** ve **onay anahtarını** almak için kullanabileceğiniz bir araç oluşturun.

1. Hyper-V Yöneticisi 'nde VM 'nizi başlatın ve bu sunucuya bağlanın.

1. Yükleme işlemini tamamlaması ve makineyi yeniden başlatmak için sanal makinedeki istemleri izleyin.

1. SANAL makinenizde oturum açın ve ardından, C için Azure IoT cihaz SDK 'sını yüklemek ve derlemek için [bir Linux geliştirme ortamı ayarlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) bölümündeki adımları izleyin.

   >[!TIP]
   >Bu makalede, Hyper-V Yöneticisi bağlantı uygulaması aracılığıyla kolay olmayan sanal makineye kopyalayıp yapıştırırsınız. IP adresini almak için sanal makineye Hyper-V Yöneticisi üzerinden bir kez bağlanmak isteyebilirsiniz. İlk olarak `sudo apt install net-tools` ve sonra öğesini çalıştırın `hostname -I` . Ardından, SSH aracılığıyla bağlanmak için IP adresini kullanabilirsiniz: `ssh <username>@<ipaddress>` .

1. TPM 'den cihaz sağlama bilgilerinizi alan SDK aracını oluşturmak için aşağıdaki komutları çalıştırın.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Çıkış penceresinde cihazın **kayıt kimliği** ve **onay anahtarı** görüntülenir. Cihazınız için bireysel bir kayıt oluştururken bu değerleri daha sonra kullanmak üzere kopyalayın.

Kayıt KIMLIĞINIZ ve onay anahtarınız olduktan sonra, [IoT Hub cihaz sağlama hizmetini ayarlama](#set-up-the-iot-hub-device-provisioning-service) bölümüne ilerleyin.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Fiziksel bir cihazdan sağlama bilgilerini alma

VM yerine bir fiziksel IoT Edge cihaz kullanıyorsanız, cihazın sağlama bilgilerini almak için kullanabileceğiniz bir araç oluşturun.

1. C için Azure IoT cihaz SDK 'sını yüklemek ve derlemek için [bir Linux geliştirme ortamı ayarlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) bölümündeki adımları izleyin.

1. TPM cihazından cihaz sağlama bilgilerinizi alan SDK aracını oluşturmak için aşağıdaki komutları çalıştırın.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **Kayıt kimliği** ve **onay anahtarı** değerlerini kopyalayın. Bu değerleri,, DPS 'de cihazınız için tek bir kayıt oluşturmak için kullanırsınız.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub cihaz sağlama hizmetini ayarlama

Azure 'da IoT Hub cihaz sağlama hizmetinin yeni bir örneğini oluşturun ve IoT Hub 'ınıza bağlayın. [IoT Hub DPS 'Yi ayarlama](../iot-dps/quick-setup-auto-provision.md)bölümündeki yönergeleri izleyebilirsiniz.

Cihaz sağlama hizmetini çalıştırdıktan sonra, genel bakış sayfasından **kimlik kapsamının** değerini kopyalayın. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız.

## <a name="create-a-dps-enrollment"></a>Bir DPS kaydı oluşturma

Sanal makinenizden sağlama bilgilerini alın ve cihaz sağlama hizmeti 'nde tek bir kayıt oluşturmak için bunu kullanın.

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu** bildirme fırsatına sahip olursunuz. Device ikizi 'da, bir cihaz için gereken bölge, ortam, konum veya cihaz türü gibi herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-at-scale.md)oluşturmak için kullanılır.

> [!TIP]
> Azure CLı 'da bir [kayıt](/cli/azure/iot/dps/enrollment) oluşturabilir ve **kenar özellikli** bayrağını kullanarak bir cihazın IoT Edge cihaz olduğunu belirtebilirsiniz.

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin.

2. **Ayarlar** altında kayıtları **Yönet**' i seçin.

3. **Bireysel kayıt Ekle** ' yi seçin, ardından kaydı yapılandırmak için aşağıdaki adımları izleyin:  

   1. **Mekanizma** için **TPM**' yi seçin.

   2. Sanal makinenizden kopyaladığınız **onay anahtarını** ve **kayıt kimliğini** sağlayın.

      > [!TIP]
      > Fiziksel bir TPM cihazı kullanıyorsanız, her TPM yongası için benzersiz olan ve kendisiyle ilişkili TPM yonga üreticisinden elde edilen **onay anahtarını** belirlemeniz gerekir. TPM cihazınız için benzersiz bir **kayıt kimliği** türetebilirsiniz; Örneğin, onay anahtarının SHA-256 karmasını oluşturma.

   3. İsterseniz cihazınız için bir KIMLIK sağlayın. Bir cihaz KIMLIĞI sağlamazsanız, kayıt KIMLIĞI kullanılır.

   4. Bu sanal makinenin IoT Edge bir cihaz olduğunu bildirmek için **true** ' ı seçin.

   5. Cihazınızı bağlamak istediğiniz bağlı IoT Hub seçin veya **yeni IoT Hub bağla**' yı seçin. Birden çok hub seçebilirsiniz ve bu cihaz seçili atama ilkesine göre bunlardan birine atanır.

   6. İsterseniz **Ilk cihaz Ikizi durumuna** bir etiket değeri ekleyin. Modül dağıtımı için cihaz gruplarını hedeflemek üzere etiketleri kullanabilirsiniz. Daha fazla bilgi için bkz. [IoT Edge modüllerini ölçekli olarak dağıtma](how-to-deploy-at-scale.md).

   7. **Kaydet**’i seçin.

Bu cihaz için bir kayıt mevcut olduğuna göre, IoT Edge çalışma zamanı cihazı yükleme sırasında otomatik olarak sağlayabilir.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükleme

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Bileşenleri kapsayıcılarda çalıştırılır ve kenarda kod çalıştırabilmeniz için cihaza ek kapsayıcılar dağıtmanıza izin verir. IoT Edge çalışma zamanını sanal makinenize yükler.

[Azure IoT Edge çalışma zamanını yüklemek](how-to-install-iot-edge.md)için bu adımları izleyin ve sonra cihazı sağlamak için bu makaleye geri dönün.

## <a name="configure-the-device-with-provisioning-information"></a>Cihazı sağlama bilgileriyle yapılandırma

Çalışma zamanı cihazınıza yüklendikten sonra, cihazı cihaz sağlama hizmetine bağlanmak için kullandığı bilgilerle yapılandırın ve IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. DPS **kimlik kapsamınızı** ve önceki bölümlerde toplanan CIHAZ **kayıt kimliğini** öğrenin.

1. IoT Edge cihazında yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Dosyanın sağlama yapılandırması bölümünü bulun. TPM sağlaması için satırların açıklamasını kaldırın ve diğer sağlama satırlarının açıklama olarak belirlendiğinden emin olun.

   `provisioning:`Satırda önünde boşluk olmaması ve iç içe geçmiş öğelerin iki boşluk olması gerekir.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Ve değerlerini, `scope_id` `registration_id` DPS ve cihaz bilgileriniz ile güncelleştirin.

1. İsteğe bağlı olarak, `always_reprovision_on_startup` `dynamic_reprovisioning` cihazınızın yeniden sağlama davranışını yapılandırmak için veya satırını kullanın. Bir cihaz başlangıçta yeniden sağlamak üzere ayarlandıysa, her zaman önce DPS ile sağlamayı dener ve ardından bu başarısız olursa sağlama yedeklemesine geri dönecektir. Bir cihaz kendisini dinamik olarak yeniden sağlamak üzere ayarlandıysa, yeniden sağlama olayı algılandığında IoT Edge yeniden başlatılır ve yeniden hazırlar. Daha fazla bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Dosyayı kaydedin ve kapatın.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. DPS **kimlik kapsamınızı** ve önceki bölümlerde toplanan CIHAZ **kayıt kimliğini** öğrenin.

1. IoT Edge cihazında yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Dosyanın sağlama yapılandırması bölümünü bulun. TPM sağlaması için satırların açıklamasını kaldırın ve diğer sağlama satırlarının açıklama olarak belirlendiğinden emin olun.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Ve değerlerini, `id_scope` `registration_id` DPS ve cihaz bilgileriniz ile güncelleştirin.

1. İsteğe bağlı olarak, dosyanın otomatik yeniden sağlama modu bölümünü bulun. `auto_reprovisioning_mode`Cihazınızın yeniden sağlama davranışını, ya da olarak yapılandırmak için parametresini kullanın `Dynamic` `AlwaysOnStartup` `OnErrorOnly` . Daha fazla bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Dosyayı kaydedin ve kapatın.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM 'ye IoT Edge erişim verme

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Cihazınızı otomatik olarak sağlamak için IoT Edge çalışma zamanının TPM 'ye erişmesi gerekir.

Hizmetin kök ayrıcalıklarına sahip olması için systemd ayarlarını geçersiz kılarak IoT Edge çalışma zamanına TPM erişimi verebilirsiniz `iotedge` . Hizmet ayrıcalıklarını yükseltmek istemiyorsanız, TPM erişimini el ile sağlamak için aşağıdaki adımları da kullanabilirsiniz.

1. Cihazınızdaki TPM donanım modülünün dosya yolunu bulun ve yerel bir değişken olarak kaydedin.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. IoT Edge çalışma zamanına tpm0 erişimini sağlayacak yeni bir kural oluşturun.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Kurallar dosyasını açın.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Aşağıdaki erişim bilgilerini kurallar dosyasına kopyalayın.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Dosyayı kaydedin ve kapatın.

6. Yeni kuralı değerlendirmek için udev sistemini tetikleyin.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kuralın başarıyla uygulandığını doğrulayın.

   ```bash
   ls -l /dev/tpm0
   ```

   Başarılı çıkış şu şekilde görünür:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Doğru izinlerin uygulandığını görmüyorsanız, udev 'yi yenilemek için makinenizi yeniden başlatmayı deneyin.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge çalışma zamanı, aracının bir cihazın TPM 'ye erişiminin bulunduğu TPM hizmetini kullanır. Cihazınızı otomatik olarak sağlamak için bu hizmetin TPM 'ye erişmesi gerekir.

Hizmetin kök ayrıcalıklarına sahip olması için systemd ayarlarını geçersiz kılarak TPM 'ye erişim izni verebilirsiniz `aziottpm` . Hizmet ayrıcalıklarını yükseltmek istemiyorsanız, TPM erişimini el ile sağlamak için aşağıdaki adımları da kullanabilirsiniz.

1. Cihazınızdaki TPM donanım modülünün dosya yolunu bulun ve yerel bir değişken olarak kaydedin.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. IoT Edge çalışma zamanına tpm0 erişimini sağlayacak yeni bir kural oluşturun.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Kurallar dosyasını açın.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Aşağıdaki erişim bilgilerini kurallar dosyasına kopyalayın.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Dosyayı kaydedin ve kapatın.

6. Yeni kuralı değerlendirmek için udev sistemini tetikleyin.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kuralın başarıyla uygulandığını doğrulayın.

   ```bash
   ls -l /dev/tpm0
   ```

   Başarılı çıkış şu şekilde görünür:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Doğru izinlerin uygulandığını görmüyorsanız, udev 'yi yenilemek için makinenizi yeniden başlatmayı deneyin.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge yeniden başlatın ve yüklemenin başarılı olduğunu doğrulayın

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Cihazda yaptığınız tüm yapılandırma değişikliklerini alması için IoT Edge çalışma zamanını yeniden başlatın.

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge çalışma zamanının çalışıp çalışmadığını denetleyin.

   ```bash
   sudo systemctl status iotedge
   ```

Daemon günlüklerini inceleyin.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Sağlama hataları görürseniz, yapılandırma değişikliklerinin henüz etkili bir şekilde yapılmamış olması olabilir. IoT Edge Daemon 'ı yeniden başlatmayı deneyin.

   ```bash
   sudo systemctl daemon-reload
   ```

Ya da değişikliklerin yeni bir başlangıç üzerinde etkin olup olmadığını görmek için sanal makinenizi yeniden başlatmayı deneyin.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Cihazda yaptığınız yapılandırma değişikliklerini uygulayın.

   ```bash
   sudo iotedge config apply
   ```

IoT Edge çalışma zamanının çalışıp çalışmadığını denetleyin.

   ```bash
   sudo iotedge system status
   ```

Daemon günlüklerini inceleyin.

   ```cmd/sh
   sudo iotedge system logs
   ```

Sağlama hataları görürseniz, yapılandırma değişikliklerinin henüz etkili bir şekilde yapılmamış olması olabilir. IoT Edge Daemon 'ı yeniden başlatmayı deneyin.

   ```bash
   sudo systemctl daemon-reload
   ```

Ya da değişikliklerin yeni bir başlangıç üzerinde etkin olup olmadığını görmek için sanal makinenizi yeniden başlatmayı deneyin.
:::moniker-end
<!-- end 1.2 -->

Çalışma zamanı başarıyla başlatıldıysa, IoT Hub giderek yeni cihazınızın otomatik olarak sağlandığını görebilirsiniz. Cihazınız artık IoT Edge modülleri çalıştırılmaya hazır.

Çalışan modülleri listeleyin.

```cmd/sh
iotedge list
```

Cihaz sağlama hizmeti 'nde oluşturduğunuz bireysel kaydın kullanıldığını doğrulayabilirsiniz. Azure portal cihaz sağlama hizmeti örneğinize gidin. Oluşturduğunuz bireysel kayıt için kayıt ayrıntılarını açın. Kayıt durumunun **atandığını** ve cihaz kimliğinin listelendiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

DPS kayıt işlemi, cihaz KIMLIĞI ve cihaz ikizi etiketlerini yeni cihazı sağladığınız zamanda ayarlamanıza olanak sağlar. Bu değerleri, otomatik cihaz yönetimi kullanarak ayrı cihazları veya cihaz gruplarını hedeflemek için kullanabilirsiniz. Azure portal veya [Azure CLI kullanarak](how-to-deploy-cli-at-scale.md) [IoT Edge modüllerini ölçekte nasıl dağıtacağınızı ve izleyeceğinizi](how-to-deploy-at-scale.md) öğrenin.
