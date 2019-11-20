---
title: Azure Active Directory Uygulama Ara Sunucusu dağıtımı planlayın
description: Kuruluşunuz içinde uygulama proxy 'si dağıtımını planlamaya yönelik uçtan uca kılavuz
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 959d959cd269884b3b75c4c23bfd0054ae64ced7
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033640"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD Uygulama Ara Sunucusu dağıtımı planlayın

Azure Active Directory (Azure AD) uygulama proxy 'Si, şirket içi uygulamalar için güvenli ve düşük maliyetli bir uzaktan erişim çözümüdür. Henüz modern protokolleri kullanmayan eski şirket içi uygulamalara erişimi yönetmek için "bulut Ilk" kuruluşları için anında geçiş yolu sağlar. Ek tanıtım bilgileri için bkz. [uygulama proxy nedir](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Uzak kullanıcılara iç kaynaklara erişim izni vermek için uygulama proxy 'Si önerilir. Uygulama proxy 'Si, bu uzaktan erişim kullanım örnekleri için bir VPN veya ters proxy gereksinimini değiştirir. Şirket ağı üzerinde olan kullanıcılar için tasarlanmamıştır. Intranet erişimi için uygulama proxy 'Si kullanan bu kullanıcılara istenmeyen performans sorunlarıyla karşılaşabilirsiniz.

Bu makale, Azure AD Uygulama Ara Sunucusu planlamak, çalıştırmak ve yönetmek için ihtiyacınız olan kaynakları içerir. 

## <a name="plan-your-implementation"></a>Uygulamanızı planlama

Aşağıdaki bölümde, etkili bir dağıtım deneyimine yönelik olarak ayarlanacak anahtar planlama öğelerinin geniş bir görünümü sunulmaktadır. 

### <a name="prerequisites"></a>Önkoşullar

Uygulamanıza başlamadan önce aşağıdaki önkoşulları karşılamanız gerekir. Ortamınızı ayarlama hakkında daha fazla bilgiyi bu ön koşullar da dahil olmak üzere bu [öğreticiye](application-proxy-add-on-premises-application.md)bakabilirsiniz.

* **Bağlayıcılar**: Bağlayıcılar, üzerine dağıtabileceğiniz basit aracılardır:
   * Şirket içi fiziksel donanım
   * Herhangi bir hiper yönetici çözümünde barındırılan bir VM
   * Uygulama ara sunucusu hizmetine giden bağlantıyı etkinleştirmek için Azure 'da barındırılan bir VM.

* Daha ayrıntılı bir genel bakış için bkz. [Azure AD uygulaması proxy bağlayıcılarını anlayın](application-proxy-connectors.md) .

     * Bağlayıcılar yüklenmeden önce bağlayıcı makinelerin [TLS 1,2 için etkinleştirilmesi](application-proxy-add-on-premises-application.md) gerekir.

     * Mümkünse, bağlayıcıları, arka uç Web uygulaması sunucularıyla [aynı ağ](application-proxy-network-topology.md) ve kesimde dağıtın. Uygulamaları bulmayı tamamladıktan sonra bağlayıcıları dağıtmak en iyisidir.
     * Yüksek kullanılabilirlik ve ölçek sağlamak için her bir bağlayıcı grubunda en az iki bağlayıcı olmasını öneririz. Her zaman bir makineye hizmet etmeniz gerekebilmeniz için üç bağlayıcı olması en iyi durumdur. Bağlayıcılara ne tür bir makine yükleyeceğinize karar vermenize yardımcı olması için [bağlayıcı kapasitesi tablosunu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) gözden geçirin. Makinenin daha büyük olması ve bağlayıcının performansı artar.

* **Ağ erişim ayarları**: Azure AD Uygulama Ara Sunucusu bağlayıcıları [, https (TCP bağlantı noktası 443) ve http (TCP bağlantı noktası 80) aracılığıyla Azure 'a bağlanır](application-proxy-add-on-premises-application.md). 

   * Bağlayıcı TLS trafiğinin sonlandırılması desteklenmez ve bağlayıcıların ilgili Azure uygulama proxy uç noktalarıyla güvenli bir kanal oluşturmasını engeller.

   * Bağlayıcılar ve Azure arasındaki giden TLS iletişimlerinde tüm satır içi denetleme biçimlerinden kaçının. Bir bağlayıcı ve arka uç uygulamaları arasında iç inceleme yapılabilir, ancak kullanıcı deneyimini azaltabilir ve bu nedenle önerilmez.

   * Bağlayıcıların Yük Dengelemesi de desteklenmez, hatta gerekli değildir.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu yapılandırmadan önce önemli konular

Azure AD Uygulama Ara Sunucusu yapılandırmak ve uygulamak için aşağıdaki temel gereksinimlerin karşılanması gerekir.

*  **Azure ekleme**: Uygulama proxy 'si dağıtılmadan önce, kullanıcı kimliklerinin şirket içi bir dizinden eşitlenmesi veya doğrudan Azure AD kiracılarınız içinden oluşturulması gerekir. Kimlik eşitlemesi, Azure AD 'nin uygulama proxy 'Si yayımlanmış uygulamalarına erişim vermeden önce kullanıcıların kimliğini doğrulamasına ve çoklu oturum açma (SSO) gerçekleştirmesi için gerekli Kullanıcı tanımlayıcı bilgilerine sahip olmasına olanak sağlar.

* **Koşullu erişim gereksinimleri**: Bu, kullanıcıları etkileyecek gecikme süresi eklediğinden intranet erişimi için uygulama proxy 'Sinin kullanılmasını önermiyoruz. İnternet 'ten uzaktan erişim için ön kimlik doğrulama ve koşullu erişim ilkeleriyle uygulama proxy 'Si kullanmanızı öneririz.  İntranet kullanımı için koşullu erişim sağlamaya yönelik bir yaklaşım, modernleştirin uygulamalar ile doğrudan AAD ile kimlik doğrulaması yapabilmeleri için uygulamalara yöneliktir. Daha fazla bilgi için, [UYGULAMALARı AAD 'ye geçirmeye yönelik kaynaklara](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) bakın. 

* **Hizmet sınırları**: Tek tek kiracılar tarafından kaynakların aşırı tüketimine karşı koruma sağlamak için uygulama ve kiracı başına azaltma sınırları vardır. Bu sınırları görmek için [Azure AD hizmet limitleri ve kısıtlamalarına](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)bakın. Bu azaltma sınırları, tipik kullanım hacmi üzerinde en yukarıdaki bir karşılaştırmaya dayanır ve çoğu dağıtımda çok fazla arabellek sağlar.

* **Ortak sertifika**: Özel etki alanı adları kullanıyorsanız, Microsoft olmayan bir güvenilen sertifika yetkilisi tarafından verilen bir genel sertifikayı oluşturmanız gerekir. Kuruluş gereksinimlerinize bağlı olarak, bir sertifikanın alınması biraz zaman alabilir ve işlemin mümkün olduğunca erken bir süre önce başlatılmasına önerilir. Azure uygulama proxy 'Si standart, [joker karakter](application-proxy-wildcard.md)veya San tabanlı sertifikaları destekler.

* **Etki alanı gereksinimleri**: Kerberos kısıtlanmış temsili (KCD) kullanarak yayımlanmış uygulamalarınızda çoklu oturum açma, bağlayıcıyı çalıştıran sunucunun ve uygulamayı çalıştıran sunucunun etki alanına katılmış ve aynı etki alanının veya güvenen etki alanlarının bir parçası olmasını gerektirir.
Konusuyla ilgili ayrıntılı bilgi için bkz. uygulama proxy 'Si ile [Çoklu oturum açma Için KCD](application-proxy-configure-single-sign-on-with-kcd.md) . Bağlayıcı hizmeti yerel sistem bağlamında çalışır ve özel bir kimlik kullanacak şekilde yapılandırılmamalıdır.

* **URL 'Ler için DNS kayıtları**

   * Uygulama proxy 'sinde özel etki alanları kullanmadan önce, genel DNS 'de bir CNAME kaydı oluşturmanız gerekir, bu da istemcilerin özel tanımlanmış dış URL 'yi önceden tanımlanmış uygulama proxy adresine çözümlemesine izin verir. Özel etki alanı kullanan bir uygulama için CNAME kaydı oluşturma, uzak kullanıcıların uygulamaya bağlanmasını engeller. CNAME kayıtları eklemek için gereken adımlar, DNS sağlayıcısından sağlayıcıya farklılık gösterebilir, bu nedenle [Azure Portal kullanarak DNS kayıtlarını ve kayıt kümelerini yönetmeyi](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)öğrenin.

   * Benzer şekilde, bağlayıcı Konakları, yayımlanmakta olan uygulamaların iç URL 'sini çözümleyebilmelidir.

* **Yönetim hakları ve rolleri**

   * **Bağlayıcı yüklemesi** , üzerinde yüklenmekte olan Windows Server için yerel yönetici hakları gerektirir. Ayrıca, kimlik doğrulaması yapmak ve bağlayıcı örneğini Azure AD kiracınıza kaydetmek için en az bir *Uygulama Yöneticisi* rolü gerektirir. 

   * **Uygulama yayımlama ve yönetim** *Uygulama Yöneticisi* rolünü gerektirir. Uygulama yöneticileri, dizinde kayıtlar, SSO ayarları, Kullanıcı ve Grup atamaları ve lisanslama, uygulama proxy 'Si ayarları ve onay dahil tüm uygulamaları yönetebilir. Koşullu erişimi yönetme yeteneği vermez. *Bulut uygulama Yöneticisi* rolü, uygulama yöneticisinin tüm yeteneklerine sahiptir, ancak uygulama proxy ayarlarının yönetimine izin vermez.

* **Lisanslama**: Uygulama proxy 'Si bir Azure AD Premium aboneliği aracılığıyla kullanılabilir. Lisanslama seçeneklerinin ve özelliklerinin tam listesi için [Azure Active Directory fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/active-directory/) bakın.  

### <a name="application-discovery"></a>Uygulama bulma

Aşağıdaki bilgileri toplayarak uygulama proxy 'Si aracılığıyla yayımlanmakta olan tüm kapsam içi uygulamaların envanterini derleyin:

| Bilgi türü| Toplanacak bilgiler |
|---|---|
| Hizmet türü| Örneğin: SharePoint, SAP, CRM, özel Web uygulaması, API |
| Uygulama platformu | Örneğin: Windows IIS, Linux üzerinde Apache, Tomcat, NGıNX |
| Etki alanı üyeliği| Web sunucusunun tam etki alanı adı (FQDN) |
| Uygulama konumu | Web sunucusunun veya grubun altyapınızda bulunduğu yer |
| İç erişim | Uygulamaya dahili olarak erişirken kullanılan tam URL. <br> Bir grup, ne tür bir yük dengeleme kullanımda? <br> Uygulamanın kendi dışında bir kaynaktan içerik çizmediği.<br> Uygulamanın WebSockets üzerinde çalışıp çalışmayacağını belirleme. |
| Dış erişim | Uygulamanın zaten dışarıdan açığa çıkarmış olduğu satıcı çözümü. <br> Dış erişim için kullanmak istediğiniz URL. SharePoint ise, [bu kılavuza](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)göre alternatif erişim eşlemelerinin yapılandırıldığından emin olun. Aksi takdirde, dış URL 'Leri tanımlamanız gerekecektir. |
| Ortak sertifika | Özel etki alanı kullanılıyorsa, ilgili konu adına sahip bir sertifika edinin. bir sertifika varsa, seri numarasını ve nereden elde edilecek konumu göz önünde bulabilirsiniz. |
| Kimlik doğrulaması türü| Temel, Windows tümleştirme kimlik doğrulaması, form tabanlı, üst bilgi tabanlı ve talepler gibi uygulama desteği tarafından desteklenen kimlik doğrulaması türü. <br>Uygulama belirli bir etki alanı hesabı altında çalışacak şekilde yapılandırıldıysa, hizmet hesabının tam etki alanı adını (FQDN) göz önünde önüne alın.<br> SAML tabanlı ise, tanımlayıcı ve yanıt URL 'Leri. <br> Başlık tabanlı ise, satıcı çözümü ve kimlik doğrulama türünü işlemeye yönelik belirli bir gereksinim. |
| Bağlayıcı grubu adı | Bu arka uç uygulamasına iletken ve SSO sağlamak üzere atanacak bağlayıcı grubunun mantıksal adı. |
| Kullanıcılar/Gruplar erişimi | Uygulamaya dış erişim verilecek kullanıcılar veya Kullanıcı grupları. |
| Ek gereksinimler | Uygulamayı yayımlamaya yönelik olarak kullanılması gereken ek uzaktan erişim veya güvenlik gereksinimlerini unutmayın. |

Bu [uygulama envanteri elektronik tablosunu](https://aka.ms/appdiscovery) , uygulamalarınızın envanterini almak için indirebilirsiniz.

### <a name="define-organizational-requirements"></a>Kuruluş gereksinimlerini tanımlama

Aşağıda, kuruluşunuzun iş gereksinimlerini tanımlamanız gereken alanlardır. Her alan, gereksinim örneklerini içerir

 **Erişim**

* Etki alanına katılmış veya Azure AD 'ye katılmış cihazlara sahip uzak kullanıcılar, sorunsuz çoklu oturum açma (SSO) ile yayınlanan uygulamalara güvenli bir şekilde erişebilir.

* Onaylanan kişisel cihazlara sahip uzak kullanıcılar, MFA 'ya kaydolmuş ve Microsoft Authenticator uygulamasını cep telefonlarına bir kimlik doğrulama yöntemi olarak kaydetmiş olan yayımlanmış uygulamalara güvenli bir şekilde erişebilir.

**#C1** 

* Yöneticiler, uygulama proxy 'Si aracılığıyla yayınlanan uygulamalara Kullanıcı atamalarının yaşam döngüsünü tanımlayabilir ve izleyebilir.

**Güvenlik**

* Yalnızca Grup üyeliği veya ayrı ayrı uygulamalar aracılığıyla uygulamalara atanan kullanıcılar bu uygulamalara erişebilir.

**Performans**

* Uygulama performansının iç ağdan uygulamaya erişilmesine kıyasla bir azalma yoktur.

**Kullanıcı deneyimi**

* Kullanıcılar, herhangi bir cihaz platformunda tanıdık şirket URL 'Lerini kullanarak uygulamalarına nasıl eriştiklerini algılar.

**Denetim**
* Yöneticiler, Kullanıcı erişimi etkinliğini denetleyebilir.


### <a name="best-practices-for-a-pilot"></a>Pilot için en iyi uygulamalar

Tek bir uygulamayı çoklu oturum açma (SSO) ile uzaktan erişim için tam olarak komisyon için gereken zaman ve çaba miktarını belirleme. İlk bulmayı, yayımlamayı ve genel testi dikkate alan bir pilot çalıştırarak bunu yapın. Zaten tümleşik Windows kimlik doğrulaması (ıWA) için önceden yapılandırılmış basit bir IIS tabanlı Web uygulaması kullanmak, bu kurulum, uzaktan erişim ve SSO 'nun başarıyla pilot için en az çaba gerektirdiğinden taban çizgisi oluşturmaya yardımcı olur.

Aşağıdaki tasarım öğeleri, pilot uygulamanızın başarısını doğrudan bir üretim kiracısında artırmalıdır.  

**Bağlayıcı yönetimi**:  

* Bağlayıcılar, şirket içi iletken BT 'yi uygulamalarınıza sağlayan bir anahtar rol oynar. **Varsayılan** bağlayıcı grubunu kullanmak, yayımlanan uygulamaların üretim için öngörüden önce pilot test testleri için yeterlidir. Uygulamalar başarıyla test edilir ve daha sonra üretim bağlayıcı gruplarına taşınabilir.

**Uygulama yönetimi**:

* İş gücünüzün bir dış URL 'nin tanıdık ve alakalı olduğunu hatırlamaları olasıdır. Önceden tanımlanmış msappproxy.net veya onmicrosoft.com sonlarımızı kullanarak uygulamanızı yayınmaktan kaçının. Bunun yerine, intranet gibi bir mantıksal ana bilgisayar adı ön eki olan tanıdık bir üst düzey doğrulanmış etki alanı sağlayın *. < customers_domain >. com*.

* Azure Uygulamaps portalındaki başlatma simgesini gizleyerek Pilot uygulamanın simgesinin görünürlüğünü bir pilot grubuyla sınırlayın. Üretime uygun olduğunda, uygulamayı aynı üretim kiracısında veya aynı zamanda üretim kiracınızda yayımlayarak ilgili hedeflenen hedef kitleye kapsamını atayabilirsiniz.

**Çoklu oturum açma ayarları**: Bazı SSO ayarlarının ayarlanabilmesi zaman alabilir, bu nedenle bağımlılıkların zamandan önce ele alınmasının ardından değişiklik denetimi gecikmelerini önleyin. Bu, Kerberos kısıtlanmış temsili (KCD) kullanarak SSO gerçekleştirmek ve zaman alan diğer etkinliklerle ilgilenmek için bağlayıcı konaklarına katılan etki alanını içerir. Örneğin, üst bilgi tabanlı SSO için bir PING erişim örneği kurma.

**Bağlayıcı ana bilgisayarı Ile hedef uygulama arasında SSL**: Güvenlik, bağlayıcı ana bilgisayar ve hedef uygulamalar arasındaki TLS her zaman kullanılmalıdır. Özellikle, Web uygulaması form tabanlı kimlik doğrulaması (FBA) için yapılandırılmışsa, Kullanıcı kimlik bilgileri daha sonra şifresiz metin olarak iletilir.

**Her adımı artımlı olarak uygulayın ve test**edin. Aşağıdaki yönergeleri izleyerek tüm Kullanıcı ve iş gereksinimlerinin karşılandığından emin olmak için bir uygulamayı yayımladıktan sonra temel işlevsel test gerçekleştirin:

1. Web uygulamasına ön kimlik doğrulaması devre dışı olarak genel erişimi test edin ve doğrulayın.
2. Başarılı olursa, ön kimlik doğrulamayı etkinleştirin ve Kullanıcı ve grupları atayın. Erişimi test edin ve doğrulayın.
3. Daha sonra, erişimi doğrulamak için uygulamanızın SSO yöntemini ekleyin ve yeniden sınayın.
4. Koşullu erişim ve MFA ilkelerini gereken şekilde uygulayın. Erişimi test edin ve doğrulayın.

**Sorun giderme araçları**: Sorun giderirken, bağlayıcı konaktaki tarayıcıdan yayımlanan uygulamaya erişimi doğrulayarak her zaman başlayın ve uygulamanın beklendiği gibi işlediğini doğrulayın. Uygulamanızın daha basit olmasının daha kolay olması, bu nedenle yalnızca tek bir bağlayıcı ve SSO kullanma gibi en düşük yapılandırmayla ilgili sorunları yeniden oluşturmaya çalışıyor. Bazı durumlarda, Telerik 'ın Fiddler gibi Web hata ayıklama araçları, bir proxy üzerinden erişilen uygulamalardaki erişimi veya içerik sorunlarını gidermek için olmazdır kanıtlayabiliyor. Fiddler Ayrıca iOS ve Android gibi mobil platformlar için izleme ve hata ayıklama trafiğine ve bir ara sunucu üzerinden yönlendirmek üzere yapılandırılabilecek her şeye yardımcı olmak üzere bir proxy görevi görebilir. Daha fazla bilgi için bkz. [sorun giderme kılavuzu](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>Çözümünüzü uygulama

### <a name="deploy-application-proxy"></a>Uygulama proxy 'Sini dağıt

Uygulama ara sunucusunu dağıtma adımları bu [öğreticide, uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md)konusunda ele alınmıştır. Yükleme başarılı olmazsa, portalda **uygulama ara sunucusunu sorun gider** ' i seçin veya [uygulama proxy 'si aracı bağlayıcısını yüklemeyle ilgili sorunlar için](application-proxy-connector-installation-problem.md)sorun giderme kılavuzunu kullanın.

### <a name="publish-applications-via-application-proxy"></a>Uygulama proxy 'Si aracılığıyla uygulama yayımlama

Uygulamaları yayımlamak, tüm önkoşulların karşılandığını ve uygulama proxy 'Si sayfasında kayıtlı ve etkin olarak gösteren birkaç bağlayıcı olduğunu varsayar.

Ayrıca, [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)kullanarak da uygulama yayımlayabilirsiniz.

Aşağıda, bir uygulamayı yayımlarken izlenecek en iyi yöntemler verilmiştir:

* **Bağlayıcı gruplarını kullan**: İlgili her uygulamayı yayımlamak için belirlenmiş bir bağlayıcı grubu atayın. Yüksek kullanılabilirlik ve ölçek sağlamak için her bir bağlayıcı grubunda en az iki bağlayıcı olmasını öneririz. Her zaman bir makineye hizmet etmeniz gerekebilmeniz için üç bağlayıcı olması en iyi durumdur. Ayrıca, bağlayıcılarınızı ağ veya konuma göre segmentlere ayırmak için bağlayıcı gruplarını [kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama](application-proxy-connector-groups.md) konusuna bakın.

* **Arka uç uygulama zaman aşımını ayarla**: Bu ayar, uygulamanın bir istemci işlemini işlemek için 75 saniyeden uzun süre gerektirebileceği senaryolarda faydalıdır. Örneğin, bir istemci bir veritabanına ön uç olarak davranan bir Web uygulamasına sorgu gönderdiğinde. Ön uç, bu sorguyu arka uç veritabanı sunucusuna gönderir ve bir yanıt bekler, ancak bir yanıt aldığında, görüşmenin istemci tarafı zaman aşımına uğrar. Zaman aşımını uzun olarak ayarlamak, daha uzun işlemlerin tamamlanabilmesi için 180 saniye sağlar.

* **Uygun tanımlama bilgisi türlerini kullan**

   * **Yalnızca http tanımlama bilgisi**: Set-Cookie HTTP yanıt üst bilgilerinde HTTPOnly bayrağını içeren uygulama proxy 'Si ile ek güvenlik sağlar. Bu ayar siteler arası komut dosyası oluşturma (XSS) gibi yararlanma açıklarını azaltmaya yardımcı olur. Oturum tanımlama bilgisine erişmesi gereken istemciler/kullanıcı aracıları için bu ayarı Hayır olarak bırakın. Örneğin, RDP/MTSC istemcisi, uygulama proxy 'Si aracılığıyla yayımlanmış bir Uzak Masaüstü Ağ Geçidi bağlanıyor.

   * **Güvenli tanımlama bilgisi**: Bir tanımlama bilgisi Secure özniteliğiyle ayarlandığında, Kullanıcı Aracısı (Istemci tarafı uygulama), istek bir TLS güvenli kanalı üzerinden iletiliydi HTTP isteklerine yalnızca tanımlama bilgisini dahil eder. Bu, bir tanımlama bilgisinin şifresiz metin kanalları üzerinde ele geçirilme riskini azaltmaya yardımcı olur, bu nedenle etkinleştirilmelidir.

   * **Kalıcı tanımlama bilgisi**: Uygulama proxy 'Si oturum tanımlama bilgisinin, süresi dolana veya silinene kadar geçerli kalan geçerli olan tarayıcı kapanışları arasında kalıcı olmasını sağlar. Office gibi zengin bir uygulamanın, yayımlanan bir Web uygulaması içindeki bir belgeye, kullanıcının kimlik doğrulaması için yeniden sorulmadan eriştiği senaryolar için kullanılır. Kalıcı tanımlama bilgileri, diğer telafi denetimleriyle birlikte kullanılmıyorsa, son olarak bir hizmeti yetkisiz erişim riskine karşı bir şekilde etkinleştirin. Bu ayar yalnızca süreçler arasında tanımlama bilgilerini paylaşabilen eski uygulamalar için kullanılmalıdır. Uygulamanızı bu ayarı kullanmak yerine süreçler arasında paylaşım tanımlama bilgilerini işleyecek şekilde güncelleştirmek daha iyidir.

* **Üst bilgilerdeki URL 'Leri çevir**: Bu ayarı, iç DNS 'nin kuruluşun ortak ad alanıyla (a. k. a bölünmüş DNS) eşleşecek şekilde yapılandırılabileceği senaryolar için etkinleştirirsiniz. Uygulamanız istemci isteğinde orijinal ana bilgisayar üst bilgisini gerektirmediği takdirde, bu değeri Evet olarak bırakın. Alternatif, bağlayıcının gerçek trafiğin yönlendirilmesi için iç URL 'de FQDN 'yi ve ana bilgisayar üst bilgisi olarak dış URL 'deki FQDN 'yi kullanmasını sağlar. Çoğu durumda bu alternatif, uzaktan erişildiği zaman uygulamanın normal şekilde çalışmasına izin vermemelidir, ancak kullanıcılarınız URL & dışında bir eşleştirme olma avantajlarını kaybeder.

* **Uygulama gövdesinde URL 'Leri çevir**: Bu uygulamadaki bağlantıların istemciye geri yanıt olarak çevrilmesini istediğinizde bir uygulama için uygulama gövdesi bağlantı çevirisini açın. Etkinleştirilirse, bu işlev, uygulama proxy 'Sinin bulduğu tüm iç bağlantıları, istemcilere döndürülmekte olan HTML ve CSS yanıtlarının çevirisi sırasında en iyi çaba bir girişim sağlar. İçerikte sabit kodlanmış mutlak ya da NetBIOS ShortName bağlantıları ya da diğer şirket içi uygulamalara bağlanan içeriğe sahip uygulamalarda bulunan uygulamalar yayımlandığında yararlı olur.

Yayımlanan bir uygulamanın diğer yayımlanmış uygulamalara bağlandığı senaryolar için, her bir uygulama için bağlantı çevirisini etkinleştirerek, uygulama başına düzeyinde kullanıcı deneyimi üzerinde denetim sahibi olmanız gerekir.

Örneğin, uygulama proxy 'Si aracılığıyla her birine bağlanan üç uygulamanız olduğunu varsayalım: Avantajlar, giderler ve seyahat yanı sıra uygulama proxy 'Si aracılığıyla yayımlanmamış bir dördüncü uygulama geri bildirimi.

![Resim 1](media/App-proxy-deployment-plan/link-translation.png)

Avantajlar uygulaması için bağlantı çevirisini etkinleştirdiğinizde, gider ve seyahat bağlantıları bu uygulamalar için dış URL 'lere yönlendirilir, böylece uygulamalara şirket ağı dışından erişen kullanıcılar erişebilir. Bağlantı çevirisi bu iki uygulama için etkinleştirilmediğinden, masrafların ve geri giderlerin avantajlara geri bağlantıları çalışmaz. Dış URL olmadığından, avantajlar uygulamasını kullanan kullanıcılar geri bildirim uygulamasına şirket ağının dışından erişemeyeceği için geri bildirimde bağlantı yeniden yönlendirilmedi. Bkz. [bağlantı çevirisi ve diğer yeniden yönlendirme seçenekleri](application-proxy-configure-hard-coded-link-translation.md)hakkında ayrıntılı bilgi.

### <a name="access-your-application"></a>Uygulamanıza erişin

Uygulama proxy 'Si yayımlanmış kaynaklarına erişimi yönetmek için çeşitli seçenekler bulunur, bu nedenle verilen senaryo ve ölçeklenebilirlik gereksinimleriniz için en uygun olanını seçin. Yaygın yaklaşımlar şunlardır: Azure AD Connect ile eşitlenen şirket içi grupları kullanma, Azure AD 'de Kullanıcı özniteliklerine dayalı olarak dinamik gruplar oluşturma, bir kaynak sahibi tarafından yönetilen self servis grupları kullanma veya bunların tümünün bir birleşimi. Her birinin avantajları için bağlantılı kaynaklara bakın.

Kullanıcılara bir uygulamaya erişim atamanın en basit yolu, yayımlanan uygulamanızın sol bölmesindeki **Kullanıcılar ve gruplar** seçeneklerine gidip grupları veya kişileri doğrudan atamayı sağlar.

![Resim 24](media/App-proxy-deployment-plan/add-user.png)

Ayrıca, kullanıcıların şu anda üyesi olmadıkları ve kendi kendine hizmet seçeneklerini yapılandırdıkları bir grup atayarak uygulamanıza self servis erişimine izin verebilirsiniz.

![Resim 25](media/App-proxy-deployment-plan/allow-access.png)

Etkinleştirilirse, kullanıcılar Uygulamaps portalında oturum açabilir ve erişim isteğinde bulunabilir ve otomatik olarak onaylanmalı ve zaten izin verilen self servis grubuna eklenebilir veya belirlenen bir onaylayanın onayına gerek duyar.

Konuk kullanıcılar ayrıca, [Azure AD B2B aracılığıyla uygulama proxy 'si aracılığıyla yayımlanan iç uygulamalara erişim için de davet](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)edilebilir.

Normalde anonim olarak erişilebilen ve kimlik doğrulaması gerektirmeyen şirket içi uygulamalarda, uygulamanın **özelliklerinde**bulunan seçeneğini devre dışı bırakmayı tercih edebilirsiniz.

![Resim 26](media/App-proxy-deployment-plan/assignment-required.png)


Bu seçeneği Hayır olarak bırakmak, kullanıcıların şirket içi uygulamaya izinler olmadan Azure AD Uygulaması Proxy aracılığıyla erişmesini sağlar, bu nedenle dikkatli olun.

Uygulamanız yayımlandıktan sonra, dış URL 'sini bir tarayıcıya veya konumundaki [https://myapps.microsoft.com](https://myapps.microsoft.com/)simgesine yazarak erişilebilir olmalıdır.

### <a name="enable-pre-authentication"></a>Ön kimlik doğrulamayı etkinleştir

Uygulamanızın dış URL aracılığıyla ona erişen uygulama proxy 'Si aracılığıyla erişilebilir olduğunu doğrulayın. 

1. Gidin **Azure Active Directory** > **kurumsal uygulamalar** > **tüm uygulamaları** ve yönetmek istediğiniz uygulamayı seçin.

2. Seçin **uygulama proxy'si**.

3. **Ön kimlik doğrulama** alanında **Azure Active Directory**' yi seçmek için açılan listeyi kullanın ve **Kaydet**' i seçin.

Ön kimlik doğrulaması etkinken, Azure AD, kullanıcıları kimlik doğrulaması için önce ve çoklu oturum açma yapılandırıldıktan sonra da uygulamanın erişim izni verilmeden önce kullanıcının kimliğini doğrulayacaktır. Ön kimlik doğrulama modunun geçiş 'den Azure AD 'ye değiştirilmesi, dış URL 'yi HTTPS ile de yapılandırır, bu nedenle başlangıçta HTTP için yapılandırılmış tüm uygulamaların artık HTTPS ile güvenliği sağlanır.

### <a name="enable-single-sign-on"></a>Çoklu oturum açmayı etkinleştir

SSO, kullanıcıların Azure AD 'ye erişirken yalnızca bir kez oturum açması gerektiğinden, en iyi olası kullanıcı deneyimini ve güvenliğini sağlar. Bir kullanıcının kimliği önceden doğrulandıktan sonra, SSO Kullanıcı adına şirket içi uygulamada kimlik doğrulaması yapan uygulama proxy Bağlayıcısı tarafından gerçekleştirilir. Arka uç uygulaması, oturum açma işlemini kullanıcının kendisi gibi işler. 

**Geçiş** seçeneğinin belirlenmesi, KULLANıCıLARıN Azure AD 'de kimlik doğrulaması yapmadan yayınlanan uygulamaya erişmesine izin verir.

SSO gerçekleştirmek yalnızca Azure AD 'nin bir kaynağa erişim isteğinde bulunan kullanıcıyı belirleyebilmesi durumunda, SSO 'nun işlevine erişimi sonrasında uygulamanızın Azure AD ile Kullanıcı kimliğini doğrulayacak şekilde yapılandırılması gerekir; Aksi takdirde, SSO seçenekleri devre dışı bırakılır.

Uygulamalarınızı yapılandırırken en uygun SSO yöntemini seçmenize yardımcı olması için [Azure AD 'deki uygulamalarda çoklu oturum açmayı](what-is-single-sign-on.md) okuyun.

###  <a name="working-with-other-types-of-applications"></a>Diğer uygulama türleriyle çalışma

Azure AD Uygulama Ara Sunucusu, Azure AD kimlik doğrulama kitaplığı ([adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) veya Microsoft kimlik doğrulama kitaplığı 'Nı ([msal](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)) kullanmak üzere geliştirilmiş uygulamaları da destekleyebilir. Kullanıcı adına ön kimlik doğrulaması gerçekleştirmek üzere istemci isteğinin üstbilgi bilgilerinde alınan Azure AD tarafından verilen belirteçleri tüketen yerel istemci uygulamalarını destekler.

Uygulama proxy 'sinin kullanılabilir yapılandırması hakkında bilgi edinmek için [yerel ve mobil istemci uygulamalarını](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) ve [talep tabanlı uygulamaları](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) yayımlama makalesini okuyun.

### <a name="use-conditional-access-to-strengthen-security"></a>Güvenliği güçlendirmek için koşullu erişimi kullanın

Uygulama güvenliği, şirket içinde ve bulutta bulunan karmaşık tehditleri koruyabilecek ve bunlara yanıt verebilecek gelişmiş bir güvenlik özellikleri kümesi gerektirir. Saldırganlar genellikle zayıf, varsayılan veya çalınan Kullanıcı kimlik bilgileriyle şirket ağına erişim elde edebilir.  Microsoft kimlik tabanlı güvenlik, hem ayrıcalıklı hem de ayrıcalıksız kimlikleri yöneterek ve koruyarak çalınan kimlik bilgilerinin kullanımını azaltır.

Aşağıdaki yetenekler Azure AD Uygulama Ara Sunucusu desteklemek için kullanılabilir:

* Kullanıcı ve konum tabanlı koşullu erişim: [Konum tabanlı koşullu erişim ilkeleriyle](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations), coğrafi konuma veya bir IP adresine dayalı olarak Kullanıcı erişimini sınırlayarak gizli verileri korumalı tutun.

* Cihaz tabanlı koşullu erişim: Yalnızca kayıtlı, onaylanan ve uyumlu cihazların [cihaz tabanlı koşullu erişimle](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)kurumsal verilere erişebildiğinden emin olun.

* Uygulama tabanlı koşullu erişim: Bir Kullanıcı kurumsal ağda olmadığında çalışmayı durdurmak zorunda değildir. [Kurumsal bulut ve şirket içi uygulamalara güvenli erişim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) ve koşullu erişim ile denetim sağlama.

* Risk tabanlı koşullu erişim: Şirket içinde veya bulutta olmak üzere tüm uygulamalara ve tüm kullanıcılara uygulanabilen [risk tabanlı bir koşullu erişim ilkesiyle](https://www.microsoft.com/cloud-platform/conditional-access) verilerinizi kötü amaçlı korsanlardan koruyun.

* Azure AD erişim paneli: Uygulama proxy hizmeti dağıtıldıktan ve uygulamalar güvenli bir şekilde yayımlanmışsa, kullanıcılarınıza tüm uygulamalarını keşfetmek ve bunlara erişmek için basit bir hub sunun. Yeni uygulamalara ve gruplara erişim isteme ya da bu kaynaklara erişimi başka kişilerin adına, [erişim paneli](https://aka.ms/AccessPanelDPDownload)aracılığıyla yönetme yeteneği gibi self servis özellikleri ile üretkenliğinizi artırın.

## <a name="manage-your-implementation"></a>Uygulamanızı yönetme

### <a name="required-roles"></a>Gerekli roller

Microsoft, gerekli görevleri Azure AD ile gerçekleştirmek için en az olası ayrıcalıkları verme ilkesini de sağlar. [Kullanılabilir farklı Azure rollerini gözden geçirin](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) ve her kişinin ihtiyaçlarını karşılamak için doğru olanı seçin. Dağıtım tamamlandıktan sonra bazı rollerin geçici olarak uygulanması ve kaldırılması gerekebilir.

| İş rolü| İş görevleri| Azure AD rolleri |
|---|---|---|
| Yardım Masası Yöneticisi | Tipik olarak, son kullanıcı tarafından bildirilen sorunları niteleyen ve kullanıcıların parolalarını değiştirme, yenileme belirteçleri geçersiz kılma ve hizmet durumunu izleme gibi sınırlı görevleri gerçekleştirme ile sınırlıdır. | Yardım Masası Yöneticisi |
| Kimlik Yöneticisi| Uygulama proxy 'Si ile ilgili sorunları gidermek için Azure AD oturum açma raporlarını ve denetim günlüklerini okuyun.| Güvenlik okuyucusu |
| Uygulama sahibi| Kurumsal uygulamaların, uygulama kayıtlarının ve uygulama proxy ayarlarının tüm yönlerini oluşturun ve yönetin.| Uygulama Yöneticisi |
| Altyapı Yöneticisi | Sertifika aktarma sahibi | Uygulama Yöneticisi |

Güvenli bilgi veya kaynaklara erişimi olan kişilerin sayısını en aza indirmek, kötü niyetli bir aktörün yetkisiz erişim sağlama olasılığını azaltmada veya yetkili bir kullanıcının yanlışlıkla hassas bir kaynağı etkilemesinin ihtimalini azaltmaya yardımcı olur. 
 
Ancak, kullanıcıların günlük ayrıcalıklı işlemleri gerçekleştirmesi gerekir. bu nedenle, Azure kaynaklarına isteğe bağlı ayrıcalıklı erişim sağlamak için tam zamanında (JıT) [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ilkelerini zorlamaya ve Azure AD 'nin önerdiğimiz yaklaşımımız Yönetici erişimini ve denetimini verimli bir şekilde yönetme.

### <a name="reporting-and-monitoring"></a>Raporlama ve izleme

Azure AD, kuruluşunuzun uygulama kullanımı ve [Denetim günlükleri ve raporları](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)aracılığıyla işletimsel sistem durumu hakkında ek öngörüler sağlar. Ayrıca, uygulama proxy 'Si Azure AD portalı ve Windows olay günlüklerinden bağlayıcıları izlemeyi çok kolay hale getirir.

#### <a name="application-audit-logs"></a>Uygulama denetim günlükleri

Bu Günlükler, uygulama proxy 'Si ve cihazla ve uygulamaya erişen kullanıcı ile yapılandırılan uygulamalara yönelik oturumlar hakkında ayrıntılı bilgi sağlar. [Denetim günlükleri](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) , Azure Portal ve dışarı aktarma IÇIN [Denetim API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) 'sinde bulunur. Ayrıca, [kullanım ve öngörü raporları](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) uygulamanız için de kullanılabilir.

#### <a name="application-proxy-connector-monitoring"></a>Uygulama proxy Bağlayıcısı izleme

Bağlayıcılar ve hizmetin tüm yüksek kullanılabilirlik görevlerini ilgileniriz. Azure AD portalındaki uygulama proxy 'Si sayfasından bağlayıcılarınızın durumunu izleyebilirsiniz. Bağlayıcı bakım hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Örnek: Azure AD Uygulama Ara Sunucusu bağlayıcıları](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows olay günlükleri ve performans sayaçları

Bağlayıcılar yönetici ve oturum günlüklerine sahiptir. Yönetici günlükler anahtar olayları ve bunların hatalarını içerir. Oturum günlükleri tüm işlemleri ve bunların işleme ayrıntılarını içerir. Günlükler ve sayaçlar Windows olay günlüklerinde bulunur daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcılarını anlama](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). [Azure izleyici 'de olay günlüğü veri kaynaklarını yapılandırmak için bu öğreticiyi](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)izleyin.

### <a name="troubleshooting-guide-and-steps"></a>Sorun giderme kılavuzu ve adımları

Yaygın sorunlar ve hata iletileriyle [ilgili sorun giderme](application-proxy-troubleshoot.md) kılavuzumuzu kullanarak bunları çözme hakkında daha fazla bilgi edinin. 

Aşağıdaki makalelerde, destek kuruluşunuza yönelik sorun giderme kılavuzlarını oluşturmak için de kullanılabilecek yaygın senaryolar ele alınmaktadır. 

* [Uygulama sayfası görüntülenirken sorun oluşuyor](application-proxy-page-appearance-broken-problem.md)
* [Uygulamanın yüklenmesi çok uzun sürüyor](application-proxy-page-load-speed-problem.md)
* [Uygulama sayfasındaki bağlantılar çalışmıyor](application-proxy-page-links-broken-problem.md)
* [Uygulamam için açılacak bağlantı noktaları](application-proxy-connectivity-ports-how-to.md)
* [Uygulamam için bağlayıcı grubunda çalışan bağlayıcı yok](application-proxy-connectivity-no-working-connector.md)
* [Azure portalda yapılandırma](application-proxy-config-how-to.md)
* [Uygulamamda çoklu oturum açmayı yapılandırma](application-proxy-config-sso-how-to.md)
* [Yönetici portalında uygulama oluştururken sorun oluşuyor](application-proxy-config-problem.md)
* [Kerberos Kısıtlanmış Temsilini Yapılandırma](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [PingAccess ile yapılandırma](application-proxy-back-end-ping-access-how-to.md)
* [Bu kurumsal uygulama hatasına erişemiyor](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Uygulama Ara Sunucusu Aracı Bağlayıcısı’nı yüklerken sorun oluşuyor](application-proxy-connector-installation-problem.md)
* [Oturum açma sorunu](application-sign-in-problem-on-premises-application-proxy.md)
