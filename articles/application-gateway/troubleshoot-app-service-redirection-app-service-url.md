---
title: App Service URL 'sine yeniden yönlendirme sorunlarını giderme
titleSuffix: Azure Application Gateway
description: Bu makalede, Azure Application Gateway ile kullanıldığında yeniden yönlendirme sorununun nasıl giderileceği hakkında bilgi verilmektedir Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: d43efd6dbd344f666c23b1ad4414ceb29992e996
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074490"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway App Service sorunlarını giderme

Azure App Service Azure Application Gateway ile arka uç hedefi olarak kullanıldığında karşılaşabileceğiniz sorunları tanılamanıza ve çözmeyi öğrenin.

## <a name="overview"></a>Genel Bakış

Bu makalede, aşağıdaki sorunları nasıl giderebileceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama Hizmeti URL 'SI, yeniden yönlendirme olduğunda tarayıcıda gösterilir.
> * App Service ARRAffinity tanımlama bilgisi etki alanı, özgün ana bilgisayar yerine App Service ana bilgisayar adı, example.azurewebsites.net olarak ayarlanır.

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
Önceki örnekte, Yanıt üstbilgisinin yeniden yönlendirme için 301 durum koduna sahip olduğuna dikkat edin. Konum üst bilgisi, `www.contoso.com`özgün ana bilgisayar adı yerine App Service 'in ana bilgisayar adına sahiptir.

## <a name="solution-rewrite-the-location-header"></a>Çözüm: konum başlığını yeniden yazın

Konum üstbilgisindeki ana bilgisayar adını Application Gateway 'in etki alanı adına ayarlayın. Bunu yapmak için, yanıttaki konum üstbilgisinin azurewebsites.net içerdiğini değerlendiren bir koşula sahip bir [yeniden yazma kuralı](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) oluşturun. Ayrıca, uygulama ağ geçidinin ana bilgisayar adına sahip olacak şekilde konum başlığını yeniden yazmak için bir eylem gerçekleştirmelidir. Daha fazla bilgi için bkz. [konum üst bilgisini yeniden yazma](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)yönergeleri.

> [!NOTE]
> HTTP üst bilgisi yeniden yazma desteği yalnızca Application Gateway [Standard_v2 ve WAF_v2 SKU 'su](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) için kullanılabilir. V1 SKU 'SU kullanıyorsanız [v1 'den v2 'ye geçiş](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)yapmanızı öneririz. V2 SKU 'SU ile kullanılabilen yeniden yazma ve diğer [Gelişmiş özellikleri](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) kullanmak istiyorsunuz.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatif çözüm: özel bir etki alanı adı kullanın

V1 SKU 'SU kullanıyorsanız, konum üst bilgisini yeniden yazabilirsiniz. Bu özellik yalnızca v2 SKU 'SU için kullanılabilir. Yeniden yönlendirme sorununu çözmek için, uygulama ağ geçidinin aynı ana bilgisayar adını da App Service 'e geçirin, bunun yerine bir konak geçersiz kılma işlemi yapın.

App Service artık uygulama ağ geçidine işaret eden aynı orijinal ana bilgisayar üst bilgisinde yeniden yönlendirmeyi (varsa) yapar ve kendi kendine değil.

Özel bir etki alanına sahip olmanız ve bu işlemi izlemeniz gerekir:

- Etki alanını App Service 'in özel etki alanı listesine kaydedin. Özel etki alanında App Service 'in FQDN 'sine işaret eden bir CNAME 'i olması gerekir. Daha fazla bilgi için bkz. [mevcut bir özel DNS adını Azure App Service eşleme](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![App Service özel etki alanı listesi](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Uygulama hizmetiniz `www.contoso.com`ana bilgisayar adını kabul etmeye hazırlanıyor. DNS 'de CNAME girdinizi, uygulama ağ geçidinin FQDN 'sine geri işaret etmek için değiştirin, örneğin, `appgw.eastus.cloudapp.azure.com`.

- DNS sorgusu yaptığınızda etki alanı `www.contoso.com` uygulama ağ geçidinin FQDN 'sine çözümlendiğinden emin olun.

- Özel araştırmasını **arka uç http ayarlarından seçim ana bilgisayar adını**devre dışı bırakacak şekilde ayarlayın. Azure portal, araştırma ayarlarındaki onay kutusunun işaretini kaldırın. PowerShell 'de, **set-AzApplicationGatewayProbeConfig** komutunda **-pickhostnamefrombackendhttpsettings** anahtarını kullanmayın. Araştırmanın konak adı alanında App Service 'in FQDN 'SI olan example.azurewebsites.net girin. Application Gateway 'den gönderilen araştırma istekleri bu FQDN 'yi konak üstbilgisine taşır.

  > [!NOTE]
  > Sonraki adımda, özel araştırmanın arka uç HTTP ayarlarınızla ilişkili olmadığından emin olun. HTTP ayarlarınızda, bu noktada **arka uç adres konak adresini seçin** anahtarı etkin kalır.

- Application Gateway 'in HTTP ayarlarını, **arka uç adresinden seçim ana bilgisayar adını**devre dışı bırakmak için ayarlayın. Azure portal onay kutusunu temizleyin. PowerShell 'de, **set-AzApplicationGatewayBackendHttpSettings** komutunda **-pickhostnamefrombackendadddress** anahtarını kullanmayın.

- Özel araştırmayı arka uç HTTP ayarlarına geri ilişkilendirin ve arka ucun sağlıklı olduğunu doğrulayın.

- Uygulama ağ geçidi artık aynı ana bilgisayar adını App Service 'e `www.contoso.com`iletmelidir. Yeniden yönlendirme aynı ana bilgisayar adı üzerinde gerçekleşir. Aşağıdaki örnek istek ve yanıt üst bilgilerini denetleyin.

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
