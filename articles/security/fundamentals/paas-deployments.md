---
title: Güvenli PaaS dağıtımları için en iyi yöntemler-Microsoft Azure
description: Azure 'da güvenli bulut uygulamaları tasarlama, derleme ve yönetmeye yönelik en iyi yöntemleri öğrenin ve PaaS 'nin ve diğer bulut hizmeti modelleriyle ilgili güvenlik avantajlarını anlayın.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: ddcf5a1df31b4b36e25b2522ada21deab19fe032
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159881"
---
# <a name="securing-paas-deployments"></a>PaaS dağıtımlarının güvenliğini sağlama

Bu makalede size yardımcı olacak bilgiler sunulmaktadır:

- Bulutta barındırma uygulamalarının güvenlik avantajlarını anlayın
- Hizmet olarak platform (PaaS) ve diğer bulut hizmeti modelleriyle karşı güvenlik avantajlarını değerlendirin
- Güvenlik odağınızı ağ merkezli bir kimlik merkezli çevre güvenlik yaklaşımına değiştirin
- Genel PaaS güvenliği en iyi yöntemler önerilerini uygulama

[Azure 'da güvenli uygulamalar geliştirmek](abstract-develop-secure-apps.md) , bulut için uygulama geliştirirken yazılım geliştirme yaşam döngüsünün her aşamasında göz önünde bulundurmanız gereken güvenlik soruları ve denetimleri için genel bir kılavuzdur.

## <a name="cloud-security-advantages"></a>Bulut güvenliği avantajları
Siz ve Microsoft arasındaki [sorumluluk](shared-responsibility.md) bölümünü anlamak önemlidir. Şirket içinde tüm yığınınızı sahiplerseniz, buluta geçtiğinizde Microsoft 'a bazı sorumluluklar aktarabilirsiniz.

[Bulutta olmasının güvenlik avantajları](shared-responsibility.md#cloud security advantages)vardır. Şirket içi bir ortamda kuruluşlar, saldırganların tüm katmanlarda güvenlik açıklarından yararlanabileceği bir ortam oluşturan sorumlulukların ve sınırlı kaynakların güvenlik altına yatırmasını sağlar.

Kuruluşlar, bir sağlayıcının bulut tabanlı güvenlik yeteneklerini ve bulut zekasını kullanarak tehdit algılamasını ve yanıt sürelerini iyileştirebilir.  Kuruluşlar bulut sağlayıcısına kaydırarak, güvenlik kaynaklarını ve bütçesini diğer iş öncelikleriyle yeniden ayırmaya olanak sağlayan daha fazla güvenlik kapsamı alabilir.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS bulut hizmeti modelinin güvenlik avantajları
Ayrıca, şirket içi Azure PaaS dağıtımının güvenlik avantajlarına göz atalım.

![PaaS 'nin güvenlik avantajları](./media/paas-deployments/advantages-of-paas.png)

Microsoft, Microsoft 'un ortak riskleri ve sorumlulukları azaltır. Microsoft bulutu sürekli olarak Microsoft tarafından izlendiğinden, saldırı zor olur. Saldırgan, Microsoft bulutunu hedef olarak ifade etmek için bir fikir vermez. Saldırgan büyük miktarda para ve kaynak içermiyorsa, saldırgan büyük olasılıkla başka bir hedefe geçebilir.  

Yığının ortasında, PaaS dağıtımı ve şirket içi arasında bir fark yoktur. Uygulama katmanında ve hesap ve erişim yönetimi katmanında benzer riskler de vardır. Bu makalenin sonraki adımlar bölümünde, bu riskleri ortadan kaldırmaya veya en aza indirmenizi sağlayacak en iyi yöntemlere kılavuzluk edeceğiz.

Veri yönetimi ve hak yönetimi yığınının en üstünde, anahtar yönetimi tarafından azaltılan bir risk üzerinde işlem yapabilirsiniz. (Anahtar yönetimi en iyi yöntemler kapsamında ele alınmıştır.) Anahtar Yönetimi ek bir sorumluluktur, ancak artık yönetmeniz gereken bir PaaS dağıtımında yer alan ve kaynakları anahtar yönetimine kaydırabilmeniz için daha fazla yönetim sağlayabilirsiniz.

Azure platformu, çeşitli ağ tabanlı teknolojiler kullanarak güçlü DDoS koruması da sağlar. Ancak, ağ tabanlı DDoS koruma yöntemlerinin tüm türleri bağlantı başına ve veri merkezi temelinde sınırlara sahiptir. Büyük DDoS saldırılarının etkilenmesinden kaçınmak için, Azure 'un temel bulut özelliğinden yararlanarak, DDoS saldırılarına karşı savunmaya hızlı ve otomatik olarak ölçeklenebilmenizi sağlayabilirsiniz. Bunu önerilen yöntemler makalelerinde nasıl yapabileceğiniz hakkında daha fazla ayrıntıya gideceğiz.

## <a name="modernizing-the-defenders-mindset"></a>Defender 'ın anlayış 'i modernleştiriliyor
PaaS dağıtımları, güvenlik için genel yaklaşımınıza bir kaydırma sağlar. Microsoft ile sorumluluğu paylaşmak için her şeyi denetlemek zorunda kalmanızın ardından kaydırma yapabilirsiniz.

PaaS ve geleneksel şirket içi dağıtımlar arasındaki diğer önemli fark, birincil güvenlik kuşağını tanımlayan yeni bir görünümüdür. Geçmişte, birincil şirket içi güvenlik çevresi ağınız ve şirket içi güvenlik tasarımlarınızın çoğu, ağı birincil güvenlik özeti olarak kullanır. PaaS dağıtımları için, kimliği birincil güvenlik çevresi olacak şekilde düşünürken daha iyi bir şekilde sunulur.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Birincil güvenlik çevresi olarak kimlik ilkesini benimseyin
Bulut bilgi işlemin beş temel özelliklerinden biri, ağ merkezli daha az ilgili düşünmeyi sağlayan geniş ağ erişimlerinden biridir. Bulut bilgi işlemin büyük bölümü, kullanıcıların konumdan bağımsız olarak kaynaklara erişmesine izin sağlamaktır. Çoğu kullanıcı için, konumları Internet 'te bir yerde olacaktır.

Aşağıdaki şekilde, güvenlik kuşağının bir ağ çevre öğesinden bir kimlik çevre 'e nasıl gelişdiğini gösterilmektedir. Güvenlik, ağınızı erteleme ve verilerinizi erteleme ve uygulamalarınızın ve kullanıcılarınızın güvenliğini yönetme hakkında daha az hale gelir. Temel fark, şirketinizde önemli olan güvenliğe daha yakın bir şekilde gönderim yapmak isteşeydir.

![Yeni güvenlik çevre birimi olarak kimlik](./media/paas-deployments/identity-perimeter.png)

Başlangıçta, Azure PaaS hizmetleri (örneğin, Web rolleri ve Azure SQL) çok az veya geleneksel bir ağ çevre savunmaları sağladı. Öğenin amacının Internet 'e (Web rolü) sunulduğunu ve kimlik doğrulamasının yeni çevre (örneğin, BLOB veya Azure SQL) sağladığını anladı.

Modern güvenlik uygulamaları, kuşağın ağ çevre 'e ihlal olduğunu varsayar. Bu nedenle, modern savunma uygulamaları kimliğe taşınmıştır. Kuruluşlar, güçlü kimlik doğrulaması ve yetkilendirme durumu (en iyi yöntemler) ile kimlik tabanlı bir güvenlik çevresi sağlamalıdır.

Ağ çevre ilkeleri ve desenleri, Decades için kullanılabilir. Buna karşılık, sektörde birincil güvenlik çevre birimi olarak kimlik kullanımı görece daha az deneyimi vardır. Bu şekilde, alanında kanıtlanmış ve neredeyse tüm PaaS hizmetlerine uygulanan bazı genel öneriler sağlamak için yeterli deneyim sunuyoruz.

Kimlik kuşağını yönetmeye yönelik en iyi yöntemler aşağıda verilmiştir.

**En iyi yöntem**: PaaS dağıtımınızın güvenliğini sağlamak için Anahtarlarınızı ve kimlik bilgilerinizi güvenli hale getirin.   
**Ayrıntı**: anahtarların ve kimlik bilgilerinin kaybolması yaygın bir sorundur. Anahtar ve parolaların donanım güvenlik modüllerinde (HSM 'ler) depolanabileceği merkezi bir çözüm kullanabilirsiniz. [Azure Key Vault](../../key-vault/key-vault-overview.md) , kimlik doğrulama anahtarlarını, depolama hesabı anahtarlarını, veri şifreleme anahtarlarını,. pfx dosyalarını ve parolaları HSM 'ler tarafından korunan anahtarları kullanarak şifreleyerek, anahtarlarınızı ve gizli dizileri korur.

**En iyi yöntem**: kimlik bilgilerini ve diğer gizli dizileri kaynak kodunda veya GitHub 'da yerleştirmeyin.   
**Ayrıntı**: Anahtarlarınızı ve kimlik bilgilerinizi kaybetmekten daha kötü olan tek şey, yetkisiz bir tarafın bunlara erişmesini sağlayabilir. Saldırganlar, GitHub gibi kod depolarında depolanan anahtar ve gizli dizileri bulmak için bot teknolojisinden yararlanabilir. Bu genel kod depolarında anahtar ve gizli dizileri yerleştirmeyin.

**En iyi yöntem**: Bu VM 'leri doğrudan uzaktan yönetmenizi sağlayan bir yönetim arabirimi kullanarak, karma PaaS ve IaaS hizmetlerindeki VM yönetimi arabirimlerinizi koruyun.   
**Ayrıntı**: [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)ve [PowerShell uzaktan iletişim](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) gibi uzak yönetim protokolleri kullanılabilir. Genel olarak, internet 'ten VM 'lere doğrudan uzaktan erişimi etkinleştirmenizi öneririz.

Mümkünse, bir Azure sanal ağındaki sanal özel ağları kullanma gibi diğer yaklaşımları kullanın. Alternatif yaklaşımlar yoksa, karmaşık parola ve iki öğeli kimlik doğrulaması ( [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)gibi) kullandığınızdan emin olun.

**En iyi yöntem**: güçlü kimlik doğrulama ve yetkilendirme platformları kullanın.   
**Ayrıntı**: Azure AD 'de özel Kullanıcı depoları yerine federe kimlikler kullanın. Federal kimlikler kullandığınızda, platform tabanlı bir yaklaşımdan yararlanabilir ve yetkili kimliklerin yönetim yetkisini iş ortaklarınıza devredebilirsiniz. Çalışanlar sonlandırıldığı ve bilgilerin birden çok kimlik ve yetkilendirme sistemine yansıtılması gerektiğinde, bir federal kimlik yaklaşımı özellikle önemlidir.

Özel kod yerine platform tarafından sağlanan kimlik doğrulama ve yetkilendirme mekanizmalarını kullanın. Bunun nedeni, özel kimlik doğrulama kodu geliştirmenin hata olabilir. Geliştiricilerinizin çoğu güvenlik uzmanı değildir ve kimlik doğrulama ve yetkilendirme konusunda en son gelişmeleri göz önünde bulundurulmamalıdır. Ticari kod (örneğin, Microsoft 'tan) genellikle yaygın olarak güvenlik gözden geçirilir.

İki öğeli kimlik doğrulaması kullanın. Kimlik doğrulama ve parola türlerinde bulunan güvenlik zayıflarını önlediği için iki öğeli kimlik doğrulama, kimlik doğrulama ve yetkilendirme için geçerli standarttır. Hem Azure Yönetim (portal/uzak PowerShell) arabirimlerine hem de müşteriye yönelik hizmetlere erişim, [azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)kullanmak üzere tasarlanmalı ve yapılandırılmalıdır.

OAuth2 ve Kerberos gibi standart kimlik doğrulama protokollerini kullanın. Bu protokoller kapsamlı bir şekilde incelendi ve büyük olasılıkla kimlik doğrulama ve yetkilendirme için platform kitaplıklarınızın bir parçası olarak uygulandı.

## <a name="use-threat-modeling-during-application-design"></a>Uygulama tasarımı sırasında tehdit modellemesini kullanma
Microsoft [güvenlik geliştirme yaşam döngüsü](https://www.microsoft.com/en-us/sdl) , takımların tasarım aşamasında tehdit modelleme adlı bir işlemde devreye geçebilmelidir. Microsoft, bu işlemi kolaylaştırmaya yardımcı olmak için [SDL Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool)oluşturmuştur. Uygulama tasarımını modelleme ve tüm güven sınırları genelinde [ilerleme](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) tehditleri numaralandırma, tasarım hatalarını erken yakalayabilirler.

Aşağıdaki tabloda, Ilerleme tehditleri listelenmekte ve Azure özelliklerini kullanan bazı örnek azaltmaları sunulmaktadır. Bu azaltmaları her durumda çalışmaz.

| Tehdit | Güvenlik özelliği | Olası Azure platformu azaltmaları |
| --- | --- | --- |
| Sızdır | Kimlik Doğrulaması | HTTPS bağlantıları gerektir. |
| Kurcalama | Bütünlük | SSL sertifikalarını doğrulayın. |
| Kar | Red olmayan | Azure [izleme ve tanılama 'yı](/azure/architecture/best-practices/monitoring)etkinleştirin. |
| Bilgilerin açığa çıkması | Gizlilik | Bekleyen hassas verileri [hizmet sertifikaları](/rest/api/appservice/certificates)kullanarak şifreleyin. |
| Hizmet reddi | Erişilebilirlik | Olası hizmet reddi koşulları için performans ölçümlerini izleyin. Bağlantı filtrelerini uygulayın. |
| Ayrıcalık yükselmesi | Yetkilendirme | [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements)kullanın. |

## <a name="develop-on-azure-app-service"></a>Azure App Service geliştirin
[Azure App Service](/azure/app-service/overview) , herhangi bir platform veya cihaz için Web ve mobil uygulamalar oluşturmanıza ve bulutta veya şirket içinde her yerden verilere bağlanmanıza olanak tanıyan bir PaaS sunumudur. App Service, daha önce Azure Web siteleri ve Azure Mobile Services olarak ayrı olarak sunulan Web ve mobil özellikleri içerir. Ayrıca iş süreçlerini otomatikleştirmek ve bulut API'leri barındırmak için yeni özellikler içerir. Tek bir tümleşik hizmet olarak, App Service Web, mobil ve tümleştirme senaryolarına zengin bir özellik kümesi sunar.

App Service kullanmak için en iyi yöntemler aşağıda verilmiştir.

**En iyi yöntem**: [Azure Active Directory aracılığıyla kimlik doğrulaması](/azure/app-service/overview-authentication-authorization).   
**Ayrıntı**: App Service kimlik sağlayıcınız Için bir OAuth 2,0 hizmeti sağlar. OAuth 2,0, Web uygulamaları, masaüstü uygulamaları ve cep telefonları için belirli yetkilendirme akışları sağlarken istemci geliştiricisi basitliği üzerine odaklanır. Azure AD, mobil uygulamalar ve Web uygulamalarına erişim yetkisi vermek için OAuth 2,0 kullanır.

**En iyi yöntem**: erişim gereksinimi ve en az ayrıcalık güvenlik ilkelerine göre erişimi kısıtlayın.   
**Ayrıntı**: erişim kısıtlama, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için zorunludur. Belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izinler atamak için RBAC kullanabilirsiniz. Kullanıcılara uygulamalara erişim verme hakkında daha fazla bilgi edinmek için bkz. [erişim yönetimi ile çalışmaya başlama](/azure/role-based-access-control/overview).

**En iyi yöntem**: anahtarlarınızı koruyun.   
**Ayrıntı**: Azure Key Vault, bulut uygulamalarının ve hizmetlerinin kullandığı şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Key Vault, anahtarları ve gizli dizileri (kimlik doğrulaması anahtarları, depolama hesabı anahtarları, veri şifreleme anahtarları,) şifreleyebilirsiniz. PFX dosyaları ve parolalar), donanım güvenlik modülleri (HSM 'ler) tarafından korunan anahtarları kullanarak. Ek güvence için anahtarları HSM’lere aktarabilir veya burada oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault](/azure/key-vault/key-vault-overview) . Otomatik yenilemeyle TLS sertifikalarınızı yönetmek için Key Vault de kullanabilirsiniz.

**En iyi yöntem**: gelen kaynak IP adreslerini kısıtla.   
**Ayrıntı**: [App Service ortamı](/azure/app-service/environment/intro) , ağ güvenlik GRUPLARı aracılığıyla gelen kaynak IP adreslerini kısıtlamanıza yardımcı olan bir sanal ağ tümleştirme özelliğine sahiptir. Sanal ağlar, erişimi denetlediğiniz internet olmayan, yönlendirilebilir bir ağa Azure kaynakları yerleştiretkinleştirmenizi sağlar. Daha fazla bilgi edinmek için bkz. [uygulamanızı bir Azure sanal ağı Ile tümleştirme](/azure/app-service/web-sites-integrate-with-vnet).

**En iyi yöntem**: App Service ortamlarınızın güvenlik durumunu izleyin.   
**Ayrıntı**: App Service ortamlarınızı Izlemek Için Azure Güvenlik Merkezi 'ni kullanın. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, gerekli denetimleri yapılandırma sürecinde size kılavuzluk eden [öneriler](../../security-center/security-center-virtual-machine-protection.md) oluşturur.

> [!NOTE]
> İzleme App Service önizleme aşamasındadır ve yalnızca güvenlik merkezi 'nin [Standart katmanında](/azure/security-center/security-center-pricing) kullanılabilir.
>
>

## <a name="install-a-web-application-firewall"></a>Web uygulaması güvenlik duvarını yükler
Web uygulamaları, bilinen yaygın güvenlik açıklarından yararlanan kötü amaçlı saldırıların giderek daha fazla hedefi olmaktadır. Bu açıklardan yararlanma örnekleri arasında SQL ekleme saldırıları, siteler arası komut dosyası saldırıları yaygındır. Uygulama kodunda bu tür saldırıların önlenmesi zor olabilir ve uygulama topolojisinin birçok katmanında ayrıntılı bakım, düzeltme eki uygulama ve izleme işlemleri gerektirebilir. Merkezi bir web uygulaması güvenlik duvarı, güvenlik yönetimini çok daha kolay hale getirir ve yetkisiz erişim ya da izinsiz giriş tehditlerine karşı uygulama yöneticilerine daha iyi güvence verir. Bir WAF çözümü, bilinen bir güvenlik açığına merkezi bir konumda düzeltme eki uygulayarak güvenlik tehdidine karşı, web uygulamalarının her birinin güvenliğini sağlamaya göre daha hızlı tepki verebilir. Var olan uygulama ağ geçitleri, web uygulaması güvenlik duvarı bulunan bir uygulama ağ geçidine kolaylıkla dönüştürülebilir.

[Web uygulaması güvenlik duvarı (WAF)](/azure/frontdoor/waf-overview) , yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi korumasını sağlayan bir Application Gateway özelliğidir. WAF, [Open Web Application Security Project (OWASP) çekirdek kural kümeleri](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 veya 2.2.9 kurallarını temel alır.

## <a name="monitor-the-performance-of-your-applications"></a>Uygulamalarınızın performansını izleme
İzleme, uygulamanızın performansını, sistem durumunu ve kullanılabilirliğini belirlemede veri toplama ve çözümleme işlemidir. Etkili bir izleme stratejisi, uygulamanız için bileşenlerin ayrıntılı işlemini anlamanıza yardımcı olur. Sorun haline gelmeden önce bunları çözebilmeniz için kritik sorunları bildirerek çalışma süresini artırmanıza yardımcı olur. Ayrıca, güvenlikle ilgili olabilecek anormallikleri tespit etmenize de yardımcı olur.

Kuruluşunuzun veya şirket içinde barındırılıp barındırılmadığını, uygulamanızın kullanılabilirliğini, performansını ve kullanımını izlemek için [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) kullanın. Application Insights kullanarak, bir kullanıcının bunları raporlamasını beklemeden uygulamanızdaki hataları hızlıca tanımlayabilir ve tanılayabilirsiniz. Topladığınız bilgilerle, uygulamanızın bakımı ve iyileştirmeleri ile ilgili bilinçli kararlar alabilirsiniz.

Application Insights, topladığı verilerle etkileşim kurmak için kapsamlı araçlara sahiptir. Application Insights, verilerini genel bir depoda saklar. Kusto sorgu diliyle uyarılar, panolar ve derin analizler gibi paylaşılan işlevlerden yararlanabilir.

## <a name="perform-security-penetration-testing"></a>Güvenlik, sızma testi gerçekleştirme
Güvenlik savunmasının doğrulanması, diğer tüm işlevleri test etmeyi önemli bir öneme sahiptir. Derleme ve dağıtım işleminizin standart bir parçasını, [sızma testi](pen-testing.md) yapın. Dağıtılan uygulamalarda düzenli güvenlik testlerini ve güvenlik açığı taramasını zamanlayın ve açık bağlantı noktaları, uç noktalar ve saldırıları izleyin.

Belirsizlik testi, bu verileri ayrıştırmak ve tüketmek üzere program arabirimlerine (giriş noktaları) hatalı biçimlendirilmiş giriş verileri sağlayarak program hatalarının (kod hataları) bulunması için bir yöntemdir. [Microsoft güvenlik riski algılama](https://www.microsoft.com/en-us/security-risk-detection/) , Azure 'a dağıtmadan önce yazılımlarınızın hatalarını ve diğer güvenlik açıklarını bulmak için kullanabileceğiniz bulut tabanlı bir araçtır. Araç, yazılım dağıtmadan önce güvenlik açıklarını yakalamak üzere tasarlanmıştır, böylece bir hataya yama yapmanız, kilitlenmelerle uğraşmak veya yazılım yayımlandıktan sonra bir saldırıya yanıt vermek zorunda kalmazsınız.


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure PaaS dağıtımının güvenlik avantajlarına ve bulut uygulamalarına yönelik en iyi güvenlik uygulamalarına odaklandık. Ardından, belirli Azure hizmetlerini kullanarak PaaS Web ve mobil çözümlerinizi güvenli hale getirmek için önerilen uygulamaları öğrenin. Azure App Service, Azure SQL veritabanı ve Azure SQL veri ambarı ve Azure Storage ile başlayacağız. Diğer Azure hizmetleri için Önerilen uygulamalarla ilgili makaleler kullanılabilir hale geldiğinde, bağlantılar aşağıdaki listede verilmiştir:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL veritabanı ve Azure SQL veri ambarı](paas-applications-using-sql.md)
- [Azure Depolama](paas-applications-using-storage.md)
- Redis için Azure Cache
- Azure Service Bus
- Web uygulaması güvenlik duvarları

Bulut için uygulama geliştirirken yazılım geliştirme yaşam döngüsünün her aşamasında göz önünde bulundurmanız gereken güvenlik soruları ve denetimleri için bkz. [Azure 'da güvenli uygulamalar geliştirme](abstract-develop-secure-apps.md) .

Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](https://blogs.msdn.microsoft.com/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına secure@microsoft.com bildirilebilir veya e-posta ile gönderebilirsiniz.
