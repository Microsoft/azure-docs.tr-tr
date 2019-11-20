---
title: Azure Traffic Manager trafik yönlendirme yöntemleri
description: Bu makaleler Traffic Manager tarafından kullanılan farklı trafik yönlendirme yöntemlerini anlamanıza yardımcı olur
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 06ce7fb5d18920be6f71821b034dc13061c60032
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051453"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager yönlendirme yöntemleri

Azure Traffic Manager, ağ trafiğinin çeşitli hizmet uç noktalarına nasıl yönlendirildiğine yönelik altı trafik yönlendirme yöntemini destekler. Her profil için, Traffic Manager onunla ilişkili trafik yönlendirme yöntemini aldığı her DNS sorgusuyla uygular. Trafik-yönlendirme yöntemi, DNS yanıtında hangi uç noktanın döndürüleceğini belirler.

Aşağıdaki trafik yönlendirme yöntemleri Traffic Manager ' de kullanılabilir:

* **[Öncelik](#priority):** Tüm trafik için bir birincil hizmet uç noktası kullanmak istediğinizde **Öncelik** ' i seçin ve birincil veya yedekleme uç noktalarının kullanılamadığı durumlarda yedeklemeler sağlayın.
* **[Ağırlıklı](#weighted):** Trafiği, istediğiniz bir uç nokta genelinde eşit veya ağırlığa göre dağıtmak istediğinizde **ağırlıklı** ' ı seçin.
* **[Performans](#performance):** Farklı coğrafi konumlarda uç noktalarınız olduğunda ve son kullanıcıların "en yakın" uç noktasını en düşük ağ gecikmesi açısından kullanmasını istiyorsanız **performans** ' ı seçin.
* **[Coğrafi](#geographic):** Kullanıcılar DNS sorgusunun kaynaklandığı coğrafi konuma göre belirli uç noktalara (Azure, dış veya Iç Içe) yönlendirilmek için **coğrafi** ' ı seçin. Bu, müşterileri bir kullanıcının coğrafi bölgesini bilmenin ve bunları temel alarak yönlendiren senaryolara olanak tanımak için Traffic Manager güçler. Örnek olarak, veri egemenlik mantarihlerle, içerik & kullanıcı deneyiminin yerelleştirilmesi ve farklı bölgelerden gelen trafiğin ölçülmesi verilebilir.
* **[Çoklu değer](#multivalue):** Yalnızca bitiş noktaları olarak IPv4/IPv6 adreslerine sahip olan Traffic Manager profilleri için **Çoklu değer** seçeneğini belirleyin. Bu profil için bir sorgu alındığında, sağlıklı tüm uç noktalar döndürülür.
* **[Alt ağ](#subnet):** Son Kullanıcı IP adresi aralıklarını bir Traffic Manager profili içindeki belirli bir uç noktaya eşlemek için **alt ağ** trafiği-yönlendirme yöntemini seçin. Bir istek alındığında döndürülen uç nokta, bu isteğin kaynak IP adresi için eşlenmiş bir değer olacaktır. 


Tüm Traffic Manager profiller, uç nokta durumunu ve otomatik uç nokta yük devretmesini izlemeyi içerir. Daha fazla bilgi için bkz. [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md). Tek bir Traffic Manager profili yalnızca bir trafik yönlendirme yöntemi kullanabilir. Profiliniz için istediğiniz zaman farklı bir trafik yönlendirme yöntemi seçebilirsiniz. Değişiklikler bir dakika içinde uygulanır ve kapalı kalma süresi olmaz. Trafik yönlendirme yöntemleri, iç içe geçmiş Traffic Manager profilleri kullanılarak birleştirilebilir. İç içe geçme, daha büyük, karmaşık uygulamaların ihtiyaçlarını karşılayan gelişmiş ve esnek trafik yönlendirme yapılandırmalarına izin verebilir. Daha fazla bilgi için bkz. [iç içe Traffic Manager profilleri](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Öncelik trafiği-yönlendirme yöntemi

Genellikle bir kuruluş, birincil hizmetlerinin kapanması olasılığına karşı bir veya daha fazla yedekleme hizmeti dağıtarak Hizmetleri için güvenilirlik sağlamak istemektedir. ' Priority ' trafik-yönlendirme yöntemi, Azure müşterilerinin bu yük devretme modelini kolayca uygulamasına olanak tanır.

![Azure Traffic Manager ' Priority ' trafiği yönlendirme yöntemi](media/traffic-manager-routing-methods/priority.png)

Traffic Manager profili, hizmet uç noktalarının öncelikli bir listesini içerir. Varsayılan olarak, Traffic Manager tüm trafiği birincil (en yüksek öncelikli) uç noktaya gönderir. Birincil uç nokta kullanılabilir değilse, trafiği ikinci uç noktaya yönlendirir Traffic Manager. Hem birincil hem de ikincil uç noktaları kullanılabilir değilse, trafik üçüncü olarak gider ve bu şekilde devam eder. Uç noktanın kullanılabilirliği, yapılandırılan durumu (etkin veya devre dışı) ve devam eden uç nokta izlemeyi temel alır.

### <a name="configuring-endpoints"></a>Uç noktaları yapılandırma

Azure Resource Manager, her bitiş noktası için ' Priority ' özelliğini kullanarak uç nokta önceliğini açık olarak yapılandırırsınız. Bu özellik 1 ile 1000 arasında bir değerdir. Daha düşük değerler daha yüksek bir öncelik temsil eder. Uç noktalar öncelik değerlerini paylaşamaz. Özelliği ayarlama isteğe bağlıdır. Atlanırsa, uç nokta sırasına göre varsayılan bir öncelik kullanılır.

## <a name = "weighted"></a>Ağırlıklı trafik-yönlendirme yöntemi
' Ağırlıklı ' trafik-yönlendirme yöntemi, trafiği eşit bir şekilde dağıtmanıza veya önceden tanımlanmış bir ağırlığa kullanmanıza olanak sağlar.

![Azure Traffic Manager ' ağırlıklı ' trafik-yönlendirme yöntemi](media/traffic-manager-routing-methods/weighted.png)

Ağırlıklı trafik-yönlendirme yönteminde, Traffic Manager profili yapılandırmasındaki her bir uç noktaya bir ağırlık atarsınız. Ağırlık 1-1000 arasında bir tamsayıdır. Bu parametre isteğe bağlıdır. Atlanırsa, trafik yöneticileri varsayılan olarak ' 1 ' ağırlığını kullanır. Daha yüksek ağırlık, öncelik daha yüksektir.

Alınan her DNS sorgusu için, Traffic Manager rastgele kullanılabilir bir uç nokta seçer. Uç nokta seçme olasılığı, kullanılabilir tüm uç noktalara atanan ağırlıkları temel alır. Tüm uç noktalarında aynı ağırlığı kullanmak, hatta trafik dağıtımına neden olur. Belirli uç noktalarda daha yüksek veya daha düşük kalınlıklar kullanmak, bu uç noktaların DNS yanıtlarına daha fazla veya daha az sıklıkta döndürülmesine neden olur.

Ağırlıklı yöntem bazı faydalı senaryolara izin verebilir:

* Aşamalı uygulama yükseltme: Yeni bir uç noktaya yönlendirmek için trafik yüzdesi ayırın ve zaman içinde trafiği zaman içinde% 100 olarak artırın.
* Azure 'a uygulama geçişi: Hem Azure hem de dış uç noktalarla bir profil oluşturun. Yeni uç noktaları tercih etmek için uç noktaların kalınlığını ayarlayın.
* Ek kapasite için bulut patlaması: Şirket içi bir dağıtımı Traffic Manager bir profilin arkasına yerleştirerek buluta hızlıca genişletin. Bulutta fazladan kapasiteye ihtiyacınız olduğunda, daha fazla uç nokta ekleyebilir veya etkinleştirebilir ve her bir uç noktaya giden trafik bölümünün ne olduğunu belirtebilirsiniz.

Azure portal kullanmanın yanı sıra, Azure PowerShell, CLı ve REST API 'Leri kullanarak ağırlıkları yapılandırabilirsiniz.

DNS yanıtlarının istemciler tarafından ve istemcilerin DNS adlarını çözümlemek için kullandığı özyinelemeli DNS sunucuları tarafından önbelleğe alınacağını anlamak önemlidir. Bu önbelleğe alma, ağırlıklı trafik dağıtımlarını etkileyebilir. İstemci sayısı ve özyinelemeli DNS sunucuları büyükse, trafik dağıtımı beklendiği gibi çalışıyor demektir. Ancak, istemci sayısı veya özyinelemeli DNS sunucuları küçük olduğunda, önbelleğe alma işlemi trafik dağıtımını önemli ölçüde eğebilir.

Yaygın kullanım örnekleri şunlardır:

* Geliştirme ve test ortamları
* Uygulamadan uygulamaya iletişimler
* Ortak bir özyinelemeli DNS altyapısını (örneğin, bir ara sunucu aracılığıyla bağlanan şirket çalışanları) paylaşan bir dar kullanıcı tabanına hedeflenmiş uygulamalar

Bu DNS önbelleğe alma etkileri yalnızca Azure Traffic Manager değil, DNS tabanlı tüm trafik yönlendirme sistemlerinde ortaktır. Bazı durumlarda, DNS önbelleğini açıkça temizlemek geçici bir çözüm sağlayabilir. Diğer durumlarda, alternatif bir trafik yönlendirme yöntemi daha uygun olabilir.

## <a name = "performance"></a>Performans trafiği-yönlendirme yöntemi

Uç noktaların dünya genelinde iki veya daha fazla konumda dağıtımı, trafiği ' en yakın ' olan konuma yönlendirerek birçok uygulamanın yanıt hızını iyileştirebilirler. ' Performance ' trafik-yönlendirme yöntemi bu yeteneği sağlar.

![Azure Traffic Manager ' Performance ' trafiği-yönlendirme yöntemi](media/traffic-manager-routing-methods/performance.png)

' En yakın ' uç noktası coğrafi mesafede ölçülen kadar yakın değildir. Bunun yerine, ' Performance ' trafik-yönlendirme yöntemi ağ gecikmesini ölçerek en yakın uç noktayı belirler. Traffic Manager, IP adresi aralıkları ve her Azure veri merkezi arasında gidiş dönüş süresini izlemek için bir Internet gecikme tablosu saklar.

Traffic Manager, Internet gecikme süresi tablosundaki gelen DNS isteğinin kaynak IP adresini arar. Traffic Manager, bu IP adresi aralığı için en düşük gecikme süresine sahip Azure veri merkezinde kullanılabilir bir uç nokta seçer ve DNS yanıtında bu uç noktayı döndürür.

[Traffic Manager nasıl çalıştığı konusunda](traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager doğrudan istemcilerden DNS sorguları almaz. Bunun yerine, DNS sorguları istemcilerin kullanmak üzere yapılandırıldığı özyinelemeli DNS hizmetinden gelir. Bu nedenle, ' en yakın ' uç noktasını belirlemede kullanılan IP adresi istemcinin IP adresidir, ancak özyinelemeli DNS hizmetinin IP adresidir. Uygulamada, bu IP adresi istemci için iyi bir ara sunucu.


Traffic Manager, Internet gecikme tablosunu genel Internet ve yeni Azure bölgelerindeki değişikliklere göre hesaba göre düzenli olarak güncelleştirir. Bununla birlikte, uygulama performansı, Internet üzerinde yükün gerçek zamanlı farklılıklara göre farklılık gösterir. Performans trafiği-yönlendirme, belirli bir hizmet uç noktasındaki yükü izlemez. Ancak, bir uç nokta kullanılamaz hale gelirse Traffic Manager DNS sorgu yanıtlarına eklemez.


Dikkat edilecek noktalar:

* Profiliniz aynı Azure bölgesinde birden fazla uç nokta içeriyorsa, Traffic Manager trafiği bu bölgedeki kullanılabilir uç noktalar arasında eşit olarak dağıtır. Bir bölge içinde farklı bir trafik dağıtımını tercih ediyorsanız, [iç içe geçmiş Traffic Manager profillerini](traffic-manager-nested-profiles.md)kullanabilirsiniz.
* En yakın Azure bölgesindeki tüm etkin uç noktalar düşürülirse Traffic Manager trafiği bir sonraki en yakın Azure bölgesindeki uç noktalara taşıdıkça. Tercih edilen bir yük devretme sırası tanımlamak istiyorsanız, [iç içe geçmiş Traffic Manager profillerini](traffic-manager-nested-profiles.md)kullanın.
* Dış uç noktalar veya iç içe geçmiş uç noktalarla performans trafiği yönlendirme yöntemini kullanırken, bu uç noktaların konumunu belirtmeniz gerekir. Dağıtımınıza en yakın Azure bölgesini seçin. Bu konumlar Internet gecikme süresi tablosu tarafından desteklenen değerlerdir.
* Uç noktayı seçen algoritma belirleyici olur. Aynı istemciden yinelenen DNS sorguları aynı uç noktaya yönlendirilir. Genellikle, istemciler seyahat ederken farklı özyinelemeli DNS sunucuları kullanır. İstemci farklı bir uç noktaya yönlendirilebilir. Yönlendirme, Internet gecikme tablosuna ait güncelleştirmelerden da etkilenebilir. Bu nedenle, performans trafiği-yönlendirme yöntemi, bir istemcinin her zaman aynı uç noktaya yönlendirildiğini garanti etmez.
* Internet gecikme süresi tablosu değiştiğinde, bazı istemcilerin farklı bir uç noktaya yönlendirildiğine dikkat edebilirsiniz. Bu yönlendirme değişikliği, geçerli gecikme verilerine göre daha doğru. Internet sürekli geliştikçe performans trafiği yönlendirmenin doğruluğunu sürdürmek için bu güncelleştirmeler önemlidir.

## <a name = "geographic"></a>Coğrafi trafik-yönlendirme yöntemi

Traffic Manager profiller, kullanıcıların DNS sorgusunun kaynaklandığı coğrafi konuma göre belirli uç noktalara (Azure, dış veya Iç Içe) yönlendirilmeleri için coğrafi yönlendirme yöntemini kullanacak şekilde yapılandırılabilir. Bu, müşterileri bir kullanıcının coğrafi bölgesini bilmenin ve bunları temel alarak yönlendiren senaryolara olanak tanımak için Traffic Manager güçler. Örnek olarak, veri egemenlik mantarihlerle, içerik & kullanıcı deneyiminin yerelleştirilmesi ve farklı bölgelerden gelen trafiğin ölçülmesi verilebilir.
Bir profil coğrafi yönlendirme için yapılandırıldığında, bu profille ilişkilendirilen her uç noktanın kendisine atanmış bir coğrafi bölge kümesi olması gerekir. Coğrafi bölge, aşağıdaki ayrıntı düzeyi düzeylerinde olabilir 
- Dünya – herhangi bir bölge
- Bölgesel gruplandırma: Örneğin, Afrika, Orta Doğu, Avustralya/Pasifik vb. 
- Ülke/bölge: Örneğin, Irlanda, Peru, Hong Kong ÖİB vb. 
- Eyalet/bölge – örneğin, ABD-California, Avustralya-Queensland, Kanada-Alberta vb. (Note: Bu ayrıntı düzeyi düzeyi yalnızca Avustralya, Kanada ve ABD 'deki eyalet/eyalet için desteklenir).

Bir bölge veya bölge kümesi bir uç noktaya atandığında, bu bölgelerden gelen istekler yalnızca o uç noktaya yönlendirilir. Traffic Manager, kullanıcının sorgulama yaptığı bölgeyi belirlemekte DNS sorgusunun kaynak IP adresini kullanır – genellikle bu, Kullanıcı adına sorgu yapan yerel DNS Çözümleyicisinin IP adresidir.  

![Azure Traffic Manager ' coğrafi ' trafik-yönlendirme yöntemi](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager, DNS sorgusunun kaynak IP adresini okur ve hangi coğrafi bölgenin kaynaklandığı karar verir. Daha sonra bu coğrafi bölgenin eşlendiği bir uç nokta olup olmadığını görmek için görünür. Bu arama en düşük ayrıntı düzeyi düzeyinde (desteklenen eyalet/eyalet, başka bir deyişle ülke/bölge düzeyinde) başlar ve **dünyanın**en yüksek düzeyine kadar tüm şekilde çalışır. Bu çapraz geçiş kullanılarak bulunan ilk eşleşme, sorgu yanıtında döndürülecek uç nokta olarak belirtilmiştir. Iç Içe geçmiş bir tür uç noktasıyla eşleştirilirken, bu alt profil içindeki bir uç nokta, yönlendirme yöntemine göre döndürülür. Aşağıdaki noktaları bu davranış için geçerlidir:

- Bir coğrafi bölge, yönlendirme türü coğrafi yönlendirme olduğunda bir Traffic Manager profilindeki bir uç nokta ile eşlenebilir. Bu, kullanıcıların yönlendirilmesini belirleyici hale gelmesini ve müşterilerin, belirsiz coğrafi sınır gerektiren senaryoları etkinleştirebilmesini sağlar.
- Bir kullanıcının bölgesi iki farklı bitiş noktası ' coğrafi eşleme altına gelirse Traffic Manager en düşük ayrıntı düzeyi olan uç noktayı seçer ve bu bölgeden diğer uç noktaya yönlendirme isteklerini göz önünde bulundurmaz. Örneğin, iki uç nokta (Endpoint1 ve Endpoint2) olan coğrafi yönlendirme türü profilini göz önünde bulundurun. Endpoint1, Irlanda 'dan trafik alacak şekilde yapılandırılmıştır ve Endpoint2, Avrupa 'dan gelen trafiği alacak şekilde yapılandırılır. Bir istek Irlanda 'dan kaynaklanıyorsa, her zaman Endpoint1 'a yönlendirilir.
- Bir bölge yalnızca bir uç nokta ile eşleştirilemediğinden, Traffic Manager uç noktanın sağlıklı olup olmamasına bakılmaksızın onu döndürür.

    >[!IMPORTANT]
    >Coğrafi yönlendirme yöntemini kullanan müşterilerin, her biri içinde en az iki uç nokta içeren alt profilleri olan Iç Içe geçmiş tür uç noktaları ile ilişkilendirilebilmesi önemle önerilir.
- Bir uç nokta eşleşmesi bulunursa ve uç nokta **durdurulmuş** durumdaysa, Traffic Manager bir NoData yanıtı döndürür. Bu durumda, coğrafi bölge hiyerarşisinde daha fazla arama yapılmaz. Bu davranış, alt profil **durdurulmuş** veya **devre dışı** durumdayken iç içe geçmiş uç nokta türleri için de geçerlidir.
- Bir uç nokta **devre dışı** durumunu görüntülüyorsa, bölge eşleştirme işlemine dahil değildir. Bu davranış, uç nokta **devre dışı** durumundayken iç içe geçmiş uç nokta türleri için de geçerlidir.
- Bir sorgu, bu profilde eşlenmesiz bir coğrafi bölgeden geliyorsa, Traffic Manager NODATA yanıtı döndürür. Bu nedenle, müşterilerin bir uç nokta ile coğrafi yönlendirme kullanması önemle önerilir. Bu, alt profil içinde en az iki uç nokta ile Iç Içe, **Dünya çapında** atanmış bölge Ile iç içe. Bu Ayrıca, bir bölgeyle eşlenmemesi gereken tüm IP adreslerinin işlenmesini de sağlar.

[Traffic Manager nasıl çalıştığı konusunda](traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager doğrudan istemcilerden DNS sorguları almaz. Bunun yerine, DNS sorguları istemcilerin kullanmak üzere yapılandırıldığı özyinelemeli DNS hizmetinden gelir. Bu nedenle, bölgeyi belirlemekte kullanılan IP adresi istemcinin IP adresidir, ancak özyinelemeli DNS hizmetinin IP adresidir. Uygulamada, bu IP adresi istemci için iyi bir ara sunucu.

### <a name="faqs"></a>SSS

* [Coğrafi yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Performans yönlendirme yöntemini mi yoksa coğrafi yönlendirme yöntemini mi kullanmalıyım Nasıl yaparım? karar veriyor musunuz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Coğrafi yönlendirme için Traffic Manager tarafından desteklenen bölgeler nelerdir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Traffic Manager bir kullanıcının sorgu nerede olduğunu nasıl belirleyebilir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Traffic Manager her durumda kullanıcının tam coğrafi konumunu doğru bir şekilde belirleyebilsin mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Bir uç noktanın coğrafi yönlendirme için yapılandırılmış ile aynı bölgede fiziksel olarak bulunması gerekir mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Coğrafi yönlendirme yapmak üzere yapılandırılmamış bir profildeki uç noktalara coğrafi bölgeler atayabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Mevcut bir profilin yönlendirme yöntemini coğrafi olarak değiştirmeyi denediğimde neden hata alıyorum?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Müşterilerin coğrafi yönlendirmeyi etkin bir profil altında olan uç noktalar yerine iç içe geçmiş profiller oluşturması kesinlikle önerilir mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [API sürümünde bu yönlendirme türünü destekleyen herhangi bir kısıtlama var mı?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>Çoklu değer trafik-yönlendirme yöntemi
Çoklu **değer** trafik-yönlendirme yöntemi, tek bir DNS sorgu yanıtında birden çok sağlıklı uç nokta almanızı sağlar. Bu, arayanın yanıt vermeyen bir uç noktanın olaydaki diğer uç noktalarla istemci tarafı yeniden denemeleri yapmasına olanak sağlar. Bu model, bir hizmetin kullanılabilirliğini artırabilir ve sağlıklı bir uç nokta elde etmek için yeni bir DNS sorgusuyla ilişkili gecikme süresini azaltabilir. Çoklu değer yönlendirme yöntemi yalnızca ' External ' türündeki tüm uç noktalar ve IPv4 veya IPv6 adresleri olarak belirtilmişse kullanılabilir. Bu profil için bir sorgu alındığında, sağlıklı tüm uç noktalar döndürülür ve yapılandırılabilir en yüksek dönüş sayısına tabidir.

### <a name="faqs"></a>SSS

* [Çok değerli yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Çok değerli yönlendirme kullanıldığında kaç uç nokta döndürülüyor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Çoklu küme yönlendirmesi kullanıldığında aynı uç nokta kümesini alacak mıyım?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>Alt ağ trafiği-yönlendirme yöntemi
**Alt ağ** trafiği-yönlendirme yöntemi, bir dizi Son Kullanıcı IP adresi aralığını bir profildeki belirli uç noktalara eşlemenizi sağlar. Bundan sonra, Traffic Manager Bu profil için bir DNS sorgusu alırsa, bu isteğin kaynak IP adresini inceleyerek (çoğu durumda bu, çağıran tarafından kullanılan DNS Çözümleyicisinin giden IP adresi olacaktır), hangi uç noktanın eşlendiğini ve t döndürecek olduğunu tespit eder sorgu yanıtında hat uç noktası. 

Bir uç noktaya eşlenecek IP adresi CıDR aralıkları (ör. 1.2.3.0/24) veya bir adres aralığı (örneğin, 1.2.3.4-5.6.7.8) olarak belirtilebilir. Bir uç noktayla ilişkili IP aralıklarının bu profilde benzersiz olması gerekir ve aynı profildeki farklı bir uç noktanın IP adresi kümesiyle çakışamaz.
Adres aralığı olmayan bir uç nokta tanımlarsanız, bu işlem geri dönüş olarak çalışır ve kalan alt ağlardan trafik alır. Geri dönüş uç noktası yoksa, Traffic Manager tanımsız aralıklar için bir NODATA yanıtı gönderir. Bu nedenle, bir geri dönüş uç noktası tanımlamanız veya diğer tüm olası IP aralıklarının uç noktalarınız genelinde belirtildiğinden emin olmanız önemle tavsiye edilir.

Alt ağ yönlendirme, belirli bir IP alanından bağlanan kullanıcılar için farklı bir deneyim sunmak üzere kullanılabilir. Örneğin, alt ağ yönlendirmeyi kullanarak bir müşteri, kurumsal ofislerinden gelen tüm isteklerin, uygulamasının yalnızca dahili bir sürümünü test ettikleri farklı bir uç noktaya yönlendirilmesini sağlayabilir. Başka bir senaryo, belirli bir ISS 'den bağlanan kullanıcılara farklı bir deneyim sağlamak istiyorsanız (örneğin, belirli bir ISS 'den kullanıcıları engelle).

### <a name="faqs"></a>SSS

* [Alt ağ yönlendirmesinin yararlı olduğu bazı kullanım durumları nelerdir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Traffic Manager son kullanıcının IP adresini nasıl bilir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Alt ağ yönlendirmeyi kullanırken IP adreslerini nasıl belirtirim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Alt ağ yönlendirmesi kullanırken bir geri dönüş uç noktası nasıl belirtebilir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Bir alt ağ yönlendirme türü profilinde bir uç nokta devre dışıysa ne olur?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Sonraki adımlar

[Traffic Manager uç nokta izleme](traffic-manager-monitoring.md) kullanarak yüksek kullanılabilirliğe sahip uygulamalar geliştirmeyi öğrenin




