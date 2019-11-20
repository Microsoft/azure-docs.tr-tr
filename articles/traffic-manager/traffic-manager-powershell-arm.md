---
title: Azure 'da Traffic Manager yönetmek için PowerShell kullanma
description: Bu öğrenme yoluyla, Traffic Manager için Azure PowerShell kullanmaya başlayın.
services: traffic-manager
documentationcenter: na
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: f8dd01f22dec58c3345798b391c1c37c968d1025
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038116"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Traffic Manager yönetmek için PowerShell kullanma

Azure Resource Manager, Azure 'daki hizmetler için tercih edilen yönetim arabirimidir. Azure Traffic Manager profilleri, Azure Resource Manager tabanlı API 'Ler ve araçlar kullanılarak yönetilebilir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Kaynak modeli

Azure Traffic Manager, Traffic Manager profili adlı bir ayarlar koleksiyonu kullanılarak yapılandırılır. Bu profil DNS ayarları, trafik yönlendirme ayarları, uç nokta izleme ayarları ve trafiğin yönlendirildiği hizmet uç noktalarının bir listesini içerir.

Her Traffic Manager profili ' TrafficManagerProfiles ' türünde bir kaynak tarafından temsil edilir. REST API düzeyinde, her profil için URI aşağıdaki gibidir:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell ayarlama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu yönergeler Microsoft Azure PowerShell kullanır. Aşağıdaki makalede Azure PowerShell nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır.

* [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview)

Bu makaledeki örneklerde, var olan bir kaynak grubunuz olduğu varsayılır. Aşağıdaki komutu kullanarak bir kaynak grubu oluşturabilirsiniz:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager tüm kaynak gruplarının bir konuma sahip olmasını gerektirir. Bu konum, bu kaynak grubunda oluşturulan kaynaklar için varsayılan değer olarak kullanılır. Ancak, Traffic Manager profil kaynakları genel olduğundan, bölgesel olmadığından kaynak grubu konumunun seçimi Azure Traffic Manager üzerinde hiçbir etkiye sahip değildir.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

Traffic Manager profili oluşturmak için `New-AzTrafficManagerProfile` cmdlet 'ini kullanın:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Aşağıdaki tabloda parametreler açıklanmaktadır:

| Parametre | Açıklama |
| --- | --- |
| Ad |Traffic Manager profili kaynağı için kaynak adı. Aynı kaynak grubundaki profillerin benzersiz adlara sahip olması gerekir. Bu ad DNS sorguları için kullanılan DNS adından ayrıdır. |
| ResourceGroupName |Profil kaynağını içeren kaynak grubunun adı. |
| TrafficRoutingMethod |DNS sorgusunun yanıt olarak hangi uç noktanın döndürüleceğini belirleyen trafik yönlendirme yöntemini belirtir. Olası değerler ' Performance ', ' ağırlıklı ' veya ' Priority ' değerleridir. |
| RelativeDnsName |Bu Traffic Manager profili tarafından belirtilen DNS adının ana bilgisayar adı bölümünü belirtir. Bu değer, profilin tam etki alanı adını (FQDN) oluşturmak için Azure Traffic Manager tarafından kullanılan DNS etki alanı adıyla birleştirilir. Örneğin, ' contoso ' değerinin ayarlanması ' contoso.trafficmanager.net. ' olur. |
| TTL |DNS yaşam süresi (TTL) değerini saniye cinsinden belirtir. Bu TTL, yerel DNS çözümleyicilerine ve DNS istemcilerine bu Traffic Manager profilinin DNS yanıtlarını ne kadar süre önbellekte olduğunu bildirir. |
| MonitorProtocol |Uç nokta durumunu izlemek için kullanılacak protokolü belirtir. Olası değerler ' HTTP ' ve ' HTTPS ' değerleridir. |
| MonitorPort |Uç nokta durumunu izlemek için kullanılan TCP bağlantı noktasını belirtir. |
| MonitorPath |Uç nokta sistem durumunu yoklamak için kullanılan uç nokta etki alanı adına göre yolu belirtir. |

Cmdlet 'i Azure 'da bir Traffic Manager profili oluşturur ve PowerShell 'e karşılık gelen bir profil nesnesi döndürür. Bu noktada, profil herhangi bir uç nokta içermez. Traffic Manager profile uç noktalar ekleme hakkında daha fazla bilgi için bkz. Traffic Manager uç noktaları ekleme.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager profili al

Varolan bir Traffic Manager profili nesnesini almak için `Get-AzTrafficManagerProfle` cmdlet 'ini kullanın:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Bu cmdlet bir Traffic Manager profili nesnesi döndürür.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager profilini güncelleştirme

Traffic Manager profillerinin değiştirilmesi 3 adımlı bir işlem izler:

1. `Get-AzTrafficManagerProfile` kullanarak profili alın veya `New-AzTrafficManagerProfile`tarafından döndürülen profili kullanın.
2. Profili değiştirin. Uç noktaları ekleyebilir ve kaldırabilir veya Endpoint veya profil parametrelerini değiştirebilirsiniz. Bu değişiklikler çevrimdışı işlemlerdir. Yalnızca, profili temsil eden bellekteki yerel nesneyi değiştiriyorsunuz.
3. `Set-AzTrafficManagerProfile` cmdlet 'ini kullanarak değişikliklerinizi işleyin.

Profilin RelativeDnsName 'ı dışında tüm profil özellikleri değiştirilebilir. RelativeDnsName 'yi değiştirmek için profili ve yeni bir adla yeni bir profili silmeniz gerekir.

Aşağıdaki örnekte profil TTL 'nin nasıl değiştirileceği gösterilmektedir:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Üç tür Traffic Manager uç noktası vardır:

1. **Azure uç noktaları** , Azure 'da barındırılan hizmetlerdir
2. **Dış uç noktalar** Azure dışında barındırılan hizmetlerdir
3. **Iç Içe geçmiş uç noktalar** , Traffic Manager profillerinin iç içe hiyerarşilerini oluşturmak için kullanılır. İç içe geçmiş uç noktalar, karmaşık uygulamalar için gelişmiş trafik yönlendirme yapılandırmalarına olanak tanır.

Üç durumda, uç noktalar iki şekilde eklenebilir:

1. Daha önce açıklanan 3 adımlı bir süreç kullanılıyor. Bu yöntemin avantajı, tek bir güncelleştirmede birkaç uç nokta değişikliği yapılabilme yöntemidir.
2. New-AzTrafficManagerEndpoint cmdlet 'ini kullanma. Bu cmdlet, tek bir işlemde varolan bir Traffic Manager profiline bir uç nokta ekler.

## <a name="adding-azure-endpoints"></a>Azure Uç Noktaları ekleme

Azure 'da barındırılan Azure uç noktaları başvuru hizmetleri. İki tür Azure uç noktası desteklenir:

1. Azure uygulama hizmeti
2. Azure Publicıpaddress kaynakları (bir yük dengeleyiciye veya bir sanal makine NIC 'sine iliştirilebilecek). Publicıpaddress, Traffic Manager kullanılmak üzere atanmış bir DNS adına sahip olmalıdır.

Her durumda:

* Hizmet, `Add-AzTrafficManagerEndpointConfig` veya `New-AzTrafficManagerEndpoint`' Targetresourceıd ' parametresi kullanılarak belirtilir.
* ' Target ' ve ' EndpointLocation ', Targetresourceıd tarafından kapsanıyor.
* ' Weight ' belirtildiğinde isteğe bağlı. Ağırlıklar yalnızca profil ' ağırlıklı ' trafik-yönlendirme yöntemini kullanacak şekilde yapılandırıldıysa kullanılır. Aksi takdirde, bunlar yok sayılır. Belirtilmişse değer 1 ile 1000 arasında bir sayı olmalıdır. Varsayılan değer ' 1 '.
* ' Priority ' belirtildiğinde isteğe bağlıdır. Öncelikler yalnızca profil ' Priority ' trafik-yönlendirme yöntemini kullanacak şekilde yapılandırıldıysa kullanılır. Aksi takdirde, bunlar yok sayılır. Geçerli değerler 1 ile 1000 arasında, daha düşük değerler daha yüksek önceliğe işaret eden değerlerdir. Bir uç nokta için belirtilmişse, bunların tüm uç noktalar için belirtilmesi gerekir. Atlanırsa, ' 1 ' den başlayan varsayılan değerler bitiş noktalarının listelendiği sırada uygulanır.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Örnek 1: `Add-AzTrafficManagerEndpointConfig` kullanarak App Service uç noktaları ekleme

Bu örnekte, bir Traffic Manager profili oluşturur ve `Add-AzTrafficManagerEndpointConfig` cmdlet 'ini kullanarak iki App Service uç nokta eklersiniz.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Örnek 2: `New-AzTrafficManagerEndpoint` kullanarak Publicıpaddress uç noktası ekleme

Bu örnekte, Traffic Manager profiline bir genel IP adresi kaynağı eklenir. Genel IP adresi, yapılandırılmış bir DNS adına sahip olmalıdır ve bir VM 'nin ya da bir yük dengeleyicinin NIC 'sine bağlanabilir.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Dış Uç Noktalar ekleme

Traffic Manager, trafiği Azure dışında barındırılan hizmetlere yönlendirmek için dış uç noktaları kullanır. Azure uç noktalarında olduğu gibi, dış uç noktalar `Add-AzTrafficManagerEndpointConfig`, ardından `Set-AzTrafficManagerProfile`veya `New-AzTrafficManagerEndpoint`kullanılarak eklenebilir.

Dış uç noktaları belirtirken:

* Uç nokta etki alanı adının ' Target ' parametresi kullanılarak belirtilmesi gerekir
* ' Performance ' trafik-yönlendirme yöntemi kullanılırsa, ' EndpointLocation ' gereklidir. Aksi takdirde, isteğe bağlıdır. Değerin [geçerli bir Azure bölge adı](https://azure.microsoft.com/regions/)olması gerekir.
* ' Weight ' ve ' Priority ' isteğe bağlıdır.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Örnek 1: `Add-AzTrafficManagerEndpointConfig` ve `Set-AzTrafficManagerProfile` kullanarak dış uç noktalar ekleme

Bu örnekte, bir Traffic Manager profili oluşturur, iki dış uç nokta ekler ve değişiklikleri işleyin.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Örnek 2: `New-AzTrafficManagerEndpoint` kullanarak dış uç noktalar ekleme

Bu örnekte, var olan bir profile dış uç nokta ekleyeceğiz. Profil, profil ve kaynak grubu adları kullanılarak belirtilir.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>' Iç Içe geçmiş ' uç noktaları ekleme

Her Traffic Manager profili tek bir trafik yönlendirme yöntemini belirtir. Ancak, tek bir Traffic Manager profili tarafından sağlanmış olan yönlendirmeden daha karmaşık trafik yönlendirmesi gerektiren senaryolar vardır. Birden fazla trafik yönlendirme yönteminin avantajlarını birleştirmek için Traffic Manager profillerini iç içe geçirebilirsiniz. İç içe geçmiş profiller, daha büyük ve daha karmaşık uygulama dağıtımlarını desteklemek için varsayılan Traffic Manager davranışını geçersiz kılmanızı sağlar. Daha ayrıntılı örnekler için bkz. [Iç içe Traffic Manager profilleri](traffic-manager-nested-profiles.md).

İç içe geçmiş uç noktalar, belirli bir uç nokta türü olan ' NestedEndpoints ' kullanılarak üst profilde yapılandırılır. İç içe geçmiş uç noktaları belirtirken:

* Uç noktanın ' Targetresourceıd ' parametresi kullanılarak belirtilmesi gerekir
* ' Performance ' trafik-yönlendirme yöntemi kullanılırsa, ' EndpointLocation ' gereklidir. Aksi takdirde, isteğe bağlıdır. Değerin [geçerli bir Azure bölge adı](https://azure.microsoft.com/regions/)olması gerekir.
* Azure uç noktaları için ' Weight ' ve ' Priority ' isteğe bağlıdır.
* ' MinChildEndpoints ' parametresi isteğe bağlıdır. Varsayılan değer ' 1 '. Kullanılabilir uç noktaların sayısı bu eşiğin altına düşerse, üst profil alt profilini ' düşürülmüş ' olarak değerlendirir ve trafiği üst profildeki diğer uç noktalara göre değerlendirir.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Örnek 1: `Add-AzTrafficManagerEndpointConfig` ve `Set-AzTrafficManagerProfile` kullanarak iç içe geçmiş uç noktaları ekleme

Bu örnekte, yeni Traffic Manager alt ve üst profiller oluşturur, alt öğeyi üst öğeye iç içe geçmiş bir uç nokta olarak ekler ve değişiklikleri işleyin.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Bu örnekteki kısaltma için alt veya üst profillere başka bir uç nokta eklemedik.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Örnek 2: `New-AzTrafficManagerEndpoint` kullanarak iç içe geçmiş uç noktaları ekleme

Bu örnekte, var olan bir alt profili var olan bir üst profile iç içe geçmiş bir uç nokta olarak ekleyeceğiz. Profil, profil ve kaynak grubu adları kullanılarak belirtilir.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Başka bir abonelikten uç noktalar ekleme

Traffic Manager, farklı aboneliklerdeki uç noktalarla çalışabilir. Traffic Manager için gereken girişi almak üzere eklemek istediğiniz uç noktayla aboneliğe geçmeniz gerekir. Ardından Traffic Manager profiliyle abonelikler 'e geçmeniz ve uç noktasını buna eklemeniz gerekir. Aşağıdaki örnek, bunun genel IP adresi ile nasıl yapılacağını göstermektedir.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager uç noktasını güncelleştirme

Mevcut bir Traffic Manager uç noktasını güncelleştirmenin iki yolu vardır:

1. `Get-AzTrafficManagerProfile`kullanarak Traffic Manager profilini alın, profil içindeki uç nokta özelliklerini güncelleştirin ve `Set-AzTrafficManagerProfile`kullanarak değişiklikleri işleyin. Bu yöntem, tek bir işlemde birden fazla uç noktayı güncelleştirebilmekte olan avantajına sahiptir.
2. `Get-AzTrafficManagerEndpoint`kullanarak Traffic Manager uç noktasını alın, uç nokta özelliklerini güncelleştirin ve `Set-AzTrafficManagerEndpoint`kullanarak değişiklikleri işleyin. Bu yöntem, profilde bitiş noktaları dizisine dizin oluşturulmasını gerektirmediğinden daha basittir.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Örnek 1: `Get-AzTrafficManagerProfile` ve `Set-AzTrafficManagerProfile` kullanarak uç noktaları güncelleştirme

Bu örnekte, var olan bir profil içindeki iki uç nokta için önceliği değiştiririz.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Örnek 2: `Get-AzTrafficManagerEndpoint` ve `Set-AzTrafficManagerEndpoint` kullanarak uç nokta güncelleştirme

Bu örnekte, var olan bir profildeki tek bir uç noktanın ağırlığını değiştiririz.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Uç noktaları ve profilleri etkinleştirme ve devre dışı bırakma

Traffic Manager, her bir uç noktanın etkinleştirilmesini ve devre dışı bırakılmasını, ayrıca tüm profillerin etkinleştirilmesini ve devre dışı bırakılmasını sağlar.
Bu değişiklikler, uç nokta veya profil kaynakları alınıyor/güncelleştiriliyor/ayarlanarak yapılabilir. Bu ortak işlemleri kolaylaştırmak için, adanmış cmdlet 'ler aracılığıyla da desteklenir.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Örnek 1: Traffic Manager profilini etkinleştirme ve devre dışı bırakma

Traffic Manager profilini etkinleştirmek için `Enable-AzTrafficManagerProfile`kullanın. Profil, profil nesnesi kullanılarak belirtilebilir. Profil nesnesi işlem hattı aracılığıyla veya '-TrafficManagerProfile ' parametresi kullanılarak geçirilebilir. Bu örnekte profili profil ve kaynak grubu adı ile belirttik.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager profilini devre dışı bırakmak için:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzTrafficManagerProfile cmdlet 'i onay ister. Bu istem '-zorla ' parametresi kullanılarak gizlenebilir.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Örnek 2: Traffic Manager uç noktasını etkinleştirme ve devre dışı bırakma

Traffic Manager uç noktası etkinleştirmek için `Enable-AzTrafficManagerEndpoint`kullanın. Uç noktasını belirtmek için iki yol vardır

1. İşlem hattı aracılığıyla veya '-TrafficManagerEndpoint ' parametresi kullanılarak geçirilen bir TrafficManagerEndpoint nesnesi kullanma
2. Uç nokta adı, uç nokta türü, profil adı ve kaynak grubu adını kullanma:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Benzer şekilde, bir Traffic Manager uç noktasını devre dışı bırakmak için:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

`Disable-AzTrafficManagerProfile`olduğu gibi, `Disable-AzTrafficManagerEndpoint` cmdlet 'i onay ister. Bu istem '-zorla ' parametresi kullanılarak gizlenebilir.

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager uç noktasını silme

Tek uç noktaları kaldırmak için `Remove-AzTrafficManagerEndpoint` cmdlet 'ini kullanın:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Bu cmdlet onay ister. Bu istem '-zorla ' parametresi kullanılarak gizlenebilir.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager profilini silme

Bir Traffic Manager profilini silmek için, profil ve kaynak grubu adlarını belirterek `Remove-AzTrafficManagerProfile` cmdlet 'ini kullanın:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Bu cmdlet onay ister. Bu istem '-zorla ' parametresi kullanılarak gizlenebilir.

Silinecek profil bir profil nesnesi kullanılarak da belirtilebilir:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Bu dizi Ayrıca şu şekilde olabilir:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Sonraki adımlar

[Traffic Manager izleme](traffic-manager-monitoring.md)

[Traffic Manager için performans konuları](traffic-manager-performance-considerations.md)
