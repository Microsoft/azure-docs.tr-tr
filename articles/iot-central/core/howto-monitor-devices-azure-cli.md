---
title: Azure IoT Central gezginini kullanarak cihaz bağlantısını izleme
description: Cihaz iletilerini izleyin ve IoT Central Explorer CLı aracılığıyla cihaz ikizi değişikliklerini gözlemleyin.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871300"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Azure CLI'yı kullanarak cihaz bağlantısını izleme

*Bu konu, cihaz geliştiricileri ve çözüm oluşturucuları için geçerlidir.*

Cihazlarınızın IoT Central gönderdiği iletileri görmek ve cihaz ikizi değişiklikleri gözlemlemek için Azure CLı IoT uzantısını kullanın. Bu aracı, cihaz bağlantısını hata ayıklama ve gözlemlemek ve ikizi değişikliklerine yanıt vermeyen buluta veya cihazlara ulaşmayan cihaz iletilerinin sorunlarını tanılamak için kullanabilirsiniz.

[Daha fazla bilgi için Azure CLı uzantıları başvurusunu ziyaret edin](/cli/azure/iot/central)

## <a name="prerequisites"></a>Önkoşullar

+ Azure CLı yüklü ve sürüm 2.7.0 veya üzeri. Çalıştırarak Azure CLı 'nizin sürümünü denetleyin `az --version` . [Azure CLI belgelerinden](/cli/azure/install-azure-cli) yüklemeyi ve güncelleştirmeyi öğrenin
+ Azure 'da bir IoT Central uygulamasına kullanıcı olarak eklenen bir iş veya okul hesabı.

## <a name="install-the-iot-central-extension"></a>IoT Central uzantısını yükler

Yüklemek için komut satırınızdan aşağıdaki komutu çalıştırın:

```azurecli
az extension add --name azure-iot
```

Şu çalıştırarak uzantının sürümünü denetleyin:

```azurecli
az --version
```

Azure-IoT uzantısının 0.9.9 veya üzeri olduğunu görmeniz gerekir. Değilse, şunu çalıştırın:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Uzantıyı kullanma

Aşağıdaki bölümlerde, çalıştırdığınızda kullanabileceğiniz ortak komutlar ve seçenekler açıklanır `az iot central` . Komutların ve seçeneklerin tam kümesini görüntülemek için `--help` `az iot central` alt komutlarına veya herhangi birine geçiş yapın.

### <a name="login"></a>Oturum aç

Azure CLı 'de oturum açarak başlayın. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central uygulamanızın uygulama KIMLIĞINI alın
**Yönetim/uygulama ayarları**' nda, **uygulama kimliği**' ni kopyalayın. Bu değeri sonraki adımlarda kullanırsınız.

### <a name="monitor-messages"></a>İletileri izleme
Cihazlarınızdan IoT Central uygulamanıza gönderilen iletileri izleyin. Çıktı tüm üst bilgileri ve ek açıklamaları içerir.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Cihaz özelliklerini görüntüle
Belirli bir cihaz için geçerli okuma ve okuma/yazma cihaz özelliklerini görüntüleyin.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bir sonraki adım [Azure IoT Central cihaz bağlantısı](./concepts-get-connected.md)hakkında bilgi almanız gerekir.