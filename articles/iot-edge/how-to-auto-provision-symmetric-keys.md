---
title: Simetrik anahtar kanıtlama kullanarak cihazları DPS ile otomatik olarak Sağlama-Azure IoT Edge | Microsoft Docs
description: Cihaz sağlama hizmeti ile Azure IoT Edge için otomatik cihaz sağlamayı test etmek için simetrik anahtar kanıtlama kullanın
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 53b1abca25119f4168aaf12a66c4347c53ed0a62
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828071"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Simetrik anahtar kanıtlama kullanarak bir IoT Edge cihazı oluşturma ve sağlama

Azure IoT Edge cihazlar, yalnızca Edge özellikli olmayan cihazlarda olduğu gibi [cihaz sağlama hizmeti](../iot-dps/index.yml) kullanılarak otomatik temin edilebilir. Otomatik sağlama işlemini tanımıyorsanız, devam etmeden önce [Otomatik sağlama kavramlarını](../iot-dps/concepts-auto-provisioning.md) gözden geçirin.

Bu makalede, aşağıdaki adımlarla bir IoT Edge cihazında simetrik anahtar kanıtlama kullanarak bir cihaz sağlama hizmeti bireysel kaydı oluşturma işlemi gösterilmektedir:

* IoT Hub cihaz sağlama hizmeti 'nin (DPS) bir örneğini oluşturun.
* Cihaz için tek bir kayıt oluşturun.
* IoT Edge çalışma zamanını yükleyip IoT Hub bağlayın.

Simetrik anahtar kanıtlama, cihaz sağlama hizmeti örneğiyle bir cihazın kimliğini doğrulamaya yönelik basit bir yaklaşımdır. Bu kanıtlama yöntemi, cihaz sağlama için yeni olan veya katı güvenlik gereksinimleri olmayan geliştiriciler için bir "Hello World" deneyimini temsil eder. [TPM](../iot-dps/concepts-tpm-attestation.md) veya [X. 509.440 sertifikaları](../iot-dps/concepts-security.md#x509-certificates) kullanan cihaz kanıtlama daha güvenlidir ve daha sıkı güvenlik gereksinimleri için kullanılmalıdır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir IoT Hub
* Fiziksel veya sanal cihaz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub cihaz sağlama hizmetini ayarlama

Azure 'da IoT Hub cihaz sağlama hizmetinin yeni bir örneğini oluşturun ve IoT Hub 'ınıza bağlayın. [IoT Hub DPS 'Yi ayarlama](../iot-dps/quick-setup-auto-provision.md)bölümündeki yönergeleri izleyebilirsiniz.

Cihaz sağlama hizmetini çalıştırdıktan sonra, genel bakış sayfasından **kimlik kapsamının** değerini kopyalayın. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Cihaz için benzersiz bir kayıt KIMLIĞI seçin

Her bir cihazı tanımlamak için benzersiz bir kayıt KIMLIĞI tanımlanmalıdır. MAC adresi, seri numarası veya cihazdan herhangi bir benzersiz bilgi kullanabilirsiniz.

Bu örnekte, bir kayıt KIMLIĞI için aşağıdaki dizeyi oluşturan bir MAC adresi ve seri numarası birleşimini kullanırız.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Cihazınız için benzersiz bir kayıt KIMLIĞI oluşturun. Geçerli karakterler küçük harfli alfasayısal ve tire ('-').

## <a name="create-a-dps-enrollment"></a>Bir DPS kaydı oluşturma

DPS 'de tek bir kayıt oluşturmak için cihazınızın kayıt KIMLIĞINI kullanın.

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu**bildirme fırsatına sahip olursunuz. Device ikizi 'da, bir cihaz için gereken bölge, ortam, konum veya cihaz türü gibi herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-monitor.md)oluşturmak için kullanılır.

> [!TIP]
> Simetrik anahtar kanıtlama kullanılırken grup kayıtları da mümkündür ve bireysel kayıtlar ile aynı kararları içerir.

1. [Azure Portal](https://portal.azure.com), IoT Hub cihaz sağlama hizmeti örneğinize gidin.

1. **Ayarlar**altında kayıtları **Yönet**' i seçin.

1. **Bireysel kayıt Ekle** ' yi seçin, ardından kaydı yapılandırmak için aşağıdaki adımları izleyin:  

   1. **Mekanizma**Için **simetrik anahtar**' ı seçin.

   1. **Anahtarları otomatik oluştur** onay kutusunu seçin.

   1. Cihazınız için oluşturduğunuz **kayıt kimliğini** belirtin.

   1. İsterseniz cihazınız için bir **IoT Hub CIHAZ kimliği** sağlayın. Modül dağıtımı için tek bir cihazı hedeflemek üzere cihaz kimliklerini kullanabilirsiniz. Bir cihaz KIMLIĞI sağlamazsanız, kayıt KIMLIĞI kullanılır.

   1. Kaydın IoT Edge bir cihaz için olduğunu bildirmek için **true** ' ı seçin. Bir grup kaydı için tüm cihazların IoT Edge cihaz olması veya hiçbirinin olmaması gerekir.

   1. **Cihazları hub 'lara atamak** istediğiniz veya bu kayda özgü farklı bir değer seçebileceğiniz cihaz sağlama hizmeti 'nin ayırma ilkesinden varsayılan değeri kabul edin.

   1. Cihazınızı bağlamak istediğiniz bağlı **IoT Hub** seçin. Birden çok hub seçebilirsiniz ve bu cihaz, seçilen ayırma ilkesine göre bu cihazdan birine atanır.

   1. Cihazların ilk kez sağlama istemesi durumunda **cihaz verilerinin yeniden hazırlanması için nasıl işleneceğini** seçin.

   1. İsterseniz **Ilk cihaz Ikizi durumuna** bir etiket değeri ekleyin. Modül dağıtımı için cihaz gruplarını hedeflemek üzere etiketleri kullanabilirsiniz. Örnek:

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

   1. **Enable girişinin** **Enable**olarak ayarlandığından emin olun.

   1. **Kaydet**’i seçin.

Bu cihaz için bir kayıt mevcut olduğuna göre, IoT Edge çalışma zamanı cihazı yükleme sırasında otomatik olarak sağlayabilir. IoT Edge çalışma zamanını yüklerken veya bir grup kaydıyla kullanmak üzere cihaz anahtarları oluşturmaya devam ediyorsanız, kaydınızın **birincil anahtar** değerini kopyalamayı unutmayın.

## <a name="derive-a-device-key"></a>Bir cihaz anahtarı türet

> [!NOTE]
> Bu bölüm yalnızca bir grup kaydı kullanılıyorsa gereklidir.

Her cihaz, sağlama sırasında kayıt ile simetrik anahtar kanıtlama gerçekleştirmek için kendi türetilmiş Cihaz anahtarını benzersiz kayıt KIMLIĞINIZLE kullanır. Cihaz anahtarını oluşturmak için, DPS kaydınızdan kopyaladığınız anahtarı kullanarak cihazın benzersiz kayıt KIMLIĞI için [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ' ı hesaplamanız ve sonucu base64 biçimine dönüştürmeniz gerekir.

Kayıt kodunuzun birincil veya ikincil anahtarını cihaz kodunuza eklemeyin.

### <a name="linux-workstations"></a>Linux iş istasyonları

Bir Linux iş istasyonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, türetilen cihaz anahtarınızı oluşturmak için OpenSSL kullanabilirsiniz.

**Anahtarın** değerini, daha önce not ettiğiniz **birincil anahtarla** değiştirin.

**REG_ID** değerini CIHAZıNıZıN kayıt kimliğiyle değiştirin.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows tabanlı iş istasyonları

Windows tabanlı bir iş istasyonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, türetilmiş cihaz anahtarınızı oluşturmak için PowerShell kullanabilirsiniz.

**Anahtarın** değerini, daha önce not ettiğiniz **birincil anahtarla** değiştirin.

**REG_ID** değerini CIHAZıNıZıN kayıt kimliğiyle değiştirin.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükler

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Bileşenleri kapsayıcılarda çalıştırılır ve kenarda kod çalıştırabilmeniz için cihaza ek kapsayıcılar dağıtmanıza izin verir.

Cihazınızı sağlarken aşağıdaki bilgilere sahip olmanız gerekir:

* DPS **kimlik kapsamı** değeri
* Oluşturduğunuz cihaz **kayıt kimliği**
* DPS kaydından kopyaladığınız **birincil anahtar**

> [!TIP]
> Grup kayıtları için, DPS kayıt anahtarı yerine her bir cihazın [türetilmiş anahtarı](#derive-a-device-key) gerekir.

### <a name="linux-device"></a>Linux cihazı

Cihazınızın mimarisine yönelik yönergeleri izleyin. IoT Edge çalışma zamanını otomatik, el ile değil, sağlama için yapılandırdığınızdan emin olun.

[Linux üzerinde Azure IoT Edge çalışma zamanını yükler](how-to-install-iot-edge-linux.md)

Simetrik anahtar sağlama yapılandırma dosyasındaki bölümü şöyle görünür:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

@No__t-0, `{registration_id}` ve `{symmetric_key}` için yer tutucu değerlerini daha önce topladığınız verilerle değiştirin.

### <a name="windows-device"></a>Windows cihazı

IoT Edge çalışma zamanını, türetilmiş bir cihaz anahtarı oluşturduğunuz cihaza yükleyebilirsiniz. IoT Edge çalışma zamanını otomatik, el ile değil, sağlama için yapılandıracaksınız.

Windows üzerinde IoT Edge yükleme hakkında daha ayrıntılı bilgi için IoT Edge, bkz. [Azure IoT Edge çalışma zamanını Windows 'A yükleme](how-to-install-iot-edge-windows.md).

1. Yönetici modunda bir PowerShell penceresi açın. PowerShell (x86) değil IoT Edge yüklerken PowerShell 'in AMD64 oturumunu kullandığınızdan emin olun.

1. **Deploy-ıotedge** komutu, Windows makinenizin desteklenen bir sürümde olup olmadığını denetler, kapsayıcılar özelliğini açar ve ardından Moby çalışma zamanını ve IoT Edge çalışma zamanını indirir. Komut varsayılan olarak Windows kapsayıcıları ' nı kullanmaktır.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Bu noktada, IoT çekirdek cihazları otomatik olarak yeniden başlatılabilir. Diğer Windows 10 veya Windows Server cihazları yeniden başlatmanızı isteyebilir. Bu durumda cihazınızı şimdi yeniden başlatın. Cihazınız çalışmaya başladıktan sonra PowerShell 'i yönetici olarak yeniden çalıştırın.

1. **Initialize-ıotedge** komutu, makinenizde IoT Edge çalışma zamanını yapılandırır. Otomatik sağlamayı kullanmak için `-Dps` bayrağını kullanmadığınız takdirde, komut, Windows kapsayıcılarıyla el ile sağlamayı varsayılan olarak belirler.

   @No__t-0, `{registration_id}` ve `{symmetric_key}` için yer tutucu değerlerini daha önce topladığınız verilerle değiştirin.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Yüklemenin başarılı olduğunu doğrulama

Çalışma zamanı başarıyla başlatıldıysa, IoT Hub ve cihazınıza IoT Edge modülleri dağıtmaya başlayabilirsiniz. Çalışma zamanının başarıyla yüklendiğini ve başlatıldığını doğrulamak için cihazınızda aşağıdaki komutları kullanın.

### <a name="linux-device"></a>Linux cihazı

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

Cihaz sağlama hizmeti 'nde oluşturduğunuz bireysel kaydın kullanıldığını doğrulayabilirsiniz. Azure portal cihaz sağlama hizmeti örneğinize gidin. Oluşturduğunuz bireysel kayıt için kayıt ayrıntılarını açın. Kayıt durumunun **atandığını** ve cihaz kimliğinin listelendiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Cihaz sağlama hizmeti kayıt işlemi, yeni cihazı sağladığınız anda cihaz KIMLIĞI ve cihaz ikizi etiketlerini ayarlamanıza olanak sağlar. Bu değerleri, otomatik cihaz yönetimi kullanarak ayrı cihazları veya cihaz gruplarını hedeflemek için kullanabilirsiniz. Azure portal veya [Azure CLI kullanarak](how-to-deploy-monitor-cli.md) [IoT Edge modüllerini ölçekte nasıl dağıtacağınızı ve izleyeceğinizi](how-to-deploy-monitor.md) öğrenin.
