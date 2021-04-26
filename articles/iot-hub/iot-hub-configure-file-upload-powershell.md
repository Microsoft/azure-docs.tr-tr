---
title: Karşıya dosya yüklemeyi yapılandırmak için Azure PowerShell kullanın | Microsoft Docs
description: Bağlı cihazlardan dosya yüklemelerini etkinleştirmek üzere IoT Hub 'ınızı yapılandırmak için Azure PowerShell cmdlet 'lerini kullanma. Hedef Azure Depolama hesabını yapılandırma hakkında bilgi içerir.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536020"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>PowerShell kullanarak IoT Hub dosya yüklemelerini yapılandırma

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub ' de karşıya dosya yükleme işlevini](iot-hub-devguide-file-upload.md)kullanmak Için öncelikle IoT Hub 'ınız Ile bir Azure Depolama hesabını ilişkilendirmeniz gerekir. Var olan bir depolama hesabını kullanabilir veya yeni bir tane oluşturabilirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Etkin bir Azure hesabı. Hesabınız yoksa, yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

* [Azure PowerShell cmdlet 'leri](/powershell/azure/install-Az-ps).

* Azure IoT Hub 'ı. IoT Hub 'ınız yoksa, bir [IoT Hub 'ı oluşturmak](iot-hub-create-through-portal.md)için [New-AzIoTHub cmdlet 'ini](/powershell/module/az.iothub/new-aziothub) kullanarak bir tane oluşturabilir veya portalını kullanabilirsiniz.

* Bir Azure depolama hesabı. Bir Azure depolama hesabınız yoksa, bir [depolama hesabı oluşturmak](../storage/common/storage-account-create.md) Için [Azure Storage PowerShell cmdlet 'lerini](/powershell/module/az.storage/) kullanarak bir tane oluşturabilir veya portalını kullanabilirsiniz

## <a name="sign-in-and-set-your-azure-account"></a>Oturum açın ve Azure hesabınızı ayarlayın

Azure hesabınızda oturum açın ve aboneliğinizi seçin.

1. PowerShell komut isteminde **Connect-AzAccount** cmdlet 'ini çalıştırın:

    ```powershell
    Connect-AzAccount
    ```

2. Birden çok Azure aboneliğiniz varsa Azure 'da oturum açmak, kimlik bilgilerinizle ilişkili tüm Azure aboneliklerine erişmenizi sağlar. Kullanabileceğiniz Azure aboneliklerini listelemek için aşağıdaki komutu kullanın:

    ```powershell
    Get-AzSubscription
    ```

    IoT Hub 'ınızı yönetmek üzere komutları çalıştırmak için kullanmak istediğiniz aboneliği seçmek için aşağıdaki komutu kullanın. Önceki komutun çıkışında yer alan abonelik adını veya kimliği kullanabilirsiniz:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Depolama hesabı ayrıntılarınızı alın

Aşağıdaki adımlarda, **Klasik** dağıtım modelini değil **Kaynak Yöneticisi** dağıtım modelini kullanarak depolama hesabınızı oluşturduğunuzu varsayalım.

Cihazlarınızdan dosya karşıya yüklemelerini yapılandırmak için bir Azure depolama hesabı için bağlantı dizesine ihtiyacınız vardır. Depolama hesabı, IoT Hub 'ınız ile aynı abonelikte olmalıdır. Ayrıca, depolama hesabındaki bir blob kapsayıcısının adına de ihtiyacınız vardır. Depolama hesabı anahtarlarınızı almak için aşağıdaki komutu kullanın:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**KEY1** depolama hesabı anahtar değerini bir yere göz önüne alın. Aşağıdaki adımlarda bu yapmanız gerekir.

Dosya karşıya yüklemelerinizin mevcut bir blob kapsayıcısını kullanabilir ya da yeni bir tane oluşturabilirsiniz:

* Depolama hesabınızdaki mevcut blob kapsayıcılarını listelemek için aşağıdaki komutları kullanın:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Depolama hesabınızda bir blob kapsayıcısı oluşturmak için aşağıdaki komutları kullanın:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub 'ınızı yapılandırma

Artık IoT Hub 'ınızı, depolama hesabı ayrıntılarınızı kullanarak [IoT Hub 'ına dosya yükleyecek](iot-hub-devguide-file-upload.md) şekilde yapılandırabilirsiniz.

Yapılandırma için aşağıdaki değerler gereklidir:

* **Depolama kapsayıcısı**: IoT Hub 'ınız ile Ilişkilendirilecek geçerli Azure aboneliğinizdeki bir Azure depolama hesabındaki blob kapsayıcısı. Önceki bölümde gerekli depolama hesabı bilgilerini almıştır. IoT Hub, dosyaları karşıya yüklerken kullanılacak cihazlar için bu blob kapsayıcısına yazma izinleri olan SAS URI 'Leri otomatik olarak oluşturur.

* **Karşıya yüklenen dosyalar için bildirim al**: dosya yükleme bildirimlerini etkinleştirin veya devre dışı bırakın.

* **SAS TTL**: Bu ayar, cihaza IoT Hub tarafından döndürülen sas URI 'lerinin yaşam süresi olarak belirlenir. Varsayılan olarak bir saat olarak ayarlanır.

* **Dosya bildirim ayarları varsayılan TTL**: süresi dolmadan önce karşıya dosya yükleme bildiriminin yaşam süresi. Varsayılan olarak bir güne ayarlanır.

* **Dosya bildirimi en fazla teslimat sayısı**: IoT Hub dosya yükleme bildirimi sunmaya kaç kez girişimde bulunulmasını sağlar. Varsayılan olarak 10 olarak ayarlanır.

IoT Hub 'ınızdaki karşıya dosya yükleme ayarlarını yapılandırmak için aşağıdaki PowerShell cmdlet 'ini kullanın:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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