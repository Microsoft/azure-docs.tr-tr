---
title: 'Başarısız bir devre sıfırlama-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Bu makale, başarısız durumundaki bir ExpressRoute devresini sıfırlamanıza yardımcı olur.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748114"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Başarısız bir ExpressRoute devresini sıfırlama

ExpressRoute bağlantı hattı üzerindeki bir işlem başarıyla tamamlanmazsa, devre bir ' başarısız ' durumuna geçebilir. Bu makale, başarısız bir Azure ExpressRoute devresini sıfırlamanıza yardımcı olur.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Devre sıfırlama

1. Azure Resource Manager PowerShell cmdlet'lerinin en son sürümünü yükleyin. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-az-ps).

2. PowerShell konsolunuzu yükseltilmiş ayrıcalıklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Birden çok Azure aboneliğiniz varsa, hesabın aboneliklerini denetleyin.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Kullanmak istediğiniz aboneliği belirtin.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Başarısız durumundaki bir devreyi sıfırlamak için aşağıdaki komutları çalıştırın:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Devre artık sağlıklı olmalıdır. Devre hala başarısız durumdaysa, [Microsoft desteğiyle](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bir destek bileti açın.

## <a name="next-steps"></a>Sonraki adımlar

Sorun yaşamaya devam ediyorsanız [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile bir destek bileti açın.
