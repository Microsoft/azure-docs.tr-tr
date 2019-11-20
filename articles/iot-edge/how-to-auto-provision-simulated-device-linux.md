---
title: DPS ile Linux cihazlarını otomatik olarak Sağlama-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge için Azure cihaz sağlama hizmeti 'ni sınamak için bir Linux sanal makinesinde sanal bir TPM kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 228851a0d528bfb222e5aa19880f856424e95ad1
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828120"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Linux sanal makinesinde sanal TPM ile bir IoT Edge cihazı oluşturma ve sağlama

Azure IoT Edge cihazlar [cihaz sağlama hizmeti](../iot-dps/index.yml)kullanılarak otomatik olarak sağlanabilir. Otomatik sağlama işlemini tanımıyorsanız, devam etmeden önce [Otomatik sağlama kavramlarını](../iot-dps/concepts-auto-provisioning.md) gözden geçirin.

Bu makalede, aşağıdaki adımlarla sanal bir IoT Edge cihazında otomatik sağlamayı test etme işlemi gösterilmektedir:

* Donanım güvenliği için benzetimli Güvenilir Platform Modülü (TPM) ile Hyper-V ' d a Linux sanal makinesi (VM) oluşturun.
* IoT Hub cihaz sağlama hizmeti 'nin (DPS) bir örneğini oluşturun.
* Cihaz için tek bir kayıt oluşturma
* IoT Edge çalışma zamanını yükleyip cihazı IoT Hub bağlayın

> [!NOTE]
> TPM kanıtlaması ile TPM kanıtlama kullanılırken TPM 2,0 gereklidir ve yalnızca bireysel, grup değil kayıtları oluşturmak için kullanılabilir.

> [!TIP]
> Bu makalede, bir TPM simülatörü kullanılarak DPS sağlama işlemi nasıl test edileceğini açıklanmaktadır, ancak bu çoğu, IoT için Azure Sertifikalı bir cihaz olan [Infineon OPTIMIZE GA @ no__t-1 TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)gıbı fiziksel TPM donanımları için geçerlidir.
>
> Fiziksel bir cihaz kullanıyorsanız, bu makaledeki [sağlama bilgilerini bir fiziksel cihazdan alma](#retrieve-provisioning-information-from-a-physical-device) bölümüne geçebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)' y i etkin bir Windows geliştirme makinesi. Bu makalede bir Ubuntu sunucu VM 'sini çalıştıran Windows 10 kullanılır.
* Etkin bir IoT Hub.
* ' In ' de [masaüstü geliştirmesi C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ' ile sanal bir TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 veya sonraki bir sürümü kullanıyorsanız.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Sanal TPM ile Linux sanal makinesi oluşturma

Bu bölümde, Hyper-V üzerinde yeni bir Linux sanal makinesi oluşturacaksınız. Otomatik sağlamanın IoT Edge ile nasıl çalıştığını test etmek üzere kullanabilmeniz için, bu sanal makineyi sanal bir TPM ile yapılandırdınız. 

### <a name="create-a-virtual-switch"></a>Sanal anahtar oluştur

Sanal bir anahtar, sanal makinenizin bir fiziksel ağa bağlanmasını sağlar.

1. Windows makinenizde Hyper-V Yöneticisi 'Ni açın. 

2. **Eylemler** menüsünde **sanal anahtar Yöneticisi**' ni seçin. 

3. Bir **dış** sanal anahtar seçin, sonra **sanal anahtar oluştur**' u seçin. 

4. Yeni sanal anahtarınızda bir ad verin, örneğin **EdgeSwitch**. Bağlantı türünün **dış ağ**olarak ayarlandığından emin olun ve ardından **Tamam**' ı seçin.

5. Bir açılır pencere, ağ bağlantısının kesintiye uğrayabileceği konusunda sizi uyarır. Devam etmek için **Evet** ' i seçin. 

Yeni sanal anahtarı oluştururken hata görürseniz, başka hiçbir anahtarın Ethernet bağdaştırıcısını kullandığından ve başka hiçbir anahtarın aynı adı kullankullanılmadığından emin olun. 

### <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Sanal makineniz için kullanmak üzere bir disk görüntüsü dosyası indirin ve yerel olarak kaydedin. Örneğin, [Ubuntu sunucusu](https://www.ubuntu.com/download/server). 

2. Hyper-V Yöneticisi 'nde, **Eylemler** menüsünde **Yeni** > **sanal makine** ' yi seçin.

3. Aşağıdaki belirli yapılandırmalara sahip **Yeni sanal makine Sihirbazı 'nı** doldurun:

   1. **Üretimi belirtin**: **2. nesil**seçeneğini belirleyin. 2\. nesil sanal makinelerde, bir sanal makinede IoT Edge çalıştırmak için gereken iç içe sanallaştırma etkinleştirilmiş.
   2. **Ağı yapılandırma**: önceki bölümde oluşturduğunuz sanal anahtarla **bağlantı** değerini ayarlayın. 
   3. **Yükleme seçenekleri**: **önyüklenebilir bir görüntü dosyasından işletim sistemi yükleme ' yi** seçin ve yerel olarak kaydettiğiniz disk görüntü dosyasına gidin.

4. Sanal makineyi oluşturmak için sihirbazda **son** ' u seçin.

Yeni VM 'nin oluşturulması birkaç dakika sürebilir. 

### <a name="enable-virtual-tpm"></a>Sanal TPM 'YI etkinleştirme

VM 'niz oluşturulduktan sonra, cihazı otomatik olarak sağlamanıza olanak tanıyan sanal Güvenilir Platform Modülü 'nü (TPM) etkinleştirmek için ayarlarını açın.

1. Sanal makineyi seçin ve ardından **ayarlarını**açın.

2. **Güvenlik**' e gidin. 

3. **Güvenli önyüklemeyi etkinleştir**seçeneğinin işaretini kaldırın.

4. **Güvenilir Platform Modülü etkinleştir**' i işaretleyin. 

5. **Tamam**’a tıklayın.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Sanal makineyi başlatın ve TPM verilerini toplayın

Sanal makinede, cihazın **kayıt kimliği** ve **onay anahtarını**almak için kullanabileceğiniz bir araç oluşturun.

1. Sanal makinenizi başlatın ve bu sunucuya bağlanın.

1. Yükleme işlemini tamamlaması ve makineyi yeniden başlatmak için sanal makinedeki istemleri izleyin.

1. SANAL makinenizde oturum açın ve ardından, C için Azure IoT cihaz SDK 'sını yüklemek ve derlemek için [bir Linux geliştirme ortamı ayarlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) bölümündeki adımları izleyin.

   >[!TIP]
   >Bu makalede, Hyper-V Yöneticisi bağlantı uygulaması aracılığıyla kolay olmayan sanal makineye kopyalanacak ve buradan yapıştırılacak. IP adresini almak için sanal makineye Hyper-V Yöneticisi 'nden bir kez bağlanmak isteyebilirsiniz: `ifconfig`. Daha sonra SSH aracılığıyla bağlanmak için IP adresini kullanabilirsiniz: `ssh <username>@<ipaddress>`.

1. TPM benzeticisinde cihaz sağlama bilgilerinizi alan SDK aracını oluşturmak için aşağıdaki komutları çalıştırın.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Bir komut penceresinden `azure-iot-sdk-c` dizinine gidin ve TPM simülatörünü çalıştırın. 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; Bu simülatörü çalışır durumda tutmanız gerekecektir.

   @No__t-0 dizininden simülatörü başlatmak için aşağıdaki komutu çalıştırın:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Visual Studio 'yu kullanarak, `azure_iot_sdks.sln` adlı `cmake` dizininde oluşturulan çözümü açın ve **Build** menüsündeki Build **Solution** komutunu kullanarak derleyin.

1. Visual Studio'nın **Çözüm Gezgini** bölmesinde **Sağlama\_Araçlar** klasörüne gidin. **tpm_device_provision** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin.

1. **Hata Ayıkla** menüsündeki **Başlat** komutlarından birini kullanarak çözümü çalıştırın. Çıktı penceresi, ' de cihazınız için tek bir kayıt oluşturduğunuzda, daha sonra kullanmak üzere kopyalamanız gereken TPM simülatörü **kayıt kimliği** ve **onay anahtarını**görüntüler. bu PENCEREYI kapatabilir (kayıt kimliği ve Onay anahtarı), ancak TPM simülatörü penceresini çalışır durumda bırakın.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Fiziksel bir cihazdan sağlama bilgilerini alma

Cihazınızda, cihazın sağlama bilgilerini almak için kullanabileceğiniz bir araç oluşturun.

1. C için Azure IoT cihaz SDK 'sını yüklemek ve derlemek için [bir Linux geliştirme ortamı ayarlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) bölümündeki adımları izleyin.

1. TPM cihazından cihaz sağlama bilgilerinizi alan SDK aracını oluşturmak için aşağıdaki komutları çalıştırın.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **Kayıt kimliği** ve **onay anahtarı**değerlerini kopyalayın. Bu değerleri,, DPS 'de cihazınız için tek bir kayıt oluşturmak için kullanırsınız.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub cihaz sağlama hizmetini ayarlama

Azure 'da IoT Hub cihaz sağlama hizmetinin yeni bir örneğini oluşturun ve IoT Hub 'ınıza bağlayın. [IoT Hub DPS 'Yi ayarlama](../iot-dps/quick-setup-auto-provision.md)bölümündeki yönergeleri izleyebilirsiniz.

Cihaz sağlama hizmetini çalıştırdıktan sonra, genel bakış sayfasından **kimlik kapsamının** değerini kopyalayın. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız. 

## <a name="create-a-dps-enrollment"></a>Bir DPS kaydı oluşturma

Sanal makinenizden sağlama bilgilerini alın ve cihaz sağlama hizmeti 'nde tek bir kayıt oluşturmak için bunu kullanın. 

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu**bildirme fırsatına sahip olursunuz. Device ikizi 'da, bir cihaz için gereken bölge, ortam, konum veya cihaz türü gibi herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-monitor.md)oluşturmak için kullanılır. 

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin. 

2. **Ayarlar**altında kayıtları **Yönet**' i seçin. 

3. **Bireysel kayıt Ekle** ' yi seçin, ardından kaydı yapılandırmak için aşağıdaki adımları izleyin:  

   1. **Mekanizma**için **TPM**' yi seçin. 

   2. Sanal makinenizden kopyaladığınız **onay anahtarını** ve **kayıt kimliğini** sağlayın.

      > [!TIP]
      > Fiziksel bir TPM cihazı kullanıyorsanız, her TPM yongası için benzersiz olan ve kendisiyle ilişkili TPM yonga üreticisinden elde edilen **onay anahtarını**belirlemeniz gerekir. TPM cihazınız için benzersiz bir **kayıt kimliği** türetebilirsiniz; Örneğin, onay anahtarının SHA-256 karmasını oluşturma.

   3. Bu sanal makinenin IoT Edge bir cihaz olduğunu bildirmek için **true** ' ı seçin. 

   4. Cihazınızı bağlamak istediğiniz bağlı **IoT Hub** seçin. Birden çok hub seçebilirsiniz ve bu cihaz, seçilen ayırma ilkesine göre bu cihazdan birine atanır. 

   5. İsterseniz cihazınız için bir KIMLIK sağlayın. Modül dağıtımı için tek bir cihazı hedeflemek üzere cihaz kimliklerini kullanabilirsiniz. Bir cihaz KIMLIĞI sağlamazsanız, kayıt KIMLIĞI kullanılır.

   6. İsterseniz **Ilk cihaz Ikizi durumuna** bir etiket değeri ekleyin. Modül dağıtımı için cihaz gruplarını hedeflemek üzere etiketleri kullanabilirsiniz. Örnek: 

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

   7. **Kaydet**’i seçin. 

Bu cihaz için bir kayıt mevcut olduğuna göre, IoT Edge çalışma zamanı cihazı yükleme sırasında otomatik olarak sağlayabilir. 

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükler

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Bileşenleri kapsayıcılarda çalıştırılır ve kenarda kod çalıştırabilmeniz için cihaza ek kapsayıcılar dağıtmanıza izin verir. IoT Edge çalışma zamanını sanal makinenize yükler. 

Cihaz türü ile eşleşen makaleye başlamadan önce, DPS **kimlik kapsamınızı** ve CIHAZ **Kayıt kimliğinizi** öğrenin. Ubuntu Server örneğini yüklediyseniz **x64** yönergelerini kullanın. IoT Edge çalışma zamanını otomatik, el ile değil, sağlama için yapılandırdığınızdan emin olun. 

[Linux üzerinde Azure IoT Edge çalışma zamanını yükler](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM 'ye IoT Edge erişim verme

IoT Edge çalışma zamanının cihazınızı otomatik olarak sağlaması için TPM 'ye erişmesi gerekir. 

**İotedge** hizmetinin kök ayrıcalıkları olması için systemd ayarlarını geçersiz kılarak IoT Edge çalışma zamanına TPM erişimi verebilirsiniz. Hizmet ayrıcalıklarını yükseltmek istemiyorsanız, TPM erişimini el ile sağlamak için aşağıdaki adımları da kullanabilirsiniz. 

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
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

   Başarılı çıkış aşağıdaki gibi görünür:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Doğru izinlerin uygulandığını görmüyorsanız, udev 'yi yenilemek için makinenizi yeniden başlatmayı deneyin. 

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yeniden başlatın

Cihazda yaptığınız tüm yapılandırma değişikliklerini alması için IoT Edge çalışma zamanını yeniden başlatın. 

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge çalışma zamanının çalışıp çalışmadığını denetleyin. 

   ```bash
   sudo systemctl status iotedge
   ```

Sağlama hataları görürseniz, yapılandırma değişikliklerinin henüz etkili bir şekilde yapılmamış olması olabilir. IoT Edge Daemon 'ı yeniden başlatmayı deneyin. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Ya da değişikliklerin yeni bir başlangıç üzerinde etkin olup olmadığını görmek için sanal makinenizi yeniden başlatmayı deneyin. 

## <a name="verify-successful-installation"></a>Yüklemenin başarılı olduğunu doğrulama

Çalışma zamanı başarıyla başlatıldıysa, IoT Hub giderek yeni cihazınızın otomatik olarak sağlandığını görebilirsiniz. Cihazınız artık IoT Edge modülleri çalıştırılmaya hazır. 

IoT Edge Daemon 'ın durumunu denetleyin.

```cmd/sh
systemctl status iotedge
```

Daemon günlüklerini inceleyin.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Çalışan modülleri listeleyin.

```cmd/sh
iotedge list
```

Cihaz sağlama hizmeti 'nde oluşturduğunuz bireysel kaydın kullanıldığını doğrulayabilirsiniz. Azure portal cihaz sağlama hizmeti örneğinize gidin. Oluşturduğunuz bireysel kayıt için kayıt ayrıntılarını açın. Kayıt durumunun **atandığını** ve cihaz kimliğinin listelendiğini unutmayın. 

## <a name="next-steps"></a>Sonraki adımlar

Cihaz sağlama hizmeti kayıt işlemi, yeni cihazı sağladığınız anda cihaz KIMLIĞI ve cihaz ikizi etiketlerini ayarlamanıza olanak sağlar. Bu değerleri, otomatik cihaz yönetimi kullanarak ayrı cihazları veya cihaz gruplarını hedeflemek için kullanabilirsiniz. Azure portal veya [Azure CLI kullanarak](how-to-deploy-monitor-cli.md) [IoT Edge modüllerini ölçekte nasıl dağıtacağınızı ve izleyeceğinizi](how-to-deploy-monitor.md) öğrenin.
