---
title: App Service URL 'sine yeniden yönlendirme sorunlarını giderme
titleSuffix: Azure Application Gateway
description: Bu makalede, Azure Application Gateway ile kullanıldığında yeniden yönlendirme sorununun nasıl giderileceği hakkında bilgi verilmektedir Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514895"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway App Service sorunlarını giderme

Azure App Service Azure Application Gateway ile arka uç hedefi olarak kullanıldığında karşılaşabileceğiniz sorunları tanılamanıza ve çözmeyi öğrenin.

## <a name="overview"></a>Genel Bakış

Bu makalede, aşağıdaki sorunları nasıl giderebileceğinizi öğreneceksiniz:

* Uygulama Hizmeti URL 'SI, yeniden yönlendirme olduğunda tarayıcıda gösterilir.
* App Service ARRAffinity tanımlama bilgisi etki alanı, özgün ana bilgisayar yerine App Service ana bilgisayar adı, example.azurewebsites.net olarak ayarlanır.

Bir arka uç uygulaması yeniden yönlendirme yanıtı gönderdiğinde, istemciyi arka uç uygulaması tarafından belirtilene göre farklı bir URL 'ye yönlendirmek isteyebilirsiniz. Bir uygulama hizmeti bir uygulama ağ geçidinin arkasında barındırılıyorsa ve istemcinin göreli yoluna yeniden yönlendirme yapması için bunu yapmak isteyebilirsiniz. Örnek, contoso.azurewebsites.net/path1 ile contoso.azurewebsites.net/path2 arasında bir yönlendirme örneğidir. 

App Service bir yeniden yönlendirme yanıtı gönderdiğinde, uygulamanın konum üst bilgisinde uygulama ağ geçidinden aldığı istek gibi aynı ana bilgisayar adını kullanır. Örneğin, istemci, Application Gateway contoso.com/path2 'e geçmek yerine isteği doğrudan contoso.azurewebsites.net/path2 'e yapar. Application Gateway 'i atlamak istemezsiniz.

Bu sorun aşağıdaki başlıca nedenlerden kaynaklanabilir:

- App Service 'te yeniden yönlendirme yapılandırdınız. Yeniden yönlendirme, isteğe bir sondaki eğik çizgi eklemek kadar basit olabilir.
- Azure Active Directory kimlik doğrulamasından sahipsiniz, bu da yeniden yönlendirmeye neden olur.

Ayrıca, uygulama hizmetlerini bir uygulama ağ geçidinin arkasında kullandığınızda, uygulama ağ geçidi (example.com) ile ilişkili etki alanı adı App Service 'in etki alanı adından (deyin, example.azurewebsites.net) farklıdır. App Service tarafından ayarlanan ARRAffinity tanımlama bilgisinin etki alanı değeri, istenen olmayan example.azurewebsites.net etki alanı adını taşır. Özgün ana bilgisayar adı, example.com, tanımlama bilgisinde etki alanı adı değeri olmalıdır.

## <a name="sample-configuration"></a>Örnek yapılandırma

- HTTP dinleyicisi: temel veya çok siteli
- Arka uç adres havuzu: App Service
- HTTP ayarları: **arka uç adresinden ana bilgisayar adı seçin** etkin
- Araştırma: **http ayarlarından ana bilgisayar adı seçin**

## <a name="cause"></a>Nedeni

App Service, çok kiracılı bir hizmettir, bu nedenle isteği doğru uç noktaya yönlendirmek için istekte ana bilgisayar üst bilgisini kullanır. App Services, *. azurewebsites.net (deyin, contoso.azurewebsites.net) varsayılan etki alanı adı, Application Gateway 'in etki alanı adından (deyin, contoso.com) farklıdır. 

İstemciden gelen özgün istek, ana bilgisayar adı olarak contoso.com, Application Gateway 'in etki alanı adına sahiptir. Uygulama ağ geçidini, isteği App Service arka ucuna yönlendirdikleri zaman App Service 'in ana bilgisayar adına değiştirmek üzere yapılandırmanız gerekir. Application Gateway 'in HTTP ayar yapılandırmasındaki **arka uç adresinden anahtar seçim ana bilgisayar adını** kullanın. Durum araştırma yapılandırmasındaki **arka uç http ayarlarından anahtar seçim ana bilgisayar adını** kullanın.



![Uygulama ağ geçidi değişiklikleri konak adı](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

App Service bir yeniden yönlendirme yaparken, aksi belirtilmedikçe, contoso.com orijinal ana bilgisayar adı yerine konum üstbilgisinde geçersiz kılınan ana bilgisayar adı contoso.azurewebsites.net kullanır. Aşağıdaki örnek istek ve yanıt üst bilgilerini denetleyin.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Önceki örnekte, Yanıt üstbilgisinin yeniden yönlendirme için 301 durum koduna sahip olduğuna dikkat edin. Konum üst bilgisi, özgün ana bilgisayar adı yerine App Service 'in ana bilgisayar adına sahiptir `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Çözüm: konum başlığını yeniden yazın

Konum üstbilgisindeki ana bilgisayar adını Application Gateway 'in etki alanı adına ayarlayın. Bunu yapmak için, yanıttaki konum üstbilgisinin azurewebsites.net içerdiğini değerlendiren bir koşula sahip bir [yeniden yazma kuralı](./rewrite-http-headers.md) oluşturun. Ayrıca, uygulama ağ geçidinin ana bilgisayar adına sahip olacak şekilde konum başlığını yeniden yazmak için bir eylem gerçekleştirmelidir. Daha fazla bilgi için bkz. [konum üst bilgisini yeniden yazma](./rewrite-http-headers.md#modify-a-redirection-url)yönergeleri.

> [!NOTE]
> HTTP üst bilgisi yeniden yazma desteği yalnızca Application Gateway [Standard_v2 ve WAF_v2 SKU 'su](./application-gateway-autoscaling-zone-redundant.md) için kullanılabilir. V2 SKU 'SU ile kullanılabilir olan üst bilgi yeniden yazma ve diğer [Gelişmiş yetenekler](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) için [v2 'ye geçiş](./migrate-v1-v2.md) yapmanızı öneririz.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatif çözüm: özel bir etki alanı adı kullanın

App Service özel etki alanı özelliği kullanmak, trafiği her zaman Application Gateway etki alanı adına (örneğimizde) yeniden yönlendirmeye yönelik başka bir çözümdür `www.contoso.com` . Bu yapılandırma, ARR benzeşim tanımlama bilgisi sorununa yönelik bir çözüm olarak da kullanılır. Varsayılan olarak, ARRAffinity tanımlama alanı, Application Gateway etki alanı adı yerine App Service varsayılan ana bilgisayar adına (example.azurewebsites.net) ayarlanır. Bu nedenle, bu gibi durumlarda tarayıcı, isteğin etki alanı adlarındaki ve tanımlama bilgisinin farkı nedeniyle tanımlama bilgisini reddeder.

Hem yeniden yönlendirme hem de ARRAffinity öğesinin tanımlama bilgisi etki alanı uyuşmazlığı sorunları için verilen yöntemi izleyebilirsiniz. Bu yöntem, özel etki alanının DNS bölgesinin erişimine sahip olmanız gerekir.

**Adım**: App Service içinde özel bir etki alanı ayarlayın ve [CNAME & txt DNS kayıtlarını](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)ekleyerek etki alanı sahipliğini doğrulayın.
Kayıtlar şuna benzer görünmelidir
-  `www.contoso.com` CNAME 'DE `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` TXT " `<verification id string>` " içinde


**Step2**: ÖNCEKI adımda CNAME kaydı yalnızca etki alanı doğrulaması için gereklidir. Son olarak, Application Gateway aracılığıyla yönlendirmeye yönelik trafiğe ihtiyacımız var. Bu nedenle, `www.contoso.com` Application Gateway FQDN 'sini işaret etmek IÇIN CNAME 'i Şimdi değiştirebilirsiniz. Application Gateway için bir FQDN ayarlamak için, genel IP adresi kaynağına gidin ve bir "DNS ad etiketi" atayın. Güncelleştirilmiş CNAME kaydı şöyle görünmelidir 
-  `www.contoso.com` CNAME 'DE `contoso.eastus.cloudapp.azure.com`


**Step3**: Ilişkili http ayarı Için "arka uç adresinden ana bilgisayar adını seçin" ayarını devre dışı bırakın.

PowerShell 'de, `-PickHostNameFromBackendAddress` komutunda anahtarını kullanmayın `Set-AzApplicationGatewayBackendHttpSettings` .


**Step4**: arka ucu sağlıklı ve işlemsel trafik olarak belirleme araştırmaları Için, ana bilgisayar alanı ile bir özel durum araştırması App Service özel veya varsayılan etki alanı olarak ayarlayın.

PowerShell 'de, `-PickHostNameFromBackendHttpSettings` komutta anahtarını kullanmayın `Set-AzApplicationGatewayProbeConfig` ve araştırmanın-HostName anahtarındaki App Service özel veya varsayılan etki alanını kullanın.

Mevcut bir kurulum için PowerShell 'i kullanarak önceki adımları uygulamak için aşağıdaki örnek PowerShell betiğini kullanın. Araştırma ve HTTP ayarları yapılandırmasındaki **-pickhostname** anahtarlarını nasıl kullandığımızda aklınızda olduğunu öğrenin.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.
