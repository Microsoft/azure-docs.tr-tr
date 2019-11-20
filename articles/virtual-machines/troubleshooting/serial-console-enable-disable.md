---
title: Azure seri konsolunu etkinleştirme ve devre dışı bırakma | Microsoft Docs
description: Azure seri konsol hizmetini etkinleştirme ve devre dışı bırakma
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: f48fe94504d8012affb77c4fd5d39df2537d72b3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300132"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Azure seri konsolu 'Nu etkinleştirme ve devre dışı bırakma

Tıpkı diğer tüm kaynaklar gibi Azure seri konsolu etkinleştirilebilir ve devre dışı bırakılabilir. Seri konsol, genel Azure 'daki tüm abonelikler için varsayılan olarak etkindir. Şu anda, seri konsolunun devre dışı bırakılması, tüm aboneliğiniz için hizmeti devre dışı bırakacak. Abonelik için seri konsolunun devre dışı bırakılması veya yeniden etkinleştirilmesi abonelik üzerinde katkıda bulunan düzey erişimi veya üzeri gerektirir.

Ayrıca, önyükleme tanılamayı devre dışı bırakarak tek bir VM veya sanal makine ölçek kümesi örneği için seri konsolunu devre dışı bırakabilirsiniz. Hem VM/sanal makine ölçek kümesi hem de önyükleme tanılama depolama hesabınızda katkıda bulunan düzeyinde erişime veya üstüne ihtiyacınız olacak.

## <a name="vm-level-disable"></a>VM düzeyi devre dışı
Seri konsol, önyükleme tanılaması ayarı devre dışı bırakılarak belirli bir VM veya sanal makine ölçek kümesi için devre dışı bırakılabilir. VM 'nin veya sanal makine ölçek kümesinin seri konsolunu devre dışı bırakmak için Azure portalından önyükleme tanılamayı devre dışı bırakın. Bir sanal makine ölçek kümesinde seri konsol kullanıyorsanız, sanal makine ölçek kümesi örneklerinizi en son modele yükseltdiğinizden emin olun.


## <a name="subscription-level-disable"></a>Abonelik düzeyi devre dışı

### <a name="azure-cli"></a>Azure CLI

Seri konsol, Azure CLı 'de aşağıdaki komutlar kullanılarak aboneliğin tamamı için devre dışı bırakılabilir ve yeniden etkinleştirilebilir:

Bir abonelik için seri konsolunu devre dışı bırakmak için aşağıdaki komutları kullanın:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Abonelik için seri konsolunu etkinleştirmek üzere aşağıdaki komutları kullanın:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Bir abonelik için seri konsolunun geçerli etkin/devre dışı durumunu almak için aşağıdaki komutları kullanın:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Seri konsol, PowerShell kullanılarak da etkinleştirilebilir ve devre dışı bırakılabilir.

Bir abonelik için seri konsolunu devre dışı bırakmak için aşağıdaki komutları kullanın:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Abonelik için seri konsolunu etkinleştirmek üzere aşağıdaki komutları kullanın:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Sonraki adımlar
* [Linux VM 'leri Için Azure seri konsolu](./serial-console-linux.md) hakkında daha fazla bilgi edinin
* [Windows VM 'leri Için Azure seri konsolu](./serial-console-windows.md) hakkında daha fazla bilgi edinin
* [Azure seri konsolu 'nda güç yönetimi seçenekleri](./serial-console-power-options.md) hakkında bilgi edinin