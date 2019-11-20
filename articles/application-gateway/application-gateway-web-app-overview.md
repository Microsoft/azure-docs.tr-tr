---
title: Çok kiracılı arka uçlar
titleSuffix: Azure Application Gateway
description: Bu sayfada, Application Gateway’in çok kiracılı arka uç desteği için genel bir bakış sunulmuştur.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075186"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>App Service gibi çok kiracılı arka uçlar için Application Gateway desteği

Web sunucularındaki çok kiracılı mimari tasarımlarda, aynı Web sunucusu örneğinde birden çok Web sitesi çalışmaktadır. Konak adları, barındırılan farklı uygulamalar arasında ayrım yapmak için kullanılır. Varsayılan olarak, application gateway istemciden gelen HTTP ana bilgisayar üst bilgisini değiştirmez ve üst bilgiyi değiştirilmemiş halde arka uca gönderir. Bu, NIC 'ler, sanal makine ölçek kümeleri, genel IP adresleri, iç IP adresleri ve FQDN 'ler gibi, doğru uç noktaya çözümlemek için belirli bir ana bilgisayar üst bilgisini veya SNı uzantısını kullanmadığı için iyi sonuç verir. Ancak, Azure App Service Web Apps ve Azure API yönetimi gibi birçok hizmet, doğası gereği çok kiracılı ve doğru uç noktaya çözümlemek için belirli bir ana bilgisayar üst bilgisini veya SNı uzantısını kullanır. Genellikle uygulamanın DNS adı, uygulama ağ geçidi ile ilişkili DNS adıdır, arka uç hizmetinin etki alanı adından farklıdır. Bu nedenle, uygulama ağ geçidi tarafından alınan özgün istekteki ana bilgisayar üst bilgisi, arka uç hizmetinin ana bilgisayar adıyla aynı değildir. Bu nedenle, uygulama ağ geçidindeki istekteki ana bilgisayar üst bilgisi arka uç hizmetinin ana bilgisayar adına değiştirilmediği için, çok kiracılı arka uçlar isteği doğru uç noktaya çözemeyebilir. 

Application Gateway kullanıcılara, arka ucun ana bilgisayar adına göre istekteki HTTP barındırma üst bilgisini geçersiz kılma olanağı tanıyan bir özellik sunar. Bu özellik Azure App Service web uygulamaları ve API Management gibi çok kiracılı arka uçlar için destek sağlar. Bu özellik hem v1 ve v2 standardı hem de WAF SKU’larında kullanılabilir. 

![Konak geçersiz kılma](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Bu, bir çok kiracılı kaynak olan Azure App Service 'ten farklı olarak, Ao 'un ayrılmış bir kaynak olduğundan, Azure App Service ortamı (Ao) için geçerli değildir.

## <a name="override-host-header-in-the-request"></a>İstekteki ana bilgisayar üst bilgisini geçersiz kıl

Bir konak geçersiz kılma belirtme özelliği, [http ayarlarında](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) tanımlanmıştır ve kural oluşturma sırasında herhangi bir arka uç havuzuna uygulanabilir. Çok kiracılı arka uçlar için konak üstbilgisini ve SNı uzantısını geçersiz kılmanın aşağıdaki iki yolu desteklenir:

- Ana bilgisayar adını HTTP ayarlarında açıkça girilen sabit bir değere ayarlama yeteneği. Bu özellik, belirli HTTP ayarlarının uygulandığı arka uç havuzuna giden tüm trafik için ana bilgisayar üstbilgisinin bu değere geçersiz kılınmasını sağlar. Uçtan uca SSL kullanılırken, geçersiz kılınan bu ana bilgisayar adı SNI uzantısında kullanılır. Bu özellik, bir arka uç havuzu grubunun gelen müşteri ana bilgisayar başlığından farklı bir ana bilgisayar üst bilgisi beklediği senaryolara olanak tanıyor.

- Ana bilgisayar adını, arka uç havuzu üyelerinin IP veya FQDN 'sinden türetebilme özelliği. HTTP ayarları Ayrıca, tek bir arka uç havuzu üyesinden ana bilgisayar adı türeme seçeneğiyle yapılandırılmışsa, arka uç havuzu üyesinin FQDN 'sinden dinamik olarak ana bilgisayar adını seçme seçeneği sağlar. Uçtan uca SSL kullanılırken, bu ana bilgisayar adı FQDN’den türetilir ve SNI uzantısında kullanılır. Bu özellik, bir arka uç havuzunun Azure Web Apps gibi iki ya da daha fazla kiracı PaaS hizmetine sahip olduğu ve isteğin her üyeye ait ana bilgisayar üst bilgisinin FQDN 'den türetilen ana bilgisayar adını içerdiği senaryolara olanak sağlar. Bu senaryoyu uygulamak için, arka uç havuzunda belirtilen bir konak üstbilgisini, arka uç havuzunda bahsedilen bir ana bilgisayar üst bilgisini dinamik olarak geçersiz kılacak bir oturum ana [bilgisayar adresini seçin](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) adlı bir anahtar kullanıyoruz.  Örneğin, arka uç havuzu FQDN 'niz "contoso11.azurewebsites.net" ve "contoso22.azurewebsites.net" içeriyorsa, özgün isteğin contoso.com olan ana bilgisayar üst bilgisi contoso11.azurewebsites.net veya contoso22.azurewebsites.net için geçersiz kılınır istek, uygun arka uç sunucusuna gönderildiğinde. 

  ![web uygulaması senaryosu](./media/application-gateway-web-app-overview/scenario.png)

Bu özellik sayesinde müşteriler, HTTP ayarları ve özel araştırmalardaki seçenekleri uygun yapılandırmaya göre belirtebilir. Bu ayar daha sonra bir kural kullanılarak bir dinleyiciye ve arka uç havuzuna bağlanır.

## <a name="special-considerations"></a>Özel durumlar

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Çok kiracılı hizmetlerle SSL sonlandırması ve uçtan uca SSL

SSL sonlandırma ve uçtan uca SSL şifrelemesi, çok kiracılı hizmetler ile desteklenir. Uygulama ağ geçidinde SSL sonlandırması için, SSL sertifikası uygulama ağ geçidi dinleyicisine eklenmek üzere olmaya devam eder. Bununla birlikte, uçtan uca SSL için, Azure App Service Web Apps gibi güvenilen Azure Hizmetleri, uygulama ağ geçidinde arka uçların beyaz listeye alınmasını gerektirmez. Bu nedenle, herhangi bir kimlik doğrulama sertifikası eklemeniz gerekmez. 

![uçtan uca SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Yukarıdaki görüntüde, App Service arka uç olarak seçildiğinde kimlik doğrulama sertifikalarının eklenmesi için bir gereksinim olmadığına dikkat edin.

### <a name="health-probe"></a>Durum yoklaması

**Http ayarlarındaki** ana bilgisayar üst bilgisini geçersiz kılmak yalnızca isteği ve onun yönlendirmesini etkiler. durum araştırma davranışını etkilemez. Uçtan uca işlevselliğin çalışması için hem araştırma hem de HTTP ayarları doğru yapılandırmayı yansıtacak şekilde değiştirilmelidir. Özel yoklamalar, araştırma yapılandırmasında bir ana bilgisayar üst bilgisi belirtme olanağı sağlamanın yanı sıra, ana bilgisayar üst bilgisini Şu anda yapılandırılmış HTTP ayarlarından türeme özelliğini de destekler. Bu yapılandırma, araştırma yapılandırmasındaki `PickHostNameFromBackendHttpSettings` parametresi kullanılarak belirtilebilir.

### <a name="redirection-to-app-services-url-scenario"></a>App Service URL senaryosuna yeniden yönlendirme

App Service 'in yanıtındaki ana bilgisayar adının, Application Gateway ilişkili etki alanı yerine Son Kullanıcı tarayıcısını *. azurewebsites.net ana bilgisayar adına yönlendirebilir. Bu sorun aşağıdaki durumlarda gerçekleşebilir:

- App Service yeniden yönlendirme yapılandırdınız. Yeniden yönlendirme, isteğe bir sondaki eğik çizgi eklemek kadar basit olabilir.
- Yeniden yönlendirmeye neden olan Azure AD kimlik doğrulaması var.

Bu tür durumları çözmek için bkz. [App Service 'ın URL sorunu için yeniden yönlendirme sorunlarını giderme](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Sonraki adımlar

Azure App Service Web uygulaması gibi çok kiracılı bir uygulamayla, arka uç havuzu üyesi olarak bir uygulama ağ geçidi ayarlamayı öğrenin [Application Gateway ile App Service Web Apps yapılandırma](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal) ziyaret edin
