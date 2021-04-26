---
title: Uç nokta kotasını artır-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS), uç nokta istek kotasını tek bir anahtarın kotasının ötesinde artırma yeteneği sağlar. Bu, lug 'ler için daha fazla anahtar oluşturularak ve bunları **kaynak ve anahtarlar** bölümündeki **Yayımla** sayfasında halsıs uygulamasına ekleyerek yapılır.
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609660"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Anahtarlar genelinde uç nokta kotasını yönetmek için Microsoft Azure Traffic Manager kullanma
Language Understanding (LUSıS), uç nokta istek kotasını tek bir anahtarın kotasının ötesinde artırma yeteneği sağlar. Bu, lug 'ler için daha fazla anahtar oluşturularak ve bunları **kaynak ve anahtarlar** bölümündeki **Yayımla** sayfasında halsıs uygulamasına ekleyerek yapılır.

İstemci-uygulamanın, anahtarlar genelinde trafiği yönetmesi vardır. LUSıS bunu yapmaz.

Bu makalede, Azure [Traffic Manager][traffic-manager-marketing]ile anahtarlar arasındaki trafiğin nasıl yönetileceği açıklanmaktadır. Eğitilen ve yayınlanmış bir LUSıS uygulamanız zaten olmalıdır. Bir tane yoksa, önceden oluşturulmuş etki alanı [hızlı](luis-get-started-create-app.md)başlangıcını izleyin.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Azure portal PowerShell 'e bağlanma
[Azure][azure-portal] portalında PowerShell penceresini açın. PowerShell penceresi simgesi, üst gezinti çubuğundaki **>_** . Portaldan PowerShell 'i kullanarak en son PowerShell sürümünü alırsınız ve kimliğiniz doğrulanır. Portalda PowerShell için bir [Azure depolama](https://azure.microsoft.com/services/storage/) hesabı gerekir.

![PowerShell penceresi açık Azure portal ekran görüntüsü](./media/traffic-manager/azure-portal-powershell.png)

Aşağıdaki bölümlerde [Traffic Manager PowerShell cmdlet 'leri](/powershell/module/az.trafficmanager/#traffic_manager)kullanılır.

## <a name="create-azure-resource-group-with-powershell"></a>PowerShell ile Azure Kaynak grubu oluşturma
Azure kaynaklarını oluşturmadan önce, tüm kaynakları içeren bir kaynak grubu oluşturun. Kaynak grubunu adlandırın `luis-traffic-manager` ve bölgeyi kullanın `West US` . Kaynak grubunun bölgesi, grupla ilgili meta verileri depolar. Kaynakları başka bir bölgedeyse yavaşlatmaz.

**[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** cmdlet 'i ile kaynak grubu oluştur:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Toplam bitiş noktası kotasını artırmak için LUSıS anahtarları oluşturma
1. Azure portal, bir içinde ve diğeri içinde olmak üzere iki **Language Understanding** anahtarı oluşturun `West US` `East US` . Adlı önceki bölümde oluşturulan mevcut kaynak grubunu kullanın `luis-traffic-manager` .

    ![Luin-Traffic-Manager kaynak grubundaki iki LUO anahtarlı Azure portal ekran görüntüsü](./media/traffic-manager/luis-keys.png)

2. [Lusıs][LUIS] Web sitesinde, **Yönet** bölümünde, **Azure kaynakları** sayfasında, uygulamaya anahtarlar atayın ve sağ üst menüdeki **Yayımla** düğmesini seçerek uygulamayı yeniden yayımlayın.

    **Uç nokta** SÜTUNUNDAKI örnek URL, bir sorgu parametresi olarak uç nokta ANAHTARıYLA bir get isteği kullanır. İki yeni anahtar uç nokta URL 'sini kopyalayın. Bu makalede daha sonra Traffic Manager yapılandırmanın bir parçası olarak kullanılırlar.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Traffic Manager anahtarlar arasında LUSıS uç noktası isteklerini yönetme
Traffic Manager uç noktalarınız için yeni bir DNS erişim noktası oluşturur. Ağ geçidi veya proxy olarak görev yapmaz, ancak DNS düzeyinde kesin olarak çalışmaz. Bu örnek, herhangi bir DNS kaydını değiştirmez. Bu belirli istek için doğru uç noktayı almak üzere Traffic Manager ile iletişim kurmak için bir DNS kitaplığı kullanır. Luönce amaçlanan _her_ Istek için, HANGI Lune uç noktanın kullanılacağını belirlemede bir Traffic Manager isteği gerekir.

### <a name="polling-uses-luis-endpoint"></a>Yoklama LUSıS uç noktasını kullanıyor
Uç noktaların hala kullanılabilir olduğundan emin olmak için uç noktaları düzenli aralıklarla yoklar Traffic Manager. Traffic Manager URL 'sinin bir GET isteğiyle erişilebilir olması ve bir 200 döndürmesi gerekir. **Yayımla** sayfasındaki uç nokta URL 'si bunu yapar. Her bitiş noktası anahtarında farklı bir yol ve sorgu dizesi parametreleri olduğundan, her bitiş noktası anahtarının farklı bir yoklama yolu olması gerekir. Her Traffic Manager her yokladığında, bir kota isteği maliyetlidir. Luo uç noktası için sorgu **dizesi parametresi,** lusıs 'e gönderilen söylenişi 'dir. Bu parametre, bir utterance göndermek yerine, Traffic Manager yapılandırılmış bir hata ayıklama tekniği olarak LUıN uç nokta günlüğüne Traffic Manager yoklamayı eklemek için kullanılır.

Her bir LUıN uç noktasının kendi yolu olması gerektiğinden, kendi Traffic Manager profiline ihtiyacı vardır. Profiller genelinde yönetmek için [ _iç içe_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) mimarisi oluşturun. Bir üst profil alt öğe profillerine işaret eder ve aralarındaki trafiği yönetir.

Traffic Manager yapılandırıldıktan sonra log = false sorgu dizesi parametresini kullanmak için yolu değiştirmeyi unutmayın. bu nedenle, günlüğeniz yoklamayla doldurulmaz.

## <a name="configure-traffic-manager-with-nested-profiles"></a>İç içe geçmiş profillerle Traffic Manager yapılandırma
Aşağıdaki bölümlerde, biri Doğu LUYA anahtarı ve Batı Luu anahtarı için olmak üzere iki alt profil oluşturulur. Sonra bir üst profil oluşturulur ve üst profile iki alt profil eklenir.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>PowerShell ile Doğu ABD Traffic Manager profili oluşturma
Doğu ABD Traffic Manager profili oluşturmak için birkaç adım vardır: profil oluşturma, uç nokta ekleme ve uç nokta ayarlama. Traffic Manager profilinde çok sayıda uç nokta olabilir, ancak her bitiş noktası aynı doğrulama yoluna sahiptir. Doğu ve Batı aboneliklerinin LUSıS uç nokta URL 'Leri bölge ve uç nokta anahtarı nedeniyle farklı olduğundan, her bir LUıN uç noktasının profilde tek bir uç nokta olması gerekir.

1. **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet 'i ile profil oluştur

    Profili oluşturmak için aşağıdaki cmdlet 'i kullanın. `appIdLuis`Ve ' i değiştirdiğinizden emin olun `subscriptionKeyLuis` . SubscriptionKey, Doğu ABD LUSıS anahtarına yöneliktir. Yol doğru değilse, LUSıS uygulama KIMLIĞI ve uç nokta anahtarı da dahil olmak üzere, `degraded` trafik yönetimi 'nın lusıs uç noktasını başarıyla isteyemediği için Traffic Manager yoklama durumudur. Değerinin, `q` `traffic-manager-east` Bu değeri lusıs uç nokta günlüklerinde görebilmenizi sağlayın.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Name|lusıs-profile-eastus|Azure portal Traffic Manager adı|
    |-ResourceGroupName|Lua-trafik-yönetici|Önceki bölümde oluşturuldu|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. Performans kullanılıyorsa, Traffic Manager URL isteği kullanıcının bölgesinden gelmelidir. Bir sohbet botu veya başka bir uygulamadan ilerleyecekseniz, Traffic Manager çağrısındaki bölgeyi taklit eden sohbet botu 'in sorumluluğundadır. |
    |-RelativeDnsName|Luo-DNS-eastus|Bu, hizmetin alt etki alanıdır: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Yoklama aralığı, 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUSıS için bağlantı noktası ve protokol HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|`<appIdLuis>`Ve `<subscriptionKeyLuis>` değerlerini kendi değerlerinizle değiştirin.|

    Başarılı bir isteğin yanıtı yok.

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet 'i ile Doğu ABD uç noktası ekleme

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-EndpointName|Lua-Doğu-uç noktası|Profil altında görünen uç nokta adı|
    |-TrafficManagerProfile|$eastprofile|1. adımda oluşturulan profil nesnesini kullanma|
    |-Tür|ExternalEndpoints|Daha fazla bilgi için bkz. [Traffic Manager uç noktası][traffic-manager-endpoints] |
    |-Target|eastus.api.cognitive.microsoft.com|Bu, LUO uç noktasının etki alanıdır.|
    |-EndpointLocation|eastus|Uç noktanın bölgesi|
    |-EndpointStatus|Etkin|Uç nokta oluşturulduğunda etkinleştirin|

    Başarılı yanıt şöyle görünür:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. **[Set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet 'i ile Doğu ABD uç noktası ayarla

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Başarılı bir yanıt, adım 2 ile aynı yanıt olacaktır.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>PowerShell ile Batı ABD Traffic Manager profili oluşturma
Batı ABD Traffic Manager profilini oluşturmak için, aynı adımları izleyin: profil oluşturma, uç nokta ekleme ve uç nokta ayarlama.

1. **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet 'i ile profil oluştur

    Profili oluşturmak için aşağıdaki cmdlet 'i kullanın. `appIdLuis`Ve ' i değiştirdiğinizden emin olun `subscriptionKeyLuis` . SubscriptionKey, Doğu ABD LUSıS anahtarına yöneliktir. Yol, LUSıS uygulama KIMLIĞI ve uç nokta anahtarı dahil doğru değilse, `degraded` trafik yönetimi 'nın lusıs uç noktasını başarıyla isteyemediği için Traffic Manager yoklama durumudur. Değerinin, `q` `traffic-manager-west` Bu değeri lusıs uç nokta günlüklerinde görebilmenizi sağlayın.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Name|lusıs-profile-westus|Azure portal Traffic Manager adı|
    |-ResourceGroupName|Lua-trafik-yönetici|Önceki bölümde oluşturuldu|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. Performans kullanılıyorsa, Traffic Manager URL isteği kullanıcının bölgesinden gelmelidir. Bir sohbet botu veya başka bir uygulamadan ilerleyecekseniz, Traffic Manager çağrısındaki bölgeyi taklit eden sohbet botu 'in sorumluluğundadır. |
    |-RelativeDnsName|Luo-DNS-westus|Bu, hizmetin alt etki alanıdır: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Yoklama aralığı, 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUSıS için bağlantı noktası ve protokol HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|`<appId>`Ve `<subscriptionKey>` değerlerini kendi değerlerinizle değiştirin. Bu uç nokta anahtarının Doğu uç nokta anahtarından farklı olduğunu unutmayın|

    Başarılı bir isteğin yanıtı yok.

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet 'i ile Batı ABD uç noktası ekleme

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-EndpointName|Lua-Batı-uç noktası|Profil altında görünen uç nokta adı|
    |-TrafficManagerProfile|$westprofile|1. adımda oluşturulan profil nesnesini kullanma|
    |-Tür|ExternalEndpoints|Daha fazla bilgi için bkz. [Traffic Manager uç noktası][traffic-manager-endpoints] |
    |-Target|westus.api.cognitive.microsoft.com|Bu, LUO uç noktasının etki alanıdır.|
    |-EndpointLocation|westus|Uç noktanın bölgesi|
    |-EndpointStatus|Etkin|Uç nokta oluşturulduğunda etkinleştirin|

    Başarılı yanıt şöyle görünür:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 'i ile Batı ABD uç noktası ayarla

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Başarılı bir yanıt, adım 2 ile aynı yanıttır.

### <a name="create-parent-traffic-manager-profile"></a>Üst Traffic Manager profili oluştur
Üst Traffic Manager profilini oluşturun ve iki alt Traffic Manager profilini üst öğeye bağlayın.

1. **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet 'i ile üst profil oluştur

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Name|Lua-profil-üst|Azure portal Traffic Manager adı|
    |-ResourceGroupName|Lua-trafik-yönetici|Önceki bölümde oluşturuldu|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. Performans kullanılıyorsa, Traffic Manager URL isteği kullanıcının bölgesinden gelmelidir. Bir sohbet botu veya başka bir uygulamadan ilerleyecekseniz, Traffic Manager çağrısındaki bölgeyi taklit eden sohbet botu 'in sorumluluğundadır. |
    |-RelativeDnsName|Lua-DNS-üst|Bu, hizmetin alt etki alanıdır: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Yoklama aralığı, 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUSıS için bağlantı noktası ve protokol HTTPS/443|
    |-MonitorPath|`/`|Bunun yerine alt uç nokta yollarının kullanıldığı bu yol önemlidir.|

    Başarılı bir isteğin yanıtı yok.

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** ve **Nestedendpoints** türü ile üst öğeye Doğu ABD alt profil ekleyin

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-EndpointName|alt uç nokta-USEast|Doğu profili|
    |-TrafficManagerProfile|$parentprofile|Bu uç noktanın atanacağı profil|
    |-Tür|NestedEndpoints|Daha fazla bilgi için bkz. [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Targetresourceıd|$eastprofile. Numarasını|Alt profilin KIMLIĞI|
    |-EndpointStatus|Etkin|Üst öğeye eklendikten sonra uç nokta durumu|
    |-EndpointLocation|eastus|Kaynağın [Azure bölge adı](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|En az alt uç nokta sayısı|

    Başarılı yanıt aşağıdakine benzer ve yeni `child-endpoint-useast` uç noktayı içerir:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet ve **Nestedendpoints** türü ile üst öğeye Batı ABD alt profil ekleyin

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Bu tabloda, cmdlet 'inin her bir değişkeni açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-EndpointName|alt uç nokta-usbatı|Batı profili|
    |-TrafficManagerProfile|$parentprofile|Bu uç noktanın atanacağı profil|
    |-Tür|NestedEndpoints|Daha fazla bilgi için bkz. [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Targetresourceıd|$westprofile. Numarasını|Alt profilin KIMLIĞI|
    |-EndpointStatus|Etkin|Üst öğeye eklendikten sonra uç nokta durumu|
    |-EndpointLocation|westus|Kaynağın [Azure bölge adı](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|En az alt uç nokta sayısı|

    Başarılı yanıt şöyle görünür ve hem önceki `child-endpoint-useast` uç noktasını hem de yeni `child-endpoint-uswest` uç noktayı içerir:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 'i ile uç noktaları ayarlama

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Başarılı bir yanıt, adım 3 ile aynı yanıttır.

### <a name="powershell-variables"></a>PowerShell değişkenleri
Önceki bölümlerde üç PowerShell değişkeni oluşturulmuştur: `$eastprofile` , `$westprofile` , `$parentprofile` . Bu değişkenler Traffic Manager yapılandırmasının sonuna doğru kullanılır. Değişkenleri oluşturmamayı veya unuttum ya da PowerShell pencerenizi zaman aşımına getirmeyi seçtiyseniz, profili yeniden almak ve bir değişkene atamak için **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** PowerShell cmdlet 'ini kullanabilirsiniz.

Açılı ayraçlar içindeki öğeleri, `<>` ihtiyacınız olan üç profilin her biri için doğru değerlerle değiştirin.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Traffic Manager çalıştığını doğrulama
Traffic Manager profillerinin çalıştığını doğrulamak için, profillerin `Online` Bu durumun durumunun, uç noktanın yoklama yoluna göre olması gerekir.

### <a name="view-new-profiles-in-the-azure-portal"></a>Azure portal yeni profilleri görüntüleme
Kaynak grubundaki kaynaklara bakarak üç profilin de oluşturulduğunu doğrulayabilirsiniz `luis-traffic-manager` .

![Azure Kaynak grubu Lua 'nın ekran görüntüsü-trafik-yönetici](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Profil durumunun çevrimiçi olduğunu doğrulama
Traffic Manager çevrimiçi olduğundan emin olmak için her uç noktanın yolunu yoklar. Çevrimiçi ise, alt profillerin durumu olur `Online` . Bu, her profile **genel bakış** üzerinde görüntülenir.

![Çevrimiçi Izleme durumunu gösteren Azure Traffic Manager profile genel bakış 'ın ekran görüntüsü](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager yoklama çalışmasının doğrulanması
Traffic Manager yoklamasını doğrulamaya yönelik başka bir yol da LUSıS uç nokta günlükleriyle birlikte çalışıyor. [Lusıs][LUIS] Web sitesi uygulamalar listesi sayfasında, uygulamanın uç nokta günlüğünü dışarı aktarın. Traffic Manager iki uç nokta için sıklıkla yokladığından, günlüklerde yalnızca birkaç dakika içinde olmaları durumunda bile girdiler vardır. Sorgunun ile başladığı girişleri aramaya unutmayın `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Traffic Manager çalışması için DNS yanıtını doğrulama
DNS yanıtının bir LUG uç noktası döndürdüğünden emin olmak için, DNS istemci kitaplığı kullanarak üst profil DNS 'sini yönetme trafiğini isteyin. Üst profilin DNS adı `luis-dns-parent.trafficmanager.net` .

Aşağıdaki Node.js kodu, üst profil için bir istek yapar ve bir LUO uç noktası döndürür:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

LUO uç noktasıyla başarılı yanıt:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Traffic Manager üst profilini kullanın
Uç noktalar arasında trafiği yönetmek için, LUO uç noktasını bulmak için Traffic Manager DNS çağrısı eklemeniz gerekir. Bu çağrı, her LUI uç noktası isteği için yapılır ve LUSıS istemci uygulaması kullanıcısının coğrafi konumunun benzetimini yapması gerekir. Uç nokta tahmini için LUSıS istemci uygulamanız ve LUSıS isteği arasında DNS yanıt kodu ekleyin.

## <a name="resolving-a-degraded-state"></a>Düşürülmüş durumu çözme

Uç nokta durumunun düşürüldüğünü görmek için Traffic Manager [tanılama günlüklerini](../../traffic-manager/traffic-manager-diagnostic-logs.md) etkinleştirin.

## <a name="clean-up"></a>Temizleme
İki LUO Endpoint anahtarını, üç Traffic Manager profilini ve bu beş kaynağı içeren kaynak grubunu kaldırın. Bu, Azure portal yapılır. Kaynaklar listesinden beş kaynağı silersiniz. Sonra kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu trafik yönetimi kodunun bir BotFramework bot 'a nasıl eklenebileceğini anlamak için BotFramework v4 içindeki [Ara yazılım](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) seçeneklerini inceleyin.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md