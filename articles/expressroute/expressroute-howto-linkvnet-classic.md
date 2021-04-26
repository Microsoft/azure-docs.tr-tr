---
title: 'Azure ExpressRoute: sanal ağı bir devresine bağlama: klasik'
description: Bu belgede, klasik dağıtım modelini ve PowerShell 'i kullanarak sanal ağların (VNet 'ler) ExpressRoute devrelerine nasıl bağlanalınacağını gösteren bir genel bakış sunulmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e8a1d48934d73f1035c6f2c4c76e56d9a0e4052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206979"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>PowerShell kullanarak bir ExpressRoute devresine sanal ağ bağlama (klasik)
> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
>

Bu makale, PowerShell kullanarak sanal ağları (VNet) Azure ExpressRoute bağlantı hattına yönlendirmenize yardımcı olur. Tek bir sanal ağ, en fazla dört ExpressRoute devresine bağlanabilir. Bağlanmakta olduğunuz her ExpressRoute devresine yeni bir bağlantı oluşturmak için bu makaledeki adımları kullanın. ExpressRoute devreleri aynı abonelikte, farklı aboneliklerde veya her ikisinin karışımı olabilir. Bu makale, klasik dağıtım modeli kullanılarak oluşturulan sanal ağlar için geçerlidir.

Bir ExpressRoute devresine en fazla 10 sanal ağ bağlayabilirsiniz. Tüm sanal ağlar aynı geopolitik bölgede olmalıdır. ExpressRoute bağlantı hattına daha fazla sayıda sanal ağ bağlayabilir veya ExpressRoute Premium eklentisini etkinleştirirseniz diğer coğrafi bölgelerdeki sanal ağları bağlayabilirsiniz. Premium eklenti hakkında daha fazla bilgi için [SSS bölümüne](expressroute-faqs.md) bakın.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure dağıtım modelleri hakkında**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Yapılandırma önkoşulları

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimlerini](expressroute-routing.md)ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir.
   * [ExpressRoute](expressroute-howto-circuit-classic.md) bağlantı hattını oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcınızın devreyi etkinleştirmesini sağlayın.
   * Devreniz için Azure özel eşlemesi 'nin yapılandırıldığından emin olun. Yönlendirme yönergeleri için [yönlendirmeyi yapılandırma](expressroute-howto-routing-classic.md) makalesine bakın.
   * Uçtan uca bağlantıyı etkinleştirebilmeniz için Azure özel eşlemesinin yapılandırıldığından ve ağınız ile Microsoft arasındaki BGP eşlemesinin yapıldığından emin olun.
   * Oluşturulmuş ve tam olarak sağlanmış bir sanal ağınız ve sanal ağ geçidi olmalıdır. [ExpressRoute için sanal ağ yapılandırma](./expressroute-howto-add-gateway-portal-resource-manager.md)yönergelerini izleyin.

### <a name="download-the-latest-powershell-cmdlets"></a>En son PowerShell cmdlet 'lerini indirin

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Aynı abonelikteki bir sanal ağı bir devreye bağlama
Aşağıdaki cmdlet 'i kullanarak bir ExpressRoute devresine bir sanal ağ bağlayabilirsiniz. Cmdlet 'ini çalıştırmadan önce, sanal ağ geçidinin oluşturulduğundan ve bağlamaya hazırlandığınızdan emin olun.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Devresine sanal ağ bağlantısını kaldırma
Aşağıdaki cmdlet 'i kullanarak bir ExpressRoute devresine sanal ağ bağlantısını kaldırabilirsiniz. Belirtilen sanal ağ için geçerli aboneliğin seçildiğinden emin olun. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Farklı abonelikteki bir sanal ağı devreye bağlama
Bir ExpressRoute devresini birden çok abonelik arasında paylaşabilirsiniz. Aşağıdaki şekilde, birden çok aboneliğin ExpressRoute devreleri için paylaşımın nasıl çalıştığına ilişkin basit bir şematik gösterilmektedir.

Büyük buluttaki küçük bulutların her biri, bir kuruluştaki farklı departmanlara ait olan abonelikleri temsil etmek için kullanılır. Kuruluştaki bölümlerin her biri, hizmetlerini dağıtmak için kendi aboneliğini kullanabilir, ancak departmanlar şirket içi ağınıza geri bağlanmak için tek bir ExpressRoute devresini paylaşabilir. Tek bir departman (Bu örnekte:) ExpressRoute devresine sahip olabilir. Kuruluştaki diğer abonelikler ExpressRoute devresini kullanabilir.

> [!NOTE]
> Adanmış devre için bağlantı ve bant genişliği ücretleri ExpressRoute bağlantı hattı sahibine uygulanır. Tüm sanal ağlar aynı bant genişliğini paylaşır.
> 
> 

![Çapraz abonelik bağlantısı](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Yönetim
*Devre sahibi* , ExpressRoute bağlantı hattının oluşturulduğu aboneliğin yönetici/ortak yöneticisidir. Devre sahibi, *devre kullanıcıları* olarak adlandırılan diğer aboneliklerdeki yöneticileri/ortak yöneticileri, sahip oldukları özel devreyi kullanmak üzere yetkilendirebilirler. Kuruluşun ExpressRoute bağlantı hattını kullanma yetkisine sahip olan devre kullanıcıları, aboneliğindeki sanal ağı, yetkilendirdikten sonra ExpressRoute devresine bağlayabilir.

Devre sahibinin, her zaman yetkilendirmeleri değiştirme ve iptal etme gücü vardır. Bir Yetkilendirmeyi iptal etmek, erişimi iptal edilen aboneliğden tüm bağlantıların silinmesine neden olur.

### <a name="circuit-owner-operations"></a>Devre sahibi işlemleri

**Yetkilendirme oluşturma**

Devre sahibi, diğer aboneliklerin yöneticilerinin belirtilen devreyi kullanmasını yetkilendirebilirsiniz. Aşağıdaki örnekte, devrenin Yöneticisi (contoso It), başka bir aboneliğin yöneticisinin (geliştirme-test), devresine iki sanal ağa bağlantı kurma imkanı sağlar. Contoso BT yöneticisi bunu, Microsoft KIMLIĞI Dev-Test belirterek sunar. Cmdlet 'i belirtilen Microsoft KIMLIĞINE e-posta göndermez. Devre sahibinin, diğer abonelik sahibine yetkilendirme tamamlandığını açıkça bildirmesi gerekir.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Şunu döndürür:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Yetkilendirmeleri gözden geçirme**

Devre sahibi, aşağıdaki cmdlet 'i çalıştırarak belirli bir devreye göre verilen tüm yetkilendirmeleri gözden geçirebilir:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Şunu döndürür:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Yetkilendirmeler güncelleştiriliyor**

Devre sahibi, aşağıdaki cmdlet 'i kullanarak yetkilendirmeleri değiştirebilir:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Şunu döndürür:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Yetkilendirmeler siliniyor**

Devre sahibi, aşağıdaki cmdlet 'i çalıştırarak kullanıcıya yetkilendirmeleri iptal edebilir/silebilir:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Devre Kullanıcı işlemleri

**Yetkilendirmeleri gözden geçirme**

Devre kullanıcısı aşağıdaki cmdlet 'i kullanarak yetkilendirmeleri gözden geçirebilir:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Şunu döndürür:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Bağlantı yetkilendirmelerini benimseme**

Devre kullanıcısı bir bağlantı yetkilendirmesi kullanmak için aşağıdaki cmdlet 'i çalıştırabilir:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Şunu döndürür:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Bu komutu, sanal ağ için yeni bağlı abonelikte çalıştırın:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).