---
title: PowerShell cmdlet 'ini kullanarak Azure IoT Hub oluşturma | Microsoft Docs
description: PowerShell cmdlet 'lerinin kaynak grubu oluşturmak ve ardından kaynak grubunda bir IoT Hub 'ı oluşturmak için nasıl kullanılacağını öğrenin. Ayrıca, hub 'ı nasıl kaldıracağınızı öğrenin.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: da021e3ba0fd93a182ea76a1ba4b7042b325aacc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142378"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>New-AzIotHub cmdlet 'ini kullanarak IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Giriş

Azure IoT Hub 'ları oluşturmak ve yönetmek için Azure PowerShell cmdlet 'lerini kullanabilirsiniz. Bu öğreticide, PowerShell ile bir IoT Hub 'ı oluşturma gösterilmektedir.

Bu nasıl yapılır? için bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure aboneliğinize bağlanma

Cloud Shell kullanıyorsanız, aboneliğinizde zaten oturum açtınız. Bunun yerine PowerShell 'i yerel olarak çalıştırıyorsanız, Azure aboneliğinizde oturum açmak için aşağıdaki komutu girin:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

IoT Hub 'ı dağıtmak için bir kaynak grubuna ihtiyacınız vardır. Mevcut bir kaynak grubunu kullanabilir ya da yeni bir kaynak grubu oluşturabilirsiniz.

IoT Hub 'ınız için bir kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) komutunu kullanın. Bu örnek **Doğu ABD** bölgesinde **MyIoTRG1** adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki adımda oluşturduğunuz kaynak grubunda bir IoT Hub 'ı oluşturmak için [New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub) komutunu kullanın. Bu örnek **Doğu ABD** bölgesinde **Mytesotub** adlı bir **S1** hub oluşturur:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT Hub 'ının adı genel olarak benzersiz olmalıdır.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Aboneliğinizdeki tüm IoT Hub 'larını [Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) komutunu kullanarak listeleyebilirsiniz:

```azurepowershell-interactive
Get-AzIotHub
```

Bu örnekte, önceki adımda oluşturduğunuz S1 standart IoT Hub gösterilmektedir.

[Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) komutunu kullanarak IoT Hub 'ını silebilirsiniz:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternatif olarak, [Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) komutunu kullanarak bir kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Sonraki adımlar

Artık bir PowerShell cmdlet 'i kullanarak bir IoT Hub 'ı dağıttıysanız, daha fazla bilgi edinmek istiyorsanız aşağıdaki makalelere göz atın:

* [IoT Hub 'ınız ile çalışmaya yönelik PowerShell cmdlet 'leri](/powershell/module/az.iothub/).

* [IoT Hub kaynak sağlayıcısı REST API](/rest/api/iothub/iothubresource).

IoT Hub için geliştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)