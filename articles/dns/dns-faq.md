---
title: SSS-Azure DNS
description: Bu makalede Azure DNS hakkında sık sorulan sorular hakkında bilgi edinin
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246391"
---
# <a name="azure-dns-faq"></a>Azure DNS SSS

## <a name="about-azure-dns"></a>Azure DNS hakkında

### <a name="what-is-azure-dns"></a>Azure DNS nedir?

Etki alanı adı sistemi (DNS), bir Web sitesi veya hizmet adını IP adresine çevirir veya çözümler. Azure DNS, DNS etki alanları için bir barındırma hizmetidir. Microsoft Azure altyapısını kullanarak ad çözümlemesi sağlar. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz.

Azure DNS 'daki DNS etki alanları, DNS ad sunucularının Azure küresel ağında barındırılır. Bu sistem, her DNS sorgusunun kullanılabilir en yakın DNS sunucusu tarafından yanıtlanabilmesi için tek noktaya yayın ağını kullanır. Azure DNS, etki alanınız için hızlı performans ve yüksek kullanılabilirlik sağlar.

Azure DNS Azure Resource Manager tabanlıdır. Azure rol tabanlı erişim denetimi, denetim günlükleri ve kaynak kilitleme gibi Kaynak Yöneticisi özelliklerden Azure DNS avantajları. Azure portal, Azure PowerShell cmdlet 'leri ve platformlar arası Azure CLı aracılığıyla etki alanlarını ve kayıtları yönetebilirsiniz. Otomatik DNS yönetimi gerektiren uygulamalar, REST API ve SDK 'lar aracılığıyla hizmetle tümleştirilebilir.

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS maliyeti ne kadar sürer?

Azure DNS faturalandırma modeli, Azure DNS barındırılan DNS bölgelerinin sayısını temel alır. Ayrıca, aldıkları DNS sorgularının sayısını temel alır. İndirimler kullanım temel alınarak sağlanır.

Daha fazla bilgi için [Azure DNS fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/dns/)bakın.

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS hizmetinin SLA değeri kaçtır?

Azure, geçerli DNS isteklerinin en az bir Azure DNS ad sunucusundan %100 yanıt alacağını garanti eder.

Daha fazla bilgi için [Azure DNS SLA sayfasına](https://azure.microsoft.com/support/legal/sla/dns)bakın.

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>DNS bölgesi nedir? Bu, bir DNS etki alanıyla aynı mı? 

Etki alanı, etki alanı adı sisteminde benzersiz bir addır. Örneğin: contoso.com.

DNS bölgesi, belirli bir etki alanına ait DNS kayıtlarını barındırmak için kullanılır. Örneğin, contoso.com etki alanı birkaç DNS kaydı içerebilir. Kayıtlar bir posta sunucusu için mail.contoso.com ve \. bir Web sitesi için www contoso.com içerebilir. Bu kayıtlar, DNS bölgesi contoso.com içinde barındırılır.

Bir etki alanı adı *yalnızca bir addır*. DNS bölgesi, bir etki alanı adı için DNS kayıtlarını içeren bir veri kaynağıdır. Azure DNS’yi kullanarak bir DNS bölgesi barındırabilir ve Azure'da bir etki alanının DNS kayıtlarını yönetebilirsiniz. Ayrıca, DNS sorgularını Internet 'ten yanıtlamak için DNS ad sunucuları sağlar.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS kullanmak için bir DNS etki alanı adı satın almam gerekiyor mu? 

Gerekli değildir.

Azure DNS bir DNS bölgesini barındırmak için bir etki alanı satın almanız gerekmez. Her zaman etki alanı adına sahip olmadan bir DNS bölgesi oluşturabilirsiniz. Bu bölge için DNS sorguları yalnızca bölgeye atanan Azure DNS ad sunucularına yönlendirildikleri takdirde çözümlenir.

DNS diliminizi küresel DNS hiyerarşisine bağlamak için etki alanı adını satın almalısınız. Ardından, dünyanın herhangi bir yerinden DNS sorguları DNS bölgenizi ve DNS kayıtlarınız ile yanıtını bulur.

## <a name="azure-dns-features"></a>Azure DNS özellikleri

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Traffic Manager etki alanı adı için diğer ad kayıtlarını kullanırken herhangi bir kısıtlama var mı?

Evet. Azure Traffic Manager ile statik genel IP adresleri kullanmanız gerekir. **Dış uç nokta** hedefini STATIK bir IP adresi kullanarak yapılandırın. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS DNS tabanlı trafik yönlendirmesi veya uç nokta yük devretmesini destekler mi?

DNS tabanlı trafik yönlendirme ve uç nokta yük devretmesi Traffic Manager tarafından sağlanır. Traffic Manager, Azure DNS birlikte kullanılabilen ayrı bir Azure hizmetidir. Daha fazla bilgi için [Traffic Manager genel bakış](../traffic-manager/traffic-manager-overview.md)bölümüne bakın.

Azure DNS, belirli bir DNS kaydı için her DNS sorgusunun her zaman aynı DNS yanıtını aldığı statik DNS etki alanlarını barındırmayı destekler.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS etki alanı adı kaydını destekliyor mu?

Hayır. Azure DNS, şu anda etki alanı adlarını satın alma seçeneğini desteklemez. Etki alanları satın almak için bir üçüncü taraf etki alanı adı kaydedici kullanmanız gerekir. Kaydedici genellikle küçük bir yıllık ücreti ücretlendirir. Etki alanları, DNS kayıtlarının yönetimi için Azure DNS bölümünde barındırılabilir. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](dns-domain-delegation.md).

Etki alanı adları satın alma özelliği Azure biriktirme listesinde izlenir. [Bu özellik için destek kaydetmek](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)üzere geri bildirim sitesini kullanın.

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS DNSSEC 'yi destekliyor mu?

Hayır. Azure DNS şu anda etki alanı adı sistemi güvenlik uzantıları (DNSSEC) desteklememektedir.

DNSSEC özelliği Azure DNS biriktirme listesinde izlenir. [Bu özellik için destek kaydetmek](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)üzere geri bildirim sitesini kullanın.

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS, bölge aktarımlarını (AXFR/ıXFR) destekler mi?

Hayır. Azure DNS şu anda bölge aktarımlarını desteklememektedir. DNS bölgeleri [, Azure CLI kullanılarak Azure DNS içine aktarılabilir](dns-import-export.md). DNS kayıtları, [Azure DNS yönetim portalı](dns-operations-recordsets-portal.md), [REST API](/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell cmdlet 'leri](dns-operations-recordsets.md)veya [CLI aracı](dns-operations-recordsets-cli.md)aracılığıyla yönetilir.

Bölge aktarımı özelliği Azure DNS biriktirme listesinde izlenir. [Bu özellik için destek kaydetmek](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)üzere geri bildirim sitesini kullanın.

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS destek URL 'SI yeniden yönlendirmeleri mi?

Hayır. URL yeniden yönlendirme hizmetleri bir DNS hizmeti değil. DNS düzeyi yerine HTTP düzeyinde çalışır. Bazı DNS sağlayıcıları bir URL yeniden yönlendirme hizmetini genel sunumunun bir parçası olarak paketleyip. Bu hizmet şu anda Azure DNS tarafından desteklenmiyor.

URL yeniden yönlendirme özelliği Azure DNS biriktirme listesinde izlenir. [Bu özellik için destek kaydetmek](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)üzere geri bildirim sitesini kullanın.

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS, TXT kayıt kümeleri için genişletilmiş ASCII kodlaması (8 bit) kümesini destekler mi?

Evet. Azure DNS, TXT kayıt kümeleri için genişletilmiş ASCII kodlama kümesini destekler. Ancak Azure REST API 'lerinin, SDK 'ların, PowerShell 'in ve CLı 'nin en son sürümünü kullanmanız gerekir. 1 Ekim 2017 veya SDK 2,1 ' den eski sürümlerde genişletilmiş ASCII kümesi desteklenmez. 

Örneğin, genişletilmiş ASCII karakteri \ 128 olan bir TXT kaydı değeri olarak bir dize sağlayabilirsiniz. Örnek, "abcd\128efgh." şeklindedir. Azure DNS, bu karakterin iç temsilinde 128 olan byte değerini kullanır. DNS çözümlemesi sırasında, yanıtta bu bayt değeri döndürülür. Ayrıca, "abc" ve "\ 097 \ 098 \ 099" ' nin çözüm açısından çok farklı şekilde değiştirilebilecek olduğunu unutmayın. 

TXT kayıtları için [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) bölge dosyası ana biçim kaçış kurallarını izliyoruz. Örneğin, `\` artık RFC başına her şeyi yok eder. `A\B`Txt kayıt değeri olarak belirtirseniz, yalnızca olarak temsil edilir ve çözümlenir `AB` . Aslında TXT kaydının çözümlenme durumunda olmasını istiyorsanız, `A\B` tekrar kaçış yapmanız gerekir `\` . Örnek olarak, öğesini belirtin `A\\B` .

Bu destek şu anda Azure portal oluşturulan TXT kayıtları için kullanılamaz.

## <a name="alias-records"></a>Diğer ad kayıtları

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Diğer ad kayıtlarının yararlı olduğu bazı senaryolar nelerdir?

[Azure DNS diğer ad kayıtlarına genel bakış](dns-alias.md)bölümünde senaryolar bölümüne bakın.

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Diğer ad kayıt kümeleri için hangi kayıt türleri desteklenir?

Diğer ad kayıt kümeleri Azure DNS bölgesindeki aşağıdaki kayıt türleri için desteklenir:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Diğer ad kayıt kümeleri için hedef olarak hangi kaynaklar desteklenir?

- **DNS A/AAAA kayıt kümesinden ortak bir IP kaynağını işaret edin.** A/AAAA kayıt kümesi oluşturabilir ve bunu bir genel IP kaynağını işaret etmek üzere bir diğer ad kayıt kümesi yapabilirsiniz.
- **DNS A/AAAA/CNAME kayıt kümesinden bir Traffic Manager profile işaret edin.** Bir DNS CNAME kayıt kümesinden bir Traffic Manager profilinin CNAME ' i işaret edebilirsiniz. Örnek olarak contoso.trafficmanager.net. Artık, DNS bölgeniz içindeki bir veya AAAA kayıt kümesinden dış uç noktaları olan bir Traffic Manager profile de işaret edebilirsiniz.
- **Azure Content Delivery Network (CDN) uç noktasını Işaret edin**. Bu, Azure depolama ve Azure CDN kullanarak statik Web siteleri oluşturduğunuzda yararlı olur.
- **Aynı bölge içindeki başka bir DNS kayıt kümesine işaret edin.** Diğer ad kayıtları aynı türdeki diğer kayıt kümelerine başvurabilir. Örneğin, bir DNS CNAME kayıt kümesinin aynı türdeki başka bir CNAME kayıt kümesine diğer ad olmasını sağlayabilirsiniz. Bu düzenleme, bazı kayıt kümelerinin diğer adlar ve diğer ad olmayan adlar olmasını istiyorsanız yararlıdır.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Azure portal diğer ad kayıtlarını oluşturabilir ve güncelleştirebilir miyim?

Evet. Azure REST API 'Leri, PowerShell, CLı ve SDK 'Lar ile birlikte Azure portal diğer ad kayıtlarını oluşturabilir veya yönetebilirsiniz.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Diğer ad, temel alınan genel IP silindiğinde DNS kayıt ayarlamış min silinip silinmediğinden emin olmak için yardımcı olur mu?

Evet. Bu özellik diğer ad kayıtlarının temel özelliklerinden biridir. Uygulamanızın kullanıcıları için olası kesintilerden kaçınmanıza yardımcı olur.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Diğer ad, temel alınan genel IP adresi değiştiğinde DNS kayıt ayarlamış min doğru IP adresine güncelleştirildiğinden emin olmak için yardımcı olur mu?

Evet. Bu özellik diğer ad kayıtlarının temel özelliklerinden biridir. Uygulamanız için olası kesintileri veya güvenlik risklerini önlemenize yardımcı olur.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Bir veya AAAA kaydı için diğer ad kayıt kümelerini Traffic Manager işaret etmek üzere kullanırken herhangi bir kısıtlama var mı?

Evet. Bir Traffic Manager profilini bir veya AAAA kayıt kümesinden diğer ad olarak göstermek için, Traffic Manager profilinin yalnızca dış uç noktaları kullanması gerekir. Traffic Manager dış uç noktaları oluşturduğunuzda, uç noktaların gerçek IP adreslerini belirtin.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Diğer ad kayıtlarını kullanmak için ek bir ücret var mı?

Diğer ad kayıtları, geçerli bir DNS kayıt kümesindeki nitedir. Diğer ad kayıtları için ek faturalandırma yoktur.

## <a name="use-azure-dns"></a>Azure DNS kullan

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Azure DNS ve başka bir DNS sağlayıcısı kullanarak bir etki alanını birlikte barındırabilirsiniz miyim?

Evet. Azure DNS, diğer DNS hizmetleriyle birlikte barındırma etki alanlarını destekler.

Ortak barındırma ayarlamak için, etki alanı için NS kayıtlarını her iki sağlayıcının ad sunucularına işaret etmek üzere değiştirin. Ad sunucusu (NS) kayıtları, hangi sağlayıcıların etki alanı için DNS sorguları alacağını denetler. Bu NS kayıtlarını, diğer sağlayıcıda ve üst bölgede Azure DNS değiştirebilirsiniz. Üst bölge genellikle etki alanı adı kaydedicisi aracılığıyla yapılandırılır. DNS temsili hakkında daha fazla bilgi için bkz. [DNS etki alanı temsili](dns-domain-delegation.md).

Ayrıca, etki alanı için DNS kayıtlarının her iki DNS sağlayıcısı arasında eşitlenmiş olduğundan emin olun. Azure DNS, DNS bölge aktarımlarını desteklememektedir. DNS kayıtları, [Azure DNS yönetim portalı](dns-operations-recordsets-portal.md), [REST API](/rest/api/dns/), [SDK](dns-sdk.md), [PowerShell cmdlet 'leri](dns-operations-recordsets.md)ya da [CLI aracı](dns-operations-recordsets-cli.md)kullanılarak eşitlenmelidir.

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Etki alanım 'ı dört Azure DNS ad sunucusuna temsilcmem gerekir mi?

Evet. Azure DNS her DNS bölgesine dört ad sunucusu atar. Bu düzenleme hata yalıtımı ve artan esnekliği içindir. Azure DNS SLA 'sını nitelemek için, etki alanınızı dört ad sunucusuna atayın.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS kullanım sınırları nelerdir?

Azure DNS kullandığınızda aşağıdaki varsayılan sınırlar geçerlidir.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Azure DNS bölgeyi kaynak grupları veya abonelikler arasında taşıyabilir miyim?

Evet. DNS bölgeleri, kaynak grupları veya abonelikler arasında taşınabilir.

DNS bölgesini taşırken DNS sorguları üzerinde hiçbir etkisi yoktur. Bölgeye atanan ad sunucuları aynı kalır. DNS sorguları, boyunca normal olarak işlenir.

DNS bölgelerini taşıma hakkında daha fazla bilgi ve yönergeler için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS değişikliklerinin etkili olması için ne kadar sürer?

Yeni DNS bölgeleri ve DNS kayıtları genellikle Azure DNS ad sunucularında hızlı bir şekilde görünür. Zamanlama birkaç saniyedir.

Mevcut DNS kayıtlarında yapılan değişiklikler biraz daha uzun sürebilir. Genellikle 60 saniye içinde Azure DNS ad sunucularında görünürler. DNS istemcileri ve Azure DNS dışındaki DNS özyinelemeli çözümleyiciler tarafından önbelleğe alma, zamanlamayı etkileyebilir. Bu önbellek süresini denetlemek için, her bir kayıt kümesinin yaşam süresi (TTL) özelliğini kullanın.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>DNS bölgelerinizi yanlışlıkla silinmeye karşı nasıl koruyabilirim?

Azure DNS, Azure Resource Manager kullanılarak yönetilir. Azure Resource Manager sağladığı erişim denetimi özelliklerinden faydaların Azure DNS. Azure rol tabanlı erişim denetimi, hangi kullanıcıların DNS bölgelerine ve kayıt kümelerine okuma veya yazma erişimi olduğunu denetlemek için kullanılabilir. Kaynak kilitleri, DNS bölgelerinin ve kayıt kümelerinin yanlışlıkla değiştirilmesini veya silinmesini engeller.

Daha fazla bilgi için bkz. [DNS bölgelerini ve kayıtlarını koruma](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Nasıl yaparım? SPF kayıtlarını Azure DNS ayarlamak mı istiyorsunuz?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Azure DNS ad sunucuları IPv6 üzerinden çözümlensin mi? 

Evet. Azure DNS ad sunucuları ikili yığınlardır. İkili yığın, IPv4 ve IPv6 adreslerine sahip oldukları anlamına gelir. DNS bölgenize atanan Azure DNS ad sunucularının IPv6 adresini bulmak için, nslookup gibi bir araç kullanın. `nslookup -q=aaaa <Azure DNS Nameserver>` bunun bir örneğidir.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Azure DNS bir ıDN mi Nasıl yaparım??

Uluslararası etki alanı adları (IDNs), her DNS adını [punıcode](https://en.wikipedia.org/wiki/Punycode)kullanarak kodlar. DNS sorguları, bu zayıf kod kodlu adlar kullanılarak yapılır.

Azure DNS ' de IDNs 'yi yapılandırmak için bölge adı veya kayıt kümesi adını punıcode olarak dönüştürün. Azure DNS, atlama kodundan veya bu koddan yerleşik dönüştürmeyi desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure DNS hakkında daha fazla bilgi edinin](dns-overview.md).

- [Özel etki alanları için Azure DNS kullanma hakkında daha fazla bilgi edinin](private-dns-overview.md).

- [DNS bölgeleri ve kayıtları hakkında daha fazla bilgi edinin](dns-zones-records.md).

- [Azure DNS kullanmaya başlayın](dns-getstarted-portal.md).
