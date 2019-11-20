---
title: Azure Traffic Manager nasıl çalışacaktır? | Microsoft Docs
description: Bu makale, Web uygulamalarınızın yüksek performans ve kullanılabilirlik trafiğini Traffic Manager nasıl yönlendirdiğini anlamanıza yardımcı olur
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 281e1e591d7c3cc31b77a116fb42af49dc27798c
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312140"
---
# <a name="how-traffic-manager-works"></a>Traffic Manager nasıl kullanılır?

Azure Traffic Manager, uygulama uç noktalarınız genelinde trafiğin dağıtımını denetlemenize olanak sağlar. Uç nokta, Azure içinde veya dışında barındırılan İnternet'e yönelik bir hizmettir.

Traffic Manager iki temel avantaj sağlar:

- Trafiğin birkaç [trafik yönlendirme yönteminden](traffic-manager-routing-methods.md) birine göre dağıtılması
- Uç noktalar başarısız olduğunda [uç nokta durumunu](traffic-manager-monitoring.md) ve otomatik yük devretmeyi sürekli izleme

Bir istemci bir hizmete bağlanmaya çalıştığında, önce hizmetin DNS adını bir IP adresine çözümlemelidir. İstemci daha sonra hizmete erişmek için o IP adresine bağlanır.

**Anlamanız gereken en önemli nokta, Traffic Manager DNS düzeyinde çalışmaktadır.**  Traffic Manager, istemcileri trafik yönlendirme yönteminin kurallarına göre belirli hizmet uç noktalarına yönlendirmek için DNS kullanır. İstemciler seçili uç noktaya **doğrudan**bağlanır. Traffic Manager bir proxy veya ağ geçidi değil. Traffic Manager istemci ile hizmet arasında geçen trafiği görmez.

## <a name="traffic-manager-example"></a>Traffic Manager örneği

Contoso Corp yeni bir iş ortağı portalı geliştirmiş. Bu portalın https://partners.contoso.com/login.aspx URL 'si. Uygulama, Azure 'un üç bölgesinde barındırılır. Kullanılabilirliği artırmak ve genel performansı en üst düzeye çıkarmak için, istemci trafiğini kullanılabilir en yakın uç noktaya dağıtmak üzere Traffic Manager kullanırlar.

Bu yapılandırmayı başarmak için aşağıdaki adımları tamamlarlar:

1. Hizmetin üç örneğini dağıtın. Bu dağıtımların DNS adları ' contoso-us.cloudapp.net ', ' contoso-eu.cloudapp.net ' ve ' contoso-asia.cloudapp.net '.
1. ' Contoso.trafficmanager.net ' adlı bir Traffic Manager profili oluşturun ve üç uç nokta genelinde ' Performance ' trafik yönlendirme yöntemini kullanacak şekilde yapılandırın.
1. ' Partners.contoso.com ', ' contoso.trafficmanager.net ' öğesini işaret eden bir DNS CNAME kaydı kullanarak bu etki alanı adını yapılandırın.

![DNS yapılandırması Traffic Manager][1]

> [!NOTE]
> Azure Traffic Manager ile bir gösterim etki alanı kullanırken, gösterim etki alanı adınızı Traffic Manager etki alanı adına göstermek için bir CNAME kullanmanız gerekir. DNS standartları, bir etki alanının ' tepesinde ' (veya kökündeki) ' de bir CNAME oluşturmanıza izin vermez. Bu nedenle, ' contoso.com ' için bir CNAME oluşturamazsınız (bazen ' Naked ' etki alanı olarak adlandırılır). Yalnızca ' contoso.com ' altında 'www.contoso.com' gibi bir etki alanı için CNAME oluşturabilirsiniz. Bu sınırlamaya geçici bir çözüm bulmak için, DNS etki alanınızı [Azure DNS](../dns/dns-overview.md) ve Traffic Manager profilinize Işaret eden [diğer ad kayıtlarını](../dns/tutorial-alias-tm.md) kullanarak barındırmalarını öneririz. Alternatif olarak, 'contoso.com' isteklerini 'www.contoso.com' gibi alternatif bir ada yönlendirmek için basit bir HTTP yeniden yönlendirme kullanabilirsiniz.

### <a name="how-clients-connect-using-traffic-manager"></a>İstemciler Traffic Manager kullanarak nasıl bağlanır

Önceki örnekte devam edildiğinde, istemci sayfayı https://partners.contoso.com/login.aspx istediğinde, istemci, DNS adını çözümlemek ve bir bağlantı kurmak için aşağıdaki adımları gerçekleştirir:

![Traffic Manager kullanarak bağlantı kurma][2]

1. İstemci, yapılandırılmış özyinelemeli DNS hizmetine ' partners.contoso.com ' adını çözümlemek için bir DNS sorgusu gönderir. Bazen ' yerel DNS ' hizmeti olarak adlandırılan özyinelemeli bir DNS hizmeti, DNS etki alanlarını doğrudan barındırmaz. Bunun yerine, istemci devre dışı-bir DNS adını çözümlemek için gereken çeşitli yetkili DNS hizmetlerine bağlantı kurma işini yükler.
2. DNS adını çözümlemek için, özyinelemeli DNS hizmeti ' contoso.com ' etki alanı için ad sunucularını bulur. Daha sonra ' partners.contoso.com ' DNS kaydını istemek için bu ad sunucularıyla iletişim kurar. Contoso.com DNS sunucuları, contoso.trafficmanager.net 'e işaret eden CNAME kaydını döndürür.
3. Ardından, özyinelemeli DNS hizmeti, Azure Traffic Manager hizmeti tarafından belirtilen ' trafficmanager.net ' etki alanı için ad sunucularını bulur. Daha sonra bu DNS sunucularına ' contoso.trafficmanager.net ' DNS kaydı için bir istek gönderir.
4. Traffic Manager ad sunucuları isteği alır. Şunları temel alan bir uç nokta seçer:

    - Her uç noktanın yapılandırılmış durumu (devre dışı uç noktalar döndürülmez)
    - Traffic Manager sistem durumu denetimleri tarafından belirlendiği şekilde, her uç noktanın geçerli sistem durumu. Daha fazla bilgi için bkz. [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md).
    - Seçilen trafik-yönlendirme yöntemi. Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri](traffic-manager-routing-methods.md).

5. Seçilen uç nokta, başka bir DNS CNAME kaydı olarak döndürülür. Bu durumda, contoso-us.cloudapp.net döndürüldüğünü varsayalım.
6. Ardından, özyinelemeli DNS hizmeti ' cloudapp.net ' etki alanı için ad sunucularını bulur. ' Contoso-us.cloudapp.net ' DNS kaydını istemek için bu ad sunucularıyla iletişim kurar. ABD tabanlı hizmet uç noktasının IP adresini içeren bir DNS ' A ' kaydı döndürülür.
7. Özyinelemeli DNS hizmeti, sonuçları birleştirir ve istemciye tek bir DNS yanıtı döndürür.
8. İstemci DNS sonuçlarını alır ve verilen IP adresine bağlanır. İstemci, Traffic Manager aracılığıyla değil, doğrudan uygulama hizmeti uç noktasına bağlanır. Bir HTTPS uç noktası olduğundan, istemci gerekli SSL/TLS el sıkışma işlemini gerçekleştirir ve ardından '/Login.aspx ' sayfası için bir HTTP GET isteği oluşturur.

Özyinelemeli DNS hizmeti, aldığı DNS yanıtlarını önbelleğe alır. İstemci cihazdaki DNS Çözümleyicisi de sonucu önbelleğe alır. Önbelleğe alma, sonraki DNS sorgularının diğer ad sunucularını sorgulamak yerine önbellekteki verileri kullanarak daha hızlı yanıtlanmasını sağlar. Önbelleğin süresi, her DNS kaydının ' yaşam süresi ' (TTL) özelliği tarafından belirlenir. Daha kısa değerler, daha hızlı önbellek süre sonu ve bu nedenle Traffic Manager ad sunucularına daha fazla gidiş dönüş elde ediyor. Daha uzun değerler, trafiğin başarısız bir uç noktadan uzaklaşmak için daha uzun sürebileceği anlamına gelir. Traffic Manager, Traffic Manager DNS yanıtlarında kullanılan TTL 'yi 0 saniye ve en fazla 2.147.483.647 saniye ( [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)ile uyumlu en yüksek Aralık) olarak yapılandırmak ve bu sayede, gereksinimlerini en iyi şekilde dengeleyen değeri seçmenizi sağlar. uygulamanız.

## <a name="faqs"></a>SSS

* [Traffic Manager hangi IP adresi kullanır?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Traffic Manager kullanılarak hangi türde trafik yönlendirilebilir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* ["Sticky" oturumlarını Traffic Manager destekler mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Traffic Manager kullanırken neden HTTP hatası görüyorum?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Traffic Manager kullanmanın performans etkisi nedir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Traffic Manager ile hangi uygulama protokollerini kullanabilirim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Traffic Manager, "Naked" etki alanı adıyla kullanabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [DNS sorgularını işlerken istemci alt ağ adresini göz önüne Traffic Manager mı?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [DNS TTL nedir ve Kullanıcılarımı nasıl etkiler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Traffic Manager yanıtları için TTL 'yi ne kadar yüksek veya düşük bir şekilde ayarlayabilirim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Profilim 'e gelen sorguların hacmini nasıl anlayabilirim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Sonraki adımlar

Traffic Manager [uç nokta izleme ve otomatik yük devretme](traffic-manager-monitoring.md)hakkında daha fazla bilgi edinin.

Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

