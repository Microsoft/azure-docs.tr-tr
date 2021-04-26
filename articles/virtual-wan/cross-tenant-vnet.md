---
title: "Çapraz kiracı VNET 'leri bir hub 'a bağlama: PowerShell"
titleSuffix: Azure Virtual WAN
description: Bu makale, PowerShell kullanarak platformlar arası VNET 'leri bir sanal hub 'a bağlamanıza yardımcı olur.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 82a5e90221a77b891df78984c7fddfd63b6532aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585426"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Platformlar arası VNET 'leri bir sanal WAN hub 'ına bağlama

Bu makale, sanal WAN 'ı kullanarak sanal ağı farklı bir Kiracıdaki bir sanal hub 'a bağlamayı sağlar. Bu mimari, aynı ağ olması gereken ancak farklı kiracılarda olan istemci iş yükleriniz varsa yararlıdır. Örneğin, aşağıdaki diyagramda gösterildiği gibi, contoso olmayan VNet (uzak kiracı) ile bir contoso sanal hub 'ına (ana kiracı) bağlanabilirsiniz.

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Yönlendirme yapılandırmasını ayarlama" :::

Bu makalede şunları öğreneceksiniz:

* Başka bir kiracıyı Azure aboneliğinize katkıda bulunan olarak ekleyin.
* Çapraz kiracı VNet 'i sanal bir hub 'a bağlayın.

Bu yapılandırma adımları Azure portal ve PowerShell 'in bir birleşimi kullanılarak gerçekleştirilir. Ancak, özelliği yalnızca PowerShell ve CLı 'de kullanılabilir.

## <a name="before-you-begin"></a>Başlamadan Önce

### <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları kullanmak için, ortamınızda aşağıdaki yapılandırmanın zaten ayarlanmış olması gerekir:

* Üst aboneliğinizdeki sanal bir WAN ve sanal hub.
* Farklı bir (uzak) Kiracıdaki bir abonelikte yapılandırılmış bir sanal ağ.
* Uzak Kiracıdaki VNet adres alanının üst sanal hub 'a zaten bağlı olan diğer VNET 'lerdeki diğer adres alanları ile çakışmadığından emin olun.

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>İzinler atama

Sanal hub 'a sahip olan üst aboneliğin uzak Kiracıdaki sanal ağlara değişiklik ve erişim izni vermek için, uzak kiracı aboneliğinden ana aboneliğinize **katkıda bulunan** izinleri atamanız gerekir.

1. **Katkıda** bulunan rolü atamasını ana hesaba (sanal WAN hub 'ı ile) ekleyin. Bu rolü atamak için PowerShell veya Azure portal kullanabilirsiniz. Aşağıdaki adımlar için **rol atamaları ekleme veya kaldırma** makalelerine bakın:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Ardından, uzak kiracı aboneliğini ve üst kiracı aboneliğini PowerShell 'in geçerli oturumuna ekleyin. Aşağıdaki komutu çalıştırın. Üst öğede oturum açtıysanız, yalnızca uzak kiracı için komutu çalıştırmanız gerekir.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Üst kimlik bilgilerini kullanarak Azure PowerShell oturum açarak rol atamasının başarılı olduğunu doğrulayın ve aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. İzinler başarıyla üst öğeye yayıldıysa ve oturuma eklendiyse, uzak kiracının sahip olduğu abonelik komutun çıktısında görünür.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>VNet 'i hub 'a bağlama

Aşağıdaki adımlarda, sanal ağı sanal hub 'a bağladığınızda iki aboneliğin bağlamı arasında geçiş yapmanız gerekir. Örnek değerleri kendi ortamınızı yansıtacak şekilde değiştirin.

1. Aşağıdaki komutu çalıştırarak uzak hesabınızın bağlamında olduğunuzdan emin olun:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Hub 'a bağlamak istediğiniz sanal ağın meta verilerini depolamak için yerel bir değişken oluşturun.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Üst hesaba geri dönün.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. VNet 'i hub 'a bağlayın.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. Yeni bağlantıyı herhangi bir PowerShell veya Azure portal görüntüleyebilirsiniz.

   * **PowerShell:** Bağlantı başarıyla biçimlendirilmişse, yeni oluşturulmuş bağlantının meta verileri PowerShell konsolunda görünür.
   * **Azure Portal:** Sanal hub, **bağlantı > sanal ağ bağlantılarına** gidin. Bağlantı işaretçisini görüntüleyebilirsiniz. Gerçek kaynağı görmek için uygun izinlere ihtiyacınız olacak.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Sorun giderme

* $Remote (Yukarıdaki [bölümden](#connect)) içindeki meta verilerin Azure Portal bilgilerle eşleştiğini doğrulayın.
* Uzak kiracı kaynak grubunun ıAM ayarlarını kullanarak veya Azure PowerShell komutlarını (Get-AzSubscription) kullanarak izinleri doğrulayabilirsiniz.
* Tekliflerin, kaynak gruplarının adlarına veya ortama özgü diğer değişkenlere (örn.) eklendiğinden emin olun. "VirtualHub1" veya "VirtualNetwork1").

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için bkz.

* Sanal WAN [hakkında SSS](virtual-wan-faq.md)
