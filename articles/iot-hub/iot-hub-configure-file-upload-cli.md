---
title: Azure CLı kullanarak IoT Hub dosya yüklemeyi yapılandırma | Microsoft Docs
description: Platformlar arası Azure CLı kullanarak Azure IoT Hub dosya karşıya yüklemeleri yapılandırma.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: ea6ec30ad5f3b1cdbc906cc94cb211295b84e802
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761736"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Azure CLı kullanarak IoT Hub dosya yüklemelerini yapılandırma

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[Bir cihazdan dosya yüklemek](iot-hub-devguide-file-upload.md)Için öncelikle IoT Hub 'ınız Ile bir Azure depolama hesabı ilişkilendirmeniz gerekir. Var olan bir depolama hesabını kullanabilir veya yeni bir tane oluşturabilirsiniz.

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Etkin bir Azure hesabı. Hesabınız yoksa, yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

* [Azure CLI](/cli/azure/install-azure-cli).

* Azure IoT Hub 'ı. IoT Hub 'ınız yoksa, [portalı kullanarak bir IoT Hub](iot-hub-create-through-portal.md)'ı oluşturmak veya oluşturmak için [ `az iot hub create` komutunu](/cli/azure/iot/hub#az_iot_hub_create) kullanabilirsiniz.

* Azure Depolama hesabı. Azure depolama hesabınız yoksa, Azure CLı kullanarak bir tane oluşturabilirsiniz. Daha fazla bilgi için bkz. [Depolama hesabı oluşturma](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Oturum açın ve Azure hesabınızı ayarlayın

Azure hesabınızda oturum açın ve aboneliğinizi seçin.

1. Komut isteminde [oturum açma komutunu](/cli/azure/get-started-with-azure-cli) çalıştırın:

    ```azurecli
    az login
    ```

    Kodu kullanarak kimlik doğrulaması gerçekleştirmek için yönergeleri uygulayın ve bir web tarayıcısı üzerinden Azure hesabınızda oturum açın.

2. Birden fazla Azure aboneliğiniz varsa Azure’da oturum açtığınızda, kimlik bilgilerinizle ilişkili tüm Azure hesaplarınıza erişim izni elde edersiniz. Kullanabileceğiniz [Azure hesaplarını listelemek için aşağıdaki komutu](/cli/azure/account) kullanın:

    ```azurecli
    az account list
    ```

    IoT Hub 'ınızı oluşturmak için komutları çalıştırmak üzere kullanmak istediğiniz aboneliği seçmek için aşağıdaki komutu kullanın. Önceki komutun çıkışında yer alan abonelik adını veya kimliği kullanabilirsiniz:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Depolama hesabı ayrıntılarınızı alın

Aşağıdaki adımlarda, **Klasik** dağıtım modelini değil **Kaynak Yöneticisi** dağıtım modelini kullanarak depolama hesabınızı oluşturduğunuzu varsayalım.

Cihazlarınızdan dosya karşıya yüklemelerini yapılandırmak için bir Azure depolama hesabı için bağlantı dizesine ihtiyacınız vardır. Depolama hesabı, IoT Hub 'ınız ile aynı abonelikte olmalıdır. Ayrıca, depolama hesabındaki bir blob kapsayıcısının adına de ihtiyacınız vardır. Depolama hesabı anahtarlarınızı almak için aşağıdaki komutu kullanın:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

**ConnectionString** değerini bir yere getirin. Aşağıdaki adımlarda bu yapmanız gerekir.

Dosya karşıya yüklemelerinizin mevcut bir blob kapsayıcısını kullanabilir veya yeni bir tane oluşturabilirsiniz:

* Depolama hesabınızdaki mevcut blob kapsayıcılarını listelemek için aşağıdaki komutu kullanın:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Depolama hesabınızda bir blob kapsayıcısı oluşturmak için aşağıdaki komutu kullanın:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Karşıya dosya yükleme

Artık IoT Hub 'ınızı, depolama hesabı ayrıntılarınızı kullanarak [IoT Hub 'ına dosya yükleme](iot-hub-devguide-file-upload.md) özelliğini etkinleştirmek için yapılandırabilirsiniz.

Yapılandırma için aşağıdaki değerler gereklidir:

* **Depolama kapsayıcısı**: IoT Hub 'ınız ile Ilişkilendirilecek geçerli Azure aboneliğinizdeki bir Azure depolama hesabındaki blob kapsayıcısı. Önceki bölümde gerekli depolama hesabı bilgilerini almıştır. IoT Hub, dosyaları karşıya yüklerken kullanılacak cihazlar için bu blob kapsayıcısına yazma izinleri olan SAS URI 'Leri otomatik olarak oluşturur.

* **Karşıya yüklenen dosyalar için bildirim al**: dosya yükleme bildirimlerini etkinleştirin veya devre dışı bırakın.

* **SAS TTL**: Bu ayar, cihaza IoT Hub tarafından döndürülen sas URI 'lerinin yaşam süresi olarak belirlenir. Varsayılan olarak bir saat olarak ayarlanır.

* **Dosya bildirim ayarları varsayılan TTL**: süresi dolmadan önce karşıya dosya yükleme bildiriminin yaşam süresi. Varsayılan olarak bir güne ayarlanır.

* **Dosya bildirimi en fazla teslimat sayısı**: IoT Hub dosya yükleme bildirimi sunmaya kaç kez girişimde bulunulmasını sağlar. Varsayılan olarak 10 olarak ayarlanır.

IoT Hub 'ınızdaki karşıya dosya yükleme ayarlarını yapılandırmak için aşağıdaki Azure CLı komutlarını kullanın:

<!--Robinsh this is out of date, add cloud powershell -->

Bash kabuğu 'nda şunu kullanın:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Aşağıdaki komutu kullanarak IoT Hub 'ınızdaki karşıya dosya yükleme yapılandırmasını gözden geçirebilirsiniz:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub dosya yükleme özellikleri hakkında daha fazla bilgi için bkz. [bir cihazdan dosya yükleme](iot-hub-devguide-file-upload.md).

Azure IoT Hub 'yi yönetme hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [IoT cihazlarını toplu yönetme](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 'ınızı izleme](monitor-iot-hub.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)
* [IoT çözümünüzü baştan sona güvenli hale getirin](../iot-fundamentals/iot-security-ground-up.md)