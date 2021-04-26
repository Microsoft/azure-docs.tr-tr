---
title: Azure IoT Edge yüklensin | Microsoft Docs
description: Windows veya Linux cihazlarda yükleme yönergelerini Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: 39e165d862d6e174f763cd58529727fd26b8bd46
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311082"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Linux için Azure IoT Edge yükleme veya kaldırma

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge çalışma zamanı, bir cihazı IoT Edge cihazına dönüştürür. Çalışma zamanı, cihazlarda Raspberry Pi kadar küçük veya endüstriyel sunucu olarak büyük olarak dağıtılabilir. Bir cihaz IoT Edge çalışma zamanıyla yapılandırıldığında, buluttan cihaza iş mantığını dağıtmaya başlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

Bu makalede, Linux cihazlarına Azure IoT Edge çalışma zamanını yüklemek için gereken adımlar listelenmektedir.

## <a name="prerequisites"></a>Önkoşullar

* [Kayıtlı bir cıhaz kimliği](how-to-register-device.md)

  Cihazınızın simetrik anahtar kimlik doğrulaması ile kaydolduysanız, cihaz bağlantı dizesinin hazır olmasını sağlayabilirsiniz.

  Cihazınızı X. 509.440 otomatik olarak imzalanan sertifika kimlik doğrulaması ile kaydettiniz, cihazı kaydetmek için kullandığınız kimlik sertifikalarından en az birine ve ilgili özel anahtarı cihazınızda kullanılabilir.

* Bir Linux cihazı

  X64, ARM32 veya ARM64 Linux cihazı vardır. Microsoft, Ubuntu Server 18,04 ve Raspberry PI OS Esnetme işletim sistemlerini yükleme paketleri sağlar.

  Şu anda üretim senaryolarında desteklenen işletim sistemleri hakkında en son bilgiler için, bkz. [Azure IoT Edge desteklenen sistemler](support.md#operating-systems)

  >[!NOTE]
  >ARM64 cihazlar için destek [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Cihazınızı Microsoft Yükleme paketlerine erişmek için hazırlayın.

  Cihaz işletim sisteminizle eşleşen depo yapılandırmasını yükler.

  * **Ubuntu Server 18,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry PI OS Esnetme**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Oluşturulan listeyi sources. List. d dizinine kopyalayın.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Microsoft GPG ortak anahtarını yükler.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge yazılım paketleri, her pakette ( `usr/share/doc/{package-name}` veya dizinde) bulunan lisans koşullarına tabidir `LICENSE` . Paket kullanmadan önce lisans koşullarını okuyun. Bir paketi yüklemeniz ve kullanmanız, bu şartlarınızın kabul edildiğini oluşturur. Lisans koşullarını kabul etmiyorsanız, bu paketi kullanmayın.

## <a name="install-a-container-engine"></a>Kapsayıcı Altyapısı yükler

Azure IoT Edge, OCı uyumlu bir kapsayıcı çalışma zamanına bağlıdır. Üretim senaryolarında Moby altyapısını kullanmanızı öneririz. Moby motoru, Azure IoT Edge ile resmi olarak desteklenen tek kapsayıcı altyapısıdır. Docker CE/EE kapsayıcı görüntüleri Moby çalışma zamanına göre uyumludur.

Cihazınızdaki paket listelerini güncelleştirin.

   ```bash
   sudo apt-get update
   ```

Moby altyapısını yükler.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby kapsayıcı altyapısını yüklerken hata alırsanız, Moby uyumluluğu için Linux çekirdeğini doğrulayın. Bazı katıştırılmış cihaz üreticileri, kapsayıcı altyapısı uyumluluğu için gerekli özellikler olmadan özel Linux çekirdekleri içeren cihaz görüntülerini sevk etti. Çekirdek yapılandırmanızı denetlemek için Moby tarafından sunulan [Check-config betiğini](https://github.com/moby/moby/blob/master/contrib/check-config.sh) kullanan aşağıdaki komutu çalıştırın:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Komut dosyasının çıktısında, ve altındaki tüm öğelerin etkin olduğunu kontrol edin `Generally Necessary` `Network Drivers` . Özellikler eksik ise, çekirdeğini kaynaktan yeniden oluşturarak ve uygun çekirdek. config dosyasına eklenmek üzere ilişkili modülleri seçerek etkinleştirin. Benzer şekilde, veya gibi bir çekirdek yapılandırma Oluşturucu kullanıyorsanız `defconfig` `menuconfig` ilgili özellikleri bulup etkinleştirin ve çekirdeğini uygun şekilde yeniden oluşturun. Yeni değiştirilen çekirdeğini dağıttıktan sonra, gerekli tüm özelliklerin başarıyla etkinleştirildiğini doğrulamak için Check-config betiğini yeniden çalıştırın.

## <a name="install-iot-edge"></a>IoT Edge yüklensin

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge güvenlik arka plan programı, IoT Edge cihazında güvenlik standartları sağlar ve korur. Arka plan programı her önyüklemede başlar ve IoT Edge çalışma zamanının geri kalanını başlatarak cihazı önyükleme.

Bu bölümdeki adımlarda, internet bağlantısı olan bir cihaza en son sürümü yüklemek için tipik bir işlem temsil etmektedir. Yayın öncesi sürüm gibi belirli bir sürümü yüklemeniz ya da çevrimdışıyken yüklemeniz gerekiyorsa, bu makalenin ilerleyen kısımlarında yer alarak [çevrimdışı veya belirli sürüm yükleme](#offline-or-specific-version-installation-optional) adımlarını izleyin.

Cihazınızdaki paket listelerini güncelleştirin.

   ```bash
   sudo apt-get update
   ```

Hangi IoT Edge sürümlerinin kullanılabilir olduğunu görmek için denetleyin.

   ```bash
   apt list -a iotedge
   ```

Güvenlik arka plan programının en son sürümünü yüklemek istiyorsanız, **libiothsm-STD** paketinin en son sürümünü de yükleyen aşağıdaki komutu kullanın:

   ```bash
   sudo apt-get install iotedge
   ```

Ya da güvenlik arka plan programının belirli bir sürümünü yüklemek istiyorsanız, apt liste çıktısından sürümü belirtin. Ayrıca, **libiothsm-STD** paketi için aynı sürümü belirtin, aksi takdirde en son sürümünü yükler. Örneğin, aşağıdaki komut 1,1 sürümünün en son sürümünü yüklüyor:

   ```bash
   sudo apt-get install iotedge=1.1* libiothsm-std=1.1*
   ```

Yüklemek istediğiniz sürüm listelenmemişse, bu makalenin ilerleyen kısımlarında yer alan [çevrimdışı veya belirli sürüm yükleme](#offline-or-specific-version-installation-optional) adımlarını izleyin. Bu bölümde, IoT Edge güvenlik arka plan programının veya sürüm adayı sürümlerinin önceki bir sürümünün nasıl hedeflenecek gösterilmektedir.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge hizmeti IoT Edge cihazında güvenlik standartları sağlar ve korur. Hizmet her önyüklemede başlatılır ve IoT Edge çalışma zamanının geri kalanını başlatarak cihazı önyükleme.

IoT kimlik hizmeti, IoT Edge sürüm 1,2 ile birlikte sunulmuştur. Bu hizmet, IoT Edge için kimlik sağlamayı ve yönetimi ve IoT Hub iletişim kurması gereken diğer cihaz bileşenlerini işler.

Bu bölümdeki adımlarda, internet bağlantısı olan bir cihaza en son sürümü yüklemek için tipik bir işlem temsil etmektedir. Yayın öncesi sürüm gibi belirli bir sürümü yüklemeniz ya da çevrimdışıyken yüklemeniz gerekiyorsa, bu makalenin ilerleyen kısımlarında yer alarak [çevrimdışı veya belirli sürüm yükleme](#offline-or-specific-version-installation-optional) adımlarını izleyin.

>[!NOTE]
>Bu bölümdeki adımlarda IoT Edge sürüm 1,2 ' nin nasıl yükleneceği gösterilmektedir.
>
>Daha eski bir sürümü çalıştıran bir IoT Edge cihazınız zaten varsa ve 1,2 sürümüne yükseltmek istiyorsanız, [IoT Edge güvenlik cini ve çalışma zamanını güncelleştirme](how-to-update-iot-edge.md)bölümündeki adımları kullanın. Sürüm 1,2, yükseltmek için belirli adımların gerekli olduğu IoT Edge önceki sürümlerinden yeterince farklıdır.

Cihazınızdaki paket listelerini güncelleştirin.

   ```bash
   sudo apt-get update
   ```

Hangi IoT Edge sürümlerinin kullanılabilir olduğunu görmek için denetleyin.

   ```bash
   apt list -a aziot-edge
   ```

En son IoT Edge sürümünü yüklemek istiyorsanız, kimlik hizmeti paketinin en son sürümünü de yükleyen aşağıdaki komutu kullanın:

   ```bash
   sudo apt-get install aziot-edge
   ```

Ya da IoT Edge belirli bir sürümünü ve kimlik hizmetini yüklemek istiyorsanız, apt liste çıktısından sürümleri belirtin. Her iki hizmet için de aynı sürümleri belirtin. Örneğin, aşağıdaki komut 1,2 sürümünün en son sürümünü yüklüyor:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Cihazı bulut kimliğiyle sağlama

Artık kapsayıcı altyapısı ve IoT Edge çalışma zamanı cihazınızda yüklü olduğuna göre, bir sonraki adım için hazır olursunuz. Bu, cihazı bulut kimliği ve kimlik doğrulama bilgileriyle ayarlamaya yöneliktir.

Kullanmak istediğiniz kimlik doğrulama türüne göre sonraki bölümü seçin:

* [Seçenek 1: simetrik anahtarlarla kimlik doğrulama](#option-1-authenticate-with-symmetric-keys)
* [Seçenek 2: X. 509.440 sertifikalarıyla kimlik doğrulama](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Seçenek 1: simetrik anahtarlarla kimlik doğrulama

Bu noktada, IoT Edge çalışma zamanı Linux cihazınıza yüklenir ve cihazı bulut kimliği ve kimlik doğrulama bilgileriyle sağlamanız gerekir.

Bu bölümde, simetrik anahtar kimlik doğrulaması ile cihaz sağlama adımları gösterilmektedir. Cihazınızı IoT Hub kaydolmalı ve cihaz bilgileri 'nden bağlantı dizesini almıştır. Aksi takdirde, [IoT Hub IoT Edge cihazı kaydetme](how-to-register-device.md)bölümündeki adımları izleyin.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge cihazda yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Dosyanın sağlama yapılandırmalarını bulun ve önceden açıklama oluşturulmamış **bir bağlantı dizesi bölümü kullanarak el ile sağlama yapılandırmasının** açıklamasını kaldırın.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**Device_connection_string** değerini IoT Edge cihazınızdan bağlantı dizesiyle güncelleştirin. Diğer tüm sağlama bölümlerinin açıklama olarak belirlendiğinden emin olun. **Sağlama:** satırının önünde boşluk olmadığından ve iç içe yerleştirilmiş öğelerin iki boşlukla girintilendiğinden emin olun.

Pano içeriğini nano `Shift+Right Click` veya Press 'e yapıştırmak için `Shift+Insert` .

Dosyayı kaydedin ve kapatın.

   `CTRL + X`, `Y`, `Enter`

Yapılandırma dosyasına sağlama bilgilerini girdikten sonra, arka plan programını yeniden başlatın:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Cihazınızın yapılandırma dosyasını, IoT Edge yüklemesinin bir parçası olarak sağlanmış bir şablon dosyasına göre oluşturun.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge cihazda yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Dosyanın **sağlama** bölümünü bulun ve bağlantı dizesi satırlarıyla el ile sağlamanın açıklamasını kaldırın.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**Connection_string** değerini IoT Edge cihazınızdan bağlantı dizesiyle güncelleştirin.

Pano içeriğini nano `Shift+Right Click` veya Press 'e yapıştırmak için `Shift+Insert` .

Dosyayı kaydedin ve kapatın.

   `CTRL + X`, `Y`, `Enter`

Yapılandırma dosyasına sağlama bilgilerini girdikten sonra, değişikliklerinizi uygulayın:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Seçenek 2: X. 509.440 sertifikalarıyla kimlik doğrulama

Bu noktada, IoT Edge çalışma zamanı Linux cihazınıza yüklenir ve cihazı bulut kimliği ve kimlik doğrulama bilgileriyle sağlamanız gerekir.

Bu bölümde, X. 509.440 sertifika kimlik doğrulamasıyla bir cihaz sağlama adımları gösterilmektedir. Cihazınızı, IoT Edge cihazınızda bulunan sertifikayla ve özel anahtarla eşleşen parmak izleri sağlayan IoT Hub 'ye kaydettiniz. Aksi takdirde, [IoT Hub IoT Edge cihazı kaydetme](how-to-register-device.md)bölümündeki adımları izleyin.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge cihazda yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Dosyanın sağlama yapılandırmaları bölümünü bulun ve **bir X. 509.440 kimlik sertifikası kullanarak el ile sağlama yapılandırmasının** açıklamasını kaldırın. Diğer tüm sağlama bölümlerinin açıklama olarak belirlendiğinden emin olun. **Sağlama:** satırının önünde boşluk olmadığından ve iç içe yerleştirilmiş öğelerin iki boşlukla girintilendiğinden emin olun.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Aşağıdaki alanları güncelleştirin:

* **iothub_hostname**: cihazın bağlanacağı IoT Hub 'ının ana bilgisayar adı. Örneğin, `{IoT hub name}.azure-devices.net`.
* **device_id**: cihazı kaydettirdiğiniz sırada verdiğiniz kimlik.
* **identity_cert**: cihazdaki bir kimlik sertifikası için URI. Örneğin, `file:///path/identity_certificate.pem`.
* **identity_pk**: belirtilen kimlik sertifikası için özel anahtar dosyasının URI 'si. Örneğin, `file:///path/identity_key.pem`.

Dosyayı kaydedin ve kapatın.

   `CTRL + X`, `Y`, `Enter`

Yapılandırma dosyasına sağlama bilgilerini girdikten sonra, arka plan programını yeniden başlatın:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Cihazınızın yapılandırma dosyasını, IoT Edge yüklemesinin bir parçası olarak sağlanmış bir şablon dosyasına göre oluşturun.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge cihazda yapılandırma dosyasını açın.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Dosyanın **sağlama** bölümünü bulun ve X. 509.440 kimlik sertifikasıyla el ile sağlama için satırların açıklamasını kaldırın. Diğer tüm sağlama bölümlerinin açıklama olarak belirlendiğinden emin olun.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Aşağıdaki alanları güncelleştirin:

* **iothub_hostname**: cihazın bağlanacağı IoT Hub 'ının ana bilgisayar adı. Örneğin, `{IoT hub name}.azure-devices.net`.
* **device_id**: cihazı kaydettirdiğiniz sırada verdiğiniz kimlik.
* **identity_cert**: cihazdaki bir kimlik sertifikasının URI 'si, örneğin: `file:///path/identity_certificate.pem` . Veya, sertifikayı EST veya yerel bir sertifika yetkilisi kullanarak dinamik olarak verme.
* **identity_pk**: belirtilen kimlik sertifikası için özel anahtar dosyasının URI 'si, örneğin: `file:///path/identity_key.pem` . Ya da bir PKCS # 11 URI 'SI girip yapılandırma bilgilerinizi daha sonra yapılandırma dosyasında **PKCS # 11** bölümünde belirtin.

Dosyayı kaydedin ve kapatın.

   `CTRL + X`, `Y`, `Enter`

Yapılandırma dosyasına sağlama bilgilerini girdikten sonra, değişikliklerinizi uygulayın:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Başarılı yapılandırmayı doğrula

Çalışma zamanının IoT Edge cihazınıza başarıyla yüklenip yapılandırıldığını doğrulayın.

>[!TIP]
>`iotedge` komutlarını çalıştırmak için yükseltilmiş ayrıcalıklara ihtiyacınız olacaktır. Makinenizdeki oturumu kapattıktan sonra IoT Edge çalışma zamanını yükleyip oturum açtığınızda izinleriniz otomatik olarak güncelleştirilir. Bundan sonra `sudo` komutların önünde kullanın.

IoT Edge sistem hizmetinin çalışıp çalışmadığını denetleyin.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Başarılı bir durum yanıtı `Ok` .

::: moniker-end

Hizmetle ilgili sorunları gidermeniz gerekirse hizmet günlüklerini alın.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

`check`Aygıtın yapılandırma ve bağlantı durumunu doğrulamak için aracını kullanın.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>`sudo`İzinleriniz güncelleştirildikten sonra bile denetim aracını çalıştırmak için her zaman kullanın. Aracın yapılandırma durumunu doğrulamak için yapılandırma dosyasına erişmesi için yükseltilmiş ayrıcalıklara ihtiyacı vardır.

IoT Edge cihazınızda çalışan tüm modülleri görüntüleyin. Hizmet ilk kez başladığında yalnızca **Edgeagent** modülünü çalışır duruma gelmelidir. EdgeAgent modülü varsayılan olarak çalışır ve cihazınıza dağıttığınız ek modüllerin yüklenmesini ve başlamasını sağlar.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Çevrimdışı veya belirli sürümü yükleme (isteğe bağlı)

Bu bölümdeki adımlar, standart yükleme adımlarının kapsamadığı senaryolar içindir. Bu şunlar olabilir:

* Çevrimdışıyken IoT Edge yüklemesi
* Sürüm Adayı sürümü yüklemesi

Tarafından kullanılamayan Azure IoT Edge çalışma zamanının belirli bir sürümünü yüklemek istiyorsanız bu bölümdeki adımları kullanın `apt-get install` . Microsoft paket listesi yalnızca sınırlı sayıda yeni sürümü ve alt sürümlerini içerir, bu nedenle bu adımlar daha eski bir sürüm veya sürüm adayı sürümü yüklemek isteyen herkese yöneliktir.

Kıvrımlı komutlarını kullanarak bileşen dosyalarını doğrudan IoT Edge GitHub deposundan hedefleyebilirsiniz.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge yayınlarına](https://github.com/Azure/azure-iotedge/releases)gidin ve hedeflemek istediğiniz yayın sürümünü bulun.

2. Bu sürümün **varlıklar** bölümünü genişletin.

3. Her yayında IoT Edge güvenlik Daemon ve hsmlib için yeni dosyalar olmalıdır. Çevrimdışı bir cihaza IoT Edge yükleyecekseniz bu dosyaları daha önce indirin. Aksi takdirde, bu bileşenleri güncelleştirmek için aşağıdaki komutları kullanın.

   1. IoT Edge cihazınızın mimarisiyle eşleşen **libiothsm-STD** dosyasını bulun. Dosya bağlantısına sağ tıklayıp bağlantı adresini kopyalayın.

   2. Hsmlib 'in bu sürümünü yüklemek için aşağıdaki komutta bulunan kopyalanmış bağlantıyı kullanın:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge cihazınızın mimarisiyle eşleşen **iotedge** dosyasını bulun. Dosya bağlantısına sağ tıklayıp bağlantı adresini kopyalayın.

   4. IoT Edge güvenlik arka plan programının bu sürümünü yüklemek için aşağıdaki komutta bulunan kopyalanmış bağlantıyı kullanın.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Cihazınız şu anda IoT Edge sürüm 1,1 veya daha eski sürümlerde çalışıyorsa, bu bölümdeki adımları izleyerek önce **ıotedge** ve **libiothsm-STD** paketlerini kaldırın. Daha fazla bilgi için bkz. [güncelleştirme 1,0 veya 1,1 ile 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. [Azure IoT Edge yayınlarına](https://github.com/Azure/azure-iotedge/releases)gidin ve hedeflemek istediğiniz yayın sürümünü bulun.

2. Bu sürümün **varlıklar** bölümünü genişletin.

3. Her sürüm IoT Edge ve kimlik hizmeti için yeni dosyalara sahip olmalıdır. Çevrimdışı bir cihaza IoT Edge yükleyecekseniz bu dosyaları daha önce indirin. Aksi takdirde, bu bileşenleri güncelleştirmek için aşağıdaki komutları kullanın.

   1. IoT Edge cihazınızın mimarisiyle eşleşen **azıot-Identity-Service** dosyasını bulun. Dosya bağlantısına sağ tıklayıp bağlantı adresini kopyalayın.

   2. Kimlik hizmetinin bu sürümünü yüklemek için aşağıdaki komutta bulunan kopyalanmış bağlantıyı kullanın:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. IoT Edge cihazınızın mimarisiyle eşleşen **azıot Edge** dosyasını bulun. Dosya bağlantısına sağ tıklayıp bağlantı adresini kopyalayın.

   4. Bu IoT Edge sürümünü yüklemek için aşağıdaki komutta bulunan kopyalanmış bağlantıyı kullanın.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Artık kapsayıcı altyapısı ve IoT Edge çalışma zamanı cihazınızda yüklü olduğuna göre, [cihazı bulut kimliğiyle sağlamak](#provision-the-device-with-its-cloud-identity)için bir sonraki adıma hazırsınız demektir.

## <a name="uninstall-iot-edge"></a>IoT Edge kaldır

IoT Edge yüklemesini cihazınızdan kaldırmak istiyorsanız aşağıdaki komutları kullanın.

IoT Edge çalışma zamanını kaldırın.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

`--purge`Yapılandırma dosyalarınız dahil olmak üzere IoT Edge ile ilişkili tüm dosyaları silmek istiyorsanız bayrağını kullanın. IoT Edge yeniden yüklemek ve gelecekte aynı yapılandırma bilgilerini kullanmak istiyorsanız bu bayrağı bırakın.

IoT Edge çalışma zamanı kaldırıldığında, oluşturduğu kapsayıcılar durdurulur ancak cihazınızda hala bulunur. Hangi olanların kaldığını görmek için tüm kapsayıcıları görüntüleyin.

```bash
sudo docker ps -a
```

İki çalışma zamanı kapsayıcısı dahil olmak üzere cihazınızdaki kapsayıcıları silin.

```bash
sudo docker rm -f <container name>
```

Son olarak, kapsayıcı çalışma zamanını cihazınızdan kaldırın.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Sonraki adımlar

Modülleri cihazınıza dağıtmayı öğrenmek için [IoT Edge modüllerini dağıtmaya](how-to-deploy-modules-portal.md) devam edin.
