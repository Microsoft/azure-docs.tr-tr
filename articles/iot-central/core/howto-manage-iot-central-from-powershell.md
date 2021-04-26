---
title: Azure PowerShell IoT Central Yönet | Microsoft Docs
description: Bu makalede, Azure PowerShell IoT Central uygulamalarınızın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: a870b72d1dda04ab29dbb5f056873d47f888b837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501401"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell’den IoT Central’ı yönetme

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) web sitesinde IoT Central uygulamaları oluşturup yönetmek yerine uygulamalarınızı yönetmek için [Azure PowerShell](/powershell/azure/) kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Yerel makinenizde Azure PowerShell çalıştırmayı tercih ediyorsanız, bkz. [Azure PowerShell modülünü yüklemek](/powershell/azure/install-az-ps). Azure PowerShell yerel olarak çalıştırdığınızda, bu makaledeki cmdlet 'leri denemeden önce Azure 'da oturum açmak için **Connect-AzAccount** cmdlet 'ini kullanın.

> [!TIP]
> PowerShell komutlarınızı farklı bir Azure aboneliğinde çalıştırmanız gerekiyorsa bkz. [etkin aboneliği değiştirme](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>IoT Central modülünü yükler

[IoT Central modülünün](/powershell/module/az.iotcentral/) PowerShell ortamınızda yüklü olup olmadığını denetlemek için aşağıdaki komutu çalıştırın:

```powershell
Get-InstalledModule -name Az.I*
```

Yüklü modüller listesi **az. ıotcentral** içermiyorsa, aşağıdaki komutu çalıştırın:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Uygulama oluşturma

Azure aboneliğinizde bir IoT Central uygulaması oluşturmak için [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet 'ini kullanın. Örnek:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Betik ilk olarak uygulamanın Doğu ABD bölgesinde bir kaynak grubu oluşturur. Aşağıdaki tabloda **New-AzIotCentralApp** komutuyla kullanılan parametreler açıklanmaktadır:

|Parametre         |Açıklama |
|------------------|------------|
|ResourceGroupName |Uygulamayı içeren kaynak grubu. Bu kaynak grubu aboneliğinizde zaten var olmalıdır. |
|Konum |Varsayılan olarak, bu cmdlet kaynak grubundaki konumu kullanır. Şu anda **Avustralya**, **Asya Pasifik**, **Avrupa**, **Birleşik Devletler**, **Birleşik Krallık** ve **Japonya** coğrafi graflarını IoT Central bir uygulama oluşturabilirsiniz. |
|Name              |Azure portal uygulamanın adı. |
|Alt etki alanı         |Uygulamanın URL 'sindeki alt etki alanı. Örnekte, uygulama URL 'SI `https://mysubdomain.azureiotcentral.com` . |
|Sku               |Şu anda, **ST1** ya da **ST2** kullanabilirsiniz. Bkz. [Azure IoT Central fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-central/). |
|Şablon          | Kullanılacak uygulama şablonu. Daha fazla bilgi için aşağıdaki tabloya bakın. |
|DisplayName       |Kullanıcı arabiriminde gösterildiği şekilde uygulamanın adı. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>IoT Central uygulamalarınızı görüntüleme

IoT Central uygulamalarınızı listelemek ve meta verileri görüntülemek için [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet 'ini kullanın.

## <a name="modify-an-application"></a>Bir uygulamayı değiştirme

Bir IoT Central uygulamasının meta verilerini güncelleştirmek için [set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet 'ini kullanın. Örneğin, uygulamanızın görünen adını değiştirmek için:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Uygulamayı kaldırma

IoT Central uygulamasını silmek için [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet 'ini kullanın. Örnek:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamalarını Azure PowerShell nasıl yönetebileceğinizi öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Uygulamanızı yönetme](howto-administer.md)