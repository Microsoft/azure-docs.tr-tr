---
title: 'Azure ExpressRoute: eşlemeyi yapılandırma: klasik'
description: Bu makalede, bir ExpressRoute bağlantı hattı için özel, ortak ve Microsoft eşlemesinin nasıl oluşturulduğu ve sağlandığı adım adım anlatılmaktadır. Bu makalede ayrıca bağlantı hattınızın durumunu denetleme, bağlantı hattını güncelleştirme veya silme işlemlerinin nasıl yapıldığı da anlatılmaktadır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.openlocfilehash: 814a73900b05b66d1bacc946b9f994135d3fc9f6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083452"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>ExpressRoute devresi için eşleme oluşturma ve değiştirme (klasik)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - özel eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - genel eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-routing-classic.md)
> 

Bu makale, PowerShell ve klasik dağıtım modeli kullanarak bir ExpressRoute bağlantı hattı için eşleme/Yönlendirme yapılandırması oluşturma ve yönetme adımlarında size yol gösterir. Aşağıdaki adımlarda ayrıca bir ExpressRoute bağlantı hattının durumunu denetleme, güncelleştirme veya bağlantı hattını silme ve eşlemelerin sağlamasını kaldırma işlemleri de anlatılmaktadır. Bir, iki veya üç eşlemenin tamamını (Azure özel, Azure genel ve Microsoft) bir ExpressRoute bağlantı hattı için yapılandırabilirsiniz. Eşlemeleri seçtiğiniz herhangi bir sırayla yapılandırabilirsiniz. Ancak, her eşlemenin yapılandırmasını birer birer tamamladığınızdan emin olmanız gerekir. 

Bu yönergeler yalnızca, katman 2 bağlantı hizmetleri sunan hizmet sağlayıcılarıyla oluşturulan devrelere uygulanır. Yönetilen sunan bir hizmet sağlayıcısı kullanıyorsanız, Katman 3 Hizmetleri (genellikle gibi bir IPVPN MPLS), bağlantı sağlayıcınız yapılandıracak ve yönlendirmeyi sizin için yönetme.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure dağıtım modelleri hakkında**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Yapılandırma önkoşulları

* Yapılandırmaya başlamadan önce [önkoşullar](expressroute-prerequisites.md) sayfasını, [yönlendirme gereksinimleri](expressroute-routing.md) sayfasını ve [iş akışları](expressroute-workflows.md) sayfasını gözden geçirdiğinizden emin olun.
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. [Bir ExpressRoute](expressroute-howto-circuit-classic.md) bağlantı hattı oluşturmak ve devam etmeden önce bağlantı sağlayıcınız için devre dışı bırakmak için yönergeleri izleyin. Aşağıda açıklanan cmdlet’leri çalıştırmanız için ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.

### <a name="download-the-latest-powershell-cmdlets"></a>En son PowerShell cmdlet 'lerini indirin

Azure Hizmet Yönetimi (SM) PowerShell modüllerine ve ExpressRoute modülünün en son sürümlerini yükleyin. Aşağıdaki örnek kullanırken, cmdlet'leri daha yeni sürümleri çıktıkça sürüm numarasını (Bu örnekte, 5.1.1) değişeceğini unutmayın.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Daha fazla bilgi için, bilgisayarınızı Azure PowerShell modüllerini kullanacak şekilde yapılandırma hakkında adım adım yönergeler için [Azure PowerShell cmdlet 'leriyle çalışmaya](/powershell/azure/overview) başlama konusuna bakın.

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

## <a name="azure-private-peering"></a>Azure özel eşlemesi

Bu bölümde bir ExpressRoute bağlantı hattı için Azure özel eşleme yapılandırmasını oluşturma, alma, güncelleştirme ve silme hakkında yönergeler açıklanmaktadır. 

### <a name="to-create-azure-private-peering"></a>Azure özel eşlemesi oluşturmak için

1. **Bir ExpressRoute bağlantı hattı oluşturun.**

   Bir [ExpressRoute bağlantı hattı](expressroute-howto-circuit-classic.md) oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcısından bağlantı hattını sağlamasını isteyin. Bağlantı sağlayıcınız yönetilen Katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcınızdan sizin için Azure özel eşlemeyi etkinleştirmesini isteyebilirsiniz. Bu durumda, sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmiyorsa, bağlantı hattınızı oluşturduktan sonra aşağıdaki yönergeleri izleyin.
2. **Sağlandığından emin olmak için ExpressRoute devresini denetleyin.**
   
   ExpressRoute bağlantı hattının sağlanıp sağlanmadığını ve etkinleştirildiğinden emin olun.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Döndürülmesini

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Devre dışı olduğundan ve devre dışı olduğundan emin olun. Değilse, bağlantı sağlayıcınızla birlikte çalışarak gerekli durum ve durum için devre alanınızı alın.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Devre için Azure özel eşlemesini yapılandırın.**

   Sonraki adımlara devam etmeden önce aşağıdaki öğelerin bulunduğundan emin olun:
   
   * Birincil bağlantı için bir /30 alt ağı. Bu, sanal ağlar için ayrılmış herhangi bir adres alanının parçası olmamalıdır.
   * İkincil bağlantı için bir /30 alt ağı. Bu, sanal ağlar için ayrılmış herhangi bir adres alanının parçası olmamalıdır.
   * Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Devre içindeki başka bir eşlemenin aynı VLAN KIMLIĞINI kullanmadığını doğrulayın.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz. Bu eşleme için özel bir AS numarası kullanabilirsiniz. 65515 kullandığınızı doğrulayın.
   * Kullanmayı seçerseniz bir MD5 karma değeri. **İsteğe bağlı**.
     
   Devreniz için Azure özel eşlemesini yapılandırmak üzere aşağıdaki örneği kullanabilirsiniz:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Bir MD5 karması kullanmak istiyorsanız, devrenizin özel eşlemesini yapılandırmak için aşağıdaki örneği kullanın:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > AS numaranızı, müşteri ASN değil, eşleme ASN olarak belirttiğinizden emin olun.
   > 

### <a name="to-view-azure-private-peering-details"></a>Azure özel eşleme ayrıntılarını görüntülemek için

Aşağıdaki cmdlet 'i kullanarak yapılandırma ayrıntılarını görüntüleyebilirsiniz:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Döndürülmesini

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Azure özel eşleme yapılandırmasını güncelleştirmek için

Aşağıdaki cmdlet'i kullanarak yapılandırmanın herhangi bir bölümünü güncelleştirebilirsiniz. Aşağıdaki örnekte, devrenin VLAN KIMLIĞI 100 ' den 500 ' e güncelleştiriliyor.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Azure özel eşlemeyi silmek için

Aşağıdaki cmdlet'i çalıştırarak eşleme yapılandırmanızı kaldırabilirsiniz. Bu cmdlet 'i çalıştırmadan önce tüm sanal ağların ExpressRoute bağlantı hattından bağlantısının olmadığından emin olmanız gerekir.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure ortak eşleme

Bu bölümde bir ExpressRoute bağlantı hattı için Azure ortak eşleme yapılandırmasını oluşturma, alma, güncelleştirme ve silme hakkında yönergeler açıklanmaktadır.

> [!NOTE]
> Azure genel eşlemesi yeni devreler için kullanım dışıdır.
>

### <a name="to-create-azure-public-peering"></a>Azure ortak eşlemesi oluşturmak için

1. **ExpressRoute bağlantı hattı oluşturma**

   Bir [ExpressRoute bağlantı hattı](expressroute-howto-circuit-classic.md) oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcısından bağlantı hattını sağlamasını isteyin. Bağlantı sağlayıcınız yönetilen Katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcınızdan sizin için Azure ortak eşlemeyi etkinleştirmesini isteyebilirsiniz. Bu durumda, sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmiyorsa, bağlantı hattınızı oluşturduktan sonra aşağıdaki yönergeleri izleyin.
2. **Sağlandığını doğrulamak için ExpressRoute devresini denetleyin**

   Önce ExpressRoute ağ geçidinin Sağlandığından ve Etkin durumda olduğundan emin olmanız gerekir.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Döndürülmesini

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Devre dışı seçeneğinin sağlandığını ve etkinleştirildiğini doğrulayın. Değilse, bağlantı sağlayıcınızla birlikte çalışarak gerekli durum ve durum için devre alanınızı alın.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Devre için Azure ortak eşlemesini yapılandırma**
   
   Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun:
   
   * Birincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * İkincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Devre içindeki başka bir eşlemenin aynı VLAN KIMLIĞINI kullanmadığını doğrulayın.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * Kullanmayı seçerseniz bir MD5 karma değeri. **İsteğe bağlı**.

   > [!IMPORTANT]
   > AS numaranızı, müşteri ASN değil, eşleme ASN olarak belirttiğinizden emin olun.
   >  
     
   Devreniz için Azure ortak eşlemesini yapılandırmak üzere aşağıdaki örneği kullanabilirsiniz:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Bir MD5 karması kullanmak istiyorsanız, devrenizi yapılandırmak için aşağıdaki örneği kullanın:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Azure ortak eşleme ayrıntılarını görüntülemek için

Yapılandırma ayrıntılarını görüntülemek için aşağıdaki cmdlet 'i kullanın:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Döndürülmesini

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Azure ortak eşleme yapılandırmasını güncelleştirmek için

Aşağıdaki cmdlet'i kullanarak yapılandırmanın herhangi bir bölümünü güncelleştirebilirsiniz. Bu örnekte, bağlantı hattının VLAN kimliği 200'den 600 olarak güncelleştiriliyor.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Devre dışı seçeneğinin sağlandığını ve etkinleştirildiğini doğrulayın. 
### <a name="to-delete-azure-public-peering"></a>Azure ortak eşlemesini silmek için

Aşağıdaki cmdlet'i çalıştırarak eşleme yapılandırmanızı kaldırabilirsiniz:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft eşlemesi

Bu bölümde bir ExpressRoute bağlantı hattı için Microsoft eşleme yapılandırmasını oluşturma, alma, güncelleştirme ve silme hakkında yönergeler açıklanmaktadır. 

### <a name="to-create-microsoft-peering"></a>Microsoft eşlemesi oluşturmak için

1. **ExpressRoute bağlantı hattı oluşturma**
  
   Bir [ExpressRoute bağlantı hattı](expressroute-howto-circuit-classic.md) oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcısından bağlantı hattını sağlamasını isteyin. Bağlantı sağlayıcınız yönetilen Katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcınızdan sizin için Azure özel eşlemeyi etkinleştirmesini isteyebilirsiniz. Bu durumda, sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmiyorsa, bağlantı hattınızı oluşturduktan sonra aşağıdaki yönergeleri izleyin.
2. **Sağlandığını doğrulamak için ExpressRoute devresini denetleyin**

   Devre dışı seçeneğinin sağlandığını ve etkinleştirildiğini doğrulayın. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Döndürülmesini
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Devre dışı seçeneğinin sağlandığını ve etkinleştirildiğini doğrulayın. Değilse, bağlantı sağlayıcınızla birlikte çalışarak gerekli durum ve durum için devre alanınızı alın.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Devre için Microsoft eşlemesini yapılandırma**
   
    Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun.
   
   * Birincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır.
   * İkincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır.
   * Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Devre içindeki başka bir eşlemenin aynı VLAN KIMLIĞINI kullanmadığını doğrulayın.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * Tanıtılan önekler: BGP oturumunda tanıtmayı planladığınız tüm öneklerin bir listesini sağlamanız gerekir. Yalnızca ortak IP adresi ön ekleri kabul edilir. Bir önek kümesi göndermek için plan yaparsanız, virgülle ayrılmış bir liste gönderebilirsiniz. Bu ön ekler size bir RIR / IRR içinde kaydedilmiş olmalıdır.
   * Müşteri ASN’si: Eşleme AS numarasına kayıtlı olmayan önekler tanıtıyorsanız, kayıtlı oldukları AS numarasını belirtebilirsiniz. **İsteğe bağlı**.
   * Yönlendirme Kayıt Defteri Adı: AS numarası ve öneklerinin kaydedildiği RIR / IRR’yi belirtebilirsiniz.
   * Kullanmayı seçerseniz bir MD5 karma değeri. **İsteğe bağlı.**
     
   Devreniz için Microsoft eşlemesini yapılandırmak üzere aşağıdaki cmdlet 'i çalıştırın:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Microsoft eşleme ayrıntılarını görüntülemek için

Aşağıdaki cmdlet 'i kullanarak yapılandırma ayrıntılarını görüntüleyebilirsiniz:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Döndürülmesini

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Microsoft eşlemesi yapılandırmasını güncelleştirmek için

Aşağıdaki cmdlet 'i kullanarak yapılandırmanın herhangi bir bölümünü güncelleştirebilirsiniz:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft eşlemesini silmek için

Aşağıdaki cmdlet'i çalıştırarak eşleme yapılandırmanızı kaldırabilirsiniz:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Sonraki adımlar

Ardından, [bir sanal ağı bir ExpressRoute devresine bağlayın](expressroute-howto-linkvnet-classic.md).

* İş akışları hakkında daha fazla bilgi için bkz. [ExpressRoute iş akışları](expressroute-workflows.md).
* Bağlantı hattı eşlemesi hakkında daha fazla bilgi için bkz. [ExpressRoute bağlantı hattı ve yönlendirme etki alanları](expressroute-circuit-peerings.md).
