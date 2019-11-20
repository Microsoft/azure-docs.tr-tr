---
title: Azure ön kapı hizmeti-ön kapı için sık sorulan sorular | Microsoft Docs
description: Bu sayfa, Azure ön kapı hizmeti hakkında sık sorulan soruların yanıtlarını sağlar
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 4001b3ed8d7a92d8a751486ab4fd3a097d032373
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809724"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure ön kapı hizmeti hakkında sık sorulan sorular

Bu makalede, Azure ön kapı hizmeti özellikleri ve işlevselliği hakkında sık sorulan sorular yanıtlanmaktadır. Sorunuzun yanıtını görmüyorsanız, aşağıdaki kanallarla (yürüyen sırada) bizimle iletişim kurmanız gerekir:

1. Bu makalenin açıklamalar bölümü.
2. [Azure ön kapı hizmeti UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft desteği:** Yeni bir destek isteği oluşturmak için, Azure portal **Yardım** sekmesinde **Yardım + Destek** düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.

## <a name="general"></a>Genel

### <a name="what-is-azure-front-door-service"></a>Azure Front Door Hizmeti nedir?

Azure ön kapı hizmeti, uygulamalarınız için çeşitli katman 7 yük dengeleme özellikleri sunan bir hizmet olarak Uygulama Teslim Ağı (ADN). Neredeyse gerçek zamanlı yük devretme ile genel yük dengeleme ile birlikte dinamik site hızlandırma (DSA) sağlar. Azure tarafından tam olarak yönetilen, yüksek oranda kullanılabilir ve ölçeklenebilir bir hizmettir.

### <a name="what-features-does-azure-front-door-service-support"></a>Azure ön kapı hizmeti hangi özellikleri destekler?

Azure ön kapı hizmeti, dinamik site hızlandırma (DSA), SSL boşaltma ve uçtan uca SSL, Web uygulaması güvenlik duvarı, tanımlama bilgisi tabanlı oturum benzeşimi, URL yolu tabanlı yönlendirme, ücretsiz sertifikalar ve birden çok etki alanı yönetimi ve diğerleri destekler. Desteklenen özelliklerin tam listesi için bkz. [Azure ön kapı hizmeti 'Ne genel bakış](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure ön kapı hizmeti ve Azure Application Gateway arasındaki fark nedir?

Hem ön kapı hem de Application Gateway katman 7 (HTTP/HTTPS) yük dengeleyiciler olsa da, birincil fark ön kapısının küresel bir hizmettir, ancak Application Gateway bölgesel bir hizmettir. Ön kapı, bölgelere göre farklı ölçek birimleriniz/kümeleriniz/damga birimleri arasında yük dengeleyebilmesini sağlarken, Application Gateway sanal makinelerleriniz/kapsayıcılarınız arasında (ölçek birimi içinde) yük dengelemesi yapmanıza olanak sağlar.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Ön kapı arkasında Application Gateway ne zaman dağıtıyoruz?

Birinci kapıdan Application Gateway kullanılması gereken önemli senaryolar şunlardır:

- Ön kapı, yalnızca genel düzeyde yol tabanlı yük dengelemesi gerçekleştirebilir, ancak biri sanal ağı (VNET) içinde daha da fazla yük dengelemek isterse, Application Gateway kullanmaları gerekir.
- Ön kapı bir VM/kapsayıcı düzeyinde çalışmadığından bağlantı boşaltma yapılamaz. Ancak Application Gateway bağlantı boşaltma yapmanıza olanak sağlar. 
- AFD 'ın arkasındaki Application Gateway, biri %100 SSL yük boşaltma elde edebilir ve yalnızca kendi sanal ağı (VNET) içinde HTTP isteklerini yönlendirebilir.
- Ön kapı ve Application Gateway her ikisi de oturum benzeşimini destekler. Ön kapı sonraki trafiği bir kullanıcı oturumundan, belirli bir bölgedeki aynı kümeye veya arka uca yönlendirebilir. Application Gateway, trafiği küme içindeki aynı sunucuya yönlendirebilir.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Ön kapıdan Azure Load Balancer dağıtabilir.

Azure ön kapı hizmeti, trafiği yönlendirmek için genel bir VIP veya genel olarak kullanılabilir bir DNS adı gerektirir. Ön kapıdan Azure Load Balancer dağıtım yaygın kullanım durumdur.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure ön kapı hizmeti hangi protokolleri destekler?

Azure ön kapı hizmeti, HTTP, HTTPS ve HTTP/2 destekler.

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure ön kapı hizmeti HTTP/2 ' i nasıl destekler?

HTTP/2 protokol desteği yalnızca Azure ön kapısına bağlanan istemciler tarafından kullanılabilir. Arka uç havuzundaki arka uçlarla iletişim HTTP/1.1 üzerinden yapılır. HTTP/2 desteği varsayılan olarak etkindir.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Bugün arka uç havuzunun bir parçası olarak hangi kaynaklar desteklenir?

Arka uç havuzları depolama, Web uygulaması, Kubernetes örnekleri veya ortak bağlantısı olan diğer özel ana bilgisayar adından oluşabilir. Azure ön kapı hizmeti, arka uçların genel bir IP veya genel olarak çözümlenebilen bir DNS ana bilgisayar aracılığıyla tanımlanmasını gerektirir. Arka uç havuzlarının üyeleri, genel bağlantı sahibi oldukları sürece bölgeler, bölgeler veya Azure dışında olabilir.

### <a name="what-regions-is-the-service-available-in"></a>Hizmet hangi bölgelerde kullanılabilir?

Azure ön kapı hizmeti küresel bir hizmettir ve belirli bir Azure bölgesine bağlı değildir. Ön kapı oluştururken belirtmeniz gereken tek konum, temel olarak kaynak grubu için meta verilerin depolanacağı yeri belirleyen kaynak grubu konumudur. Ön kapı kaynağı bir genel kaynak olarak oluşturulur ve yapılandırma tüm pop 'Lara (varlık noktası) genel olarak dağıtılır. 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Azure ön kapı hizmeti için POP konumları nelerdir?

Azure ön kapı hizmeti, Microsoft 'tan Azure CDN aynı POP (varlık noktası) konumları listesine sahiptir. Pop 'larımızın tüm listesi için, lütfen [Microsoft 'tan Azure CDN pop konumlarına](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)başvurur.

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure ön kapı hizmeti, Uygulamam için adanmış bir dağıtım mi yoksa müşteriler arasında mi paylaşılıyor?

Azure ön kapı hizmeti, küresel olarak dağıtılmış çok kiracılı bir hizmettir. Bu nedenle, ön kapıya ait altyapı tüm müşterileri arasında paylaşılır. Bununla birlikte, ön kapılı bir profil oluşturarak uygulamanız için gerekli olan özel yapılandırmayı tanımlarsınız ve ön kapıda diğer ön kapı yapılandırmalarında hiçbir değişiklik yapılmaz.

### <a name="is-http-https-redirection-supported"></a>HTTP-> HTTPS yeniden yönlendirmesi destekleniyor mu?

Evet. Azure ön kapı hizmeti, ana bilgisayar, yol ve sorgu dizesi yeniden yönlendirmeyi, URL yeniden yönlendirmenin bir parçasını da destekler. [URL yeniden yönlendirme](front-door-url-redirect.md)hakkında daha fazla bilgi edinin. 

### <a name="in-what-order-are-routing-rules-processed"></a>Yönlendirme kuralları hangi sırada işlendi?

Ön kapılarınızın rotaları sıralı değildir ve en iyi eşleşme temelinde belirli bir rota seçilir. [Ön kapısının bir yönlendirme kuralına yönelik Istekleri nasıl eşleştirtiği](front-door-route-matching.md)hakkında daha fazla bilgi edinin.

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Nasıl yaparım? arka ucuma erişimi yalnızca Azure ön kapısına kilitler mi?

Uygulamanızı yalnızca belirli ön kapıdan gelen trafiği kabul edecek şekilde kilitlemek için, arka ucunuz için IP ACL 'Lerini ayarlamanız ve ardından Azure ön kapısının gönderdiği ' X-Iletilen-Host ' üst bilgisi için kabul edilen değerler kümesini kısıtlamak gerekecektir. Bu adımlar aşağıdaki şekilde ayrıntılı olarak verilmiştir:

- Arka uçlarınızın IP adresini, Azure ön kapısından gelen trafiği ve yalnızca Azure 'un altyapı hizmetlerini kabul edecek şekilde yapılandırın. [Azure IP aralıkları ve hizmet etiketleriyle](https://www.microsoft.com/download/details.aspx?id=56519) tümleştirmeye yönelik çalışıyoruz, ancak şimdilik IP aralıklarını aşağıda gösterildiği gibi başvurabilirsiniz:
 
    - Ön kapı 'nın **IPv4** arka uç IP alanı: `147.243.0.0/16`
    - Ön kapı **IPv6** arka uç IP alanı: `2a01:111:2050::/44`
    - Sanallaştırılmış konak IP adresleri aracılığıyla Azure 'un [temel altyapı hizmetleri](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) : `168.63.129.16` ve `169.254.169.254`

    > [!WARNING]
    > Ön kapısının arka uç IP alanı daha sonra değişebilir, ancak [Azure IP aralıkları ve hizmet etiketleri](https://www.microsoft.com/download/details.aspx?id=56519)ile tümleştirilebilmemiz için bu işlem yapılmadan önce bu şekilde daha önce de olur. Herhangi bir değişiklik veya güncelleştirme için [Azure IP aralıklarına ve hizmet etiketlerine](https://www.microsoft.com/download/details.aspx?id=56519) abone olmanızı öneririz.

-   Ön kapı tarafından gönderilen '**X-iletilen-Host**' gelen üstbilgisinin değerlerini filtreleyin. Üst bilgi için izin verilen tek değerler, ön kapı yapılandırmasında tanımlanan ön uç Konakları olmalıdır. Özellikle de daha da özel olarak, bu belirli arka uçta yalnızca trafiğini kabul etmek istediğiniz ana bilgisayar adları.
    - Örnek: ön kapı yapılandırma 'nın şu ön uç Konakları _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _ (C) ve _`notifications.contoso.com`_ (D) olduğunu varsayalım. İki arka uç X ve Y olduğunu varsayalım. 
    - Arka uç X yalnızca A ve B ana bilgisayar adlarından trafik almalıdır. arka uç Y, bir, C ve D 'den trafik alabilir.
    - Bu nedenle, arka uç X 'te yalnızca '**X-iletilen-Host**' üst bilgisi _`contoso.azurefd.net`_ veya _`www.contoso.com`_ olarak ayarlanan trafiği kabul etmelisiniz. Diğer her şey için, arka uç X trafiği reddeder.
    - Benzer şekilde, arka uç Y 'de, yalnızca "**X-iletilen-Host**" başlığına sahip trafiği yalnızca _`contoso.azurefd.net`_ , _`api.contoso.com`_ veya _`notifications.contoso.com`_ olarak kabul etmelisiniz. Diğer her şey için, arka uç Y trafiği reddetmelidir.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Her noktaya yayın IP 'nin ön kapımın ömrü boyunca değiştirilmesini mi?

Ön kapılarınızın ön uç noktaya yayın IP 'si genellikle değişmemelidir ve ön kapısının ömrü boyunca statik kalabilir. Ancak, aynı için **garanti yoktur** . Lütfen IP üzerinde hiçbir doğrudan bağımlılık almaz.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure ön kapı hizmeti statik veya ayrılmış IP 'Leri destekliyor mu?

Hayır, Azure ön kapı hizmeti şu anda statik veya adanmış ön uç noktaya IP 'Leri desteklemiyor. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure ön kapı hizmeti, üst bilgiler için x ile iletime destekliyor mu?

Evet, Azure ön kapılı hizmeti X-iletilen-Host ve X-Iletilen-proto üst bilgilerini destekler. X-Iletilen için-üst bilgi zaten mevcutsa, ön kapı istemci yuvasını IP 'ye ekler. Aksi takdirde, üst bilgiyi istemci yuva IP 'si ile ilgili değer olarak ekler. X-Iletilen-Host ve X-Iletilen-proto için değer geçersiz kılınır.

[Ön kapı desteklenen http üstbilgileri](front-door-http-headers-protocol.md)hakkında daha fazla bilgi edinin.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Azure ön kapı hizmeti 'ni dağıtmak ne kadar sürer? Ön kapım güncelleştirilirken çalışmaya devam ediyor mu?

Yeni bir ön kapı oluşturma veya var olan bir ön kapıdaki herhangi bir güncelleştirme, genel dağıtım için yaklaşık 3 ila 5 dakika sürer. Bu, yaklaşık 3 ila 5 dakika içinde, ön kapı yapılandırmanızın tüm pop 'larda küresel olarak dağıtılmasını sağlar.

Note-özel SSL sertifika güncelleştirmelerinin genel olarak dağıtılması yaklaşık 30 dakika sürer.

## <a name="configuration"></a>Yapılandırma

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure ön kapısının yük dengelemesi yapabilir veya trafiği bir sanal ağ içinde yönlendirebilir mi?

Azure ön kapısı (AFD), trafiği yönlendirmek için genel IP veya genel olarak çözümlenebilen bir DNS adı gerektirir. Bu nedenle, yanıt hiçbir AFD doğrudan bir sanal ağ içinde yönlendirilemez, ancak arasında bir Application Gateway veya Azure Load Balancer kullanmak bu senaryoyu çözmeyecektir.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Azure ön kapı hizmeti için çeşitli zaman aşımları ve sınırlar nelerdir?

[Azure ön kapı hizmeti için tüm belgelenmiş zaman aşımları ve sınırlar](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)hakkında bilgi edinin.

## <a name="performance"></a>Performans

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure ön kapı hizmeti yüksek kullanılabilirliği ve ölçeklenebilirliği nasıl destekler?

Azure ön kapı hizmeti, uygulamanızın ölçeklenebilirlik ihtiyaçlarını karşılamak için çok büyük hacimli kapasiteye sahip, küresel olarak dağıtılmış çok kiracılı bir platformdur. Microsoft 'un küresel ağı kenarından sunulan ön kapı, tüm uygulamanızı veya hatta tek tek mikro hizmetleri bölge veya farklı bulutlar üzerinde yük devredebilmeniz için genel yük dengeleme özelliği sunar.

## <a name="ssl-configuration"></a>SSL yapılandırması

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Azure ön kapı hizmeti hangi TLS sürümlerini destekler?

Eylül 2019 ' den sonra oluşturulan tüm ön kapılı profiller varsayılan en düşük düzeyde TLS 1,2 ' i kullanır.

Ön kapı 1,0, 1,1 ve 1,2 TLS sürümlerini destekler. TLS 1,3 henüz desteklenmiyor.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Azure ön kapı hizmeti 'nde hangi sertifikalar desteklenir?

HTTPS protokolünü bir ön kapılı özel etki alanında güvenli bir şekilde teslim etmek üzere etkinleştirmek için, Azure ön kapısı hizmeti tarafından yönetilen bir sertifika kullanmayı veya kendi sertifikanızı kullanmayı seçebilirsiniz.
Ön kapı yönetimli seçeneği, DigiCert aracılığıyla standart bir SSL sertifikası sağlar ve ön kapı Key Vault depolanır. Kendi sertifikanızı kullanmayı seçerseniz, desteklenen bir CA 'dan bir sertifika ekleyebilirsiniz ve standart bir SSL, genişletilmiş doğrulama sertifikası veya hatta bir joker bir sertifika olabilir. Otomatik olarak imzalanan sertifikalar desteklenmez. [Özel bir etki alanı IÇIN https 'yi etkinleştirmeyi](https://aka.ms/FrontDoorCustomDomainHTTPS)öğrenin.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Ön kapı, sertifikaların oto döndürmesini destekliyor mu?

Ön kapı tarafından yönetilen sertifika seçeneği için sertifikalar ön kapıya göre oto döndürülür. Ön kapılı yönetilen bir sertifika kullanıyorsanız ve sertifikanın süre sonu tarihinin 60 günden daha az olduğunu görürseniz, bir destek bileti dosyası kullanın.
</br>Kendi özel SSL sertifikanız için, oto döndürme desteklenmez. Belirli bir özel etki alanı için ilk kez ayarlamaya benzer şekilde, ön kapıyı Key Vault doğru sertifika sürümüne işaret etmeniz ve ön kapıdaki hizmet sorumlusunun Key Vault hala erişime sahip olduğundan emin olmanız gerekir. Bu sertifika dağıtım işlemi ön kapıya göre güncelleştirilmiş olduğundan, sertifika için konu adı veya SAN sağlanmadığından hiçbir üretim etkisi olmaz.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure ön kapı hizmeti tarafından desteklenen geçerli şifre paketleri nelerdir?

Azure ön kapı hizmeti tarafından desteklenen geçerli şifre paketleri şunlardır:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure ön kapı hizmeti Ayrıca arka uca trafiği yeniden şifrelemeyi de destekliyor mu?

Evet, Azure ön kapı Hizmeti SSL yük boşaltma 'yı ve arka uca trafiği yeniden şifreleyen son SSL 'yi destekler. Aslında, arka uca bağlantıların ortak IP 'si üzerinden gerçekleşmesi nedeniyle, ön Kapıınızı iletme protokolü olarak HTTPS kullanacak şekilde yapılandırmanız önerilir.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL protokolü sürümlerini denetlemek için SSL ilkesi yapılandırabilir miyim?

Azure [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)aracılığıyla Azure ön kapıda en düşük TLS sürümünü yapılandırabilirsiniz. Şu anda 1,0 ve 1,2 arasında seçim yapabilirsiniz.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Ön kapıyı yalnızca belirli şifre paketlerini destekleyecek şekilde yapılandırabilir miyim?

Hayır, belirli şifre paketleri için ön kapıyı yapılandırma desteklenmiyor. 

## <a name="diagnostics-and-logging"></a>Tanılama ve günlüğe kaydetme

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile hangi türlerde ölçümler ve Günlükler mevcuttur?

Günlükler ve diğer tanılama özellikleri hakkında bilgi için bkz. [ön kapıda izleme ölçümleri ve günlükleri](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Tanılama günlüklerinde bekletme ilkesi nedir?

Tanılama günlükleri, müşteriler depolama hesabına akar ve müşteriler, saklama ilkesini tercihe göre ayarlayabilir. Tanılama günlükleri, bir olay hub 'ına veya Azure Izleyici günlüklerine de gönderilebilir. Daha fazla bilgi için bkz. [Azure ön kapı hizmeti tanılaması](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Azure ön kapı hizmeti için Denetim günlüklerini almak Nasıl yaparım? mı?

Denetim günlükleri Azure ön kapı hizmeti için kullanılabilir. Portalda, ön kapılarınızın menü dikey penceresinde **etkinlik günlüğü** ' ne tıklayarak denetim günlüğüne erişin. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile uyarıları ayarlayabilir miyim?

Evet, Azure ön kapı hizmeti uyarıları destekler. Uyarılar, ölçümler üzerinde yapılandırılır. 

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
