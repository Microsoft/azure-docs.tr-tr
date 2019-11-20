---
title: Azure Traffic Manager-SSS
description: Bu makale Traffic Manager hakkında sık sorulan soruların yanıtlarını sağlar
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.openlocfilehash: f08915c07db6759a03fc9bd0695523dead6dcb7f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72784824"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager sık sorulan sorular (SSS)

## <a name="traffic-manager-basics"></a>Traffic Manager temel bilgiler

### <a name="what-ip-address-does-traffic-manager-use"></a>Traffic Manager hangi IP adresi kullanır?

[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager DNS düzeyinde çalışmaktadır. İstemcileri uygun hizmet uç noktasına yönlendirmek için DNS yanıtları gönderir. İstemciler daha sonra Traffic Manager aracılığıyla değil, doğrudan hizmet uç noktasına bağlanır.

Bu nedenle Traffic Manager, istemcilerin bağlanması için bir uç nokta veya IP adresi sağlamaz. Hizmetiniz için statik IP adresi istiyorsanız, bu, Traffic Manager değil, hizmette yapılandırılması gerekir.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Traffic Manager kullanılarak hangi türde trafik yönlendirilebilir?
[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, bir Traffic Manager uç noktası, Azure içinde veya dışında barındırılan internet 'e yönelik herhangi bir hizmet olabilir. Bu nedenle Traffic Manager, genel İnternet 'ten gelen trafiği de internet 'e yönelik bir uç nokta kümesine yönlendirebilir. Özel bir ağ içinde (örneğin, [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)iç sürümü) veya kullanıcıların bu Iç ağlardan DNS istekleri yapmasını istiyorsanız, bu trafiği yönlendirmek için Traffic Manager kullanamazsınız.

### <a name="does-traffic-manager-support-sticky-sessions"></a>"Sticky" oturumlarını Traffic Manager destekler mi?

[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager DNS düzeyinde çalışmaktadır. İstemcileri uygun hizmet uç noktasına yönlendirmek için DNS yanıtlarını kullanır. İstemciler, Traffic Manager aracılığıyla değil, hizmet uç noktasına doğrudan bağlanır. Bu nedenle, Traffic Manager istemci ile sunucu arasındaki HTTP trafiğini görmez.

Ayrıca, Traffic Manager tarafından alınan DNS sorgusunun kaynak IP adresi, istemciye değil özyinelemeli DNS hizmetine aittir. Bu nedenle, Traffic Manager bağımsız istemcileri izlemenin bir yolu yoktur ve ' Sticky ' oturumlarını uygulayamaz. Bu sınırlama, tüm DNS tabanlı trafik yönetimi sistemleri için ortaktır ve Traffic Manager özgü değildir.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager kullanırken neden HTTP hatası görüyorum?

[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager DNS düzeyinde çalışmaktadır. İstemcileri uygun hizmet uç noktasına yönlendirmek için DNS yanıtlarını kullanır. İstemciler daha sonra Traffic Manager aracılığıyla değil, doğrudan hizmet uç noktasına bağlanır. Traffic Manager istemci ve sunucu arasında HTTP trafiği görmez. Bu nedenle, gördüğünüz herhangi bir HTTP hatası uygulamanızdan geliyor olmalıdır. İstemcinin uygulamaya bağlanması için tüm DNS çözümleme adımları tamamlanmıştır. Bu, uygulama trafik akışında Traffic Manager sahip olan herhangi bir etkileşimi içerir.

Bu nedenle, daha fazla araştırma uygulamaya odaklanmalıdır.

İstemcinin tarayıcısından gönderilen HTTP ana bilgisayar üst bilgisi en yaygın sorun kaynağıdır. Uygulamanın, kullanmakta olduğunuz etki alanı adı için doğru ana bilgisayar üstbilgisini kabul edecek şekilde yapılandırıldığından emin olun. Azure App Service kullanan uç noktalar için, bkz. [Traffic Manager kullanarak Azure App Service bir Web uygulaması için özel etki alanı adı yapılandırma](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Traffic Manager kullanmanın performans etkisi nedir?

[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager DNS düzeyinde çalışmaktadır. İstemciler hizmet uç noktalarınıza doğrudan bağlandıklarından, bağlantı kurulduktan sonra Traffic Manager kullanılırken performans etkisi yoktur.

Traffic Manager, DNS düzeyindeki uygulamalarla tümleştiğinden, DNS çözümleme zincirine ek bir DNS aramasının eklenmesi gerekir. DNS çözümleme süresi üzerinde Traffic Manager etkisi en düşük düzeydedir. Traffic Manager, ad sunucularından oluşan küresel bir ağı kullanır ve DNS sorgularının her zaman kullanılabilir en yakın ad sunucusuna yönlendirildiğinden emin olmak için [her noktaya yayın](https://en.wikipedia.org/wiki/Anycast) ağını kullanır. Ayrıca, DNS yanıtlarının önbelleğe alınması, Traffic Manager kullanılarak oluşan ek DNS gecikmesi yalnızca bir oturum kesri için geçerlidir.

Performans yöntemi, trafiği kullanılabilir en yakın uç noktaya yönlendirir. Net sonucu, bu yöntemle ilişkili genel performans etkisinin en az olması gerekir. DNS gecikmede herhangi bir artış, uç noktaya daha düşük ağ gecikme süresine göre denkleştirilir.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Traffic Manager ile hangi uygulama protokollerini kullanabilirim?

[Traffic Manager nasıl çalıştığı konusunda](../traffic-manager/traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager DNS düzeyinde çalışmaktadır. DNS araması tamamlandıktan sonra, istemciler Traffic Manager aracılığıyla değil, doğrudan uygulama uç noktasına bağlanır. Bu nedenle, bağlantı herhangi bir uygulama protokolünü kullanabilir. İzleme protokolü olarak TCP ' yi seçerseniz, Traffic Manager uç nokta sistem durumu izleme herhangi bir uygulama protokolü kullanılmadan yapılabilir. Sistem durumunun bir uygulama protokolünü kullanarak doğrulanmasını seçerseniz, uç noktasının HTTP veya HTTPS GET isteklerine yanıt verebilmeleri gerekir.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Traffic Manager, "Naked" etki alanı adıyla kullanabilir miyim?

Evet. Tepesinde etki alanı adınız için bir Azure Traffic Manager profiline başvurmak üzere bir diğer ad kaydı oluşturma hakkında bilgi edinmek için bkz. [Traffic Manager ile tepesinde etki alanı adlarını desteklemek için bir diğer ad kaydı yapılandırma](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>DNS sorgularını işlerken istemci alt ağ adresini göz önüne Traffic Manager mı? 

Evet, aldığı DNS sorgusunun kaynak IP adresine ek olarak (genellikle DNS Çözümleyicisinin IP adresidir), coğrafi, performans ve alt ağ yönlendirme yöntemlerinin aramalarını gerçekleştirirken Traffic Manager, şu durumlarda istemci alt ağ adresini de dikkate alır. Bu, isteği Son Kullanıcı adına getiren çözümleyici tarafından sorguya dahil edilmiştir.  
Özellikle, [RFC 7871 – DNS sorgularında Istemci alt ağı](https://tools.ietf.org/html/rfc7871) [(EDNS0)](https://tools.ietf.org/html/rfc2671) tarafından desteklenen çözümleyicilerine istemci alt ağ adresini geçirebilen bir uzantı mekanizması sağlar.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>DNS TTL nedir ve Kullanıcılarımı nasıl etkiler?

Bir DNS sorgusu Traffic Manager olduğunda, yanıtta yaşam süresi (TTL) olarak adlandırılan bir değer ayarlar. Birimi saniye cinsinden olan bu değer, DNS çözümleyicilerine bu yanıtın ne kadar süre önbellekte tutulacağını gösterir. DNS çözümleyicilerine bu sonucu önbelleğe almaya garanti edilmediği sürece, önbelleğe alma Traffic Manager işlemi, DNS sunucularına gitmek yerine önbelleğin sonraki tüm sorgulara yanıt vermesini sağlar. Bu, yanıtları aşağıda gösterildiği gibi etkiler:

- daha yüksek bir TTL, Traffic Manager DNS sunucularında geçen sorguların sayısını azaltır ve bu, sunulan sorgu sayısı faturalandırılabilir bir kullanım olduğundan müşterinin maliyetini azaltabilir.
- daha yüksek bir TTL, DNS araması yapmak için geçen süreyi azaltabilir.
- daha yüksek bir TTL Ayrıca, verilerinizin araştırma aracıları aracılığıyla Traffic Manager en son sistem durumu bilgilerini yansıtmayacağı anlamına gelir.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Traffic Manager yanıtları için TTL 'yi ne kadar yüksek veya düşük bir şekilde ayarlayabilirim?

Her profil düzeyinde, DNS TTL değerini 0 saniye ve en fazla 2.147.483.647 saniye ( [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )ile uyumlu maksimum Aralık) olarak ayarlayabilirsiniz. 0 TTL değeri, aşağı akış DNS çözümleyiclarının sorgu yanıtlarını önbelleğe belirtmeyeceği ve tüm sorguların çözümlenmek üzere Traffic Manager DNS sunucularına ulaşması beklenmediği anlamına gelir.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Profilim 'e gelen sorguların hacmini nasıl anlayabilirim? 

Traffic Manager tarafından verilen ölçülerden biri, bir profil tarafından yanıtlanan sorgu sayısıdır. Bu bilgileri profil düzeyindeki bir toplamada alabilir veya belirli uç noktaların döndürüldüğü sorgu hacmini görmek için daha fazla ayırabilirsiniz. Ayrıca, sorgu yanıt birimi ayarladığınız koşulları aştığında size bildirimde bulunan uyarılar ayarlayabilirsiniz. Daha ayrıntılı bilgi için [Traffic Manager ölçümleri ve uyarıları](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager coğrafi trafik yönlendirme yöntemi

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Coğrafi yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?

Coğrafi yönlendirme türü, bir Azure müşterisinin kullanıcılarını coğrafi bölgelere göre ayırt etmek için ihtiyacı olan herhangi bir senaryoda kullanılabilir. Örneğin, coğrafi trafik yönlendirme yöntemini kullanarak belirli bölgelerdeki kullanıcılara diğer bölgelerden farklı bir kullanıcı deneyimi verebilirsiniz. Diğer bir örnek, belirli bir bölgedeki kullanıcıların yalnızca ilgili bölgedeki uç noktalarla sunulmasını gerektiren yerel veri egemenlik mantarihleriyle uyumlu değildir.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Performans yönlendirme yöntemini mi yoksa coğrafi yönlendirme yöntemini mi kullanmalıyım Nasıl yaparım? karar veriyor musunuz?

Bu iki popüler yönlendirme yöntemi arasındaki önemli fark, birincil hedefiniz için en düşük gecikme süresini sağlayan uç noktaya trafik göndermek, ancak birincil hedefin coğrafi olarak bir coğrafi bölge zorlaması sağlamaktır belirli bir uç noktaya kasıtlı olarak yönlendirebilmeniz için çağıranlarınızın dilimi. Çakışma, coğrafi closeness ve daha düşük gecikme arasında bir bağıntı olduğundan, her zaman doğru olmamasına rağmen oluşur. Farklı bir Coğrafya 'da, çağıran için daha iyi bir gecikme deneyimi sağlayabilen bir uç nokta olabilir ve bu durumda performans yönlendirmenin kullanıcıyı bu uç noktaya gönderebilmesi, coğrafi yönlendirmenin her zaman onları her zaman için eşleştirmekte olduğunuz uç noktaya göndermesini sağlayabilirsiniz. coğrafi bölge. Daha ayrıntılı hale getirmek için aşağıdaki örneği göz önünde bulundurun-coğrafi yönlendirme ile, Asya 'daki tüm trafiği ABD 'de ve tüm ABD trafiğiyle Asya 'daki uç noktalara Gönder gibi yaygın olmayan eşlemeler yapabilirsiniz. Bu durumda, coğrafi yönlendirme, tam olarak sizin yapılandırdığınız ve performans iyileştirmesi dikkate alınmaz. 
>[!NOTE]
>Hem performans hem de coğrafi yönlendirme özelliklerine ihtiyacınız olabilecek senaryolar olabilir, bu senaryolar için iç içe geçmiş profiller harika seçim olabilir. Örneğin, Kuzey Amerika tüm trafiği ABD 'de uç noktalara sahip bir iç içe geçmiş profile gönderirseniz ve bu trafiği bu küme içindeki en iyi uç noktaya göndermek için performans yönlendirmeyi kullandığınızda, coğrafi yönlendirmeye sahip bir üst profil ayarlayabilirsiniz. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Coğrafi yönlendirme için Traffic Manager tarafından desteklenen bölgeler nelerdir?

Traffic Manager tarafından kullanılan ülke/bölge hiyerarşisi [burada](traffic-manager-geographic-regions.md)bulunabilir. Bu sayfa herhangi bir değişiklikle güncel tutulurken, aynı bilgileri [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)kullanarak program aracılığıyla da alabilirsiniz. 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Traffic Manager bir kullanıcının sorgu nerede olduğunu nasıl belirleyebilir?

Traffic Manager sorgunun kaynak IP 'sini arar (Bu, büyük olasılıkla Kullanıcı adına sorgu yapan yerel bir DNS çözümleyicidir) ve konumu belirleyebilmek için bir iç IP ile bölge eşlemesi kullanır. Bu harita, internet 'teki değişikliklere yönelik olarak sürekli olarak güncelleştirilir. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Traffic Manager her durumda kullanıcının tam coğrafi konumunu doğru bir şekilde belirleyebilsin mi?

Hayır, Traffic Manager bir DNS sorgusunun kaynak IP adresinden çıkardığımız coğrafi bölgenin, her zaman aşağıdaki nedenlerden dolayı kullanıcının konumuna karşılık geldiğini garanti edemez:

- İlk olarak, önceki SSS bölümünde açıklandığı gibi, görtiğimiz kaynak IP adresi, Kullanıcı adına arama yapan bir DNS çözümleyicisine ait. DNS Çözümleyicisinin coğrafi konumu kullanıcının coğrafi konumu için iyi bir ara sunucu olsa da, DNS çözümleyici hizmetinin parmak izine ve müşterinin kullanmayı seçtiği belirli DNS çözümleyici hizmetine bağlı olarak farklı olabilir. Örnek olarak, Malezya 'da bulunan bir müşterinin cihaz ayarlarında, bu kullanıcı/cihaz için sorgu çözümlerini işlemek üzere Singapur 'daki DNS sunucusu bir DNS Çözümleyicisi hizmeti kullanır. Bu durumda Traffic Manager, yalnızca Singapur konumuna karşılık gelen çözümleyici 'nin IP adresini görebilirler. Ayrıca, bu sayfada istemci alt ağ adresi desteğiyle ilgili önceki SSS bölümüne bakın.

- İkincisi, Traffic Manager IP adresini coğrafi bölge çevirisi yapmak için bir iç eşleme kullanır. Bu harita doğrulanırken ve internet 'in gelişmiş olması için doğruluğu ve hesabını artırmak üzere sürekli olarak güncelleştirildiğinden, bilgilerinizin tüm IP 'nin coğrafi konumunun tam bir gösterimi olmaması de mümkün değildir. gideren.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Bir uç noktanın coğrafi yönlendirme için yapılandırılmış ile aynı bölgede fiziksel olarak bulunması gerekir mi?

Hayır, uç noktanın konumu, hangi bölgelere eşleştirilebileceği konusunda hiçbir kısıtlama vermez. Örneğin, ABD-Orta Azure bölgesindeki bir uç noktanın, Hindistan 'a yönlendiren tüm kullanıcılara sahip olması olabilir.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Coğrafi yönlendirme yapmak üzere yapılandırılmamış bir profildeki uç noktalara coğrafi bölgeler atayabilir miyim?

Evet, bir profilin yönlendirme yöntemi coğrafi değilse, bu profildeki uç noktalara coğrafi bölgeler atamak için [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) kullanabilirsiniz. Coğrafi olmayan yönlendirme türü profilleri söz konusu olduğunda bu yapılandırma yok sayılır. Bu tür bir profili daha sonra coğrafi yönlendirme türüne değiştirirseniz Traffic Manager bu eşlemeleri kullanabilir.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Mevcut bir profilin yönlendirme yöntemini coğrafi olarak değiştirmeyi denediğimde neden hata alıyorum?

Coğrafi yönlendirmeye sahip bir profil altındaki tüm uç noktalara en az bir bölge eşlenmiş olması gerekir. Var olan bir profili coğrafi yönlendirme türüne dönüştürmek için, ilk olarak coğrafi bölgeleri [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) kullanarak tüm uç noktalarına ilişkilendirmeniz gerekir. Bu, yönlendirme türünü coğrafi olarak değiştirmeden önce. Portalı kullanıyorsanız, önce uç noktaları silin, profilin yönlendirme yöntemini coğrafi olarak değiştirin ve sonra uç noktaları, coğrafi bölge eşlemeleriyle birlikte ekleyin.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Müşterilerin coğrafi yönlendirmeyi etkin bir profil altında olan uç noktalar yerine iç içe geçmiş profiller oluşturması kesinlikle önerilir mi?

Bölge, coğrafi yönlendirme yöntemini kullanıyorsa, bir profil içinde yalnızca bir uç noktaya atanabilir. Bu uç nokta, kendisine bağlı bir alt profili olan iç içe bir tür değilse, herhangi bir trafiği göndermeyen bir alternatif olmadığından, bu uç nokta sağlıksız durumda Traffic Manager trafik göndermeye devam eder. Traffic Manager, atanan bölge sağlıksız olan uç noktaya atanmış bölgenin bir "üst" olduğunda bile başka bir uç noktaya yük devretmez (örneğin, Ispanya bölgesi olan bir uç nokta sağlıksız olursa, başka bir uç noktaya yük devredebiliriz Avrupa bölgesine atanmış bölgeleri içerir). Bu, Traffic Manager müşterinin profilinde kurulu olan coğrafi sınırlara karşılık olmasını sağlamak için yapılır. Bir uç nokta sağlıksız olduğunda başka bir uç noktaya yük devretme avantajı sağlamak için, coğrafi bölgelerin tek uç noktalar yerine, içindeki birden çok uç nokta ile iç içe geçmiş profillere atanması önerilir. Bu şekilde, iç içe geçmiş alt profildeki bir uç nokta başarısız olursa, trafiği aynı iç içe alt profil içindeki başka bir uç noktaya yük devredebilirler.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>API sürümünde bu yönlendirme türünü destekleyen herhangi bir kısıtlama var mı?

Evet, yalnızca API sürüm 2017-03-01 ve üzeri, coğrafi yönlendirme türünü destekler. Eski API sürümleri, coğrafi yönlendirme türünün profillerini oluşturma veya uç noktalara coğrafi bölgeler atama için kullanılamaz. Bir Azure aboneliğinden profilleri almak için eski bir API sürümü kullanılırsa, coğrafi yönlendirme türünün herhangi bir profili döndürülmez. Ayrıca, eski API sürümleri kullanılırken, coğrafi bölge ataması olan bitiş noktalarına sahip olan herhangi bir profilde gösterilen coğrafi bölge ataması yoktur.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager alt ağ trafiği yönlendirme yöntemi

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Alt ağ yönlendirmesinin yararlı olduğu bazı kullanım durumları nelerdir?

Alt ağ yönlendirme, DNS isteklerinin IP adresinin kaynak IP 'si tarafından tanımlanan belirli kullanıcı kümeleri için teslim ettiğiniz deneyimi ayırt etmenize olanak tanır. Kullanıcılar şirket HQ 'ınızdan bir Web sitesine bağlanıyorsa bir örnek farklı içerik gösterir. Diğeri ise belirli ISS 'lerden gelen kullanıcıları, yalnızca IPv6 kullanıldığında bu ISS 'Lerin alt düzey performansa sahip olması durumunda yalnızca IPv4 bağlantılarını destekleyen uç noktalara erişmelerini kısıtlamalardır.
Alt ağ yönlendirme yöntemini kullanmanın başka bir nedeni, iç içe geçmiş bir profil kümesindeki diğer profillerle birlikte bulunur. Örneğin, kullanıcılarınıza coğrafi olarak sınırlama eklemek için coğrafi yönlendirme yöntemini kullanmak istiyorsanız, ancak belirli bir ISS için farklı bir yönlendirme yöntemi yapmak istiyorsanız, üst profil olarak bir profil geçiş alt ağı yönlendirme yöntemine sahip olabilir ve bu ISS 'yi belirli bir alt Pro 'yu kullanacak şekilde geçersiz kılabilirsiniz dosyasına ve diğer herkese yönelik standart coğrafi profile sahip olabilir.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Traffic Manager son kullanıcının IP adresini nasıl bilir?

Son Kullanıcı cihazları, genellikle DNS aramasını kendi adına bir DNS Çözümleyicisi kullanır. Bu çözümleyiciler giden IP 'si Traffic Manager kaynak IP olarak görüşeydir. Buna ek olarak, alt ağ yönlendirme yöntemi de istekle geçirilmiş bir EDNS0 genişletilmiş Istemci alt ağı (ECS) bilgisi olup olmadığını görmek için de görünür. ECS bilgileri mevcutsa, bu, yönlendirmeyi belirlemede kullanılan adrestir. ECS bilgileri yokluğunda, sorgunun kaynak IP 'si yönlendirme amacıyla kullanılır.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Alt ağ yönlendirmeyi kullanırken IP adreslerini nasıl belirtirim?

Bir uç nokta ile ilişkilendirilecek IP adresleri iki şekilde belirtilebilir. İlk olarak, aralığı belirtmek için bir başlangıç ve bitiş adresleriyle dört noktalı ondalık sekizli gösterimini kullanabilirsiniz (örneğin, 1.2.3.4-5.6.7.8 veya 3.4.5.6-3.4.5.6). İkinci olarak, aralığı belirtmek için CıDR gösterimini kullanabilirsiniz (örneğin, 1.2.3.0/24). Birden çok Aralık belirtebilir ve bir Aralık kümesinde her iki gösterim türünü de kullanabilirsiniz. Birkaç kısıtlama geçerlidir.

-   Her IP 'nin yalnızca tek bir uç noktaya eşlenmesi gerektiğinden, adres aralıklarının çakışamaz
-   Başlangıç adresi bitiş adresinden daha fazla olamaz
-   CıDR gösteriminde, '/' öncesindeki IP adresinin bu aralığın başlangıç adresi olması gerekir (örneğin, 1.2.3.0/24 geçerlidir ancak 1.2.3.4.4/24 geçerli DEĞILDIR)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Alt ağ yönlendirmesi kullanırken bir geri dönüş uç noktası nasıl belirtebilir?

Alt ağ yönlendirme içeren bir profilde, alt ağa eşlenmiş bir uç noktanız varsa, diğer uç noktalarla eşleşmeyen istekler buraya yönlendirilir. Bir isteğin geldiği bir NXDOMAIN yanıtı döndürmesi ve herhangi bir uç noktaya eşlenmediğinden veya bir uç nokta ile eşlenmişse, ancak o uç nokta sağlıksız olduğundan, Traffic Manager profilinizde bu tür bir geri dönüş uç noktası olması önemle önerilir.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Bir alt ağ yönlendirme türü profilinde bir uç nokta devre dışıysa ne olur?

Alt ağ yönlendirme içeren bir profilde, devre dışı bırakılmış bir uç noktanız varsa Traffic Manager, bu uç nokta ve sahip olduğu alt ağ eşlemeleri gibi davranır. IP adresi eşlemesiyle eşleştirileceği bir sorgu alınır ve uç nokta devre dışıysa Traffic Manager, bir geri dönüş uç noktası döndürür (biri eşlemeyle birlikte) veya böyle bir uç nokta yoksa bir NXDOMAIN yanıtı döndürür.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager çok değerli trafik yönlendirme yöntemi

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Çok değerli yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?

Çoklu değer yönlendirme tek bir sorgu yanıtında birden çok sağlıklı uç nokta döndürüyor. Bunun başlıca avantajı, bir uç noktanın sağlıksız olması durumunda istemcinin başka bir DNS çağrısı yapmadan yeniden denemek için daha fazla seçeneğe (yukarı akış önbelleğinden aynı değeri döndürebileceğini) sahip olması anlamına gelir. Bu, kapalı kalma süresini en aza indirmek isteyen kullanılabilirlik duyarlı uygulamalar için geçerlidir.
Çok değerli yönlendirme yöntemi için başka bir kullanım, bir uç noktanın hem IPv4 hem de IPv6 adresleri için "çift bağlantılı" olması ve çağrıyı yapana bir bağlantı başlattığında her iki seçeneği de seçim yapmak istediğinizde sağlar.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Çok değerli yönlendirme kullanıldığında kaç uç nokta döndürülüyor?

Döndürülecek en fazla uç nokta sayısını, bir sorgu alındığında birden çok sağlıklı uç noktasından daha fazla geri dönemeyeceğini belirtebilirsiniz. Bu yapılandırma için olası en yüksek değer 10 ' dur.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Çoklu küme yönlendirmesi kullanıldığında aynı uç nokta kümesini alacak mıyım?

Her sorguda aynı uç nokta kümesinin döndürülmeyeceğini garanti edemeyiz. Bu Ayrıca, bazı uç noktaların, yanıta dahil edilmeyeceği noktada sağlıksız bir şekilde gidebileceği gerçeden etkilenir

## <a name="real-user-measurements"></a>Gerçek Kullanıcı Ölçümleri

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Gerçek Kullanıcı Ölçümleri kullanmanın avantajları nelerdir?

Performans yönlendirme yöntemini kullandığınızda Traffic Manager, son kullanıcılarınızın bağlantı için en iyi Azure bölgesini kaynak IP ve EDNS Istemci alt ağını inceleyerek (geçirilirse) ve hizmetin koruduğu ağ gecikmesi zekasına karşı kontrol ederek kullanır. Gerçek Kullanıcı Ölçümleri, bu tablo, son kullanıcılarınızın Azure 'a bağlandıklarında Son Kullanıcı ağlarını yeterince kapsadığından emin olmanın yanı sıra bu gecikme süresi tablosuna katkıda bulunmak için bunu Son Kullanıcı tabanınız için geliştirir. Bu, son kullanıcılarınızın yönlendirmesinde daha fazla doğruluk doğurur.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Azure olmayan bölgelerle Gerçek Kullanıcı Ölçümleri kullanabilir miyim?

Yalnızca Azure bölgelerine ulaşma gecikmede ölçü ve rapor Gerçek Kullanıcı Ölçümleri. Azure dışı bölgelerde barındırılan uç noktalarla performans tabanlı yönlendirme kullanıyorsanız, bu uç noktayla ilişkilendirilmesi için seçtiğiniz temsili Azure bölgesi hakkında daha fazla gecikme bilgisi alarak bu özellikten faydalanabilirsiniz.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Gerçek Kullanıcı Ölçümleri hangi yönlendirme yöntemi avantajları?

Gerçek Kullanıcı Ölçümleri aracılığıyla elde edilen ek bilgiler yalnızca performans yönlendirme yöntemini kullanan profiller için geçerlidir. Gerçek Kullanıcı Ölçümleri bağlantı, Azure portal üzerinden görüntülediğinizde tüm profillerden kullanılabilir.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Her profili ayrı Gerçek Kullanıcı Ölçümleri etkinleştirmem gerekir mi?

Hayır, her abonelik için yalnızca bir kez etkinleştirmeniz gerekir ve ölçülen ve raporlanan tüm gecikme bilgileri tüm profiller tarafından kullanılabilir.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Nasıl yaparım? Aboneliğim için Gerçek Kullanıcı Ölçümleri devre dışı bırakmak istiyor musunuz?

İstemci uygulamanızdan gecikme süresi ölçümlerini toplamayı ve geri göndermeyi durdurduğunuzda Gerçek Kullanıcı Ölçümleri ile ilgili tahakkuk eden ücretleri durdurabilirsiniz. Örneğin, Web sayfalarına katıştırılmış JavaScript 'i kullandığınızda, JavaScript 'i kaldırarak veya sayfa işlendiğinde çağrılmasını kapatarak bu özelliği kullanmayı durdurabilirsiniz.

Ayrıca, anahtarınızı silerek Gerçek Kullanıcı Ölçümleri kapatabilirsiniz. Anahtarı sildikten sonra, bu anahtarla Traffic Manager gönderilen ölçümler atılır.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Gerçek Kullanıcı Ölçümleri Web sayfaları dışındaki istemci uygulamalarla kullanabilir miyim?

Evet, Gerçek Kullanıcı Ölçümleri, farklı Son Kullanıcı istemcileri türü aracılığıyla toplanan verileri almak için tasarlanmıştır. Bu SSS, yeni istemci uygulamaları türleri desteklendiğinden güncelleştirilecektir.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Gerçek Kullanıcı Ölçümleri etkinleştirilmiş Web sayfası her işlendiğinde kaç ölçüm yapıldı?

Gerçek Kullanıcı Ölçümleri, belirtilen ölçüm JavaScript 'i ile kullanıldığında, her sayfa işleme, altı ölçüyle sonuçlanır. Bunlar daha sonra Traffic Manager hizmetine geri bildirilir. Bu özellik için Traffic Manager hizmetine bildirilen ölçümlerin sayısına göre ücretlendirilirsiniz. Örneğin, ölçümler çekilirken ancak bildirilmeden önce Kullanıcı web sayfamızı uzaklaştığında, bu ölçümler faturalandırma amaçlarıyla dikkate alınmaz.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Web sayfamda Gerçek Kullanıcı Ölçümleri betiği çalışmadan önce bir gecikme var mı?

Hayır, komut dosyası çağrılmadan önce programlanmış gecikme yok.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Gerçek Kullanıcı Ölçümleri yalnızca ölçmek istediğim Azure bölgeleriyle kullanabilir miyim?

Hayır, her çağrıldığında Gerçek Kullanıcı Ölçümleri betiği, hizmet tarafından belirlendiği şekilde altı Azure bölgesinin bir kümesini ölçer. Bu, farklı çağrılar arasında değişiklik yapmak ve çok fazla sayıda çağırma gerçekleştiğinde, ölçüm kapsamının farklı Azure bölgelerine yayıldığı zaman.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Belirli bir numaraya yapılan ölçümlerin sayısını sınırlayabilir miyim?

JavaScript ölçümü, Web sayfanıza katıştırılır ve ne zaman başlatılacağını ve bu denetimin ne zaman durdurulacağını kullanın. Traffic Manager hizmeti, Azure bölgelerinin bir listesi için bir istek aldığı sürece, bir dizi bölge döndürülür.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>İstemci uygulamamın Gerçek Kullanıcı Ölçümleri bir parçası olarak alınan ölçümleri görebilir miyim?

Ölçüm mantığı istemci uygulamanızdan çalıştırıldığı için, gecikme ölçümlerini görmek dahil olmak üzere neler olduğunu tam olarak denetleyebilirsiniz. Traffic Manager, aboneliğinize bağlı anahtarın altında alınan ölçümlerin toplam görünümünü raporlamaz.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Traffic Manager tarafından sunulan ölçüm betiğini değiştirebilir miyim?

Web sayfanızda nelerin gömültüğünü kontrol ederken, gecikme sürelerini doğru şekilde ölçmesini ve raporlamayacağını sağlamak için ölçüm betiğiyle ilgili herhangi bir değişiklik yapmaktan kaçınıyoruz.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Başkalarının Gerçek Kullanıcı Ölçümleri ile kullandığım anahtarı görmesi mümkün olacaktır mi?

Ölçüm betiğini bir Web sayfasına eklediğinizde başkalarının betiği ve Gerçek Kullanıcı Ölçümleri (RUM) anahtarınızı görmesi mümkün olacaktır. Ancak bu anahtarın abonelik Kimliğinizle farklı olduğunu ve yalnızca bu amaçla kullanılmak üzere Traffic Manager tarafından oluşturulduğunu bilmemiz önemlidir. RUM anahtarınızı bilmek, Azure hesap güvenliğinizi tehlikeye atabilir.

### <a name="can-others-abuse-my-rum-key"></a>Diğerleri RUM anahtarımı kötüye kullanabilir mi?

Başkalarının Azure 'a yanlış bilgi göndermek için anahtarınızı kullanması mümkün olsa da, birkaç yanlış ölçüm, aldığımız diğer ölçümlerle birlikte hesaba götürülüyoruz. Anahtarlarınızı değiştirmeniz gerekiyorsa, anahtarı, eski anahtarın atılma noktasında yeniden oluşturabilirsiniz.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Ölçüm JavaScript 'ı tüm Web sayfalarıma yerleştirmem gerekir mi?

Gerçek Kullanıcı Ölçümleri, ölçüm sayısı artdıkça daha fazla değer sağlar. Bunu söylemenin, bunu tüm Web sayfalarınıza veya birkaç seçim yapmanız gerekip gerekmediğini belirten karardır. Önerimiz, bir kullanıcının bu sayfada beş saniye veya daha fazla yerde kalması beklenen en son ziyaret ettiğiniz sayfaya yerleştirilerek başlatılacak.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Gerçek Kullanıcı Ölçümleri kullanırsam son kullanıcılarım hakkındaki bilgiler Traffic Manager tarafından tanımlanabilir mi?

Belirtilen ölçüm JavaScript 'i kullanıldığında Traffic Manager, son kullanıcının istemci IP adresi ve kullandıkları yerel DNS Çözümleyicisinin kaynak IP adresi için görünürlük sahibi olacaktır. Traffic Manager, istemci IP adresini yalnızca, ölçümleri Gönderen belirli son kullanıcıyı belirleyemeyecek şekilde kesildikten sonra kullanır.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Gerçek Kullanıcı Ölçümleri Web sayfasının yönlendirme için Traffic Manager kullanıyor olması gerekiyor mu?

Hayır, Traffic Manager kullanması gerekmez. Traffic Manager yönlendirme tarafı gerçek Kullanıcı ölçümü bölümünden ayrı olarak çalışır ve aynı Web özelliğinde olması gereken harika bir fikir olsa da, bunların olması gerekmez.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Azure bölgelerinde Gerçek Kullanıcı Ölçümleri kullanmak için herhangi bir hizmeti barındırmalıyım miyim?

Hayır, Gerçek Kullanıcı Ölçümleri çalışması için Azure 'da herhangi bir sunucu tarafı bileşeni barındırmanıza gerek yoktur. Ölçüm JavaScript tarafından indirilen tek piksellik resim ve farklı Azure bölgelerinde çalışan hizmet Azure tarafından barındırılır ve yönetilir. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Gerçek Kullanıcı Ölçümleri kullandığım zaman Azure bant genişliği kullanımı artar mi?

Önceki cevap bölümünde belirtildiği gibi, Gerçek Kullanıcı Ölçümleri sunucu tarafı bileşenleri Azure tarafından sahiplenilir ve yönetilir. Bu, Gerçek Kullanıcı Ölçümleri kullandığınız için Azure bant genişliği kullanımınız artmayacak anlamına gelir. Bu, Azure ücretlerinden herhangi bir bant genişliği kullanımı içermez. Bir Azure bölgesine yönelik gecikmeyi ölçmeniz için yalnızca tek bir piksel görüntüsünü indirerek kullanılan bant genişliğini en aza indirdik. 

## <a name="traffic-view"></a>Trafik Görünümü

### <a name="what-does-traffic-view-do"></a>Trafik Görünümü ne yapar?

Trafik Görünümü, kullanıcılarınız ve deneyiminin nasıl olduğu hakkında daha fazla bilgi anlamanıza yardımcı olan bir Traffic Manager özelliğidir. Bu, Traffic Manager tarafından alınan sorguları ve hizmetin aşağıdakileri sağlamak üzere sakladığı ağ gecikme bilgileri tablolarını kullanır:

- Kullanıcılarınızın Azure 'daki uç noktalarınıza bağlandığı bölgeler.
- Bu bölgelerden bağlanan kullanıcıların hacmi.
- Yönlendirildikleri Azure bölgeleri.
- Bu Azure bölgelerinde gecikme yaşar.

Bu bilgiler, indirmeniz için ham veriler olarak kullanılabilmesinin yanı sıra portaldaki coğrafi harita kaplamayı ve tablo görünümlerini kullanmanıza yardımcı olur.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Trafik Görünümü kullanmanın avantajlarından faydalanabilirsiniz.

Trafik Görünümü, Traffic Manager profillerinin alacağı trafiğin genel görünümünü sağlar. Özellikle, Kullanıcı temelinizin, ortalama gecikme deneyiminin ne olduğunu ve ne kadar önemli olduğunu anlamak için kullanılabilir. Daha sonra bu bilgileri, örneğin, Azure parmak izini, bu kullanıcılara daha düşük gecikme süresine sahip bir bölgeye genişleterek, örneğin, odaklanmanız gereken alanları bulmak için kullanabilirsiniz. Trafik Görünümü kullanarak türetilebildiğiniz başka bir anlayış, farklı bölgelere trafik desenlerini görmektir. bu bölgeler, bu bölgelerde stok artırma veya azaltma kararları almanıza yardımcı olabilir.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Azure izleyici aracılığıyla kullanılabilen Traffic Manager ölçümlerinden farklı Trafik Görünümü nedir?

Azure Izleyici, profilinizin ve uç noktalarının aldığı trafiği bir toplama düzeyinde anlamak için kullanılabilir. Ayrıca sistem durumu denetimi sonuçlarını açığa çıkarmak için uç noktaların sistem durumunu izlemenize olanak sağlar. Bunların ötesine gidip son kullanıcının Azure 'a bölgesel düzeyde bağlanma deneyiminizi anladığınızda, bunu elde etmek için Trafik Görünümü kullanılabilir.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Trafik Görünümü EDNS Istemci alt ağ bilgilerini kullanır mi?

Azure Traffic Manager tarafından sunulan DNS sorguları, yönlendirmenin doğruluğunu artırmak için ECS bilgilerini göz önünde bulundurun. Ancak kullanıcıların nereden bağlanıldığını gösteren veri kümesini oluştururken, Trafik Görünümü yalnızca DNS Çözümleyicisinin IP adresini kullanıyor.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Kaç gün veri Trafik Görünümü kullanır?

Trafik Görünümü, sizin tarafınızdan görüntülenmeden önce günün yedi gün öncesine ait verileri işleyerek çıktısını oluşturur. Bu bir taşıma penceresidir ve en son veriler her ziyaret edildiğinde kullanılacaktır.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Trafik Görünümü dış uç noktaları nasıl işler?

Bir Traffic Manager profilinde Azure bölgelerinin dışında barındırılan dış uç noktaları kullandığınızda, bunun gecikme özelliklerine yönelik bir ara sunucu olan bir Azure bölgesine eşlenmenizi seçebilirsiniz (performans yönlendirme yöntemini kullanıyorsanız bu aslında gereklidir). Bu Azure bölge eşlemesine sahipse, Trafik Görünümü çıktısı oluşturulurken Azure bölgesinin gecikme ölçümleri kullanılacaktır. Hiçbir Azure bölgesi belirtilmemişse, bu dış uç noktaların verilerinde gecikme bilgileri boş olur.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Aboneliğimde her profil için Trafik Görünümü etkinleştirmem gerekir mi?

Önizleme dönemi boyunca, Trafik Görünümü abonelik düzeyinde etkinleştirilmiştir. Genel kullanıma sunulmadan önce yaptığımız geliştirmelerin bir parçası olarak artık Trafik Görünümü bir profil düzeyinde etkinleştirebilirsiniz ve bu özelliğin daha ayrıntılı bir şekilde etkinleştirilmesini sağlayabilirsiniz. Varsayılan olarak, Trafik Görünümü profil için devre dışı bırakılır.

>[!NOTE]
>Önizleme sırasında bir abonelik düzeyinde Trafik Görünümü etkinleştirdiyseniz, bu aboneliğin altındaki her profil için yeniden etkinleştirmeniz gerekir.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Trafik Görünümü nasıl kapatırım?

Portalı veya REST API kullanarak herhangi bir profil için Trafik Görünümü devre dışı bırakabilirsiniz. 

### <a name="how-does-traffic-view-billing-work"></a>Trafik Görünümü Faturalandırma nasıl çalışır?

Trafik Görünümü fiyatlandırması, çıktıyı oluşturmak için kullanılan veri noktalarının sayısını temel alır. Şu anda desteklenen tek veri türü, profilinizin aldığı sorgulardır. Ayrıca, yalnızca Trafik Görünümü etkinleştirildiğinde gerçekleştirilen işleme için faturalandırılırsınız. Bu, bir ay içinde Trafik Görünümü bir zaman dilimi için bir süre etkinleştirirseniz ve diğer saatlerde devre dışı bırakırsanız, yalnızca bir özelliğin faturanızda olması halinde işlenen veri noktalarının işlendiği anlamına gelir.

## <a name="traffic-manager-endpoints"></a>Traffic Manager uç noktaları

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Birden çok abonelikteki uç noktalarla Traffic Manager kullanabilir miyim?

Azure Web Apps ile birden çok abonelikteki uç noktaların kullanılması mümkün değildir. Azure Web Apps, Web Apps birlikte kullanılan herhangi bir özel etki alanı adının yalnızca tek bir abonelik içinde kullanılmasını gerektirir. Aynı etki alanı adına sahip birden çok abonelikten Web Apps kullanmak mümkün değildir.

Diğer uç nokta türleri için birden fazla abonelikteki uç noktalarla Traffic Manager kullanmak mümkündür. Kaynak Yöneticisi, Traffic Manager profilini yapılandıran kişinin uç noktaya okuma erişimi olduğu sürece herhangi bir abonelikteki uç noktalar Traffic Manager eklenebilir. Bu izinler, [Azure Resource Manager rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/role-assignments-portal.md)kullanılarak verilebilir.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Bulut hizmeti ' hazırlama ' yuvaları ile Traffic Manager kullanabilir miyim?

Evet. Bulut hizmeti ' hazırlama ' yuvaları, Traffic Manager dış uç noktalar olarak yapılandırılabilir. Sistem durumu denetimleri hala Azure Uç Noktaları ücret üzerinden ücretlendirilir.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager IPv6 uç noktalarını destekliyor mu?

Traffic Manager şu anda IPv6 adreslenebilir ad sunucuları sağlamıyor. Ancak, IPv6 uç noktalarına bağlanan IPv6 istemcileri tarafından hala Traffic Manager kullanılabilir. İstemci DNS isteklerini Traffic Manager doğrudan yapmaz. Bunun yerine, istemci özyinelemeli bir DNS hizmeti kullanır. Yalnızca IPv6 istemcisi, istek IPv6 aracılığıyla özyinelemeli DNS hizmetine gönderilir. Ardından özyinelemeli hizmet, IPv4 kullanarak Traffic Manager ad sunucularıyla iletişim kurabilmelidir.

Traffic Manager, uç noktanın DNS adı veya IP adresi ile yanıt verir. IPv6 uç noktasını desteklemek için iki seçenek vardır. Uç noktayı ilişkili bir AAAA kaydına sahip bir DNS adı olarak ekleyebilirsiniz ve Traffic Manager durum, bu uç noktayı denetler ve sorgu yanıtında bir CNAME kayıt türü olarak döndürür. Ayrıca, bu uç noktayı IPv6 adresini kullanarak doğrudan ekleyebilirsiniz ve Traffic Manager sorgu yanıtında bir AAAA türü kaydı döndürür.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Aynı bölgede birden fazla Web uygulamasıyla Traffic Manager kullanabilir miyim?

Genellikle Traffic Manager, trafiği farklı bölgelerde dağıtılan uygulamalara yönlendirmek için kullanılır. Ancak, aynı bölgede bir uygulamanın birden fazla dağıtımı olduğunda da kullanılabilir. Traffic Manager Azure uç noktaları aynı Azure bölgesindeki birden fazla Web uygulaması uç noktasının aynı Traffic Manager profiline eklenmesine izin vermez.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Traffic Manager profillerinin Azure uç noktalarını farklı bir kaynak grubuna veya aboneliğe taşımak Nasıl yaparım? istiyor musunuz?

Bir Traffic Manager profiliyle ilişkili Azure uç noktaları, kaynak kimlikleri kullanılarak izlenir. Uç nokta olarak kullanılan bir Azure kaynağı (örneğin, genel IP, klasik bulut hizmeti, WebApp veya iç içe geçmiş bir şekilde kullanılan başka bir Traffic Manager profili) farklı bir kaynak grubuna veya aboneliğe taşındığında, kaynak KIMLIĞI değişir. Şu anda bu senaryoda, Traffic Manager profilini önce silerek ve sonra uç noktaları Profile geri ekleyerek güncelleştirmeniz gerekir.

## <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager uç nokta izleme

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Azure bölge hatalarıyla Traffic Manager dayanıklı mi?

Traffic Manager, Azure 'da yüksek oranda kullanılabilir uygulamalar tesliminin temel bir bileşenidir.
Yüksek kullanılabilirlik sağlamak için Traffic Manager, daha fazla yüksek düzeyde kullanılabilirlik ve bölgesel hatalara dayanıklı olması gerekir.

Tasarıma göre Traffic Manager bileşenleri, herhangi bir Azure bölgesinin tamamen başarısız olması halinde esnektir. Bu esnekliği tüm Traffic Manager bileşenleri için geçerlidir: DNS ad sunucuları, API, depolama katmanı ve uç nokta izleme hizmeti.

Tüm Azure bölgesinin kesintiye neden olması olası olmayan bir olayda, Traffic Manager normal şekilde çalışmaya devam etmesi beklenir. Birden çok Azure bölgesinde dağıtılan uygulamalar, trafiği uygulamasının kullanılabilir bir örneğine yönlendirmek için Traffic Manager güvenebilirler.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Kaynak grubu konumu seçimi Traffic Manager nasıl etkiler?

Traffic Manager tek bir küresel hizmettir. Bölgesel değildir. Kaynak grubu konumunun seçimi, bu kaynak grubunda dağıtılan Traffic Manager profillerinin herhangi bir farklılık yapmaz.

Azure Resource Manager, tüm kaynak gruplarının bu kaynak grubunda dağıtılan kaynakların varsayılan konumunu belirleyen bir konum belirtmesini gerektirir. Bir Traffic Manager profili oluşturduğunuzda, bu bir kaynak grubunda oluşturulur. Tüm Traffic Manager profilleri konum olarak **Global** kullanır ve kaynak grubu varsayılanını geçersiz kılar.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Nasıl yaparım? her uç noktanın geçerli sistem durumunu mı belirlemelisiniz?

Her uç noktanın geçerli izleme durumu, genel profilin yanı sıra Azure portal görüntülenir. Bu bilgiler ayrıca trafik Izleyicisi [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell cmdlet 'leri](https://docs.microsoft.com/powershell/module/az.trafficmanager)ve [platformlar arası Azure CLI](../cli-install-nodejs.md)aracılığıyla da kullanılabilir.

Azure Izleyici 'yi, uç noktalarınızın durumunu izlemek ve bunların görsel bir gösterimini görmek için de kullanabilirsiniz. Azure Izleyici 'yi kullanma hakkında daha fazla bilgi için bkz. [Azure izleme belgeleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>HTTPS uç noktalarını izleyebilir miyim?

Evet. Traffic Manager, HTTPS üzerinde yoklama destekler. İzleme yapılandırmasında protokol olarak **https** yapılandırın.

Traffic Manager aşağıdakiler dahil olmak üzere herhangi bir sertifika doğrulaması sağlayamaz:

* Sunucu tarafı sertifikalar doğrulanmadı
* SNı sunucu tarafı sertifikalar doğrulanmadı
* İstemci sertifikaları desteklenmez

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Uç nokta eklerken bir IP adresi veya DNS adı kullanıyorum mi?

Traffic Manager, bir DNS adı olarak bir IPv4 adresi ve bir IPv6 adresi olarak başvurmak için üç yol kullanarak uç noktaların eklenmesini destekler. Uç nokta bir IPv4 veya IPv6 adresi olarak eklenirse, sorgu yanıtı sırasıyla A veya AAAA kayıt türü olacaktır. Uç nokta bir DNS adı olarak eklendiyse, sorgu yanıtı CNAME kayıt türü olacaktır. Uç noktaların IPv4 veya IPv6 adresi olarak eklenmesine yalnızca uç nokta **External**türünde olduğunda izin verilir.
Tüm yönlendirme yöntemleri ve izleme ayarları, üç uç nokta adresleme türü tarafından desteklenir.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Uç nokta eklerken ne tür IP adresleri kullanabilirim?

Traffic Manager, uç noktaları belirtmek için IPv4 veya IPv6 adresleri kullanmanıza izin verir. Aşağıda listelenen bazı kısıtlamalar vardır:

- Ayrılmış özel IP adresi alanlarına karşılık gelen adreslere izin verilmez. Bu adresler, RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 ve RFC 5771 ' de çağrılan örnekleri içerir
- Adres herhangi bir bağlantı noktası numarası içermemelidir (profil yapılandırma ayarlarında kullanılacak bağlantı noktalarını belirtebilirsiniz)
- Aynı profilde iki uç nokta aynı hedef IP adresine sahip olamaz

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Tek bir profil içinde farklı uç nokta adresleme türlerini kullanabilir miyim?

Hayır, Traffic Manager, IPv4 ve IPv6 adresleme türlerini karıştırabileceğiniz çok değerli yönlendirme türüne sahip bir profil olması dışında, bir profil içinde uç nokta adresleme türlerini karıştırtırabilmeniz için izin vermez

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Gelen sorgunun kayıt türü, uç noktaların adresleme türüyle ilişkili kayıt türünden farklı olduğunda ne olur?

Bir profille bir sorgu alındığında Traffic Manager önce, belirtilen yönlendirme yöntemine ve uç noktaların sistem durumuna göre döndürülmesi gereken bitiş noktasını bulur. Ardından, gelen sorguda istenen kayıt türüne ve aşağıdaki tabloya göre bir yanıt döndürmeden önce uç noktayla ilişkili kayıt türüne bakar.

Çoklu değer dışında herhangi bir yönlendirme yöntemine sahip profiller için:

|Gelen sorgu isteği|    Uç nokta türü|  Yanıt sağlanmış|
|--|--|--|
|KAYDEDILMEMIŞ |  A/AAAA/CNAME |  Hedef uç nokta| 
|A |    A/CNAME | Hedef uç nokta|
|A |    AAAA |  NODATA |
|AAAA | AAAA/CNAME |  Hedef uç nokta|
|AAAA | A | NODATA |
|CNAME |    CNAME | Hedef uç nokta|
|CNAME  |A/AAAA | NODATA |
|

Yönlendirme yöntemi olan profillerin çoklu değer olarak ayarlandığı profiller için:

|Gelen sorgu isteği|    Uç nokta türü | Yanıt sağlanmış|
|--|--|--|
|KAYDEDILMEMIŞ |  A ve AAAA karışımı | Hedef uç noktalar|
|A |    A ve AAAA karışımı | Yalnızca A türünde hedef uç noktalar|
|AAAA   |A ve AAAA karışımı|     Yalnızca AAAA türünde hedef uç noktalar|
|CNAME |    A ve AAAA karışımı | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Bir profili, iç içe geçmiş bir profilde IPv4/IPv6 ile ilgili belirtilen uç noktalarla kullanabilir miyim?

Evet, birden çok değer türündeki bir profilin iç içe geçmiş bir profil kümesindeki bir üst profil olması özel durumla birlikte olabilirsiniz.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Traffic Manager profilimde bir Web uygulaması uç noktasını durdurdum, ancak yeniden başlattıktan sonra bile hiç trafik almadım. Bunu nasıl giderebilirim?

Bir Azure Web uygulaması uç noktası durdurulduğunda Traffic Manager sistem durumunu denetlemeyi durduruyor ve sistem durumu denetimlerini yalnızca uç noktanın yeniden başlatıldığını algıladıktan sonra yeniden başlatır. Bu gecikmeyi engellemek için, uç noktayı yeniden başlattıktan sonra bu uç noktayı Traffic Manager profilinde devre dışı bırakın ve yeniden etkinleştirin.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Uygulamamın HTTP veya HTTPS desteği olmasa bile Traffic Manager kullanabilir miyim?

Evet. İzleme protokolü olarak TCP belirtebilirsiniz ve Traffic Manager bir TCP bağlantısı başlatabilir ve uç noktadan bir yanıt bekleyebilir. Uç nokta bağlantı isteğine bağlantı kurmak için bir Yanıt ile yanıt verir, zaman aşımı süresi içinde, bu uç nokta sağlıklı olarak işaretlenir.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>TCP izlemesi kullanılırken uç noktadan hangi belirli yanıtlar gerekir?

TCP izleme kullanıldığında Traffic Manager, belirtilen bağlantı noktasında uç noktaya bir SYN isteği göndererek üç yönlü bir TCP el sıkışması başlatır. Ardından, uç noktadan bir süre için bir SYN-ACK yanıtı bekler (zaman aşımı ayarlarında belirtilir).

- İzleme ayarlarında belirtilen zaman aşımı süresi içinde bir SYN-ACK yanıtı alınmışsa, bu uç nokta sağlıklı olarak değerlendirilir. Bir FIN veya FIN-ACK, düzenli olarak bir yuva sonlandırdığında Traffic Manager beklenen yanıttır.
- Belirtilen zaman aşımından sonra bir SYN-ACK yanıtı alınmışsa Traffic Manager bağlantıyı sıfırlamak için bir RST ile yanıt verir.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Kullanıcılarınızın sağlıksız bir uç noktadan uzakta mi ne kadar hızlı hareket Traffic Manager?

Traffic Manager, Traffic Manager profilinizin yük devretme davranışını şu şekilde denetlemenize yardımcı olabilecek birden çok ayar sağlar:

- Yoklama aralığını 10 saniye olarak ayarlayarak Traffic Manager bitiş noktalarını daha sık yoklamalarını belirtebilirsiniz. Bu, sağlıksız durumda olan herhangi bir uç noktanın mümkün olan en kısa sürede algılanmamasını sağlar. 
- bir sistem durumu denetimi isteğinin zaman aşımına uğramadan önce ne kadar bekleneceğini belirtebilirsiniz (minimum zaman aşımı değeri 5 sn olur).
- uç nokta sağlıksız olarak işaretlenmeden önce kaç tane başarısızlık meydana gelebilir belirtebilirsiniz. Bu değer 0 olarak düşük olabilir. Bu durumda, ilk durum denetiminde başarısız olur almaz bitiş noktası sağlıksız olarak işaretlenir. Ancak, toleranslı başarısızlık sayısı için en düşük değer olan 0 ' ın kullanılması, yoklama sırasında oluşabilecek geçici sorunlar nedeniyle, uç noktaların dönüşten çıkmasına neden olabilir.
- DNS yanıtının 0 ' dan düşük olması için yaşam süresi (TTL) belirtebilirsiniz. Bunun yapılması DNS çözümleyiclarının yanıtı önbelleğe alamaz ve her yeni sorgu Traffic Manager sahip olduğu en güncel sağlık bilgilerini içeren bir yanıt alır.

Bu ayarları kullanarak Traffic Manager, bir uç nokta sağlıksız olduktan ve ilgili profilde bir DNS sorgusu yapıldıktan sonra 10 saniye içinde yük devretme sağlayabilir.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Bir profildeki farklı uç noktalar için farklı izleme ayarlarını nasıl belirtirim?

Traffic Manager izleme ayarları her profil düzeyinde yapılır. Yalnızca bir uç nokta için farklı bir izleme ayarı kullanmanız gerekiyorsa, bu uç noktanın izleme ayarları üst profilden farklı olan [iç içe geçmiş bir profil](traffic-manager-nested-profiles.md) olarak bulunması yapılabilir.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Uç noktalarıma Traffic Manager sistem durumu denetimlerine HTTP üstbilgileri nasıl atayabilirim?

Traffic Manager, HTTP (S) sistem durumu denetimlerinde, uç noktalarınız için başlattığı özel üst bilgileri belirtmenize olanak tanır. Özel bir üst bilgi belirtmek istiyorsanız, bunu profil düzeyinde yapabilirsiniz (tüm uç noktalara uygulanabilir) veya bunu uç nokta düzeyinde belirtebilirsiniz. Her iki düzeyde de bir üst bilgi tanımlanmışsa, uç nokta düzeyinde belirtilen bir tane, profil düzeyini geçersiz kılar.
Bunun yaygın bir kullanım durumu, Traffic Manager isteklerinin çok kiracılı bir ortamda barındırılan bir uç noktaya doğru şekilde yönlendirilebilmesi için konak üstbilgilerini belirtmektir. Bunun başka bir kullanım durumu, bir uç noktanın HTTP (S) istek günlüklerinden Traffic Manager isteklerini belirlemektir

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Uç nokta sistem durumu denetimleri hangi konak üstbilgisini kullanır?

Özel bir ana bilgisayar üst bilgisi ayarı sağlanmazsa, Traffic Manager tarafından kullanılan ana bilgisayar üstbilgisi, varsa, profilde yapılandırılan uç nokta hedefinin DNS adıdır.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Sistem durumu denetimlerinin kaynağı olan IP adresleri nelerdir?

Traffic Manager sistem durumu denetimlerinin kaynağı olan IP adreslerini listeleyen JSON dosyasını görüntülemek için [buraya](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) tıklayın. Bu IP adreslerinden gelen bağlantılara sistem durumunu kontrol etmek üzere uç noktalarda izin verildiğinden emin olmak için JSON dosyasında listelenen IP 'Leri gözden geçirin.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Uç noktadan kaç tane sistem durumu denetlemesi Traffic Manager bekleyebilir?

Uç noktanıza ulaşan Traffic Manager durum denetimi sayısı aşağıdakilere bağlıdır:

- İzleme aralığı (daha küçük Aralık) için ayarladığınız değer, belirli bir süre içinde uç noktanıza daha fazla istek giriş anlamına gelir.
- durum denetimlerinin gerçekleştiği konum sayısı (bu denetimlerin önceki SSS bölümünde listelendiği IP adresleri).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Uç noktalarımı bir veya daha fazla kaldığında nasıl bildirim alabilirim?

Traffic Manager tarafından belirtilen ölçülerden biri, bir profildeki uç noktaların sistem durumudur. Bunu bir profil içindeki tüm uç noktaların toplamı olarak görebilirsiniz (örneğin, uç noktalarınızın %75 ' u) veya her bir uç nokta düzeyinde. Traffic Manager ölçümler Azure Izleyici aracılığıyla sunulur ve uç noktanızın sistem durumunda bir değişiklik olduğunda bildirimleri almak için [uyarı yeteneklerini](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) kullanabilirsiniz. Daha ayrıntılı bilgi için bkz. [Traffic Manager ölçümleri ve uyarıları](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>İç içe geçmiş profiller Traffic Manager

### <a name="how-do-i-configure-nested-profiles"></a>İç içe geçmiş profilleri yapılandırmak Nasıl yaparım??

İç içe Traffic Manager profilleri hem Azure Resource Manager hem de klasik Azure REST API 'Leri, Azure PowerShell cmdlet 'ler ve platformlar arası Azure CLı komutları kullanılarak yapılandırılabilir. Bunlar da yeni Azure portal aracılığıyla desteklenir.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Trafik Yöneticisi kaç tane iç içe geçme katmansın destekler?

Profilleri en fazla 10 düzey derinlikte iç içe geçirebilirsiniz. ' Döngüler ' kullanımına izin verilmez.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Aynı Traffic Manager profilinde, iç içe geçmiş alt profillerle diğer uç nokta türlerini karışık yapabilir miyim?

Evet. Bir profil içindeki farklı türlerin uç noktalarını nasıl birleştirebileceğiniz konusunda bir kısıtlama yoktur.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Fatura modeli, Iç Içe geçmiş profiller için nasıl uygulanır?

İç içe geçmiş profillerin kullanılması için negatif bir fiyatlandırma etkisi yoktur.

Traffic Manager faturalandırma iki bileşene sahiptir: Endpoint durum denetimleri ve milyonlarca DNS sorgusu

* Uç nokta sistem durumu denetimleri: bir üst profilde bitiş noktası olarak yapılandırıldığında bir alt profil için ücret alınmaz. Alt profildeki uç noktaların izlenmesi, her zamanki şekilde faturalandırılır.
* DNS sorguları: her sorgu yalnızca bir kez sayılır. Alt profilden bir uç nokta döndüren üst profile yönelik bir sorgu, yalnızca üst profile karşı sayılır.

Tüm ayrıntılar için [Traffic Manager fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/traffic-manager/)bakın.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>İç içe geçmiş profiller için bir performans etkisi var mı?

Hayır. İç içe geçmiş profiller kullanılırken performans etkisi yoktur.

Traffic Manager ad sunucuları her DNS sorgusunu işlerken profil hiyerarşisinde dahili olarak çapraz geçiş yapar. Üst profile yönelik bir DNS sorgusu, bir alt profilden gelen uç noktayla bir DNS yanıtı alabilir. Tek bir CNAME kaydı veya iç içe geçmiş profiller kullandığınızda kullanılır. Hiyerarşideki her profil için bir CNAME kaydı oluşturmaya gerek yoktur.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Traffic Manager bir üst profilde iç içe geçmiş bir uç noktanın durumunu nasıl hesaplamıştır?

Üst profil, alt öğe üzerinde doğrudan bir sistem durumu denetimi gerçekleştirmez. Bunun yerine, alt profilin genel durumunu hesaplamak için alt profilin uç noktalarının sistem durumu kullanılır. Bu bilgiler, iç içe geçmiş uç noktanın sistem durumunu belirlemede iç içe geçmiş profil hiyerarşisini yayIr. Üst profil, trafiğin alt öğeye yönlendirilip yönlendirilmeyeceğini anlamak için bu toplanmış durumu kullanır.

Aşağıdaki tabloda, iç içe geçmiş bir uç nokta için Traffic Manager sistem durumu denetimlerinin davranışı açıklanmaktadır.

| Alt profil Izleme durumu | Üst uç nokta Izleyici durumu | Notlar |
| --- | --- | --- |
| Devre dışı. Alt profil devre dışı bırakıldı. |Durdurulan |Üst uç nokta durumu durduruldu, devre dışı değil. Devre dışı durumu, üst profildeki uç noktayı devre dışı bırakmış olduğunu belirtecek şekilde ayrılmıştır. |
| Düzey. En az bir alt profil uç noktası düşürülmüş bir durumda. |Çevrimiçi: alt profildeki çevrimiçi uç noktaların sayısı en az MinChildEndpoints değeridir.<BR>CheckingEndpoint: alt profildeki çevrimiçi ve CheckingEndpoint uç noktalarının sayısı en az MinChildEndpoints değeridir.<BR>Düşürülmüş: Aksi durumda. |Trafik, bir durum CheckingEndpoint uç noktasına yönlendirilir. MinChildEndpoints çok yüksek ayarlandıysa, uç nokta her zaman düşürülmüş olur. |
| Çevrimiç. En az bir alt profil uç noktası çevrimiçi bir durumdur. Düşürülmüş durumunda uç nokta yok. |Yukarıdaki bölümüne bakın. | |
| CheckingEndpoints. En az bir alt profil uç noktası ' CheckingEndpoint '. Uç nokta ' çevrimiçi ' veya ' düşürülmüş ' yok |Yukarıdaki gibi. | |
| Olmadan. Tüm alt profil uç noktaları devre dışı veya durdurulmuş ya da bu profilde uç nokta yok. |Durdurulan | |

## <a name="next-steps"></a>Sonraki adımlar:

- Traffic Manager [uç nokta izleme ve otomatik yük devretme](../traffic-manager/traffic-manager-monitoring.md)hakkında daha fazla bilgi edinin.
- Traffic Manager [trafik yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.
