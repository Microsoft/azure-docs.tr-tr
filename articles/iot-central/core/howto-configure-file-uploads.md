---
title: Dosyaları cihazlardan Azure depolama 'ya yükleme | Microsoft Docs
description: Cihazlarınızdan buluta dosya yükleme işlemlerini yapılandırma. Karşıya dosya yükleme yapılandırdıktan sonra, cihazlarınıza dosya yükleme uygulayın.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796916"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Cihazlarınızdan buluta dosya yükleme

*Bu konu, Yöneticiler ve cihaz geliştiricileri için geçerlidir.*

IoT Central, bağlı cihazlardan bulut depolamaya medya ve diğer dosyaları karşıya yüklemenize olanak sağlar. IoT Central uygulamanızda karşıya dosya yükleme özelliğini yapılandırın ve ardından cihaz kodunuzda dosya karşıya yüklemelerini uygulayın.

## <a name="prerequisites"></a>Önkoşullar

Dosya yüklemelerini yapılandırmak için IoT Central uygulamanızda bir yönetici olmanız gerekir.

Karşıya yüklenen dosyaları depolamak için bir Azure depolama hesabınızın ve kapsayıcısının olması gerekir. Mevcut bir depolama hesabınız ve kullanmak üzere Kapsayıcınız yoksa, [Azure Portal yeni bir depolama hesabı](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)oluşturun.

## <a name="configure-device-file-uploads"></a>Cihaz dosya yüklemelerini yapılandırma

Cihaz dosya yüklemelerini yapılandırmak için:

1. Uygulamanızdaki **Yönetim** bölümüne gidin.

1. **Cihaz dosya yükleme**' yi seçin.

1. Kullanılacak depolama hesabını ve kapsayıcıyı seçin. Depolama hesabı, uygulamanızdan farklı bir Azure aboneliğinden farklıysa, bir depolama hesabı bağlantı dizesi girin.

1. Gerekirse, karşıya yükleme isteğinin ne kadar süreyle geçerli kalacağını ayarlayan karşıya yükleme zaman aşımını ayarlayın. Geçerli değerler 1 ile 24 saat arasında.

1. **Kaydet**’i seçin. Durum **yapılandırıldığında**, cihazlardan dosya yüklemeye hazırsınız demektir.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Uygulamada karşıya dosya yüklemeyi yapılandırma":::

## <a name="disable-device-file-uploads"></a>Cihaz dosya yüklemelerini devre dışı bırak

IoT Central uygulamanıza cihaz dosya yüklemelerini devre dışı bırakmak istiyorsanız:

1. Uygulamanızdaki **Yönetim** bölümüne gidin.

1. **Cihaz dosya yükleme**' yi seçin.

1. **Sil**’i seçin.

## <a name="upload-a-file-from-a-device"></a>Cihazdan dosya yükleme

IoT Central, cihazların dosyaları karşıya yüklemesine olanak tanımak için IoT Hub dosya yükleme özelliğini kullanır. Bir cihazdan dosya yükleme işleminin nasıl yapılacağını gösteren örnek kod için [IoT Central dosya yükleme cihazı örneği](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

IoT Central cihaz dosyası karşıya yüklemelerinin nasıl yapılandırılacağını ve uygulanacağını öğrenmiş olduğunuza göre, bir sonraki adım daha fazla cihaz dosyası karşıya yüklemesi öğrenmelidir:

- [IoT Hub (.NET) ile cihazınızdan buluta dosya yükleme](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [IoT Hub (Java) ile cihazınızdan buluta dosya yükleme](../../iot-hub/iot-hub-java-java-file-upload.md)
- [IoT Hub (Node.js) ile cihazınızdan buluta dosya yükleme](../../iot-hub/iot-hub-node-node-file-upload.md)
- [IoT Hub (Python) ile cihazınızdan buluta dosya yükleme](../../iot-hub/iot-hub-python-python-file-upload.md)