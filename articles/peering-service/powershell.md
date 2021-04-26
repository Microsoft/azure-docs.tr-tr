---
title: 'Eşleme hizmeti bağlantısını kaydetme-Azure PowerShell '
description: Bu öğreticide, PowerShell ile bir eşleme hizmeti bağlantısını kaydetmeyi öğrenin.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: ed4f628f6c3b888a640d61048c39f945a81901d7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060848"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak bir eşleme hizmeti bağlantısı kaydetme

Bu öğreticide, eşleme hizmetini Azure PowerShell kullanarak nasıl kaydedeceğinizi öğreneceksiniz.

Azure eşleme hizmeti, Microsoft 365, Dynamics 365, hizmet olarak yazılım (SaaS) Hizmetleri, Azure veya genel İnternet üzerinden erişilebilen herhangi bir Microsoft hizmeti gibi Microsoft bulut hizmetleriyle müşteri bağlantısını geliştiren bir ağ hizmetidir. Bu makalede, Azure PowerShell kullanarak bir eşleme hizmeti bağlantısının nasıl kaydedileceği hakkında bilgi edineceksiniz.

Azure aboneliğiniz yoksa, şimdi bir [Hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bunun yerine PowerShell 'i yerel olarak yükleyip kullanmaya karar verirseniz, bu hızlı başlangıç, Azure PowerShell Module sürümü 1.0.0 veya üzerini kullanmanızı gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükleme ve yükseltme bilgileri için bkz. [yükleme Azure PowerShell modülü](/powershell/azure/install-az-ps).

Son olarak, PowerShell 'i yerel olarak çalıştırıyorsanız de çalıştırmanız gerekir `Connect-AzAccount` . Bu komut Azure ile bir bağlantı oluşturur.

Eşleme hizmetini kaydetmek ve yönetmek için Azure PowerShell modülünü kullanın. Eşleme hizmetini PowerShell komut satırından veya betiklerden kaydedebilir veya yönetebilirsiniz.


## <a name="prerequisites"></a>Önkoşullar  
Aşağıdakilere sahip olmanız gerekir:

### <a name="azure-account"></a>Azure hesabı

Geçerli ve etkin bir Microsoft Azure hesabınızın olması gerekir. Eşleme hizmeti bağlantısını kurmak için bu hesap gereklidir. Eşleme hizmeti, Azure aboneliklerindeki bir kaynaktır.

### <a name="connectivity-provider"></a>Bağlantı sağlayıcısı

Ağınızı Microsoft Network 'e bağlamak için eşleme hizmeti edinmek üzere bir Internet hizmeti sağlayıcısı veya Internet Exchange ortağıyla birlikte çalışabilirsiniz.

Bağlantı sağlayıcılarının Microsoft ile iş ortaklığına sahip olduğundan emin olun.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Kaynak sağlayıcısı ve özellik bayrağıyla bir abonelik kaydetme

Eşleme hizmeti kaydetme adımlarına geçmeden önce, Azure PowerShell kullanarak aboneliğinizi kaynak sağlayıcısı ve özellik bayrağıyla kaydedin. Azure PowerShell komutları burada belirtilir:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Konum ve hizmet sağlayıcısını getir 

Eşleme hizmetinin etkinleştirilmesi gereken konum ve hizmet sağlayıcısını almak için Azure PowerShell ' de aşağıdaki komutları çalıştırın. 

Eşleme hizmeti konumlarını al:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Eşleme hizmeti sağlayıcılarını al:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Eşleme hizmeti bağlantısını Kaydet

Eşleme hizmeti bağlantısını, Azure PowerShell aracılığıyla aşağıdaki komut kümesini kullanarak kaydedin. Bu örnek, Mtypeınfo Eringservice adlı eşleme hizmetini kaydeder.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Eşleme hizmeti önekini Kaydet

Azure PowerShell aracılığıyla aşağıdaki komutları yürüterek bağlantı sağlayıcısı tarafından sunulan ön eki kaydettirin. Bu örnek Myprefıx adlı öneki kaydeder.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Tüm eşleme Hizmetleri bağlantılarını listeleme

Tüm eşleme hizmetlerinin listesini görüntülemek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Tüm eşleme hizmeti öneklerini listeleme

Tüm eşleme hizmeti ön eklerinin listesini görüntülemek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Eşleme hizmeti önekini kaldır

Eşleme hizmeti önekini kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Sonraki adımlar

- Eşleme hizmeti bağlantısı hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantısı](connection.md).
- Eşleme hizmeti bağlantı telemetrisi hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantı telemetrisi](connection-telemetry.md).
- Azure portal kullanarak bir eşleme hizmeti bağlantısı kaydetmek için bkz. [eşleme hizmeti bağlantısını kaydetme-Azure Portal](azure-portal.md).
- Azure CLı kullanarak bir eşleme hizmeti bağlantısı kaydetmek için bkz. [eşleme hizmeti bağlantısı kaydetme-Azure CLI](cli.md).
