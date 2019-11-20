---
title: Uç nokta kotasını artır-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS), tek bir anahtarın kota dışında uç nokta isteği Kotayı artırmak olanağı sunar. LUIS için daha fazla anahtarları oluşturma ve bunları LUIS uygulamaya ekleme tarafından yapıldığını **Yayımla** sayfasını **kaynakları ve anahtarları** bölümü.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256598"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Uç nokta kota anahtarlarını yönetmek için Microsoft Azure Traffic Manager'ı kullanma
Language Understanding (LUIS), tek bir anahtarın kota dışında uç nokta isteği Kotayı artırmak olanağı sunar. LUIS için daha fazla anahtarları oluşturma ve bunları LUIS uygulamaya ekleme tarafından yapıldığını **Yayımla** sayfasını **kaynakları ve anahtarları** bölümü. 

Anahtarlar trafiği yönetmek istemci uygulaması vardır. LUIS, yapmaz. 

Bu makalede, Azure [Traffic Manager][traffic-manager-marketing]ile anahtarlar arasındaki trafiğin nasıl yönetileceği açıklanmaktadır. Önceden eğitilmiş ve yayımlanmış bir LUIS uygulaması olmalıdır. Biri yoksa, önceden oluşturulmuş etki alanı izleyin [hızlı](luis-get-started-create-app.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>PowerShell için Azure portalında bağlanma
[Azure][azure-portal] portalında PowerShell penceresini açın. PowerShell penceresi için simge **> _** üst gezinti çubuğunda. Portaldan PowerShell kullanarak en son PowerShell sürümünü alın ve kimlik doğrulaması yapılır. Portal, PowerShell gerektirir bir [Azure depolama](https://azure.microsoft.com/services/storage/) hesabı. 

![Azure portalının ekran görüntüsü ile bir Powershell penceresi açın](./media/traffic-manager/azure-portal-powershell.png)

Aşağıdaki bölümlerde [Traffic Manager PowerShell cmdlet'lerini](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>PowerShell ile Azure kaynak grubu oluşturun
Azure kaynakları oluşturmadan önce tüm kaynakları içerecek bir kaynak grubu oluşturun. Kaynak grubunu adlandırın `luis-traffic-manager` ve bölgenin `West US`. Kaynak grubu bölgesi grup hakkındaki meta verileri depolar. Başka bir bölgede olmaları durumunda kaynaklarınızı yavaş olmaz. 

**[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet 'i ile kaynak grubu oluştur:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS toplam uç nokta Kotayı artırmak için anahtarları oluşturma
1. Azure portalında, iki oluşturma **Language Understanding** anahtarları birinde `West US` ve diğeri `East US`. Adlı önceki bölümde oluşturduğunuz mevcut kaynak grubunu kullanın `luis-traffic-manager`. 

    ![Azure portalının ekran görüntüsü luıs traffic manager kaynak grubunda iki LUIS anahtarları](./media/traffic-manager/luis-keys.png)

2. [Lusıs][LUIS] Web sitesinde, **Yönet** bölümünde, **Azure kaynakları** sayfasında, uygulamaya anahtarlar atayın ve sağ üst menüdeki **Yayımla** düğmesini seçerek uygulamayı yeniden yayımlayın. 

    Örnek URL'yi **uç nokta** sütun uç noktası anahtarı ile bir GET isteği bir sorgu parametresi olarak kullanır. İki yeni anahtarları uç nokta URL'lerini kopyalayın. Bunlar, bu makalenin devamındaki Traffic Manager yapılandırması bir parçası olarak kullanılır.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Traffic Manager ile anahtarları arasında LUIS uç nokta isteklerini yönetme
Traffic Manager uç noktalarınız için yeni bir DNS erişim noktası oluşturur. Bir ağ geçidi veya proxy olarak ancak DNS düzeyinde kesinlikle davranmaz. Bu örnekte, tüm DNS kayıtlarını değiştirmez. Bir DNS kitaplığı konusu istek için doğru uç noktaya almak için Traffic Manager'ile iletişim kurmak için kullanır. _Her_ LUIS ilk kullanmak için hangi LUIS uç noktaya belirlemek için bir Traffic Manager istek doğrulamasında yönelik istek. 

### <a name="polling-uses-luis-endpoint"></a>Yoklama LUIS uç noktası kullanır.
Traffic Manager Uç noktalara düzenli aralıklarla uç nokta hala kullanılabilir olduğundan emin olmak için yoklar. Traffic Manager URL ile bir GET isteği erişilebilir olmasını ve 200 dönüş sorguladı. Uç nokta URL'sini üzerinde **Yayımla** sayfasında bunu yapar. Her uç noktası anahtarı farklı bir yol ve sorgu dizesi parametreleri olduğundan, her bir uç noktası anahtarı farklı yoklama yolu gerekiyor. Traffic Manager'ı yoklar, her zaman bir kota isteği ücreti. Sorgu dizesi parametresi **q** LUIS LUIS için gönderilen utterance uç noktadır. Bir utterance göndermek yerine, bu parametre Traffic Manager yoklama LUIS uç nokta günlüğe Traffic Manager'ın yapılandırılmış alınırken bir hata ayıklama teknik eklemek için kullanılır.

Kendi yolun her LUIS uç nokta gerekir çünkü kendi Traffic Manager profili gerekiyor. Profillerinde yönetmek için oluşturmak bir [ _iç içe geçmiş_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) mimarisi. Bir üst profil alt profillere işaret ve bunların arasında trafiği yönetmek.

Traffic Manager yapılandırıldıktan sonra günlük kullanılacak yol değiştirmeyi unutmayın günlüğünüzün yoklama ile doluyor değil için false sorgu dizesi parametresi =.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Traffic Manager ile iç içe geçmiş profillerini yapılandırma
Aşağıdaki bölümlerde, biri Doğu LUIS anahtar diğeri Batı LUIS anahtarı için iki alt profili oluşturun. Üst profil oluşturulduktan sonra iki alt profilleri üst profiline eklenir. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>PowerShell ile Doğu ABD Traffic Manager profili oluşturma
Doğu ABD Traffic Manager profili oluşturmak için birkaç adım vardır: profil oluşturma, uç nokta ekleyin ve uç noktası ayarlayın. Traffic Manager profili fazla uç nokta olabilir, ancak her uç nokta aynı doğrulama yolu vardır. LUIS uç nokta URL'leri Doğu ve Batı abonelikler için bölge ve uç noktası anahtarı nedeniyle farklı olduğundan, her LUIS uç noktası profilindeki tek bir uç nokta olması gerekir. 

1. **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet 'i ile profil oluştur

    Profili oluşturmak için aşağıdaki cmdlet'i kullanın. Değiştirdiğinizden emin olun `appIdLuis` ve `subscriptionKeyLuis`. SubscriptionKey için ABD Doğu LUIS anahtardır. Traffic Manager yoklama yolu LUIS uygulama kimliği ve uç noktası anahtarı dahil olmak üzere doğru değil, durumunun ise `degraded` çünkü trafiği yönetmek LUIS uç noktası başarıyla istenemiyor. Değerini emin `q` olduğu `traffic-manager-east` LUIS uç nokta günlüklerinde bu değeri görebilirsiniz.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:
    
    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Ad|luıs profili eastus|Azure portalındaki traffic Manager adı|
    |-ResourceGroupName|luıs traffic manager|Önceki bölümde oluşturduğunuz|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. URL isteği için Traffic Manager, performans kullanıyorsanız, kullanıcının bölgesi gelmelidir. Bir sohbet Robotu veya başka bir uygulama giderek, ' % s'çağrısı için Traffic Manager bölgede taklit etmek için Sohbet botu'nın sorumluluk olur. |
    |-RelativeDnsName|luıs dns eastus|Bu hizmet için bir alt etki alanı olur: luıs dns eastus.trafficmanager.net|
    |-Ttl|30|Yoklama aralığı 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Bağlantı noktası ve protokol LUIS için HTTPS/443'tür|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Değiştirin `<appIdLuis>` ve `<subscriptionKeyLuis>` kendi değerlerinizle.|
    
    Başarılı bir istek, yanıt aldı.

2. **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet 'i ile Doğu ABD uç noktası ekleme

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Uçnoktaadı|luıs Doğu endpoint|Profili altında görüntülenen uç nokta adı|
    |-TrafficManagerProfile|$eastprofile|1\. adımda oluşturduğunuz profili nesnesini kullanın|
    |-Type|ExternalEndpoints|Daha fazla bilgi için bkz. [Traffic Manager uç noktası][traffic-manager-endpoints] |
    |-Hedef|eastus.api.cognitive.microsoft.com|LUIS uç noktası için etki alanı budur.|
    |-EndpointLocation|"eastus"|Uç nokta bölgesi|
    |-EndpointStatus|Etkin|Oluşturulduğunda, uç noktayı etkinleştirme|

    Başarılı yanıt şu şekilde görünür:

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

3. **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet 'i ile Doğu ABD uç noktası ayarla

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Başarılı bir yanıt aynı yanıt olarak 2. adım olacaktır.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>PowerShell ile Batı ABD Traffic Manager profili oluşturma
Batı ABD Traffic Manager profili oluşturmak için aynı adımları izleyin: profil oluşturma, uç nokta ekleyin ve uç noktası ayarlayın.

1. **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet 'i ile profil oluştur

    Profili oluşturmak için aşağıdaki cmdlet'i kullanın. Değiştirdiğinizden emin olun `appIdLuis` ve `subscriptionKeyLuis`. SubscriptionKey için ABD Doğu LUIS anahtardır. Yol LUIS uygulama kimliği ve uç noktası anahtarı dahil olmak üzere doğru değilse, Traffic Manager yoklama durumu olan `degraded` çünkü trafiği yönetmek LUIS uç noktası başarıyla istenemiyor. Değerini emin `q` olduğu `traffic-manager-west` LUIS uç nokta günlüklerinde bu değeri görebilirsiniz.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:
    
    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Ad|luıs profili westus|Azure portalındaki traffic Manager adı|
    |-ResourceGroupName|luıs traffic manager|Önceki bölümde oluşturduğunuz|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. URL isteği için Traffic Manager, performans kullanıyorsanız, kullanıcının bölgesi gelmelidir. Bir sohbet Robotu veya başka bir uygulama giderek, ' % s'çağrısı için Traffic Manager bölgede taklit etmek için Sohbet botu'nın sorumluluk olur. |
    |-RelativeDnsName|luıs-dns-westus|Bu hizmet için bir alt etki alanı olur: luıs dns westus.trafficmanager.net|
    |-Ttl|30|Yoklama aralığı 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Bağlantı noktası ve protokol LUIS için HTTPS/443'tür|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Değiştirin `<appId>` ve `<subscriptionKey>` kendi değerlerinizle. Bu uç noktası anahtarı Doğu uç noktası anahtarı farklı olduğunu unutmayın|
    
    Başarılı bir istek, yanıt aldı.

2. **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet 'i ile Batı ABD uç noktası ekleme

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Uçnoktaadı|luıs Batı endpoint|Profili altında görüntülenen uç nokta adı|
    |-TrafficManagerProfile|$westprofile|1\. adımda oluşturduğunuz profili nesnesini kullanın|
    |-Type|ExternalEndpoints|Daha fazla bilgi için bkz. [Traffic Manager uç noktası][traffic-manager-endpoints] |
    |-Hedef|westus.api.cognitive.microsoft.com|LUIS uç noktası için etki alanı budur.|
    |-EndpointLocation|"westus"|Uç nokta bölgesi|
    |-EndpointStatus|Etkin|Oluşturulduğunda, uç noktayı etkinleştirme|

    Başarılı yanıt şu şekilde görünür:

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

3. **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 'i ile Batı ABD uç noktası ayarla

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Başarılı yanıt aynı yanıt olarak 2. adım:.

### <a name="create-parent-traffic-manager-profile"></a>Üst Traffic Manager profili oluşturma
Üst Traffic Manager profili oluşturun ve iki alt Traffic Manager profili üst öğeye bağlayın.

1. **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet 'i ile üst profil oluştur

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Ad|luıs profili üst|Azure portalındaki traffic Manager adı|
    |-ResourceGroupName|luıs traffic manager|Önceki bölümde oluşturduğunuz|
    |-TrafficRoutingMethod|Performans|Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri][routing-methods]. URL isteği için Traffic Manager, performans kullanıyorsanız, kullanıcının bölgesi gelmelidir. Bir sohbet Robotu veya başka bir uygulama giderek, ' % s'çağrısı için Traffic Manager bölgede taklit etmek için Sohbet botu'nın sorumluluk olur. |
    |-RelativeDnsName|luıs dns üst|Bu hizmet için bir alt etki alanı olur: luıs dns parent.trafficmanager.net|
    |-Ttl|30|Yoklama aralığı 30 saniye|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Bağlantı noktası ve protokol LUIS için HTTPS/443'tür|
    |-MonitorPath|`/`|Alt uç nokta yolları yerine kullanıldığı için bu yolu önemli değildir.|

    Başarılı bir istek, yanıt aldı.

2. **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** ve **Nestedendpoints** türü ile üst öğeye Doğu ABD alt profil ekleyin

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Uçnoktaadı|alt uç nokta useast|Doğu profili|
    |-TrafficManagerProfile|$parentprofile|Bu uç noktaya atamak için profili|
    |-Type|NestedEndpoints|Daha fazla bilgi için bkz. [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Targetresourceıd|$eastprofile. Kimliği|Alt profil kimliği|
    |-EndpointStatus|Etkin|Üst öğeye ekledikten sonra uç nokta durumu|
    |-EndpointLocation|"eastus"|[Azure bölgesi adı](https://azure.microsoft.com/global-infrastructure/regions/) kaynağı|
    |-MinChildEndpoints|1|Alt uç nokta sayısı için alt sınır|

    Başarılı yanıt Ara yeni içerir ve aşağıdaki gibi `child-endpoint-useast` uç noktası:    

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

3. **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet ve **Nestedendpoints** türü ile üst öğeye Batı ABD alt profil ekleyin

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Bu tabloda, her bir değişken cmdlet'inde açıklanmaktadır:

    |Yapılandırma parametresi|Değişken adı veya değeri|Amaç|
    |--|--|--|
    |-Uçnoktaadı|alt uç nokta uswest|Batı profili|
    |-TrafficManagerProfile|$parentprofile|Bu uç noktaya atamak için profili|
    |-Type|NestedEndpoints|Daha fazla bilgi için bkz. [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Targetresourceıd|$westprofile. Kimliği|Alt profil kimliği|
    |-EndpointStatus|Etkin|Üst öğeye ekledikten sonra uç nokta durumu|
    |-EndpointLocation|"westus"|[Azure bölgesi adı](https://azure.microsoft.com/global-infrastructure/regions/) kaynağı|
    |-MinChildEndpoints|1|Alt uç nokta sayısı için alt sınır|

    Başarılı yanıt Ara hem de önceki içerir ve gibi `child-endpoint-useast` uç noktası ve yeni `child-endpoint-uswest` uç noktası:

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

4. **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 'i ile uç noktaları ayarlama 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Başarılı yanıt aynı yanıt olarak 3. adım:.

### <a name="powershell-variables"></a>PowerShell değişkenleri
Önceki bölümlerde üç PowerShell değişkenleri oluşturulan: `$eastprofile`, `$westprofile`, `$parentprofile`. Bu değişkenler, Traffic Manager yapılandırması sonuna doğru kullanılır. Değişkenleri oluşturmamayı veya unuttum ya da PowerShell pencerenizi zaman aşımına getirmeyi seçtiyseniz, profili yeniden almak ve bir değişkene atamak için **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** PowerShell cmdlet 'ini kullanabilirsiniz. 

Açılı ayraçlar öğeleri değiştirin `<>`, her gereksinim duyduğunuz üç profil için doğru değerlerle. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Trafik Yöneticisi'nin çalıştığını doğrulama
İş Traffic Manager profilleri, Profiller durumunu gerek doğrulamak için `Online` bu durum uç noktaya yoklama yolda temel alır. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Azure portalında yeni profilleri görüntüle
Üç profil kaynakları bakarak oluşturulduğunu doğrulayabilirsiniz `luis-traffic-manager` kaynak grubu.

![Ekran görüntüsü, Azure kaynak grubu luıs-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Profili durumunun çevrimiçi olduğunu doğrulayın
Traffic Manager çevrimiçi olduğundan emin olmak için her uç nokta yolunu yoklar. Çevrimiçi ise, durumu alt profilleri: `Online`. Bu görüntülenen **genel bakış** her profil. 

![Ekran Azure Traffic Manager profili, izleme durumu çevrimiçi gösteren genel bakış](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager'ın çalıştığı yoklama doğrula
Traffic manager Works yoklama doğrulamak için başka bir LUIS uç nokta günlüklerle yoludur. [Lusıs][LUIS] Web sitesi uygulamalar listesi sayfasında, uygulamanın uç nokta günlüğünü dışarı aktarın. Traffic Manager için iki uç nokta yoklayacağını çünkü girişler vardır günlüklerde bile yalnızca birkaç dakikada silinmiş. Sorgu başladığı ile girdilerini arayın unutmayın `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>DNS yanıtı Traffic Manager çalıştığını doğrulama
DNS yanıtı LUIS uç nokta döndürür doğrulamak için bir DNS istemci kitaplığını kullanarak DNS trafiği yönetmek üst profili isteyin. Üst profili için DNS adı `luis-dns-parent.trafficmanager.net`.

Aşağıdaki Node.js kod üst profili için bir istek gönderir ve LUIS uç noktasına döndürür:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

LUIS uç noktasıyla başarılı yanıt şöyledir:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Traffic Manager ana profilini kullanın
Uç noktalar genelinde trafiği yönetmek için Traffic Manager DNS LUIS uç noktasını bulmak için bir çağrı eklemeniz gerekir. Bu çağrı için her LUIS uç nokta isteği yapılır ve coğrafi konum LUIS istemci uygulamasının kullanıcının benzetimini yapmak gerekiyor. LUIS istemci uygulamanız ile istek aralığındaki DNS yanıt kodu için uç nokta tahmin için LUIS ekleyin. 

## <a name="resolving-a-degraded-state"></a>Düşürülmüş durumu çözme

Uç nokta durumunun düşürüldüğünü görmek için Traffic Manager [tanılama günlüklerini](../../traffic-manager/traffic-manager-diagnostic-logs.md) etkinleştirin.

## <a name="clean-up"></a>Temizleme
İki LUIS uç noktası anahtarı, üç Traffic Manager profillerini ve beş bu kaynakları içeren kaynak grubunu kaldırın. Bu, Azure portalından gerçekleştirilir. Beş kaynakları kaynakları listeden silin. Kaynak grubunu silin. 

## <a name="next-steps"></a>Sonraki adımlar

Gözden geçirme [ara yazılım](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) Botframework'te v4 seçeneklerinde Bu trafiği yönetim kodu Botframework'te robota nasıl eklenebileceğini öğrenin. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
