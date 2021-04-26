---
title: DPS ile Windows cihazlarını otomatik olarak Sağlama-Azure IoT Edge | Microsoft Docs
description: Cihaz sağlama hizmeti ile Azure IoT Edge için otomatik cihaz sağlamayı test etmek üzere Windows makinenizde bir sanal cihaz kullanın
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482016"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Windows üzerinde sanal TPM ile sanal bir IoT Edge cihaz oluşturma ve sağlama

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge cihazlar, yalnızca Edge özellikli olmayan cihazlarda olduğu gibi [cihaz sağlama hizmeti](../iot-dps/index.yml) kullanılarak otomatik temin edilebilir. Otomatik sağlama işlemini tanımıyorsanız, devam etmeden önce [sağlamaya](../iot-dps/about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin.

DPS, hem bireysel kayıt hem de grup kaydında IoT Edge cihazlar için simetrik anahtar kanıtlamayı destekler. Grup kaydı için, simetrik anahtar kanıtlama içinde "IoT Edge cihaz" seçeneğini true olarak belirlerseniz, bu kayıt grubu altında kayıtlı tüm cihazlar IoT Edge cihaz olarak işaretlenir.

Bu makalede, aşağıdaki adımlarla sanal bir IoT Edge cihazında otomatik sağlamayı test etme işlemi gösterilmektedir:

* IoT Hub cihaz sağlama hizmeti 'nin (DPS) bir örneğini oluşturun.
* Donanım güvenliği için benzetimli Güvenilir Platform Modülü (TPM) ile Windows makinenizde sanal bir cihaz oluşturun.
* Cihaz için tek bir kayıt oluşturun.
* IoT Edge çalışma zamanını yükleyip cihazı IoT Hub bağlayın.

> [!TIP]
> Bu makalede, sanal cihazlarda TPM kanıtlaması kullanılarak otomatik sağlama testi açıklanır, ancak fiziksel TPM donanımı de kullanılırken bunun çoğu geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

* Bir Windows geliştirme makinesi. Bu makalede Windows 10 kullanılır.
* Etkin bir IoT Hub.

> [!NOTE]
> TPM kanıtlaması ile TPM kanıtlama kullanılırken TPM 2,0 gereklidir ve yalnızca bireysel, grup değil kayıtları oluşturmak için kullanılabilir.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub cihaz sağlama hizmetini ayarlama

Azure 'da IoT Hub cihaz sağlama hizmetinin yeni bir örneğini oluşturun ve IoT Hub 'ınıza bağlayın. [IoT Hub DPS 'Yi ayarlama](../iot-dps/quick-setup-auto-provision.md)bölümündeki yönergeleri izleyebilirsiniz.

Cihaz sağlama hizmetini çalıştırdıktan sonra, genel bakış sayfasından **kimlik kapsamının** değerini kopyalayın. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız.

> [!TIP]
> Fiziksel bir TPM cihazı kullanıyorsanız, her TPM yongası için benzersiz olan ve kendisiyle ilişkili TPM yonga üreticisinden elde edilen **onay anahtarını** belirlemeniz gerekir. TPM cihazınız için benzersiz bir **kayıt kimliği** türetebilirsiniz; Örneğin, onay anahtarının SHA-256 karmasını oluşturma.
>
> Kayıt bilgilerinizi DPS içinde oluşturmak için [Azure portalı ile cihaz kayıtlarını yönetme](../iot-dps/how-to-manage-enrollments.md) makalesindeki yönergeleri izleyin ve sonra devam etmek için bu makaledeki [IoT Edge çalışma zamanını Install](#install-the-iot-edge-runtime) bölümüne ilerleyin.

## <a name="simulate-a-tpm-device"></a>TPM cihazının benzetimini yapma

Windows geliştirme makinenizde sanal bir TPM cihazı oluşturun. Cihazınızın **kayıt kimliği** ve **onay anahtarını** alın ve DPS 'de tek bir kayıt girişi oluşturmak için bunları kullanın.

DPS 'de bir kayıt oluşturduğunuzda, bir **Ilk cihaz Ikizi durumu** bildirme fırsatına sahip olursunuz. Cihaz ikizi, çalışma, cihaz, konum veya cihaz türü gibi çözümünüzde ihtiyaç duyduğunuz herhangi bir ölçüme cihazları gruplamak için Etiketler ayarlayabilirsiniz. Bu Etiketler [otomatik dağıtımlar](how-to-deploy-at-scale.md)oluşturmak için kullanılır.

Sanal cihazı oluşturmak için kullanmak istediğiniz SDK dilini seçin ve bireysel kayıt oluşturana kadar adımları izleyin.

Tek bir kaydı oluşturduğunuzda, Windows geliştirme makinenizdeki sanal bir TPM cihazının **IoT Edge bir cihaz** olduğunu bildirmek için **true** ' ı seçin.

> [!TIP]
> Azure CLı 'da bir [kayıt](/cli/azure/iot/dps/enrollment) veya [kayıt grubu](/cli/azure/iot/dps/enrollment-group) oluşturabilir ve **kenar özellikli** bayrağını kullanarak bir cihazın veya cihaz grubunun IoT Edge bir cihaz olduğunu belirtebilirsiniz.

Sanal cihaz ve bireysel Kayıt Kılavuzu:

* [,](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Bireysel kaydı oluşturduktan sonra **kayıt kimliği** değerini kaydedin. IoT Edge çalışma zamanını yapılandırırken bu değeri kullanırsınız.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükleme

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Bileşenleri kapsayıcılarda çalıştırılır ve kenarda kod çalıştırabilmeniz için cihaza ek kapsayıcılar dağıtmanıza izin verir. IoT Edge çalışma zamanını, sanal TPM 'nin çalıştığı cihaza yükler.

[Azure IoT Edge çalışma zamanını yüklemek](how-to-install-iot-edge.md)için bu adımları izleyin ve sonra cihazı sağlamak için bu makaleye geri dönün.

> [!TIP]
> Yükleme ve test etme sırasında TPM simülatörünü çalıştıran pencereyi açık tutun.

## <a name="configure-the-device-with-provisioning-information"></a>Cihazı sağlama bilgileriyle yapılandırma

Çalışma zamanı cihazınıza yüklendikten sonra, cihazı cihaz sağlama hizmetine bağlanmak için kullandığı bilgilerle yapılandırın ve IoT Hub.

1. DPS **kimlik kapsamınızı** ve önceki bölümlerde toplanan CIHAZ **kayıt kimliğini** öğrenin.

1. Yönetici modunda bir PowerShell penceresi açın. PowerShell (x86) değil IoT Edge yüklerken PowerShell 'in AMD64 oturumunu kullandığınızdan emin olun.

1. **Deploy-ıotedge** komutu, Windows makinenizin desteklenen bir sürümde olup olmadığını denetler, kapsayıcılar özelliğini açar ve ardından Moby çalışma zamanını ve IoT Edge çalışma zamanını indirir. Komut varsayılan olarak Windows kapsayıcıları ' nı kullanmaktır.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Bu noktada, IoT çekirdek cihazları otomatik olarak yeniden başlatılabilir. Windows 10 veya Windows Server cihazları yeniden başlatmanızı isteyebilir. Bu durumda cihazınızı şimdi yeniden başlatın. Cihazınız çalışmaya başladıktan sonra PowerShell 'i yönetici olarak yeniden çalıştırın.

1. **Initialize-ıotedge** komutu, makinenizde IoT Edge çalışma zamanını yapılandırır. Komut, Windows kapsayıcıları ile el ile sağlamayı varsayılan olarak belirler. `-Dps`El ile sağlama yerine cihaz sağlama hizmetini kullanmak için bayrağını kullanın.

   Ve için yer tutucu değerlerini `{scope_id}` `{registration_id}` daha önce topladığınız verilerle değiştirin.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Yüklemenin başarılı olduğunu doğrulama

Çalışma zamanı başarıyla başlatıldıysa, IoT Hub ve cihazınıza IoT Edge modülleri dağıtmaya başlayabilirsiniz. Çalışma zamanının başarıyla yüklendiğini ve başlatıldığını doğrulamak için cihazınızda aşağıdaki komutları kullanın.  

IoT Edge hizmetinin durumunu kontrol edin.

```powershell
Get-Service iotedge
```

Son 5 dakikadan hizmet günlüklerini inceleyin.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Çalışan modülleri listeleyin.

```powershell
iotedge list
```

## <a name="next-steps"></a>Sonraki adımlar

Cihaz sağlama hizmeti kayıt işlemi, yeni cihazı sağladığınız anda cihaz KIMLIĞI ve cihaz ikizi etiketlerini ayarlamanıza olanak sağlar. Bu değerleri, otomatik cihaz yönetimi kullanarak ayrı cihazları veya cihaz gruplarını hedeflemek için kullanabilirsiniz. Azure portal veya [Azure CLI kullanarak](how-to-deploy-cli-at-scale.md) [IoT Edge modüllerini ölçekte nasıl dağıtacağınızı ve izleyeceğinizi](how-to-deploy-at-scale.md) öğrenin
