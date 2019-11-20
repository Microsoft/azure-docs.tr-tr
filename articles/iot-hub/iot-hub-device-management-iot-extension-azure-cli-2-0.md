---
title: Azure CLı için IoT Uzantısı ile Azure IoT cihaz yönetimi | Microsoft Docs
description: Doğrudan Yöntemler ve Ikizi 'ın istenen özellikler yönetim seçeneklerine sahip Azure IoT Hub cihaz yönetimi için Azure CLı aracı IoT uzantısı ' nı kullanın.
author: chrissie926
manager: ''
keywords: Azure IoT cihaz yönetimi, Azure IoT Hub cihaz yönetimi, cihaz yönetimi IoT, IoT Hub cihaz yönetimi
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 93efd6e53470fb78bb6d823652437e7a37c33732
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640563"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Azure için Azure CLı IoT Hub cihaz yönetimi için IoT uzantısını kullanma

![Uçtan uca diyagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) , [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)'nin özelliklerine ekleyen yeni bir açık kaynaklı IoT uzantısıdır. Azure CLı, Azure Resource Manager ve yönetim uç noktalarıyla etkileşime yönelik komutlar içerir. Örneğin, Azure CLı kullanarak bir Azure VM veya IoT Hub 'ı oluşturabilirsiniz. CLı uzantısı, Azure hizmeti 'nin Azure CLı 'yi genişletmesine olanak tanıyarak, hizmete özgü ek yeteneklere erişmenizi sağlar. IoT uzantısı, IoT geliştiricilerine tüm IoT Hub, IoT Edge ve IoT Hub cihaz sağlama hizmeti özelliklerine yönelik komut satırı erişimi sağlar.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Yönetim seçeneği          | Görev  |
|----------------------------|-----------|
| Doğrudan yöntemler             | İleti göndermeyi başlatma veya durdurma ya da cihazı yeniden başlatma gibi bir cihaz oluşturun.                                        |
| İkizi istenen özellikler    | Bir cihazı, bir ışığı yeşil olarak ayarlama veya telemetri gönderme aralığını 30 dakikaya ayarlama gibi belirli durumlara yerleştirin.         |
| İkizi bildirilen özellikler   | Bir cihazın bildirilen durumunu alır. Örneğin, cihaz, LED 'in Şu anda yanıp söndüğünü bildiriyor.                                    |
| İkizi etiketleri                  | Cihaza özel meta verileri bulutta depolayın. Örneğin, bir havalandırma makinesinin dağıtım konumu.                         |
| Cihaz ikizi sorguları        | Bu TWINS 'i, kullanıma açık olan cihazları tanımlama gibi rastgele koşullarla almak için tüm cihaz iklerini sorgulayın. |

Bu seçenekleri kullanmayla ilgili farklılıklar ve yönergeler hakkında daha ayrıntılı bilgi için bkz. [cihazdan buluta iletişim Kılavuzu](iot-hub-devguide-d2c-guidance.md) ve [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md).

Cihaz çiftleri, cihaz durumu bilgilerini (meta veriler, yapılandırmalar ve koşullar) depolayan JSON belgelerdir. IoT Hub, kendisine bağlanan her cihaz için bir cihaz ikizi devam ettirir. Cihaz TWINS hakkında daha fazla bilgi için bkz. [cihaz ikgörülerle çalışmaya başlama](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Öğrenecekleriniz

Geliştirme makinenizde çeşitli yönetim seçenekleriyle Azure CLı için IoT uzantısını kullanmayı öğrenirsiniz.

## <a name="what-you-do"></a>Yapabilecekleriniz

Azure CLI ve IoT uzantısını çeşitli yönetim seçenekleriyle çalıştırın.

## <a name="what-you-need"></a>Ne gerekiyor

* [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun; Örneğin, [Node. js Ile Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md). Bu öğeler aşağıdaki gereksinimleri kapsar:

  - Etkin bir Azure aboneliği.
  - Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.
  - Azure IoT Hub 'ınıza ileti gönderen bir istemci uygulaması.

* Bu öğretici sırasında cihazınızın istemci uygulamasıyla çalıştığından emin olun.

* [Python 2.7x veya Python 3.x](https://www.python.org/downloads/)

<!-- I'm not sure we need all this info, so comment out this include for now. Robin 7.26.2019
[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)] -->

* Azure CLı. Yüklemeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). En az 2.0.24 Azure CLI sürümünüzü olmalıdır veya üzeri. Doğrulamak için `az –version` kullanın.

* IoT uzantısını yükler. En basit yol `az extension add --name azure-cli-iot-ext` komutunu çalıştırmaktır. [IoT uzantısı benioku](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) dosyası, uzantıyı yüklemenin birkaç yolunu açıklar.

## <a name="sign-in-to-your-azure-account"></a>Azure hesabınızda oturum açma

Aşağıdaki komutu çalıştırarak Azure hesabınızda oturum açın:

```bash
az login
```

## <a name="direct-methods"></a>Doğrudan yöntemler

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Cihaz ikizi istenen özellikleri

Şu komutu çalıştırarak istenen bir özellik aralığı = 3000 ayarlayın:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Bu özellik cihazınızdan okunabilir.

## <a name="device-twin-reported-properties"></a>Cihaz ikizi bildirilen özellikler

Aşağıdaki komutu çalıştırarak cihazın bildirilen özelliklerini alın:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

İkizi tarafından bildirilen özelliklerden biri $metadata. $lastUpdated, bu, cihaz uygulamasının bildirilen özellik kümesini en son güncelleştirdiği zamanı gösterir.

## <a name="device-twin-tags"></a>Cihaz ikizi etiketleri

Aşağıdaki komutu çalıştırarak cihazın etiketlerini ve özelliklerini görüntüleyin:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Aşağıdaki komutu çalıştırarak cihaza bir alan rolü = sıcaklık & nem ekleyin:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Cihaz ikizi sorguları

Aşağıdaki komutu çalıştırarak cihazları rol = ' sıcaklık & nem ' etiketiyle sorgulayın:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Aşağıdaki komutu çalıştırarak, rol = ' sıcaklık & nem ' olan Etiketler hariç tüm cihazları sorgulayın:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Sonraki adımlar

IoT cihazınız ile Azure IoT Hub arasında cihazdan buluta iletileri izlemeyi ve buluttan cihaza iletiler göndermenizi öğrendiniz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
