---
title: Şablon kullanarak Azure IoT Hub oluşturma (PowerShell) | Microsoft Docs
description: Azure PowerShell ile IoT Hub oluşturmak için Azure Resource Manager şablonu kullanma.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 1fedadfa7e5b4ec3d7de30d0ad3ef1b1bfa0e0ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144405"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager şablonu kullanarak IoT Hub oluşturma (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Bir Azure Resource Manager şablonu kullanarak bir IoT Hub ve bir tüketici grubu oluşturma hakkında bilgi edinin. Resource Manager şablonları, çözümünüz için dağıtmanız gereken kaynakları tanımlayan JSON dosyalarıdır. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi için bkz. [Azure Resource Manager belgeleri](../azure-resource-manager/index.yml).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Bu hızlı başlangıçta kullanılan Kaynak Yöneticisi şablonu [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Şablonun bir kopyası aşağıda verilmiştir:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Şablon, üç uç nokta (eventhub, buluttan cihaza ve mesajlaşma) ve bir tüketici grubu içeren bir Azure IoT Hub 'ı oluşturur. Daha fazla şablon örneği için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). IoT Hub şablonu şeması  [burada](/azure/templates/microsoft.devices/iothub-allversions)bulunabilir.

Şablon dağıtmak için çeşitli yöntemler vardır.  Bu öğreticide Azure PowerShell kullanırsınız.

PowerShell betiğini çalıştırmak için, Azure Cloud Shell 'i açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından Yapıştır ' ı seçin:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

PowerShell betiğinizden görebileceğiniz gibi, kullanılan şablon Azure hızlı başlangıç şablonlarından de yapılır. Kendinizinkini kullanmak için önce şablon dosyasını Cloud Shell 'e yüklemeniz, ardından `-TemplateFile` anahtarı kullanarak dosya adını belirtmeniz gerekir.  Bir örnek için bkz. [şablonu dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Sonraki adımlar

Artık bir Azure Resource Manager şablonu kullanarak bir IoT Hub 'ı dağıttıysanız, daha fazla incelemek isteyebilirsiniz:

* [IoT Hub kaynak sağlayıcısı REST API][lnk-rest-api]özellikleri hakkında bilgi edinin.
* Azure Resource Manager özellikleri hakkında daha fazla bilgi edinmek için [Azure Resource Manager genel bakış][lnk-azure-rm-overview] konusunu okuyun.
* Şablonlarda kullanılacak JSON sözdizimi ve özellikler için bkz. [Microsoft. Devices kaynak türleri](/azure/templates/microsoft.devices/iothub-allversions).

IoT Hub için geliştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [C SDK 'ya giriş][lnk-c-sdk]
* [Azure IoT SDK’ları][lnk-sdks]

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IOT Edge ile sınır cihazlarına Al dağıtma][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md