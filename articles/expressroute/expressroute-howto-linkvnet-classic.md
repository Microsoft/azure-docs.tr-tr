---
title: 'ExpressRoute: sanal ağı bir devresine bağlama: klasik'
description: Bu belgede, klasik dağıtım modelini ve PowerShell 'i kullanarak sanal ağların (VNet 'ler) ExpressRoute devrelerine nasıl bağlanalınacağını gösteren bir genel bakış sunulmaktadır.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: e02073e777c62be00b5c25c2242294e54795a0d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031608"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>PowerShell kullanarak bir ExpressRoute devresine sanal ağ bağlama (klasik)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
>

Bu makale, PowerShell kullanarak sanal ağları (VNet) Azure ExpressRoute bağlantı hattına yönlendirmenize yardımcı olur. En fazla dört ExpressRoute bağlantı hatları için tek bir sanal ağa bağlanabilir. Bağlanmakta olduğunuz her ExpressRoute devresine yeni bir bağlantı oluşturmak için bu makaledeki adımları kullanın. ExpressRoute bağlantı hatları, aynı abonelik, farklı Aboneliklerde veya her ikisinin bir karışımı olabilir. Bu makale, klasik dağıtım modeli kullanılarak oluşturulan sanal ağlar için geçerlidir.

Bir ExpressRoute devresine en fazla 10 sanal ağ bağlayabilirsiniz. Tüm sanal ağlar aynı geopolitik bölgede olmalıdır. ExpressRoute bağlantı hattına daha fazla sayıda sanal ağ bağlayabilir veya ExpressRoute Premium eklentisini etkinleştirirseniz diğer coğrafi bölgelerdeki sanal ağları bağlayabilirsiniz. Premium eklenti hakkında daha fazla bilgi için [SSS bölümüne](expressroute-faqs.md) bakın.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure dağıtım modelleri hakkında**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Yapılandırma önkoşulları

* Gözden geçirme [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimleri](expressroute-routing.md), ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir.
   * [ExpressRoute](expressroute-howto-circuit-classic.md) bağlantı hattını oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcınızın devreyi etkinleştirmesini sağlayın.
   * Bağlantı hattınız için yapılandırılmış Azure özel eşleme olduğundan emin olun. Bkz: [yönlendirmeyi yapılandırma](expressroute-howto-routing-classic.md) makale için yönlendirme yönergeleri.
   * Azure özel eşdüzey hizmet sağlama yapılandırılır ve uçtan uca bağlantıyı etkinleştirmek üzere ağınız ile Microsoft arasında BGP eşliği ayarlama olduğundan emin olun.
   * Oluşturulmuş ve tam olarak sağlanmış bir sanal ağınız ve sanal ağ geçidi olmalıdır. [ExpressRoute için sanal ağ yapılandırma](expressroute-howto-vnet-portal-classic.md)yönergelerini izleyin.

### <a name="download-the-latest-powershell-cmdlets"></a>En son PowerShell cmdlet 'lerini indirin

Azure Hizmet Yönetimi (SM) PowerShell modüllerine ve ExpressRoute modülünün en son sürümlerini yükleyin. Aşağıdaki örnek kullanırken, cmdlet'leri daha yeni sürümleri çıktıkça sürüm numarasını (Bu örnekte, 5.1.1) değişeceğini unutmayın.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Azure PowerShell hakkında daha fazla bilgiye ihtiyacınız varsa bkz [Azure PowerShell cmdlet'lerini kullanmaya Başlarken](/powershell/azure/overview) bilgisayarınızın Azure PowerShell modüllerinin kullanacak şekilde yapılandırma hakkında adım adım yönergeler için.

### <a name="sign-in"></a>Oturum aç

Azure hesabınızda oturum açmak için aşağıdaki örnekleri kullanın:

1. PowerShell konsolunuzu yükseltilmiş haklarla açın ve hesabınıza bağlanın.

   ```powershell
   Connect-AzAccount
   ```
2. Hesapla ilişkili abonelikleri kontrol edin.

   ```powershell
   Get-AzSubscription
   ```
3. Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Ardından, Azure aboneliğiniz için PowerShell Klasik dağıtım modeli için eklemek için aşağıdaki cmdlet'i kullanın.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Bir sanal ağ ile aynı abonelikte devreye bağlama
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
Bir ExpressRoute bağlantı hattı birden çok farklı abonelikler arasında paylaşabilirsiniz. Aşağıdaki şekilde birden fazla aboneliği analiz basit bir ExpressRoute bağlantı hatları için nasıl paylaşım Works şematik gösterir.

Her küçük bulutların büyük bulut içinde bir kuruluştaki farklı departmanlara ait abonelikleri temsil etmek için kullanılır. Kuruluştaki bölümlerin her biri, hizmetlerini dağıtmak için kendi aboneliğini kullanabilir, ancak departmanlar şirket içi ağınıza geri bağlanmak için tek bir ExpressRoute devresini paylaşabilir. Tek bir bölüm (Bu örnekte: BT) ExpressRoute bağlantı hattına sahip olabilir. Kuruluştaki diğer abonelikler, ExpressRoute bağlantı hattı kullanabilirsiniz.

> [!NOTE]
> ExpressRoute bağlantı hattı sahibinden için adanmış bir bağlantı hattı için bağlantı ve bant genişliği ücretleri uygulanır. Tüm sanal ağları, aynı bant genişliğini paylaşır.
> 
> 

![Çapraz abonelik bağlantısı](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Yönetim
*Devre sahibi* , ExpressRoute bağlantı hattının oluşturulduğu aboneliğin yönetici/ortak yöneticisidir. Devre sahibi, *devre kullanıcıları*olarak adlandırılan diğer aboneliklerdeki yöneticileri/ortak yöneticileri, sahip oldukları özel devreyi kullanmak üzere yetkilendirebilirler. Kuruluşun ExpressRoute bağlantı hattını kullanma yetkisine sahip olan devre kullanıcıları, aboneliğindeki sanal ağı, yetkilendirdikten sonra ExpressRoute devresine bağlayabilir.

Bağlantı hattı sahibinden yetkilendirme dilediğiniz zaman iptal et ve değiştirmek için gücüne sahiptir. Bir Yetkilendirmeyi iptal etmek, erişimi iptal edilen aboneliğden tüm bağlantıların silinmesine neden olur.

### <a name="circuit-owner-operations"></a>Bağlantı hattı sahibi işlemleri

**Yetkilendirme oluşturma**

Devre sahibi, diğer aboneliklerin yöneticilerinin belirtilen devreyi kullanmasını yetkilendirebilirsiniz. Aşağıdaki örnekte, devrenin Yöneticisi (contoso It), başka bir aboneliğin yöneticisinin (geliştirme-test), devresine iki sanal ağa bağlantı kurma imkanı sağlar. Contoso BT Yöneticisi, geliştirme ve test Microsoft KIMLIĞINI belirterek bunu mümkün kılar. Cmdlet 'i belirtilen Microsoft KIMLIĞINE e-posta göndermez. Devre sahibinin, diğer abonelik sahibine yetkilendirme tamamlandığını açıkça bildirmesi gerekir.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Döndürülmesini

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Yetkilendirmeleri gözden geçirme**

Bağlantı hattı sahibinden belirli bir bağlantı hattı üzerinde aşağıdaki cmdlet'i çalıştırarak düzenlenen tüm yetkilendirmeleri gözden geçirebilirsiniz:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Döndürülmesini

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

  Döndürülmesini

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Yetkilendirmeler siliniyor**

Bağlantı hattı sahibinden iptal etme/yetkilendirmeleri kullanıcı için aşağıdaki cmdlet'i çalıştırarak ya da silebilir:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Bağlantı hattı kullanıcı işlemleri

**Yetkilendirmeleri gözden geçirme**

Devre kullanıcısı aşağıdaki cmdlet 'i kullanarak yetkilendirmeleri gözden geçirebilir:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Döndürülmesini

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

Bağlantı hattı kullanıcısı bağlantı yetkilendirme kullanmak için aşağıdaki cmdlet'i çalıştırabilirsiniz:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Döndürülmesini

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
