---
title: Azure PowerShell IoT Central Yönet | Microsoft Docs
description: Bu makalede, Azure PowerShell IoT Central uygulamalarınızın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: eadc021335488bed1f236d3728acca245be7d71e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806639"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell’den IoT Central’ı yönetme

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) web sitesinde IoT Central uygulamaları oluşturup yönetmek yerine uygulamalarınızı yönetmek için [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Yerel makinenizde Azure PowerShell çalıştırmayı tercih ediyorsanız, bkz. [Azure PowerShell modülünü yüklemek](https://docs.microsoft.com/powershell/azure/install-az-ps). Azure PowerShell yerel olarak çalıştırdığınızda, bu makaledeki cmdlet 'leri denemeden önce Azure 'da oturum açmak için **Connect-AzAccount** cmdlet 'ini kullanın.

## <a name="install-the-iot-central-module"></a>IoT Central modülünü yükler

[IoT Central modülünün](https://docs.microsoft.com/powershell/module/az.iotcentral/) PowerShell ortamınızda yüklü olup olmadığını denetlemek için aşağıdaki komutu çalıştırın:

```powershell
Get-InstalledModule -name Az.I*
```

Yüklü modüller listesi **az. ıotcentral**içermiyorsa, aşağıdaki komutu çalıştırın:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Uygulama oluşturma

Azure aboneliğinizde bir IoT Central uygulaması oluşturmak için [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet 'ini kullanın. Örneğin:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Betik ilk olarak uygulamanın Doğu ABD bölgesinde bir kaynak grubu oluşturur. Aşağıdaki tabloda **New-AzIotCentralApp** komutuyla kullanılan parametreler açıklanmaktadır:

|Parametre         |Açıklama |
|------------------|------------|
|ResourceGroupName |Uygulamayı içeren kaynak grubu. Bu kaynak grubu aboneliğinizde zaten var olmalıdır. |
|Konum |Varsayılan olarak, bu cmdlet kaynak grubundaki konumu kullanır. Şu anda **Doğu ABD**, **Batı ABD**, **Kuzey Avrupa**veya **Batı Avrupa** bölgelerinde veya **Avustralya** ya da **Asya Pasifik** coğrafi bölgelerde IoT Central bir uygulama oluşturabilirsiniz.  |
|Ad              |Azure portal uygulamanın adı. |
|alanınızın         |Uygulamanın URL 'sindeki alt etki alanı. Örnekte, uygulama URL 'SI https://mysubdomain.azureiotcentral.com. |
|Sku               |Şu anda tek değer **S1** 'dir (Standart katman). Bkz. [Azure IoT Central fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-central/). |
|Şablon          | Kullanılacak uygulama şablonu. Daha fazla bilgi için aşağıdaki tabloya bakın: |
|displayName       |Kullanıcı arabiriminde gösterildiği şekilde uygulamanın adı. |

**Uygulama şablonları**

|Şablon adı  |Açıklama |
|---------------|------------|
|iotc-default@1.0.0 |Kendi cihaz şablonlarınız ve cihazlarınızla doldurabileceğiniz boş bir uygulama oluşturur. |
|iotc-demo@1.0.0    |Bir Soğutmalı Otomat için önceden oluşturulmuş cihaz şablonunu içeren bir uygulama oluşturur. Azure IoT Central'ı incelemeye başlamak için bu şablonu kullanın. |
|iotc-devkit-sample@1.0.0 |MXChip veya Raspberry Pi cihazını bağlamak amacıyla sizin için hazırlanmış cihaz şablonlarıyla bir uygulama oluşturur. Bu cihazlardan herhangi birini denemek için bir cihaz geliştiricisi iseniz bu şablonu kullanın. |

> [!NOTE]
> **Önizleme uygulaması** şablonu şu anda yalnızca **Kuzey Avrupa** ve **Orta ABD** bölgelerinde kullanılabilir.

## <a name="view-your-iot-central-applications"></a>IoT Central uygulamalarınızı görüntüleme

IoT Central uygulamalarınızı listelemek ve meta verileri görüntülemek için [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet 'ini kullanın.

## <a name="modify-an-application"></a>Bir uygulamayı değiştirme

Bir IoT Central uygulamasının meta verilerini güncelleştirmek için [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet 'ini kullanın. Örneğin, uygulamanızın görünen adını değiştirmek için:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Uygulamayı kaldırma

IoT Central uygulamasını silmek için [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet 'ini kullanın. Örneğin:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamalarını Azure PowerShell nasıl yönetebileceğinizi öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Uygulamanızı yönetme](howto-administer.md)
