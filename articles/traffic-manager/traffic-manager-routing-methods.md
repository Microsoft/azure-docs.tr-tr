---
title: Azure Traffic Manager trafik yönlendirme yöntemleri
description: Bu makale, Traffic Manager tarafından kullanılan farklı trafik yönlendirme yöntemlerini anlamanıza yardımcı olur
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730981"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager yönlendirme yöntemleri

Azure Traffic Manager, ağ trafiğinin çeşitli hizmet uç noktalarına nasıl yönlendirildiğine yönelik altı trafik yönlendirme yöntemini destekler. Her profil için, Traffic Manager onunla ilişkili trafik yönlendirme yöntemini aldığı her DNS sorgusuyla uygular. Trafik-yönlendirme yöntemi, DNS yanıtında hangi uç noktanın döndürüleceğini belirler.

Aşağıdaki trafik yönlendirme yöntemleri Traffic Manager ' de kullanılabilir:

* **[Öncelik](#priority-traffic-routing-method):** tüm trafik için bir birincil hizmet uç noktası olmasını istediğinizde **Öncelik** yönlendirmeyi seçin. Yedekleme uç noktalarından birincil veya birinin kullanılamadığı durumlarda birden çok yedekleme uç noktası sağlayabilirsiniz.
* **[Ağırlıklı](#weighted):** bir uç nokta genelinde trafiği ağırlığa göre dağıtmak istediğinizde **ağırlıklı** yönlendirmeyi seçin. Ağırlığı tüm uç noktalar arasında eşit olarak dağıtılacak şekilde ayarlayın.
* **[Performans](#performance):** farklı coğrafi konumlarda uç noktalarınız olduğunda ve son kullanıcıların en düşük ağ gecikmesi için "en yakın" uç noktasını kullanmasını istiyorsanız **performans** yönlendirmesi ' ni seçin.
* **[Coğrafi](#geographic):** kullanıcıları DNS sorgularının coğrafi olarak nereden kaynaklandığı belirli uç noktalara (Azure, dış veya iç içe) yönlendirmek için **coğrafi** yönlendirme ' yi seçin. Bu yönlendirme yöntemiyle, veri egemenlik mantıkları, içerik & Kullanıcı deneyimi ve farklı bölgelerdeki trafiği ölçme gibi senaryolarla uyum sağlamanıza olanak sağlar.
* **[Çoklu değer](#multivalue):** yalnızca uç nokta olarak IPv4/IPv6 adreslerine sahip olan Traffic Manager profilleri için **Çoklu değer** seçeneğini belirleyin. Bu profil için bir sorgu alındığında, sağlıklı tüm uç noktalar döndürülür.
* **[Alt ağ](#subnet):** Son Kullanıcı IP adresi aralıklarının kümelerini belirli bir uç noktaya eşlemek için **alt ağ** trafiği-yönlendirme yöntemini seçin. Bir istek alındığında döndürülen uç nokta, bu isteğin kaynak IP adresi için eşlenmiş bir değer olacaktır. 


Tüm Traffic Manager profillerinin sistem durumu izleme ve uç noktaların otomatik yük devretmesi vardır. Daha fazla bilgi için bkz. [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md). Bir Traffic Manager profili içinde, tek seferde yalnızca bir trafik yönlendirme yöntemi yapılandırabilirsiniz. Profiliniz için istediğiniz zaman farklı bir trafik yönlendirme yöntemi seçebilirsiniz. Değişiklikleriniz, herhangi bir kesinti olmadan bir dakika içinde uygulanır. Trafik yönlendirme yöntemlerini, iç içe geçmiş Traffic Manager profillerini kullanarak birleştirebilirsiniz. İç içe geçmiş profiller, daha büyük ve karmaşık uygulamaların ihtiyaçlarını karşılayan gelişmiş trafik yönlendirme yapılandırmalarına olanak tanır. Daha fazla bilgi için bkz. [iç içe Traffic Manager profilleri](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Öncelik trafiği-yönlendirme yöntemi

Genellikle bir kuruluş, Hizmetleri için güvenilirlik sağlamak istemektedir. Bunu yapmak için, birincili bir veya daha fazla yedekleme hizmeti dağıtarak birincil bir durum yaşırlar. ' Priority ' trafik-yönlendirme yöntemi, Azure müşterilerinin bu yük devretme modelini kolayca uygulamasına olanak tanır.

![Azure Traffic Manager ' Priority ' trafiği yönlendirme yöntemi](media/traffic-manager-routing-methods/priority.png)

Traffic Manager profili, önceliği belirlenmiş hizmet uç noktalarının bir listesini içerir. Traffic Manager varsayılan olarak tüm trafiği birincil (en yüksek öncelikli) uç noktaya gönderir. Birincil uç noktanın kullanılabilir olmaması halinde Traffic Manager, trafiği ikinci uç noktaya yönlendirir. Birincil ve ikincil bitiş noktalarının kullanılamadığı bir durumda, trafik üçüncü olarak gider ve bu şekilde devam eder. Uç noktanın kullanılabilirliği, yapılandırılan duruma (etkin veya devre dışı) ve devam eden uç nokta izleme durumuna göre belirlenir.

### <a name="configuring-endpoints"></a>Uç noktaları yapılandırma

Azure Resource Manager, her bitiş noktası için ' Priority ' özelliğini kullanarak uç nokta önceliğini açık olarak yapılandırırsınız. Bu özellik 1 ile 1000 arasında bir değerdir. Daha düşük bir değer, daha yüksek bir önceliği temsil eder. Uç noktalar öncelik değerlerini paylaşamaz. Özelliği ayarlama isteğe bağlıdır. Atlanırsa, uç nokta sırasına göre varsayılan bir öncelik kullanılır.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ağırlıklı trafik-yönlendirme yöntemi
' Ağırlıklı ' trafik-yönlendirme yöntemi, trafiği eşit bir şekilde dağıtmanıza veya önceden tanımlanmış bir ağırlığa kullanmanıza olanak sağlar.

![Azure Traffic Manager ' ağırlıklı ' trafik-yönlendirme yöntemi](media/traffic-manager-routing-methods/weighted.png)

Ağırlıklı trafik-yönlendirme yönteminde, Traffic Manager profili yapılandırmasındaki her bir uç noktaya bir ağırlık atarsınız. Ağırlık 1-1000 arasında bir tamsayıdır. Bu parametre isteğe bağlıdır. Atlanırsa, trafik yöneticileri varsayılan olarak ' 1 ' ağırlığını kullanır. Daha yüksek ağırlık, öncelik daha yüksektir.

Traffic Manager, alınan her DNS sorgusu için rastgele olarak kullanılabilir durumdaki bir uç noktayı seçer. Bir uç noktanın seçilme ihtimali, kullanılabilir durumdaki tüm uç noktalara atanmış olan ağırlıklara bağlıdır. Tüm uç noktalarında aynı ağırlığı kullanmak, hatta trafik dağıtımına neden olur. Belirli uç noktalarda daha yüksek veya daha düşük kalınlıklar kullanmak, bu uç noktaların DNS yanıtlarına daha fazla veya daha az sıklıkta döndürülmesine neden olur.

Ağırlıklı yöntem bazı faydalı senaryolara izin verebilir:

* Aşamalı uygulama yükseltme: yeni bir uç noktaya yönlendirmek için trafik yüzdesi verilsin ve zaman içindeki trafiği aşamalı olarak %100 oranında artırmış.
* Azure 'a uygulama geçişi: hem Azure hem de dış uç noktalarla bir profil oluşturun. Yeni uç noktaları tercih etmek için uç noktaların kalınlığını ayarlayın.
* Bulut-daha fazla kapasite için: bir Traffic Manager profilinin arkasına yerleştirerek şirket içi bir dağıtımı buluta hızlıca genişletin. Bulutta fazladan kapasiteye ihtiyacınız olduğunda, daha fazla uç nokta ekleyebilir veya etkinleştirebilir ve her bir uç noktaya giden trafik bölümünün ne olduğunu belirtebilirsiniz.

Azure portal, Azure PowerShell, CLı veya REST API 'Lerini kullanarak ağırlıkları yapılandırabilirsiniz.

Anımsanacak bir nokta, DNS yanıtlarının istemciler tarafından önbelleğe alınacağını unutmayın. Ayrıca, istemcilerin DNS adlarını çözümlemek için kullandığı özyinelemeli DNS sunucuları tarafından önbelleğe alınır. Bu önbelleğe alma, ağırlıklı trafik dağıtımları üzerinde etkiye sahip olabilir. İstemci sayısı ve özyinelemeli DNS sunucuları büyükse, trafik dağıtımı beklendiği gibi çalışıyor demektir. Ancak, istemci sayısı veya özyinelemeli DNS sunucuları küçük olduğunda, önbelleğe alma işlemi trafik dağıtımını önemli ölçüde eğebilir.

Yaygın kullanım örnekleri şunlardır:

* Geliştirme ve test ortamları
* Uygulamadan uygulamaya iletişimler
* Ortak bir özyinelemeli DNS altyapısını (örneğin, bir ara sunucu aracılığıyla bağlanan şirket çalışanları) paylaşan bir dar kullanıcı tabanına hedeflenmiş uygulamalar

Bu DNS önbelleğe alma etkileri yalnızca Azure Traffic Manager değil, DNS tabanlı tüm trafik yönlendirme sistemlerinde ortaktır. Bazı durumlarda, DNS önbelleğini açıkça temizlemek geçici bir çözüm sağlayabilir. Bu işe yaramazsa, alternatif bir trafik yönlendirme yöntemi daha uygun olabilir.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Performans trafiği-yönlendirme yöntemi

Dünya genelindeki iki veya daha fazla konumda uç noktalar dağıtmak, uygulamalarınızın yanıt hızını iyileştirebilir. ' Performance ' trafik-yönlendirme yöntemiyle, trafiği ' en yakın ' olan konuma yönlendirebilirsiniz.

![Azure Traffic Manager ' Performance ' trafiği-yönlendirme yöntemi](media/traffic-manager-routing-methods/performance.png)

' En yakın ' uç noktası coğrafi mesafede ölçülen şekilde en yakın değildir. Bunun yerine, ' Performance ' trafik-yönlendirme yöntemi ağ gecikmesini ölçerek en yakın uç noktayı belirler. Traffic Manager, IP adresi aralıkları ve her Azure veri merkezi arasında gidiş dönüş süresini izlemek için bir Internet gecikme tablosu saklar.

Traffic Manager, Internet gecikme süresi tablosundaki gelen DNS isteğinin kaynak IP adresini arar. Traffic Manager daha sonra, bu IP adresi aralığı için en düşük gecikme süresine sahip Azure veri merkezinde kullanılabilir bir uç nokta seçer. Sonra Traffic Manager, DNS yanıtında bu uç noktayı döndürür.

[Traffic Manager nasıl çalıştığı konusunda](traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager doğrudan istemcilerden DNS sorguları almaz. Bunun yerine, DNS sorguları istemcilerin kullanmak üzere yapılandırıldığı özyinelemeli DNS hizmetinden gelir. Bu nedenle, ' en yakın ' uç noktasını belirlemede kullanılan IP adresi istemcinin IP adresidir, ancak özyinelemeli DNS hizmetinin IP adresidir. Bu IP adresi, istemci için iyi bir ara sunucu.


Traffic Manager, Internet gecikme tablosunu genel Internet ve yeni Azure bölgelerindeki değişikliklere göre hesaba göre düzenli olarak güncelleştirir. Bununla birlikte, uygulama performansı, Internet üzerinde yükün gerçek zamanlı farklılıklara göre farklılık gösterir. Performans trafiği-yönlendirme, belirli bir hizmet uç noktasındaki yükü izlemez. Bir uç nokta kullanılamaz hale gelirse Traffic Manager DNS sorgu yanıtlarına dahil değildir.


Şunlara işaret eder:

* Profiliniz aynı Azure bölgesinde birden fazla uç nokta içeriyorsa, Traffic Manager trafiği bu bölgedeki kullanılabilir uç noktalar arasında eşit olarak dağıtır. Bir bölge içinde farklı bir trafik dağıtımını tercih ediyorsanız, [iç içe geçmiş Traffic Manager profillerini](traffic-manager-nested-profiles.md)kullanabilirsiniz.
* En yakın Azure bölgesindeki tüm etkin uç noktalar düşürülirse Traffic Manager trafiği bir sonraki en yakın Azure bölgesindeki uç noktalara taşıdıkça. Tercih edilen bir yük devretme sırası tanımlamak istiyorsanız, [iç içe geçmiş Traffic Manager profillerini](traffic-manager-nested-profiles.md)kullanın.
* Dış uç noktalar veya iç içe geçmiş uç noktalarla performans trafiği yönlendirme yöntemini kullanırken, bu uç noktaların konumunu belirtmeniz gerekir. Dağıtımınıza en yakın Azure bölgesini seçin. Bu konumlar Internet gecikme süresi tablosu tarafından desteklenen değerlerdir.
* Uç noktayı seçen algoritma belirleyici olur. Aynı istemciden yinelenen DNS sorguları aynı uç noktaya yönlendirilir. Genellikle, istemciler seyahat ederken farklı özyinelemeli DNS sunucuları kullanır. İstemci farklı bir uç noktaya yönlendirilebilir. Yönlendirme, Internet gecikme tablosuna ait güncelleştirmelerden da etkilenebilir. Performans trafiği-yönlendirme yönteminin, istemcinin her zaman aynı uç noktaya yönlendirildiğini garanti etmez.
* Internet gecikme süresi tablosu değiştiğinde, bazı istemcilerin farklı bir uç noktaya yönlendirildiğine dikkat edebilirsiniz. Bu yönlendirme değişikliği, geçerli gecikme verilerine göre daha kesin. Internet sürekli geliştikçe performans trafiği yönlendirmenin doğruluğunu sürdürmek için bu güncelleştirmeler önemlidir.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Coğrafi trafik-yönlendirme yöntemi

Traffic Manager profilleri, kullanıcıların DNS sorgusunun kaynaklandığı coğrafi konuma göre belirli uç noktalara (Azure, dış veya Iç Içe) yönlendirilmelerini sağlamak için coğrafi yönlendirme yöntemini kullanacak şekilde yapılandırılabilir. Bu yönlendirme yöntemiyle, veri egemenlik mantarihleriyle, içerik & kullanıcı deneyiminin yerelleştirilmesi ve farklı bölgelerden gelen trafiğin ölçülmesi ile uyum sağlamanıza olanak sağlar.
Bir profil coğrafi yönlendirme için yapılandırıldığında, bu profille ilişkilendirilen her uç noktanın kendisine atanmış bir coğrafi bölge kümesi olması gerekir. Coğrafi bölge, aşağıdaki ayrıntı düzeyi düzeylerinde olabilir 
- Dünya – herhangi bir bölge
- Bölgesel gruplandırma: Örneğin, Afrika, Orta Doğu, Avustralya/Pasifik vb. 
- Ülke/bölge: Örneğin, Irlanda, Peru, Hong Kong ÖIB vb. 
- Eyalet/Il – Örneğin, ABD-California, Avustralya-Queensland, Canada-Alberta vb. (Bu ayrıntı düzeyi düzeyi yalnızca Avustralya, Kanada ve ABD 'deki eyalet/eyalet için desteklenir).

Bir bölge veya bölge kümesi bir uç noktaya atandığında, bu bölgelerden gelen istekler yalnızca o uç noktaya yönlendirilir. Traffic Manager, bir kullanıcının sorgulamakta olduğu bölgeyi belirleyebilmek için DNS sorgusunun kaynak IP adresini kullanır. Genellikle, Kullanıcı için sorgu yapan yerel DNS Çözümleyicisinin IP adresi olarak bulunur.  

![Azure Traffic Manager ' coğrafi ' trafik-yönlendirme yöntemi](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager, DNS sorgusunun kaynak IP adresini okur ve hangi coğrafi bölgenin kaynak olduğunu belirler. Daha sonra bu coğrafi bölgenin eşlendiği bir uç nokta olup olmadığını görmek için görünür. Bu arama en düşük ayrıntı düzeyi düzeyinde (desteklenen eyalet/eyalet, başka bir deyişle ülke/bölge düzeyinde) başlar ve **dünyanın** en yüksek düzeyine kadar tüm şekilde çalışır. Bu çapraz geçiş kullanılarak bulunan ilk eşleşme, sorgu yanıtında döndürülecek uç nokta olarak seçilir. Iç Içe geçmiş bir tür uç noktasıyla eşleştirilirken, bu alt profil içindeki bir uç nokta, yönlendirme yöntemine göre döndürülür. Aşağıdaki noktaları bu davranış için geçerlidir:

- Bir coğrafi bölge, yönlendirme türü coğrafi yönlendirme olduğunda bir Traffic Manager profilindeki bir uç nokta ile eşlenebilir. Bu kısıtlama, kullanıcıların yönlendirmesinin belirleyici olmasını sağlar ve müşteriler, belirsiz coğrafi sınır gerektiren senaryoları etkinleştirebilir.
- Bir kullanıcının bölgesi iki farklı bitiş noktasının coğrafi eşleme altında listeleniyorsa Traffic Manager en düşük ayrıntı düzeyi olan uç noktayı seçer. Traffic Manager, bu bölgeden diğer uç noktaya yönlendirme isteklerini kabul etmezler. Örneğin, iki uç nokta (Endpoint1 ve Endpoint2) olan coğrafi yönlendirme türü profilini göz önünde bulundurun. Endpoint1, Irlanda 'dan trafik alacak şekilde yapılandırılmıştır ve Endpoint2, Avrupa 'dan gelen trafiği alacak şekilde yapılandırılır. Bir istek Irlanda 'dan kaynaklanıyorsa, her zaman Endpoint1 'a yönlendirilir.
- Bir bölge yalnızca bir uç nokta ile eşleştirilemediğinden, Traffic Manager uç noktanın sağlıklı olup olmadığını bir yanıt döndürür.

    >[!IMPORTANT]
    >Coğrafi yönlendirme yöntemini kullanan müşterilerin, her biri içinde en az iki uç nokta içeren alt profilleri olan Iç Içe geçmiş tür uç noktaları ile ilişkilendirilebilmesi önemle önerilir.
- Bir uç nokta eşleşmesi bulunursa ve uç nokta **durdurulmuş** durumdaysa, Traffic Manager bir NoData yanıtı döndürür. Bu durumda, coğrafi bölge hiyerarşisinde daha fazla arama yapılmaz. Bu davranış, alt profil **durdurulmuş** veya **devre dışı** durumdayken iç içe geçmiş uç nokta türleri için de geçerlidir.
- Bir uç nokta **devre dışı** durumunu görüntülüyorsa, bölge eşleştirme işlemine dahil değildir. Bu davranış, uç nokta **devre dışı** durumundayken iç içe geçmiş uç nokta türleri için de geçerlidir.
- Bir sorgu, bu profilde eşlenmesiz bir coğrafi bölgeden geliyorsa, Traffic Manager NODATA yanıtı döndürür. Tek bir uç nokta ile coğrafi yönlendirme kullanmanızı kesinlikle öneririz. En az iki uç nokta ile Iç Içe, alt profil içinde, **Dünya** bölgesine atanmış olan türü idealdir. Bu yapılandırma aynı zamanda bir bölgeyle eşlenmeyen IP adreslerinin işlenmesini de sağlar.

[Traffic Manager nasıl çalıştığı konusunda](traffic-manager-how-it-works.md)açıklandığı gibi, Traffic Manager doğrudan istemcilerden DNS sorguları almaz. DNS sorguları, istemcilerin kullanmak üzere yapılandırıldığı özyinelemeli DNS hizmetinden gelir. Bu nedenle, bölgeyi belirlemede kullanılan IP adresi istemcinin IP adresi değil, özyinelemeli DNS hizmetinin IP adresidir. Bu IP adresi, istemci için iyi bir ara sunucu.

### <a name="faqs"></a>SSS

* [Coğrafi yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Performans yönlendirme yöntemini mi yoksa coğrafi yönlendirme yöntemini mi kullanmalıyım Nasıl yaparım? karar veriyor musunuz?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Coğrafi yönlendirme için Traffic Manager tarafından desteklenen bölgeler nelerdir?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Traffic Manager bir kullanıcının sorgu nerede olduğunu nasıl belirleyebilir?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Traffic Manager her durumda kullanıcının tam coğrafi konumunu doğru bir şekilde belirleyebilsin mi?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Bir uç noktanın coğrafi yönlendirme için yapılandırıldığı bölge ile aynı bölgede fiziksel olarak bulunması gerekir mi?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Coğrafi yönlendirme yapmak üzere yapılandırılmamış bir profildeki uç noktalara coğrafi bölgeler atayabilir miyim?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Mevcut bir profilin yönlendirme yöntemini coğrafi olarak değiştirmeyi denediğimde neden hata alıyorum?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Müşterilerin coğrafi yönlendirmeyi etkin bir profil altında olan uç noktalar yerine iç içe geçmiş profiller oluşturması kesinlikle önerilir mi?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [API sürümünde bu yönlendirme türünü destekleyen herhangi bir kısıtlama var mı?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Çoklu değer trafik-yönlendirme yöntemi
Çoklu **değer** trafik-yönlendirme yöntemi, tek bir DNS sorgu yanıtında birden çok sağlıklı uç nokta almanızı sağlar. Bu yapılandırma, çağıran bir uç noktanın yanıt vermemesi durumunda çağıranın diğer uç noktalarla istemci tarafı yeniden denemeleri yapmasına olanak sağlar. Bu düzen bir hizmetin kullanılabilirliğini artırabilir ve iyi durumdaki bir uç noktayı alma amacıyla yapılacak yeni DNS sorgusundan kaynaklanan gecikme süresini azaltabilir. Çoklu değer yönlendirme yöntemi yalnızca ' External ' türündeki tüm uç noktalar ve IPv4 veya IPv6 adresleri olarak belirtilmişse kullanılabilir. Bu profil için bir sorgu alındığında, sağlıklı tüm uç noktalar döndürülür ve yapılandırılabilir en yüksek dönüş sayısına tabidir.

### <a name="faqs"></a>SSS

* [Çok değerli yönlendirmenin yararlı olduğu bazı kullanım durumları nelerdir?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Çok değerli yönlendirme kullanıldığında kaç uç nokta döndürülüyor?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Çoklu küme yönlendirmesi kullanıldığında aynı uç nokta kümesini alacak mıyım?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Alt ağ trafiği-yönlendirme yöntemi
**Alt ağ** trafiği-yönlendirme yöntemi, bir dizi Son Kullanıcı IP adresi aralığını bir profildeki belirli uç noktalara eşlemenizi sağlar. Traffic Manager Bu profil için bir DNS sorgusu alırsa, bu isteğin kaynak IP adresini inceleyeceksiniz. Daha sonra hangi uç noktanın eşlendiği ve bu uç noktayı sorgu yanıtında döndürecek olan bitiş noktasını tespit eder. Çoğu durumda kaynak IP adresi, çağıran tarafından kullanılan DNS çözümleyicisine sahiptir.

Bir uç noktaya eşlenecek IP adresi CıDR aralıkları (örneğin, 1.2.3.0/24) veya bir adres aralığı (örneğin, 1.2.3.4-5.6.7.8) olarak belirtilebilir. Bir uç noktayla ilişkili IP aralıklarının bu profil içinde benzersiz olması gerekir. Adres aralığının aynı profildeki farklı bir uç noktanın IP adresi kümesiyle bir çakışması olamaz.
Adres aralığı olmayan bir uç nokta tanımlarsanız, bu işlem geri dönüş olarak çalışır ve kalan alt ağlardan trafik alır. Geri dönüş uç noktası yoksa, Traffic Manager tanımsız aralıklar için bir NODATA yanıtı gönderir. Tüm olası IP aralıklarının uç noktalarınız genelinde belirtilmesini sağlamak için bir geri dönüş uç noktası tanımlamanız önemle tavsiye edilir.

Alt ağ yönlendirme, belirli bir IP alanından bağlanan kullanıcılar için farklı bir deneyim sunmak üzere kullanılabilir. Örneğin, kurumsal ofisinizdeki tüm isteklerin farklı bir uç noktaya yönlendirilmesini sağlayabilirsiniz. Bu yönlendirme yöntemi özellikle, uygulamanızın yalnızca dahili bir sürümünü test etmek istiyorsanız kullanışlıdır. Başka bir senaryo da belirli bir ISP'den bağlanan kullanıcılara farklı bir deneyim sağlamak olabilir (belirli bir ISP'den bağlanan kullanıcıları engellemek gibi).

### <a name="faqs"></a>SSS

* [Alt ağ yönlendirmesinin yararlı olduğu bazı kullanım durumları nelerdir?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Traffic Manager son kullanıcının IP adresini nasıl bilir?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Alt ağ yönlendirmeyi kullanırken IP adreslerini nasıl belirtirim?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Alt ağ yönlendirmesi kullanırken bir geri dönüş uç noktası nasıl belirtebilir?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Bir alt ağ yönlendirme türü profilinde bir uç nokta devre dışıysa ne olur?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Sonraki adımlar

[Traffic Manager uç nokta izleme](traffic-manager-monitoring.md) kullanarak yüksek kullanılabilirliğe sahip uygulamalar geliştirmeyi öğrenin