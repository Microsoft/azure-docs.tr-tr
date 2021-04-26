---
title: 'Hızlı başlangıç: ExpressRoute ile devre oluşturma ve değiştirme Azure PowerShell'
description: Bu hızlı başlangıçta bir ExpressRoute bağlantı hattı oluşturma, sağlama, doğrulama, güncelleştirme, silme ve sağlamayı kaldırma işlemlerinin nasıl yapılacağı gösterilir.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-api
ms.openlocfilehash: 50307e40526f5cce4d1349180b29fac00fb70943
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534960"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak bir ExpressRoute bağlantı hattı oluşturma ve değiştirme

Bu hızlı başlangıçta, PowerShell cmdlet 'lerini ve Azure Resource Manager dağıtım modelini kullanarak bir ExpressRoute bağlantı hattı oluşturma gösterilmektedir. Ayrıca, durumu denetleyebilir, güncelleştirebilir, silebilir veya devre sağlamayı kaldırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md) ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.
* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute bağlantı hattı oluşturma ve sağlama
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Desteklenen sağlayıcıların, konumların ve bant genişliklerinin listesini al
ExpressRoute bağlantı hattı oluşturmadan önce, desteklenen bağlantı sağlayıcıları, konumlar ve bant genişliği seçeneklerinin listesine ihtiyacınız vardır.

**Get-AzExpressRouteServiceProvider** PowerShell cmdlet 'i, sonraki adımlarda kullanacağınız bu bilgileri döndürür:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Bağlantı sağlayıcınızın orada listelenip listelenmediğini denetleyin. Daha sonra bir devre oluşturduğunuzda ihtiyacınız olan aşağıdaki bilgileri unutmayın:

* Name
* PeeringLocations
* BandwidthsOffered

Artık bir ExpressRoute devresi oluşturmaya hazırsınız.

### <a name="create-an-expressroute-circuit"></a>ExpressRoute bağlantı hattı oluşturma
Henüz bir kaynak grubunuz yoksa, ExpressRoute bağlantı hattını oluşturmadan önce bir tane oluşturmanız gerekir. Şu komutu çalıştırarak bunu yapabilirsiniz:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Aşağıdaki örnek, bir 200 Mbps ExpressRoute bağlantı hattının, Silicon Valley içinde Equinx aracılığıyla nasıl oluşturulacağını göstermektedir. Farklı bir sağlayıcı ve farklı ayarlar kullanıyorsanız, isteğinizi yaparken bu bilgileri değiştirin. Yeni bir hizmet anahtarı istemek için aşağıdaki örneği kullanın:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Doğru SKU katmanını ve SKU ailesini belirttiğinizden emin olun:

* SKU katmanı, bir ExpressRoute devresinin [Yerel](expressroute-faqs.md#expressroute-local), standart veya [Premium](expressroute-faqs.md#expressroute-premium)olduğunu belirler. *Yerel*, * standart veya *Premium* belirtebilirsiniz. SKU 'YU *Standart/Premium* 'dan *Yerel* olarak değiştiremezsiniz.
* SKU ailesi Faturalandırma türünü belirler. Tarifeli veri planı için *MeteredData* , sınırsız bir veri planı Için de *unlimiteddata* belirtebilirsiniz. Faturalandırma türünü *MeteredData* 'Den *unlimiteddata* olarak değiştirebilirsiniz, ancak türü *Unlimiteddata* iken *MeteredData* olarak değiştiremezsiniz. *Yerel* bir bağlantı hattı her zaman *ayrıcalıklıya verisi* değildir.

> [!IMPORTANT]
> ExpressRoute bağlantı hattı, hizmet anahtarının verildiği andan itibaren faturalandırılır. Bağlantı sağlayıcısı devre sağlamaya hazırsa bu işlemi gerçekleştirdiğinizden emin olun.
>

Yanıt, hizmet anahtarını içerir. Aşağıdaki komutu çalıştırarak tüm parametrelerin ayrıntılı açıklamalarını alabilirsiniz:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Tüm ExpressRoute devreleri listeleyin
Oluşturduğunuz tüm ExpressRoute devrelerinin listesini almak için **Get-Azexpressroutedevresi** komutunu çalıştırın:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Bu bilgileri, cmdlet 'ini kullanarak istediğiniz zaman alabilirsiniz `Get-AzExpressRouteCircuit` . Hiçbir parametre olmadan çağrının yapılması tüm devreleri listeler. Hizmet anahtarınız *ServiceKey* alanında listelenir:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Hizmet anahtarını sağlama için bağlantı sağlayıcınıza gönderin
*Serviceproviderprovisioningstate* , hizmet sağlayıcı tarafında sağlama durumunun geçerli durumu hakkında bilgi sağlar. Durum size Microsoft tarafında durum verir. Devre sağlama durumları hakkında daha fazla bilgi için bkz. [Iş akışları](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Yeni bir ExpressRoute bağlantı hattı oluşturduğunuzda, devre aşağıdaki durumda olur:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Bağlantı sağlayıcısı şu anda sizin için etkinleştirdiğinden, devre aşağıdaki duruma geçer:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

ExpressRoute devresini kullanmak için, aşağıdaki durumda olmalıdır:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Devre anahtarının durumunu ve durumunu düzenli olarak denetleyin
Hizmet anahtarının durumunun ve durumunun denetlenmesi, sağlayıcınızın devrenizi ne zaman sağladığından haberdar etmenize olanak tanır. Devre yapılandırıldıktan sonra, aşağıdaki örnekte gösterildiği gibi *Serviceproviderprovisioningstate* *sağlandı* olarak görünür:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>Yönlendirme yapılandırmanızı oluşturma
Adım adım yönergeler için, bkz. bağlantı hattı eşlemelerini oluşturmak ve değiştirmek için [ExpressRoute devrouting yapılandırma](expressroute-howto-routing-arm.md) makalesi.

> [!IMPORTANT]
> Bu yönergeler yalnızca, katman 2 bağlantı hizmetleri sunan hizmet sağlayıcılarıyla oluşturulan devrelere uygulanır. Yönetilen katman 3 Hizmetleri (genellikle MPLS gibi bir IP VPN) sunan bir hizmet sağlayıcısı kullanıyorsanız, bağlantı sağlayıcınız yönlendirmeyi yapılandırır ve yönetir.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute bağlantı hattına bir sanal ağı bağlama
Ardından, bir sanal ağı ExpressRoute bağlantı hattına bağlayın. Kaynak Yöneticisi dağıtım modeliyle çalışırken [sanal ağları ExpressRoute devrelerine bağlama](expressroute-howto-linkvnet-arm.md) makalesini kullanın.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute devresinin durumunu alma
**Get-Azexpressroutedevresi** cmdlet 'ini kullanarak bu bilgileri istediğiniz zaman alabilirsiniz. Hiçbir parametre olmadan çağrının yapılması tüm devreleri listeler.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Yanıt aşağıdaki örneğe benzer:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Kaynak grubu adını ve devre adını çağrıya bir parametre olarak geçirerek belirli bir ExpressRoute devresi hakkında bilgi alabilirsiniz:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                        "Name": "Standard_MeteredData",
                                        "Tier": "Standard",
                                        "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Aşağıdaki komutu çalıştırarak tüm parametrelerin ayrıntılı açıklamalarını alabilirsiniz:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute devresini değiştirme
Bağlantıyı etkilemeden bir ExpressRoute devresine ait belirli özellikleri değiştirebilirsiniz.

Kapalı kalma süresi olmadan aşağıdaki görevleri gerçekleştirebilirsiniz:

* ExpressRoute devreniz için bir ExpressRoute Premium eklentisini etkinleştirin veya devre dışı bırakın. SKU 'nun *Standart/Premium* 'dan *Yerel* olarak değiştirilmesi desteklenmez.
* Bağlantı noktasında kullanılabilir kapasiteyi sağlayan ExpressRoute bağlantı hattının bant genişliğini artırın. Devre dışı bir bağlantı hattının eski sürüme düşürülmesi desteklenmez.
* Ölçüm planını ölçülen verilerden sınırsız veriye değiştirin. Ölçüm planının sınırsız verilerden ölçülen verilerle değiştirilmesi desteklenmez.
* *Klasik Işlemlere Izin ver*' i etkinleştirebilir ve devre dışı bırakabilirsiniz.

Sınırlar ve sınırlamalar hakkında daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium eklentisini etkinleştirmek için
Aşağıdaki PowerShell kod parçacığını kullanarak mevcut bağlantı hattınızı ExpressRoute Premium eklentisi ' ni etkinleştirebilirsiniz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Devre dışı bırakıldı ExpressRoute Premium eklenti özellikleri etkinleştirilmiştir. Komut başarıyla çalıştırıldığında, Premium eklenti özelliği için faturalandırmaya başlayacağız.

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium eklentisini devre dışı bırakmak için
> [!IMPORTANT]
> Standart devre için izin verilen miktardan daha büyük kaynaklar kullanıyorsanız, bu işlem başarısız olabilir.
>

Aşağıdaki bilgileri not edin:

* Premium 'dan standart sürümüne düşürme yapmadan önce, bağlantı hattına bağlı sanal ağların sayısının 10 ' dan küçük olduğundan emin olmanız gerekir. Bunu yapmazsanız, güncelleştirme isteğiniz başarısız olur ve size Premium ücretler üzerinden faturalandırırız.
* Diğer geopolitik bölgelerdeki tüm sanal ağların ilk olarak bağlantısı kaldırılmalıdır. Bağlantıyı kaldıramazsanız, güncelleştirme isteğiniz başarısız olur ve Premium ücretlerden faturalandırıldığımızda çalışmaya devam ediyoruz.
* Yol tablonuz, özel eşleme için 4.000 rotadan az olmalıdır. Yol tablonuzun boyutu 4.000 rotadan büyükse BGP oturumu başlatılır. Tanıtılan ön ek sayısı 4.000 altına çıkana kadar BGP oturumu yeniden etkinleştirilmez.

Aşağıdaki PowerShell cmdlet 'ini kullanarak mevcut devrenin ExpressRoute Premium eklentisini devre dışı bırakabilirsiniz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute bağlantı hattı bant genişliğini güncelleştirmek için
Sağlayıcınız için desteklenen bant genişliği seçenekleri için bkz. [ExpressRoute SSS](expressroute-faqs.md). Mevcut devrenin boyutundan daha büyük bir boyut seçebilirsiniz.

> [!IMPORTANT]
> Mevcut bağlantı noktasında yetersiz kapasite varsa ExpressRoute devresini yeniden oluşturmanız gerekebilir. Bu konumda kullanılabilir ek kapasite yoksa, devreyi yükseltemezsiniz.
>
> Bir ExpressRoute devresinin bant genişliğini kesintiye uğramadan azaltamaz. Bant genişliğini düşürme işlemi, ExpressRoute bağlantı hattının sağlanması ve ardından yeni bir ExpressRoute devresine yeniden sağlanması gerektirir.
>

Hangi boyuta ihtiyacınız olduğuna karar verdikten sonra, devrenizi yeniden boyutlandırmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Devreniz Microsoft tarafında yükseltilecek. Daha sonra bu değişikliği eşleştirmek üzere kendi taraflarındaki yapılandırmaların güncelleştirilmesini sağlamak için bağlantı sağlayıcınızla iletişim kurmanız gerekir. Bu bildirimi yaptıktan sonra, güncelleştirilmiş bant genişliği seçeneği için faturalandırmaya başlayacağız.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU 'YU tarifeli 'ten sınırsız 'e taşımak için
Aşağıdaki PowerShell kod parçacığını kullanarak bir ExpressRoute devresine ait SKU 'sunu değiştirebilirsiniz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Klasik ve Kaynak Yöneticisi ortamlarına erişimi denetlemek için
[ExpressRoute devrelerini klasik Kaynak Yöneticisi dağıtım modeline taşıma](expressroute-howto-move-arm.md)bölümündeki yönergeleri gözden geçirin.

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute devresinin sağlamasını kaldırma
Aşağıdaki bilgileri not edin:

* Tüm sanal ağların ExpressRoute bağlantı hattı 'ndan bağlantısı kaldırılmalıdır. Bu işlem başarısız olursa, devline bağlı sanal ağların olup olmadığını denetleyin.
* ExpressRoute bağlantı hattı hizmeti sağlayıcısı sağlama **durumu sağlandıysa veya** sağlanmışsa  , devre dışı bırakmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Hizmet sağlayıcısı, devre sağlamasını kaldırmayı ve bize haber verene kadar, kaynakları ayırmaya ve sizi faturalandırmaya devam ediyoruz.
* Hizmet sağlayıcı, hizmet sağlayıcısı sağlama durumunun **sağlanmamış** olarak ayarlandığı anlamına gelen devre sağlamasını kaldırdıysa, devreyi silebilirsiniz. Devre için faturalandırma daha sonra durur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Aşağıdaki komutu çalıştırarak ExpressRoute devrenizi silebilirsiniz:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı hattını oluşturduktan ve sağlayıcınızda sağladıktan sonra, eşlemeyi yapılandırmak için sonraki adıma geçin:

> [!div class="nextstepaction"]
> [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-arm.md)
