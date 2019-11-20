---
title: Azure’da uzaktan yönetim güvenliğini artırma | Microsoft Docs
description: Bu makalede, bulut Hizmetleri, sanal makineler ve özel uygulamalar dahil Microsoft Azure ortamlarını yönetirken uzaktan yönetim güvenliğini geliştirme adımları ele alınmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: 45efaadf7d15fff290165fe831c45c0bc063db53
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643791"
---
# <a name="security-management-in-azure"></a>Azure’da Güvenlik Yönetimi
Azure aboneleri yönetim iş istasyonları, geliştirici PC’leri ve hatta göreve özel izinleri bulunan ayrıcalıklı son kullanıcı cihazları dahil birden fazla cihazda kendi bulut ortamlarını yönetebilir. Bazı durumlarda, yönetim işlevleri [Azure portal](https://azure.microsoft.com/features/azure-portal/) gibi web tabanlı konsollar aracılığıyla gerçekleştirilir Diğer durumlarda, Sanal Özel Ağlar (VPN), Terminal Hizmetleri, istemci uygulaması protokolleri ya da (programlı olarak) Azure Service Management API (SMAPI) üzerinden şirket için sistemlerden Azure’a bağlantılar olabilir. Ayrıca, istemci uç noktaları ya da etki alanına katılmış veya yalıtılmış ve yönetilmeyen olabilir, tabletler veya akıllı telefonlar gibi.

Çoklu erişim ve yönetim özellikleri zengin seçenekler sunsa da, bu değişkenlik bulut dağıtımına önemli bir risk ekleyebilir. Yönetim eylemlerini yönetmek, izlemek ve denetlemek güç olabilir. Bu değişkenlik bulut hizmetlerini yönetmek için kullanılan istemci uç noktalarına düzenlenmemiş erişim aracılığıyla güvenlik tehditlerine neden olabilir. Altyapı geliştirme ve yönetme amacıyla genel ya da kişisel iş istasyonlarını kullanmak, web’e gözatma (örneğin, su kaynağı saldırıları) ya da e-posta (örneğin, sosyal mühendislik ve kimlik avı) gibi öngörülemeyen tehdit vektörlerini açar.

![](./media/management/typical-management-network-topology.png)

Çok değişken uç noktalardan Azure arabirimlerine (SMAPI gibi) erişimi uygun şekilde yönetmek üzere güvenlik ilkeleri ve mekanizmaları oluşturmayı zorlaştırdığından, saldırı potansiyeli bu tür ortamlarda artar.

### <a name="remote-management-threats"></a>Uzaktan yönetim tehditleri
Saldırganlar genellikle hesap kimlik bilgilerinin gizliliğini ihlal ederek (örneğin, parolanıza deneme yanılma yapma, kimlik avı ve kimlik bilgisi toplama yoluyla) ya da kullanıcıları zararlı kod kullanmak üzere kandırarak (örneğin, yönlendirmeli indirme içeren zararlı web sitelerinden ya da zararlı e-posta eklerinden) ayrıcalıklı erişim elde etmeye çalışır. Uzaktan yönetilen bulut ortamında, hesap ihlalleri her yerden, her zaman erişim nedeniyle riskin artmasına yol açabilir.

Birincil yönetici hesaplarında sıkı denetimlerle dahi, düşük düzey kullanıcı hesapları birinin güvenlik stratejisindeki zayıflıklardan yararlanmak için kullanılabilir. Uygun güvenlik eğitimi olmaması da hesap bilgilerinin yanlışlıkla açıklanması veya ortaya çıkması yoluyla ihlallere neden olabilir.

Bir kullanıcı iş istasyonu yönetim görevleri için kullanıldığında, birçok farklı noktada tehlikeye girebilir. Bir kullanıcının web’e gözatması, 3. taraf ve açık kaynaklı araçları kullanması veya truva atı içeren zararlı bir belge açması fark etmez.

Genel olarak, masaüstü bilgisayarlarda, veri ihlalleriyle sonuçlanan çoğu hedefli saldırı tarayıcı açıkları, eklenti açıkları (Flash, PDF, Java gibi) ve kimlik avı (e-posta) saldırılarıdır. Bu makineler, geliştirme veya diğer varlıklar için kullanıldığında dinamik sunuculara ya da işlemler için ağ cihazlarında erişim amacıyla yönetim düzeyi veya hizmet düzeyi izinlere sahip olabilir.

### <a name="operational-security-fundamentals"></a>İşlem güvenliği temelleri
Daha güvenli yönetim ve işlemler için giriş noktası sayısını azaltarak istemcinin saldırı yüzeyini en aza indirebilirsiniz. Bu, güvenlik ilkeleri aracılığıyla yapılabilir: “görevler ayrımı” ve “ortamların ayrımı”.

Bir düzeydeki hatanın başka birinde ihlale yol açması olasılığını azaltmak için birindeki hassas işlevleri diğerinden yalıtın. Örnekler:

* Yönetim görevleri bir tehlikeye yol açabilecek etkinliklerle birleştirilmemelidir (örneğin, bir yöneticinin e-postasındaki kötü amaçlı yazılımın altyapı sunucusuna bulaşması).
* Yüksek duyarlılık işlemleri için kullanılan bir iş istasyonu İnternet’e gözatma gibi yüksek riskli amacıyla kullanılanla aynı sistem olmamalıdır.

Gereksiz yazılımları kaldırarak sistemin saldırı yüzeyini azaltın. Örnek:

* Standart yönetim, destek ya da geliştirme iş istasyonu, cihazın ana amacı bulut hizmetlerini yönetmek ise, bir e-posta istemcisi ya da diğer üretim uygulamalarının yüklenmesini gerektirmemelidir.

Altyapı bileşenlerine yönetici erişimine sahip istemci sistemleri güvenlik risklerini azaltmak amacıyla en sıkı olası ilkelere tabi olmalıdır. Örnekler:

* Güvenlik ilkeleri cihazdan açık İnternet erişimini reddeden Grup İlkesi ayarları ve kısıtlayıcı güvenlik duvarı yapılandırması kullanımını içerebilir.
* Doğrudan erişim gerekiyorsa, Internet Protokolü Güvenliği (IPsec) VPN’leri kullanın.
* Ayır yönetim ve geliştirme Active Directory etki alanları yapılandırın.
* Yönetim iş istasyonu ağ trafiğini yalıtın ve filtreleyin.
* Kötü amaçlı yazılımdan korunma yazılımı kullanın.
* Çalınan kimlik bilgileri riskini azaltmak için multi-factor authentication uygulayın.

Erişim kaynaklarını sağlamlaştırmak ve yönetilmeyen uç noktaları ortadan kaldırmak da yönetim görevlerini kolaylaştırır.

### <a name="providing-security-for-azure-remote-management"></a>Azure remote management için güvenlik sağlama
Azure, Azure bulut hizmetlerini ve sanal makineleri yöneten yöneticilere yardım etmek amacıyla güvenlik mekanizmaları sağlar. Bu mekanizmalar şunları:

* Kimlik doğrulama ve [rol tabanlı erişim denetimi](../../role-based-access-control/role-assignments-portal.md).
* İzleme, günlük kaydı ve denetim.
* Sertifikalar ve şifreli iletişim.
* Web yönetim portalı.
* Ağ paketi filtreleme.

İstemci tarafı güvenlik yapılandırması ve yönetim ağ geçidinin veri merkezi dağıtımı ile, bulut uygulamalarına ve verilerine yönetici erişimini kısıtlamak ve izlemek mümkündür.

> [!NOTE]
> Bu makaledeki bazı öneriler artan veri, ağ ya da işlem kaynağı kullanımına neden olabilir ve lisans ya da abonelik maliyetlerinizi artırabilir.
>
>

## <a name="hardened-workstation-for-management"></a>Yönetim için sağlamlaştırılmış iş istasyonu
Bir iş istasyonunu sağlamlaştırmanın amacı, potansiyel saldırı yüzeyini mümkün olduğunca küçülterek çalışması için gerekli olan en kritik işlevler haricindekileri ortadan kaldırmaktır. Sistem sağlamlaştırma, yüklü hizmetlerin ve uygulamaların sayısını en aza indirme, uygulama yürütmeyi sınırlama, ağ erişimini yalnızca gerekli olanla kısıtlama ve sistemi her zaman güncel tutmayı içerir. Ayrıca, yönetim için sağlamlaştırılmış bir iş istasyonu kullanmak yönetim araçlarını ve etkinlikleri diğer son kullanıcı görevlerinden ayırır.

Şirket içi kurumsal ortam ile, ayrılmış yönetim ağları, kart erişimli sunucu odaları ve ağın korumalı bölgelerinde çalışan iş istasyonları aracılığıyla fiziksel altyapınızın saldırı yüzeyini sınırlayabilirsiniz. Bulut ya da karma BT modelinde, güvenli yönetim hizmetleri hakkında dikkatli olmak BT kaynaklarına fiziksel erişim eksiği nedeniyle daha karmaşık olabilir. Koruma çözümleri uygulamak dikkatli yazılım yapılandırma, güvenlik odaklı işlemler ve kapsamlı ilkeler gerektirir.

Bulut yönetimi ve ayrıca uygulama geliştirme için kilitli bir iş istasyonunda en az ayrıcalıklı, en aza indirilmiş yazılım ayak izi kullanmak, uzaktan yönetim ve geliştirme ortamlarını standart hale getirerek güvenlik olayları riskini azaltabilir. Sağlamlaştırılmış bir istasyonu yapılandırması kötü amaçlı yazılımlar ve açıklar tarafından kullanılan birçok ortak yolu kapatarak kritik bulut kaynaklarını yönetmek için kullanılan hesapların tehlikeye düşmesini engellemeye yardımcı olabilir. Özellikle, e-posta ve İnternet’e gözatma dahil istemci sistemi davranışlarını denetlemek ve yalıtmak ve tehditleri azaltmak için [Windows AppLocker](https://technet.microsoft.com/library/dd759117.aspx) ve Hyper-V teknolojisi kullanabilirsiniz.

Sağlamlaştırılmış bir iş istasyonunda, yönetici standart kullanıcı hesabı çalıştırır (yönetici düzeyi yürütmeyi engelleyen) ve ilişkili uygulamalar bir izin listesi tarafından denetlenir. Sağlamlaştırılmış bir iş istasyonunun temel öğeleri aşağıdaki gibidir:

* Etkin tarama ve düzeltme eki uygulama. Kötü amaçlı yazılımdan koruma yazılımı dağıtın, düzenli olarak normal güvenlik açığı taramaları gerçekleştirin ve en son güvenlik güncelleştirmesini vakitli şekilde kullanarak tüm iş istasyonlarını güncelleştirin.
* Sınırlı işlev. Gerekli olmayan uygulamaları kaldırın ve gereksiz hizmetleri (başlangıç) devre dışı bırakın.
* Ağ sağlamlaştırma. Yalnızca Azure yönetimi ile ilgili geçerli IP adresleri, bağlantı noktaları ve URL'lere izin vermek için Windows Güvenlik Duvarı kurallarını kullanın. İş istasyonuna gelen uzak bağlantıların engellendiğinden emin olun.
* Yürütme kısıtlama. Yalnızca yürütmek üzere yönetime gerekli olarak önceden tanımlanmış yürütülebilir dosyalar kümesine izin verin (“varsayılan ret” olarak bilinir). Varsayılan olarak, açıkça izin verilenler listesinde tanımlı olmadığı sürece kullanıcılara herhangi bir programı çalıştırma izni reddedilmelidir.
* En düşük öncelik. Yönetim iş istasyonu kullanıcıları yerel makinenin kendisinde yönetim ayrıcalıklarına sahip olmamalıdır. Bu şekilde, sistem yapılandırmasını veya sistem dosyalarını, kasıtlı veya kasıtsız olarak değiştiremezler.

Bunların tümünü, Active Directory Domain Services’da (AD DS) [Grup İlkesi Nesneleri](../../active-directory-domain-services/manage-group-policy.md) (GPO) kullanarak ve bunları tüm yönetim hesaplarınıza (yerel) yönetim etki alanınız aracılığıyla uygulayarak gerçekleştirebilirsiniz.

### <a name="managing-services-applications-and-data"></a>Hizmetleri, uygulamaları ve verileri yönetme
Azure bulut hizmetleri yapılandırması Azure portal ya da SMAPI üzerinden, Windows PowerShell komut satırı arabirimi veya bu RESTful arabirimlerinden yararlanan özel olarak geliştirilmiş uygulama aracılığıyla gerçekleştirilir. Bu mekanizmaları kullanan hizmetler Azure Active Directory (Azure AD), Azure Storage, Azure Websites ve Azure Virtual Network’ü içerir.

Sanal Makine–dağıtılmış uygulamalar gerekliyse kendi istemci araçlarını ve arabirimlerini sağlar, örneğin, kurumsal bir yönetim konsolu olarak Microsoft Yönetim Konsolu (MMC) (Microsoft System Center ya da Windows Intune gibi) ya da başka bir yönetim uygulaması (örneğin, Microsoft SQL Server Management Studio). Bu araçlar, genellikle bir kurumsal ortamda veya istemci ağında bulunur. Bunlar, doğrudan, durum bilgisi olan bağlantılar gerektiren, Uzak Masaüstü Protokolü (RDP) gibi belirli ağ protokollerine bağlı olabilir. Bazıları açık yayımlanmaması ya da İnternet aracılığıyla erişilmemesi gereken web etkin arabirimlere sahip olabilir.

[Multi-factor authentication](/azure/active-directory/authentication/multi-factor-authentication), [X.509 yönetim sertifikaları](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) ve güvenlik duvarları kullanarak Azure’da altyapı ve platform hizmetlerine erişimi kısıtlayabilirsiniz. Azure portal ve SMAPI Aktarım Katmanı Güvenliği (TLS) gerektirir. Ancak, Azure’a dağıttığınız hizmetler ve uygulamalar uygulamanıza dayalı uygun koruma önlemleri almanızı gerektirir. Bu mekanizmalar standart sağlamlaştırılmış iş istasyonu konfigürasyonuyla sık sık daha kolay etkinleştirilebilir.

### <a name="management-gateway"></a>Yönetimi ağ geçidi
Tüm yönetim erişimini merkezileştirmek ve izlemeyi ve günlüğe kaydetmeyi basitleştirmek için, Azure ortamınıza bağlı, şirket için ağınızdaki ayrılmış bir [Uzak Masaüstü Ağ Geçidi](https://technet.microsoft.com/library/dd560672) (RD Ağ Geçidi) sunucusu dağıtabilirsiniz.

Uzak Masaüstü Ağ geçidi, güvenlik gereksinimlerini uygulayan ilke tabanlı bir RDP proxy hizmetidir. Windows Server Network Access Protection ile RD Ağ Geçidi uygulamak yalnızca Active Directory Etki Alanı Hizmetleri (AD DS) Grup İlkesi Nesneleri (GPO'lar) tarafından oluşturulan belirli güvenlik durumu ölçütlerini karşılayan istemcilerin bağlanabilmesinin sağlanmasına yardımcı olur. Buna ek olarak:

* Azure portalına erişimine izin verilen tek ana bilgisayar olacak şekilde, RD Ağ Geçidi’nde [Azure yönetim sertifikası](https://msdn.microsoft.com/library/azure/gg551722.aspx) sağlayın.
* RD Ağ Geçidi’ni yönetici iş istasyonları olarak aynı [yönetim etki alanına](https://technet.microsoft.com/library/bb727085.aspx) ekleyin. Bu, Azure AD’ye tek yön trust sahibi olan bir etki alanında siteden siteye IPsec VPN ya da ExpressRoute kullanırken ya da şirket için AD DS örneği ve Azure AD’niz arasında kimlik bilgilerini birleştirirken gereklidir.
* RD Ağ Geçidi’nin istemci makinesi adının geçerli (etki alanına katılmış) ve Azure portalına erişimine izin verilmiş olduğunu doğrulamasına izin vermek için, bir [istemci bağlantısı yetkilendirme ilkesi](https://technet.microsoft.com/library/cc753324.aspx) yapılandırın.
* Yönetim trafiğini ileri gizli dinleme ve belirteç hırsızlığından korumak üzere [Azure VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) için IPsec kullanın ya da [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) aracılığıyla yalıtılmış bir İnternet bağlantısını dikkate alın.
* RD Ağ Geçidi üzerinden oturum açan yöneticiler için multi-factor authentication ([Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication) aracılığıyla) ya da akıllı kart kimlik doğrulamasını etkinleştirin.
* İzin verilen yönetim uç noktalarının sayısını en aza indirmek için Azure’da [IP adresi kısıtlamaları](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) ya da [Ağ Güvenlik Grupları](/azure/virtual-network/security-overview) yapılandırın.

## <a name="security-guidelines"></a>Güvenlik yönergeleri
Genel olarak, bulutla kullanıma yönelik olarak yönetici iş istasyonlarının güvenliğini sağlamaya yardımcı olmak, şirket içi iş istasyonları için kullanılan uygulamalara benzerdir. Örneğin, en aza indirilmiş yapı ve kısıtlayıcı izinler. Bulut yönetiminin bazı benzersiz yönleri uzaktan ya da bant dışı kurumsal yönetime daha yakındır. Bunlar kimlik bilgilerini, gelişmiş güvenlikli uzaktan erişimin ve tehdit algılama ve yanıtın denetlenmesini içerir.

### <a name="authentication"></a>Kimlik Doğrulaması
Yönetim araçlarına ve denetim erişim isteklerine erişim için kaynak IP adreslerini sınırlamak amacıyla Azure oturum açma kısıtlamalarını kullanabilirsiniz. Azure’un yönetim istemcilerini (iş istasyonları ve/veya uygulamalar) tanımlamasına yardımcı olmak için, SSL sertifikalarının yanı sıra, istemci tarafı yönetim sertifikalarının yüklenmesini gerekli kılmaya yönelik olarak SMAPI (Windows PowerShell cmdlet’leri gibi müşteri tarafından geliştirilen araçlar aracılığıyla) ve Azure portalını yapılandırabilirsiniz. Yönetici erişiminin multi-factor authentication gerektirmesine de öneriyoruz.

Azure’a dağıttığınız bazı uygulamalar veya hizmetler hem son kullanıcı hem de yönetici erişimi için kendi kimlik doğrulama mekanizmalarına sahip olabilirken, diğerleri Azure AD’den faydalanabilir. Kimlik bilgilerini Active Directory Federasyon Hizmetleri (AD FS) aracılığıyla birleştirip birleştirmemenize bağlı olarak, dizin eşitlemeyi kullanmak veya bulutta yalnızca kullanıcı hesaplarını tutmak, [Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx) (Azure AD Premium’un parçası) kullanmak kaynaklar arasında kimlik yaşam döngülerini yönetmenize yardımcı olur.

### <a name="connectivity"></a>Bağlantı
Azure sanal ağlarınıza güvenli istemci bağlantılarına yardımcı olmak için çeşitli mekanizmalar kullanılabilir. Bu mekanizmaların ikisi,endüstri standardı IPsec (S2S) kullanım sağlayan [siteden siteye VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) ve [noktadan siteye VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) (P2S) ya da şifreleme ve tünel oluşturma için [Güvenli Yuva Tünel Protokolü](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx)’dür. (SSTP) (P2S). Azure, Azure portalı gibi genel kullanıma yönelik Azure hizmetleri yönetimine bağlandığında, Azure Güvenli Köprü Metni Aktarım Protokolü (HTTPS) gerektirir.

Azure’a RD ağ geçidi üzerinden bağlanmayan sağlamlaştırılmış bir iş istasyonu Azure Virtual Network’e ilk bağlantıyı oluşturmak için SSTP tabanlı noktadan siteye VPN kullanmalı ve ardından VPN tünelinden ayrı sanal makinelere RDP bağlantısı oluşturmalıdır.

### <a name="management-auditing-vs-policy-enforcement"></a>Yönetim denetimi ilke uygulama karşılaştırması
Genellikle, güvenli yönetim işlemlerine yardımcı olan iki yaklaşım vardır: denetim ve ilke uygulama. İkisini de uygulamak kapsamlı denetimler sağlasa da bu, her durumda mümkün olmayabilir. Ayrıca, her ikisi de özellikle hem kişiler hem de sistem yapılarındaki güven düzeyiyle ilgili olduğundan, güvenlik yönetimiyle ilgili farklı düzeylerde risk, maliyet ve çaba söz konusudur.

İzleme, günlüğe kaydetme ve denetim yönetim etkinliklerini takip etme ve anlamaya ilişkin bir temel sunar ancak, oluşturulan veri miktarı nedeniyle tüm eylemlerin her ayrıntısının denetlenmesi uygulanabilir olmayabilir. Ancak, yönetim ilkelerinin verimliliğini denetlemek en iyi uygulamadır.

Sıkı erişim denetimleri içeren ilke uygulama yönetici eylemlerini yönetebilecek programlı bir mekanizma oluşturur ve olası tüm koruma önlemlerinin kullanılmasını sağlar. Günlük kaydı kimin neyi, nerede ve ne zaman yaptığının yanı sıra uygulama kanıtı sağlar. Günlük kaydı ayrıca yöneticilerin ilkeleri nasıl izlediğine ilişkin bilgilerin denetimini ve karşılaştırmasını yapmanızı sağlar ve etkinliklere dair kanıt sunar.

## <a name="client-configuration"></a>İstemci yapılandırması
Sağlamlaştırılmış iş istasyonu için üç temel yapılandırma öneririz. Bunlar arasındaki en büyük fark, tüm seçeneklerde benzer güvenlik profili sağlarken, maliyet, kullanılabilirlik ve erişilebilirliktir. Aşağıdaki tabloda her birinin avantajları ve risklerinin kısa bir çözümlemesini sağlar. (“kurumsal PC” ifadesinin, rollerden bağımsız olarak, tüm etki alanı kullanıcıları için dağıtılabilecek standart masaüstü PC yapılandırması anlamına geldiğini unutmayın.)

| Yapılandırma | Avantajlar | Simgeler |
| --- | --- | --- |
| Tek başına sağlamlaştırılmış iş istasyonu |Sıkı denetlenen iş istasyonu |ayrılmış masaüstü bilgisayarlar için daha yüksek maliyet |
| - | Azaltılmış uygulama açıkları riski |Artan yönetim çabası |
| - | Net görev ayrımları | - |
| Sanal makine olarak kurumsal PC |Sınırlı donanım maliyetleri | - |
| - | Rol ve uygulamaların ayrımı | - |
| BitLocker Sürücü Şifrelemesi ile Windows to go |Çoğu PC ile uyumluluk |Varlık izleme |
| - | Düşük maliyet ve taşınabilirlik | - |
| - | Yalıtılmış yönetim ortamı |- |

Sağlamlaştırılmış is istasyonunun, ana bilgisayar işletim sistemi ile donanım arasında bir şey olmadan, konuk değil ana bilgisayar olması önemlidir. “Temiz kaynak ilkesi”ni (“güvenli kaynak” olarak da bilinir) izleme ana bilgisayarın en fazla sağlamlaştırılmış olması gereken olduğu anlamına gelir. Aksi takdirde, sağlamlaştırılmış iş istasyonu (konuk) üzerinde barındırıldığı sistemde saldırılara maruz kalır.

Gerekli görevler için özel yerel AD DS GPO’larıyla, seçilen Azure ve bulut uygulamalarını yönetmek için yalnızca gereken araçlar ve izinlere sahip olan her bir sağlamlaştırılmış iş istasyonu için ayrılmış sistem görüntüleri üzerinden yönetim işlevlerini daha da ayırabilirsiniz.

Şirket içi altyapısı bulunmaya BT ortamları için (örneği, tüm sunucular bulutta olduğundan GPO’lar için yerel bir AD DS örneğine erişimi olmayan), [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) gibi bir hizmet iş istasyonu dağıtmayı ve bakımını kolay hale getirebilir.

### <a name="stand-alone-hardened-workstation-for-management"></a>Yönetim için tek başına sağlamlaştırılmış iş istasyonu
Tek başına sağlamlaştırılmış iş istasyonu ile, yöneticiler yönetim görevleri için kullandıkları bir PC ya da dizüstü bilgisayara ve yönetim dışı görevler için kullandıkları ayrı PC ya da dizüstü bilgisayara sahiptir. Azure hizmetlerinizi yönetmeye ayrılmış bir iş istasyonu diğer uygulamaların yüklenmesini gerektirmez. Ayrıca [Güvenilir Platform Modülü](https://technet.microsoft.com/library/cc766159) (TPM) veya benzeri donanım düzeyinde şifreleme teknolojisi yardımcılarını destekleyen iş istasyonları kullanma, cihaz kimlik doğrulaması ve belirli saldırıların önlenmesi konusunda yardımcı olur. TPM ayrıca [BitLocker Sürücü Şifrelemesi](https://technet.microsoft.com/library/cc732774.aspx) kullanarak sistem sürücüsünün tam birim korumasını destekler.

(Aşağıda gösterilen) tek başına sağlamlaştırılmış iş istasyonu senaryosunda, Windows Güvenlik Duvarı (veya Microsoft dışı istemci güvenlik duvarı) RDP gibi, gelen bağlantıları engellemek üzere yapılandırılmıştır. Yönetici sağlamlaştırılmış iş istasyonunda oturum açabilir ve VPN Azure Sanal Ağı ile VPN bağlantısı oluşturduktan sonra Azure’a bağlanan bir RDP oturumu başlatabilir, kurumsal PC’de oturum açamaz ve sağlamlaştırılmış iş istasyonunun kendisine bağlanmak için RDP kullanamaz.

![](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Sanal makine olarak kurumsal PC
Ayrı bir tek başına sağlamlaştırılmış iş istasyonunun engelleyici ya da kullanışsız maliyeti olması durumunda, sağlamlaştırılmış is istasyonu yönetim dışı görevleri gerçekleştirmek için bir sanal makine barındırabilir.

![](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Sistem Yönetimi ve diğer günlük iş görevleri için bir iş istasyonu kullanarak oluşabilecek çeşitli güvenlik risklerini önlemek için, sağlamlaştırılmış iş istasyonuna Windows Hyper-V sanal makinesi dağıtabilirsiniz. Bu sanal makine kurumsal PC olarak kullanılabilir. Kurumsal PC ortamı, saldırı yüzeyini azaltan ve önemli yönetim görevleri ile birlikte bulunan kullanıcının günlük etkinliklerini (örneğin, e-posta) kaldıran, Ana Bilgisayardan yalıtılmış durumda kalabilir.

Kurumsal PC sanal makinesi korumalı alanda çalışır ve kullanıcı uygulamaları sağlar. Ana bilgisayar "temiz kaynak" olarak kalır ve kök işletim sisteminde katı ağ ilkeleri uygular (örneğin, sanal makineden RDP erişimini engelleme).

### <a name="windows-to-go"></a>Windows To Go
Tek başına sağlamlaştırılmış iş istasyonu gerekli kılmanın başka bir alternatifi, istemci tarafı USB önyükleme özelliğini destekleyen bir özellik olan[Windows To Go](https://technet.microsoft.com/library/hh831833.aspx) kullanmaktır. Windows To Go kullanıcıların şifrelenmiş bir USB flash sürücüde çalışan yalıtılmış bir sistem görüntüsüne uyumlu bir PC önyüklemesine olanak sağlar. Görüntü, sıkı güvenlik ilkeleri, minimum işletim sistemi yapısı ve TPM desteğiyle kurumsal BT grubu tarafından tam olarak yönetilebildiğinden, uzaktan yönetin uç noktaları için ek denetimler sağlar.

Aşağıdaki çizimde, taşınabilir görüntü yalnızca Azure’a bağlanmak için önceden yapılandırılmış etki alanına katılmış sistemdir, multi-factor authentication gerektirir ve tüm yönetim dışı trafiği engeller. Bir kullanıcı aynı PC’yi standart kurumsal görüntüye önyükler ve Azure yönetim araçları için RD Ağ Geçidine erişmeye çalışırsa, oturum engellenir. Windows To Go kök düzeyinde işletim sistemi haline gelir ve dış saldırılara karşı daha savunmasız olabilecek ek katman gerekmez (ana bilgisayar işletim sistemi, hiper yönetici, sanal makine).

![](./media/management/hardened-workstation-using-windows-to-go-on-a-usb-flash-drive.png)

USB flash sürücülerin ortalama bir masaüstü bilgisayara göre daha kolay kaybolduğuna dikkat etmek önemlidir. Birimin tamamını, güçlü bir parola ile şifrelemek için BitLocker kullanmak, bir saldırganın zararlı amaçlar için sürücü görüntüsü kullanabilme olasılığını düşürür. Ayrıca, USB flash sürücü kaybolursa, hızlı parola sıfırlama ile iptal etme ve [yeni bir yönetim sertifikası verme](https://technet.microsoft.com/library/hh831574.aspx) tehlikeye maruz kalmayı azaltabilir. Yönetim denetim günlüklerinin, istemcide değil, Azure’da bulunması da potansiyel veri kayıplarını azaltır.

## <a name="best-practices"></a>En iyi uygulamalar
Azure’da uygulama ve veri yönetirken aşağıdaki ek yönergeleri dikkate alın.

### <a name="dos-and-donts"></a>Yapılması gerekenler ve yapılmaması gerekenler
Bir iş istasyonu kilitlenmiş olduğu için diğer genel güvenlik gereksinimlerinin karşılanmasının gerekmediği varsayımında bulunmayın. Riski yönetici hesaplarının genellikle sahip olduğu yükseltilmiş erişim düzeyleri nedeniyle potansiyel risk daha yüksektir. Risk örnekleri ve bunların alternatif güvenli uygulamaları aşağıdaki tabloda gösterilmiştir.

| Yapmayın | Yapın |
| --- | --- |
| Yönetici erişimine ilişkin kimlik bilgilerini veya diğer parolaları (ör. SSL veya yönetim sertifikaları) e-posta ile göndermeyin |Hesap adlarını ve parolaları sesli olarak ileterek gizliliği koruyun (ancak bunları sesli posta olarak depolamayın), istemci/sunucu sertifikalarının uzaktan yüklemesini gerçekleştirin (şifreli oturum yoluyla), korumalı ağ paylaşımından indirin ya da taşınabilir medya kullanarak el ile dağıtın. |
| - | Yaşam döngüsü yönetimi sertifikanızı proaktif olarak yönetin. |
| Hesap parolalarını şifrelenmemiş ya da karma olmayan uygulama depolama biriminde depolamayın (örneğin, elektronik tablolarda, SharePoint sitelerinde ya da dosya paylaşımlarında). |Güvenlik yönetim ilkeleri ve sistem sağlamlaştırma ilkeleri oluşturun ve bunları geliştirme ortamınıza uygulayın. |
| - | Azure SSL/TLS sitelerine uygun erişim sağlamak için [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) sertifikası sabitleme kuralları kullanın. |
| Hesapları ve parolaları yöneticiler arasında paylaşmayın veya parolaları birden fazla kullanıcı hesabında ya da hizmette yeniden kullanmayın, özellikle sosyal medya ve diğer yönetim dışı etkinlikler için olanları. |Azure aboneliğinizi yönetmek üzere, kişisel e-postalar için kullanılmayan bir hesap olarak, ayrılmış bir Microsoft hesabı oluşturun. |
| Yapılandırma dosyalarını e-posta ile göndermeyin. |Yapılandırma dosyaları ve profilleri, e-posta gibi kolayca tehlikeye girebilecek bir mekanizmadan değil, güvenilir bir kaynaktan yüklenmelidir (örneğin, şifrelenmiş bir USB flash sürücü). |
| Zayıf veya basit oturum açma parolaları kullanmayın. |Güçlü parola ilkeleri, sona erme döngüleri (ilk kullanımda değiştirme), konsol zaman aşımları ve otomatik hesap kilitlemeleri uygulayın. Parola kasa erişimi için multi-factor authentication ile istemci parola yönetimi sistemi kullanın. |
| Yönetim bağlantı noktalarını İnternet’e sunmayın. |Yönetim erişimini kısıtlamak için Azure bağlantı noktalarını ve IP adreslerini kilitleyin. Daha fazla bilgi edinmek için [Azure Ağ Güvenliği](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) teknik incelemesine bakın. |
| - | Tüm yönetim bağlantıları için güvenlik duvarları, VPN’ler ve NAP kullanın. |

## <a name="azure-operations"></a>Azure işlemleri
Microsoft’un Azure işlemleri çerçevesinde, Azure’un üretim sistemlerine erişimi olan işlem mühendisleri ve destek personeli iç kurumsal ağ erişimi ve uygulamaları için (e-posta, intranet gibi) kendilerine sağlanan [sanal makineler ile sağlamlaştırılmış iş istasyonu bilgisayarları](#stand-alone-hardened-workstation-for-management) kullanır. Tüm yönetim iş istasyonu bilgisayarlarında TPM’ler bulunur, ana bilgisayar önyükleme sürücüsü BitLocker ile şifrelenmiştir ve bunlar Microsoft’un birincil kurumsal etki alanındaki özel bir kurumsal birimde (OU) birleştirilmiştir.

Sistem sağlamlaştırma merkezileştirilmiş yazılım güncelleştirme ile Grup İlkesi aracılığıyla uygulanır. Denetim ve çözümleme için, olay günlükleri (örneğin, güvenlik ve AppLocker) yönetim istasyonlarından toplanır ve merkezi bir konuma kaydedilir.

Ayrıca, Azure’un üretim ağına bağlanmak için Microsoft ağında iki öğeli kimlik doğrulama gerektiren ayrılmış jump-box’lar kullanılır.

## <a name="azure-security-checklist"></a>Azure güvenlik denetim listesi
Sağlamlaştırılmış bir iş istasyonunda yöneticilerin gerçekleştirebileceği görev sayısını en aza indirmek, geliştirme ve yönetim ortamınızdaki saldırı yüzeyini en aza indirmenize yardımcı olur. Sağlamlaştırılmış iş istasyonunuzu korumaya yardımcı olması için aşağıdaki teknolojileri kullanın:

* IE sağlamlaştırma. Internet Explorer tarayıcı (bu bağlamda herhangi bir web tarayıcısı) dış sunucularla olan yoğun etkileşimleri nedeniyle zararlı bir kod için temel giriş noktasıdır. İstemci ilkelerinizi inceleyin ve korumalı modda çalışma, eklentileri devre dışı bırakma, dosya yüklemelerini devre dışı bırakma ve [Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx) filtrelemesi seçeneklerini kullanın. Güvenlik uyarılarının göründüğünden emin olun. İnternet bölgelerinden faydalanın ve makul sağlamlaştırma yapılandırdığınız güveniliri siteler için bir listesini oluşturun. Tüm diğer siteleri ve ActiveX ve Java gibi tarayıcı içi kodları engelleyin.
* Standart kullanıcı. Standart kullanıcı olarak çalıştırmanın getirdiği bir çok avantaj vardır; bunlardan en büyüğü kötü amaçlı yazılımı aracılığıyla yönetici kimlik bilgilerinin çalınmasının daha zor olmasıdır. Buna ek olarak, standart kullanıcı hesabının kök işletim sisteminde yükseltilmiş ayrıcalıkları yoktur ve birçok yapılandırma seçeneği ve API’leri varsayılan olarak kilitlidir.
* AppLocker. Kullanıcıların çalıştırabileceği programları ve betikleri kısıtlamak için [AppLocker](https://technet.microsoft.com/library/ee619725.aspx) kullanabilirsiniz. AppLocker’ı denetim veya zorlama modunda çalıştırabilirsiniz. Varsayılan olarak, AppLocker istemcideki tüm kodları çalıştırmak üzere yönetici belirtecine sahip olan kullanıcıların bunu yapmasını mümkün kılan izin vermek kuralına sahiptir. Bu kural yöneticilerin kendilerini içeriye kilitlemelerini önlemek içindir ve yalnızca yükseltilmiş belirteçler için geçerlidir. Ayrıca Windows Server [çekirdek güvenlik](https://technet.microsoft.com/library/dd348705.aspx) parçası olarak Kod Bütünlüğü’ne bakın.
* Kod imzalama. Yöneticiler tarafından kullanılan tüm araçlar ve betiklerde kod imzalama uygulama kilitleme ilkeleri dağıtmak için yönetilebilir bir mekanizma sağlar. Karmalar koddaki hızlı değişimlerde ölçeklenmez ve dosya yolları yüksek düzeyde güvenlik sağlamaz. AppLocker kurallarını yalnızca özel imzalı kod ve betiklerin [yürütülmesine](https://technet.microsoft.com/library/ee176961.aspx) izin veren PowerShell [yürütme ilkesi](https://technet.microsoft.com/library/hh849812.aspx) ile birleştirmelisiniz.
* Grup İlkesi. Yönetim (ve diğer tüm kişilerden gelen erişimi engellemek) için kullanılan tüm etki alanı iş istasyonlarının yanı sıra, bu istasyonlarında kimlik doğrulaması yapılan kullanıcı hesaplarına uygulanan genel bir yönetim ilkesi oluşturun.
* Gelişmiş güvenlik sağlama. Kurcalanmaya karşı korumaya yardımcı olmak için taban çizgisi sağlamlaştırılmış iş istasyonunu görüntünüzü koruyun. Görüntüler, sanal makineler ve betikleri depolamak için şifreleme ve yalıtma gibi güvenlik önlemleri kullanın ve erişimi kısıtlayın (ya da denetlenebilir iade etme/kullanıma alma işlemi kullanın).
* Düzeltme eki uygulama. Tutarlı yapıyı koruyun (ya da geliştirme işlemleri ve diğer yönetim görevleri için ayrı görüntülere sahip olun), değişiklikler ve kötü amaçlı yazılımlara karşı düzenli tarama yapın, yapıyı güncel tutun ve makineleri yalnızca gerektiğinden etkinleştirin.
* Şifreleme. Daha güvenli bir şekilde etkin [Şifreleme Dosya Sistemi](https://technet.microsoft.com/library/cc700811.aspx) (EFS) ve BitLocker için yönetim iş istasyonlarının TPM içerdiğinden emin olun. Windows To Go kullanıyorsanız, yalnızca şifrelenmiş USB anahtarlarını BitLocker ile birlikte kullanın.
* İdare. Tüm yöneticilerin Windows arabirimleri için (dosya paylaşımı gibi) AD DS GPO’larını kullanın. Yönetim iş istasyonlarını denetim, izleme ve günlüğe kaydetme işlemlerine dahil edin. Tüm yönetici ve geliştirici erişimini ve kullanımını izleyin.

## <a name="summary"></a>Özet
Azure bulut hizmetlerinizi, Sanal Makinelerinizi ve uygulamalarınızı yönetmek için sağlamlaştırılmış iş istasyonları kullanmak kritik önemdeki BT altyapısını uzaktan yönetmenin getirebileceği çok sayıda riskten ve tehditten kaçınmanıza yardımcı olabilir. Azure ve Windows iletişimleri, kimlik doğrulamayı ve istemci davranışını korumaya ve denetlemeye yardımcı olması için uygulayabileceğiniz mekanizmalar sağlar.

## <a name="next-steps"></a>Sonraki adımlar
Azure ve ilgili Microsoft hizmetlerinin yanı sıra bu belgede başvurulan belirli öğeler hakkında daha genle bilgi aşağıdaki kaynaklarda verilmiştir:

* [Ayrıcalıklı Erişimi Güvenli Hale Getirme](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) – Azure yönetimi için güveni bir iş istasyonu tasarlama ve oluşturmaya ilişkin teknik ayrıntıları alın
* [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure) -Azure dokusunu koruyan Azure platformu özellikleri ve Azure üzerinde çalışan iş yükleri hakkında bilgi edinin
* [Microsoft Security Response Center](https://www.microsoft.com/msrc) --Azure ile ilgili sorunlar da dahil Microsoft güvenlik açıklarının bildirilebileceği veya [secure@microsoft.com](mailto:secure@microsoft.com) adresine e-posta yoluyla gönderilebileceği yer
