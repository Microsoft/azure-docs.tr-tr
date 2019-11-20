---
title: Azure IoT Central gezginini kullanarak cihaz bağlantısını izleme
description: Cihaz iletilerini izleyin ve IoT Central Explorer CLı aracılığıyla cihaz ikizi değişikliklerini gözlemleyin.
author: viv-liu
ms.author: viviali
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 2b1c4c64fc02df67f38e36194072efd5db3b8e38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895702"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Azure CLı kullanarak cihaz bağlantısını izleme (Önizleme özellikleri)

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

Cihazlarınızın IoT Central gönderdiği iletileri görmek ve cihaz ikizi değişiklikleri gözlemlemek için Azure CLı IoT uzantısını kullanın. Bu aracı, cihaz bağlantısını hata ayıklama ve gözlemlemek ve ikizi değişikliklerine yanıt vermeyen buluta veya cihazlara ulaşmayan cihaz iletilerinin sorunlarını tanılamak için kullanabilirsiniz.

[Daha fazla bilgi için Azure CLı uzantıları başvurusunu ziyaret edin](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Ön koşullar

+ Azure CLı yüklü ve sürüm 2.0.7 veya üzeri. `az --version`çalıştırarak Azure CLı 'nizin sürümünü denetleyin. [Azure CLI belgelerinden](https://docs.microsoft.com/cli/azure/install-azure-cli) yüklemeyi ve güncelleştirmeyi öğrenin
+ Azure 'da bir IoT Central uygulamasına kullanıcı olarak eklenen bir iş veya okul hesabı.

## <a name="install-the-iot-central-extension"></a>IoT Central uzantısını yükler

Yüklemek için komut satırınızdan aşağıdaki komutu çalıştırın:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

' İ çalıştırarak uzantının sürümünü denetleyin 
```cmd/sh
az --version
```
Azure-cli-IoT-ext uzantısının 0.8.1 veya üzeri olduğunu görmeniz gerekir. Değilse, Çalıştır
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Uzantıyı kullanma

Aşağıdaki bölümlerde, `az iot central`çalıştırdığınızda kullanabileceğiniz ortak komutlar ve seçenekler açıklanır. Komutların ve seçeneklerin tam kümesini görüntülemek için `--help` `az iot central` veya alt komutlarının birine geçirin.

### <a name="login"></a>Oturum Aç

Azure CLı 'de oturum açarak başlayın. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central uygulamanızın uygulama KIMLIĞINI alın
**Yönetim/uygulama ayarları**' nda, **uygulama kimliği**' ni kopyalayın. Bu, sonraki adımlarda kullanacaksınız.

### <a name="monitor-messages"></a>İletileri izleme
Cihazlarınızdan IoT Central uygulamanıza gönderilen iletileri izleyin. Bu, tüm üst bilgileri ve ek açıklamaları içerir.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Cihaz özelliklerini görüntüle
Belirli bir cihaz için geçerli okuma ve okuma/yazma cihaz özelliklerini görüntüleyin.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Central gezgin 'i nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [cihazları yönetmeyi IoT Central](howto-manage-devices.md)keşfetmeye yönelik olarak sunulmuştur.
