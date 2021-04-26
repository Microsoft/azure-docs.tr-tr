---
title: "Hızlı başlangıç: IoT mikro Aracısı (Önizleme) için Defender 'ı yükler"
description: Bu hızlı başlangıçta, Defender Micro Agent 'ı yüklemeyi ve kimlik doğrulamasını öğrenin.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: a153b640a1d1e86f9b761817d05fda7d3e47da98
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384417"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Hızlı başlangıç: IoT mikro Aracısı (Önizleme) için Defender 'ı yükler

Bu makalede, Defender mikro aracısının nasıl yükleneceğine ve kimlik doğrulamasının nasıl yapılacağı hakkında bir açıklama sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

IoT modülünü için Defender 'ı yüklemeden önce IoT Hub bir modül kimliği oluşturmanız gerekir. Modül kimliği oluşturma hakkında daha fazla bilgi için bkz. [Defender IoT mikro aracı modülü oluşturma ikizi (Önizleme)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Paketi yükler

[Bu yönergeleri](/windows-server/administration/linux-package-repository-for-microsoft-software)izleyerek Microsoft paket deposunu yükleyip yapılandırın. 

DEMIN 9 ' da, yönergeler eklenmesi gereken depoyu içermez, depoyu eklemek için aşağıdaki komutları kullanın: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Defender Micro Agent paketini debir ve Ubuntu tabanlı Linux dağıtımlarına yüklemek için aşağıdaki komutu kullanın:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Mikro aracı kimlik doğrulama yöntemleri 

IoT mikro aracısının Defender kimliğini doğrulamak için kullanılan iki seçenek şunlardır: 

- Modül kimliği bağlantı dizesi. 

- Sertifika.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Modül kimlik bağlantı dizesi kullanarak kimlik doğrulama

Bu makaleye yönelik [önkoşulların](#prerequisites) karşılandığından ve bu adımları başlatmadan önce bir modül kimliği oluşturduğunuzdan emin olun. 

#### <a name="get-the-module-identity-connection-string"></a>Modül kimlik bağlantı dizesini al

IoT Hub modül kimliği bağlantı dizesini almak için: 

1. IoT Hub gidin ve hub 'ınızı seçin.

1. Sol taraftaki menüde, **araştırma yapanlar** bölümünde **IoT cihazları**' nı seçin.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Sol taraftaki menüden IoT cihazları ' nı seçin.":::

1. Cihaz **ayrıntıları** sayfasını görüntülemek IÇIN cihaz kimliği listesinden bir cihaz seçin.

1.  **Modül kimlikleri**   sekmesini seçin ve ardından cihazla ilişkili modül kimlikleri listesinden **Savunmiotmicroagent**   modülünü seçin.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Modül kimlikleri sekmesini seçin.":::

1. **Modül kimlik ayrıntıları** sayfasında, **Kopyala** düğmesini seçerek birincil anahtarı kopyalayın.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Birincil anahtarı kopyalamak için Kopyala düğmesini seçin.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Modül kimlik bağlantı dizesi kullanarak kimlik doğrulamasını yapılandırma

Aracıyı bir modül kimliği bağlantı dizesi kullanarak kimlik doğrulaması yapacak şekilde yapılandırmak için:

1. `connection_string.txt`Aşağıdaki komutu girerek Defender aracı dizin yolunda UTF-8 ile kodlanmış bağlantı dizesini içeren adlı bir dosya yerleştirin `/var/defender_iot_micro_agent` :

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    , `connection_string.txt` Aşağıdaki yol konumunda bulunmalıdır `/var/defender_iot_micro_agent/connection_string.txt` .

1. Bu komutu kullanarak hizmeti yeniden başlatın:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Sertifika kullanarak kimlik doğrulama

Sertifika kullanarak kimlik doğrulaması yapmak için:

1. [Bu yönergeleri](../iot-hub/iot-hub-security-x509-get-started.md)izleyerek bir sertifikayı temin edin.

1. Sertifikanın PEMENCODED ortak kısmını ve özel anahtarı, içindeki Defender Agent dizinine ve içindeki adlı dosyasına yerleştirin `certificate_public.pem` `certificate_private.pem` . 

1. İlgili bağlantı dizesini `connection_string.txt` dosyasına yerleştirin. bağlantı dizesi şuna benzemelidir: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Bu dize, kimlik doğrulaması için bir sertifikanın sağlanması beklendiğinde Defender Agent 'ı uyarır. 

1. Aşağıdaki komutu kullanarak hizmeti yeniden başlatın:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Yüklemenizi doğrulama

Yüklemenizi doğrulamak için:

1. Mikro aracısının aşağıdaki komutla düzgün çalıştığından emin olun:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Hizmetin kararlı olduğundan `active` ve işlemin çalışma süresinin uygun olduğundan emin olun

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Hizmetinizin kararlı ve etkin olduğundan emin olun.":::
 
## <a name="testing-the-system-end-to-end"></a>Sistemi uçtan uca test etme 

Cihazda bir tetikleyici dosyası oluşturarak sistemi uçtan uca test edebilirsiniz. Tetikleyici dosyası, aracıdaki ana hat taramasının dosyayı temel ihlal olarak algılamasına neden olur. 

Aşağıdaki komutla dosya sisteminde bir dosya oluşturun:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Hub 'da bir temel doğrulama hatası önerisi, `CceId` CIS-de,-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0 ile gerçekleşir: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Hub 'da oluşan taban çizgisi doğrulama hatası önerisi." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Önerinin hub 'da görünmesi için bir saate kadar bekleyin. 

## <a name="micro-agent-versioning"></a>Mikro aracı sürümü oluşturma 

Defender IoT mikro aracısının belirli bir sürümünü yüklemek için şu komutu çalıştırın: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kaynak kodundan Defender Micro Agent oluşturma](quickstart-building-the-defender-micro-agent-from-source.md)
