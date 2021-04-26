---
title: Azure AD Uygulama Ara Sunucusu ile şirket içi uygulamaları yayımlama
description: Şirket içi Web uygulamalarını uzak kullanıcılara yayımlamak için uygulama proxy 'Sinin neden kullanılacağını anlayın. Uygulama proxy 'Si mimarisi, bağlayıcılar, kimlik doğrulama yöntemleri ve güvenlik avantajları hakkında bilgi edinin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1569e577ec535a4680cfaec6f7975c056275ecbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258787"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Uzak kullanıcılar için şirket içi uygulamalar yayımlamak üzere Azure AD Uygulama Ara Sunucusu kullanma

Azure Active Directory (Azure AD), bulutta ve Şirket içindeki kullanıcıları, uygulamaları ve verileri korumak için birçok özellik sunar. Özellikle Azure AD Uygulama Ara Sunucusu özelliği, şirket içi Web uygulamalarını dışarıdan yayınlamak isteyen BT uzmanları tarafından uygulanabilir. İç uygulamalara erişmesi gereken uzak kullanıcılar, bunlara güvenli bir şekilde erişebilir.

Ağınızın dışından iç uygulamalara güvenli bir şekilde erişme özelliği, modern çalışma alanında daha da fazla önemli hale gelir. BYOD (kendi cihazını getir) ve mobil cihazlar gibi senaryolarla BT uzmanlarının iki hedefi karşılaması gerekir:

* Son kullanıcıların her zaman ve her yerde üretken olmalarını sağlayın
* Şirket varlıklarını her zaman koruma

Birçok kuruluş, kaynakların şirket ağlarının sınırları dahilinde olduğu durumlarda denetim halinde olduğunu ve koruduğuna inanmaktadır. Ancak bugünün dijital çalışma alanında, bu sınır, bulutta yönetilen mobil cihazlar ve kaynaklar ve hizmetler ile genişletilir. Artık kullanıcılarınızın ve uygulamalarında depolanan kimliklerini ve verilerini koruma karmaşıklığını yönetmeniz gerekir.

Belki de şirket içinde barındırılan Web uygulamalarının yanı sıra Microsoft 365 ve diğer SaaS uygulamalarına erişmesi gereken buluttaki kullanıcıları yönetmek için zaten Azure AD kullanıyor olabilirsiniz. Zaten Azure AD 'niz varsa, şirket içi uygulamalarınıza sorunsuz ve güvenli erişim sağlamak için bunu bir denetim düzlemi olarak kullanabilirsiniz. Ya da yine de buluta geçiş şablonu oluşturmaya devam ediyor olabilirsiniz. Bu durumda, uygulama proxy 'Si uygulayarak ve güçlü bir Identity Foundation oluşturmaya yönelik ilk adımı gerçekleştirerek, yolculuğa buluta başlayabilirsiniz.

Kapsamlı olmasa da aşağıdaki listede, karma bir birlikte bulunma senaryosunda uygulama proxy 'Si uygulayarak etkinleştirebileceğiniz bazı şeyler gösterilmektedir:

* Şirket içi Web uygulamalarını DMZ olmadan basitleştirilmiş bir şekilde yayımlayın
* Bulutta ve şirket içinde cihazlar, kaynaklar ve uygulamalar arasında çoklu oturum açma (SSO) desteği
* Buluttaki ve Şirket içindeki uygulamalar için Multi-Factor Authentication 'ı destekleme
* Microsoft Bulut güvenliği ile bulut özelliklerinden hızlıca yararlanın
* Kullanıcı hesabı yönetimini merkezileştirme
* Kimlik ve güvenlik denetimini merkezileştirin
* Grup üyeliğine göre uygulamalara otomatik olarak Kullanıcı erişimi ekleme veya kaldırma

Bu makalede, Azure AD ve uygulama proxy 'Sinin uzak kullanıcılara çoklu oturum açma (SSO) deneyimi verme yöntemi açıklanmaktadır. Kullanıcılar VPN veya çift bağlantılı sunucular ve güvenlik duvarı kuralları olmadan şirket içi uygulamalara güvenli bir şekilde bağlanır. Bu makale, uygulama proxy 'Sinin şirket içi Web uygulamalarınıza bulut özelliklerini ve güvenlik avantajlarını nasıl sağladığını anlamanıza yardımcı olur. Ayrıca, mümkün olan mimari ve topolojiler de açıklanmaktadır.

## <a name="remote-access-in-the-past"></a>Geçmişte uzaktan erişim

Daha önce, uzak kullanıcıların erişimini kolaylaştırırken saldırganların iç kaynakları korumasına yönelik denetim düzlemi DMZ veya çevre ağındadır. Ancak dış istemciler tarafından şirket kaynaklarına erişmek için kullanılan DMZ içinde dağıtılan VPN ve ters proxy çözümleri, bulut dünyasına uygun değildir. Bunlar genellikle aşağıdaki dezavantajlardan zarar görür:

* Donanım maliyetleri
* Güvenliği koruma (düzeltme eki uygulama, izleme bağlantı noktaları vb.)
* Kenarda kullanıcıların kimliğini doğrulama
* Çevre ağındaki Web sunucuları için kullanıcıların kimliğini doğrulama
* VPN istemci yazılımının dağıtımı ve yapılandırması ile uzak kullanıcılara VPN erişimini koruma. Ayrıca, dış saldırılara karşı savunmasız olabilecek DMZ etki alanına katılmış sunucuları koruma.

Günümüzün bulutta birinci dünyada Azure AD, ağınızı kimin ve ne olduğunu denetlemek için idealdir. Azure AD Uygulama Ara Sunucusu, SaaS uygulamaları ve kimlik sağlayıcıları gibi modern kimlik doğrulaması ve bulut tabanlı teknolojilerle tümleştirilir. Bu tümleştirme, kullanıcıların uygulamalara her yerden erişmelerini sağlar. Artık App proxy, günümüzün dijital çalışma alanı için daha güvenli değildir; VPN ve ters proxy çözümlerinin daha güvenlidir ve uygulama daha kolay olur. Uzak kullanıcılar, şirket içi uygulamalarınıza Microsoft ve Azure AD ile tümleştirilmiş diğer SaaS uygulamalarına erişen şekilde erişebilirler. Uygulama Ara Sunucusu ile çalışması için uygulamanızı değiştirmeniz veya güncelleştirmeniz gerekmez. Ayrıca, uygulama proxy 'Si, gelen bağlantıları güvenlik duvarınız aracılığıyla açmanızı gerektirmez. Uygulama proxy 'Si ile onu ayarlamanız ve unutmanız yeterlidir.

## <a name="the-future-of-remote-access"></a>Uzaktan erişim 'in geleceği

Bugünün dijital çalışma alanında, kullanıcılar birden çok cihaz ve uygulama ile her yerde çalışır. Tek sabit Kullanıcı kimliğidir. Günümüzde güvenli bir ağın ilk adımı, [Azure AD 'nin kimlik yönetimi](../../security/fundamentals/identity-management-overview.md) yeteneklerini güvenlik denetim düzlemi olarak kullanmaktır. Denetim düzlemi olarak kimlik kullanan bir model genellikle aşağıdaki bileşenlerden oluşur:

* Kullanıcıları ve Kullanıcı ile ilgili bilgileri izlemek için bir kimlik sağlayıcısı.
* Şirket kaynaklarına erişimi olan cihazların listesini tutmak için cihaz dizini. Bu dizin, ilgili cihaz bilgilerini (örneğin, cihaz türü, bütünlük vb.) içerir.
* Bir Kullanıcı ve cihazın güvenlik yöneticileri tarafından belirlenen ilkeye uygun olup olmadığını belirleme ilkesi değerlendirme hizmeti.
* Kuruluş kaynaklarına erişim izni verme veya reddetme özelliği.

Azure AD, uygulama proxy 'Si ile şirket içinde ve bulutta yayınlanan web uygulamalarına erişmesi gereken kullanıcıları izler. Bu uygulamalar için merkezi bir yönetim noktası sağlar. Gerekli olmasa da, Azure AD koşullu erişimini de etkinleştirmeniz önerilir. Kullanıcıların kimlik doğrulamasının ve erişim kazanmasıyla ilgili koşulları tanımlayarak, doğru kişilerin uygulamalara erişimi olduğundan emin olursunuz.

**Note:** Azure AD Uygulama Ara Sunucusu iç kaynaklara erişmesi gereken gezici (veya uzak) kullanıcılar için bir VPN veya ters proxy değişikliği olduğunu anlamak önemlidir. Şirket ağındaki dahili kullanıcılara yönelik değildir. Uygulama proxy 'Sini gereksiz şekilde kullanan dahili kullanıcılar beklenmedik ve istenmeyen performans sorunları ortaya çıkarabilir.

![Azure Active Directory ve tüm uygulamalarınız](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Uygulama proxy 'Sinin nasıl çalıştığına genel bakış

Uygulama proxy 'Si, Azure portal yapılandırdığınız bir Azure AD hizmetidir. Azure bulutunda, kuruluşunuzda bir iç uygulama sunucusu URL 'sine bağlanan bir dış genel HTTP/HTTPS URL uç noktası yayımlamanıza olanak sağlar. Bu şirket içi web uygulamaları, çoklu oturum açmayı desteklemek için Azure AD ile tümleştirilebilir. Son kullanıcılar daha sonra şirket içi Web uygulamalarına Microsoft 365 ve diğer SaaS uygulamalarına erişen şekilde erişebilir.

Bu özelliğin bileşenleri, bulutta çalışan uygulama proxy hizmeti 'ni, şirket içi sunucuda çalışan hafif bir aracı olan uygulama proxy bağlayıcısını ve kimlik sağlayıcısı olan Azure AD ' yi içerir. Her üç bileşen birlikte çalışarak, kullanıcıya şirket içi Web uygulamalarına erişmek için çoklu oturum açma deneyimi sağlar.

Oturum açtıktan sonra dış kullanıcılar, masaüstü veya iOS/MAC cihazlarından tanıdık bir URL 'YI veya [uygulamalarımı](../user-help/my-apps-portal-end-user-access.md) kullanarak şirket içi Web uygulamalarına erişebilir. Örneğin, uygulama proxy 'Si Uzak Masaüstü, SharePoint siteleri, Tableau, Qlik, Web üzerinde Outlook ve iş kolu (LOB) uygulamaları için uzaktan erişim ve çoklu oturum açma sağlayabilir.

![Azure AD Uygulama Ara Sunucusu mimarisi](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Kimlik Doğrulaması

Bir uygulamayı çoklu oturum açma için yapılandırmanın birkaç yolu vardır ve seçtiğiniz yöntem uygulamanızın kullandığı kimlik doğrulamasına bağlıdır. Uygulama proxy 'Si aşağıdaki uygulama türlerini destekler:

* Web uygulamaları
* Farklı cihazlarda zengin uygulamalarda kullanıma sunmak istediğiniz Web API 'Leri
* Uzak Masaüstü Ağ Geçidi arkasında barındırılan uygulamalar
* [Microsoft kimlik doğrulama kitaplığı (msal)](../develop/v2-overview.md) ile tümleştirilmiş zengin istemci uygulamaları

Uygulama proxy 'Si, aşağıdaki yerel kimlik doğrulama protokolünü kullanan uygulamalarla birlikte kullanılabilir:

* **[Tümleşik Windows kimlik doğrulaması (ıWA)](application-proxy-configure-single-sign-on-with-kcd.md).** IWA için uygulama proxy bağlayıcıları, Kerberos uygulamasında kullanıcıların kimliğini doğrulamak için Kerberos kısıtlanmış temsili (KCD) kullanır.

Ayrıca, uygulama proxy 'Si, üçüncü taraf tümleştirmeyle veya belirli yapılandırma senaryolarında aşağıdaki kimlik doğrulama protokollerini destekler:

* [**Üst bilgi tabanlı kimlik doğrulama**](./application-proxy-configure-single-sign-on-with-headers.md). Bu oturum açma yöntemi, PingAccess adlı bir üçüncü taraf kimlik doğrulama hizmeti kullanır ve uygulamanın kimlik doğrulaması için üst bilgileri kullanması durumunda kullanılır. Bu senaryoda, kimlik doğrulaması PingAccess tarafından işlenir.
* [**Form veya parola tabanlı kimlik doğrulama**](application-proxy-configure-single-sign-on-password-vaulting.md). Bu kimlik doğrulama yöntemiyle, kullanıcılar ilk kez erişirken Kullanıcı adı ve parola ile uygulama üzerinde oturum açabilirler. İlk oturum açma işleminden sonra Azure AD, uygulamaya Kullanıcı adı ve parola sağlar. Bu senaryoda, kimlik doğrulaması Azure AD tarafından işlenir.
* [**SAML kimlik doğrulaması**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML tabanlı çoklu oturum açma, SAML 2,0 veya WS-Federation protokollerini kullanan uygulamalar için desteklenir. SAML çoklu oturum açma ile Azure AD, kullanıcının Azure AD hesabını kullanarak uygulamanın kimliğini doğrular.

Desteklenen yöntemler hakkında daha fazla bilgi için bkz. [Çoklu oturum açma yöntemi seçme](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Güvenlik avantajları

Uygulama proxy 'Si ve Azure AD tarafından sunulan uzaktan erişim çözümü, müşterilerin avantajlarından yararlanarak aşağıdakiler de dahil olmak üzere çeşitli güvenlik avantajlarını destekler:

* **Kimliği doğrulanmış erişim**. Uygulama proxy 'Si, yalnızca kimliği doğrulanmış bağlantıların ağınıza ulaşmasını sağlamak üzere [ön kimlik doğrulama](application-proxy-security.md#authenticated-access) ile uygulama yayımlamak için idealdir. Ön kimlik doğrulamasıyla yayınlanan uygulamalarda, geçerli bir belirteç olmadan uygulama ara sunucusu hizmetinden şirket içi ortamınıza geçiş yapılmasına izin verilmez. Ön kimlik doğrulaması, çok doğası gereği, yalnızca kimliği doğrulanmış kimlikler arka uç uygulamasına erişebilse de, önemli sayıda hedeflenmiş saldırıları engeller.
* **Koşullu erişim**. Ağınıza yönelik bağlantılar oluşturulmadan daha zengin ilke denetimleri uygulanabilir. Koşullu erişimle, arka uç uygulamanıza ulaşmalarını sağlayan trafik üzerinde kısıtlamalar tanımlayabilirsiniz. Oturum açma işlemlerini konuma, kimlik doğrulama gücüne ve Kullanıcı risk profiline göre sınırlayan ilkeler oluşturursunuz. Koşullu erişim geliştikçe, Microsoft Cloud App Security (MCAS) ile tümleştirme gibi ek güvenlik sağlamak için daha fazla denetim eklenmektedir. MCAS tümleştirmesi, koşullu erişim ilkelerine bağlı olarak oturumları gerçek zamanlı olarak izlemek ve denetlemek için koşullu erişimi kullanarak [gerçek zamanlı izleme](application-proxy-integrate-with-microsoft-cloud-application-security.md) için şirket içi bir uygulamayı yapılandırmanızı sağlar.
* **Trafik sonlandırma**. Oturum arka uç sunucusuyla yeniden oluşturulduğunda, arka uç uygulamasına giden tüm trafik, buluttaki uygulama ara sunucusu hizmetinde sonlandırılır. Bu bağlantı stratejisi, arka uç sunucularınızın doğrudan HTTP trafiğine açık olmadığı anlamına gelir. Güvenlik duvarınız saldırı altında olmadığı için hedeflenen DoS (hizmet reddi) saldırılarına karşı daha iyi korunur.
* **Tüm erişim giden**. Uygulama proxy 'Si bağlayıcıları, 80 ve 443 bağlantı noktalarında buluttaki uygulama ara sunucusu hizmetine giden bağlantıları yalnızca kullanır. Gelen bağlantı olmadan, DMZ gelen bağlantılara veya bileşenlere yönelik güvenlik duvarı bağlantı noktalarını açmaya gerek yoktur. Tüm bağlantılar giden ve güvenli bir kanal üzerinden yapılır.
* **Güvenlik analizi ve Machine Learning (ml) tabanlı zeka**. Azure Active Directory bir parçası olduğundan, uygulama proxy 'Si [Azure AD kimlik koruması](../identity-protection/overview-identity-protection.md) kullanabilir ( [Premium P2 lisanslama](https://azure.microsoft.com/pricing/details/active-directory/)gerektirir). Azure AD Kimlik Koruması, güvenliği aşılmış hesapları önceden belirlemek için Microsoft 'un [dijital Crimes birimi](https://news.microsoft.com/stories/cybercrime/index.html) ve [Microsoft Güvenlik Yanıt Merkezi](https://www.microsoft.com/msrc) 'ndeki veri akışlarıyla makine öğrenimi güvenlik zekasını birleştirir. Kimlik koruması, yüksek riskli oturum açma işlemlerinin gerçek zamanlı korumasını sağlar. Bir oturumun risk profilini artırmak için, virüslü cihazlardan erişilen, ağları bir veya olağan dışı ve olası konumlardan erişim gibi önemli faktörleri göz önünde bulundurun. Bu risk profili gerçek zamanlı koruma için kullanılır. Bu raporların ve olayların birçoğu SıEM sistemlerinizle tümleştirme için bir API aracılığıyla zaten kullanılabilir.

* **Hizmet olarak uzaktan erişim**. Uzaktan erişimi etkinleştirmek için şirket içi sunucuları koruma ve düzeltme eki uygulama konusunda endişelenmeniz gerekmez. Uygulama proxy 'Si Microsoft 'un sahip olduğu bir internet ölçeklendirme hizmetidir, bu nedenle her zaman en son güvenlik düzeltme eklerini ve yükseltmelerini alırsınız. Çok sayıda saldırı için düzeltme eki yüklenmemiş yazılım hala hesaplar. Homeland Security departmanına göre, [hedeflenen saldırılardan yüzde 85 ' luk bir koruyucu tablo olduğundan](https://www.us-cert.gov/ncas/alerts/TA15-119A). Bu hizmet modeliyle, uç sunucularınızı yönetmenin ağır yükünü ve gerektiğinde bunlara yama yapmak zorunda kalmazsınız.

* **Intune tümleştirmesi**. Intune ile şirket trafiği kişisel trafikten ayrı olarak yönlendirilir. Uygulama proxy 'Si, şirket trafiğinin doğrulanmasını sağlar. [Uygulama proxy 'si ve Intune Managed Browser özelliği,](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) uzak kullanıcıların IOS ve Android cihazlarından iç Web sitelerine güvenli bir şekilde erişmesini sağlamak için birlikte da kullanılabilir.

### <a name="roadmap-to-the-cloud"></a>Bulutun yol haritası

Uygulama proxy 'Si uygulamanın başka bir avantajı da Azure AD 'yi şirket içi ortamınıza genişletedir. Aslında uygulama proxy 'Si uygulamak, kuruluşunuzu ve uygulamalarınızı buluta taşırken önemli bir adımdır. Buluta geçerek şirket içi kimlik doğrulamasından ayrıldığınızda, şirket içi parmak izinizi azaltır ve denetim düzlemi olarak Azure AD 'nin kimlik yönetimi özelliklerini kullanabilirsiniz. Mevcut uygulamalarda en az veya güncelleştirme olmadan, çoklu oturum açma, Multi-Factor Authentication ve merkezi yönetim gibi bulut özelliklerine erişebilirsiniz. Gerekli bileşenlerin uygulama proxy 'sine yüklenmesi, uzaktan erişim çerçevesi oluşturmaya yönelik basit bir işlemdir. Ve buluta geçerek, yüksek kullanılabilirlik ve olağanüstü durum kurtarma gibi en son Azure AD özelliklerine, güncelleştirmelerine ve işlevlerine erişebilirsiniz.

Uygulamalarınızı Azure AD 'ye geçirme hakkında daha fazla bilgi edinmek için bkz. [uygulamalarınızı Azure Active Directory 'ye geçirme](https://aka.ms/migrateapps/whitepaper) teknik incelemesi.

## <a name="architecture"></a>Mimari

Aşağıdaki diyagramda, son kullanıcılara şirket içi uygulamalarda çoklu oturum açma sağlamak için Azure AD kimlik doğrulama hizmetleri ve uygulama proxy 'Sinin birlikte nasıl çalıştığı gösterilmektedir.

![Azure AD Uygulama Ara Sunucusu kimlik doğrulama akışı](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Kullanıcı bir uç nokta aracılığıyla uygulamaya erişduktan sonra, Kullanıcı Azure AD oturum açma sayfasına yönlendirilir. Koşullu erişim ilkelerini yapılandırdıysanız, kuruluşunuzun güvenlik gereksinimleriyle uyumlu olduğundan emin olmak için belirli koşullar Şu anda denetlenir.
2. Başarılı bir oturum açma işleminden sonra Azure AD, kullanıcının istemci cihazına bir belirteç gönderir.
3. İstemci belirteci, belirteç üzerinden Kullanıcı asıl adını (UPN) ve güvenlik asıl adını (SPN) alan uygulama ara sunucusu hizmetine gönderir.
4. Uygulama proxy 'Si, uygulama proxy [Bağlayıcısı](application-proxy-connectors.md)tarafından seçilen isteği iletir.
5. Bağlayıcı, Kullanıcı adına gereken ek kimlik doğrulamasını gerçekleştirir (*kimlik doğrulama yöntemine bağlı olarak Isteğe bağlıdır*), uygulama sunucusunun iç uç noktasını ister ve isteği şirket içi uygulamaya gönderir.
6. Uygulama sunucusundan gelen yanıt bağlayıcı aracılığıyla uygulama proxy 'Si hizmetine gönderilir.
7. Yanıt, uygulama ara sunucusu hizmetinden kullanıcıya gönderilir.

|**Bileşen**|**Açıklama**|
|:-|:-|
|Uç Nokta|Uç nokta bir URL veya [Son Kullanıcı portalıdır](end-user-experiences.md). Kullanıcılar, dış bir URL 'ye erişerek ağınızın dışından uygulamalara erişebilir. Ağınızdaki kullanıcılar bir URL veya Son Kullanıcı Portalı aracılığıyla uygulamaya erişebilir. Kullanıcılar bu uç noktalardan birine gittiklerinde, Azure AD 'de kimlik doğrular ve ardından bağlayıcı aracılığıyla şirket içi uygulamaya yönlendirilir.|
|Azure AD|Azure AD, bulutta depolanan kiracı dizinini kullanarak kimlik doğrulamasını gerçekleştirir.|
|Uygulama proxy hizmeti|Bu uygulama proxy hizmeti, Azure AD 'nin bir parçası olarak bulutta çalışır. Kullanıcının oturum açma belirtecini, uygulama proxy Bağlayıcısı 'na geçirir. Uygulama proxy 'Si, istekteki tüm erişilebilir üstbilgileri iletir ve üst bilgileri protokol başına, istemci IP adresine ayarlar. Ara sunucuya gelen istek zaten bu üstbilgiye sahipse, istemci IP adresi üst bilginin değeri olan virgülle ayrılmış listenin sonuna eklenir.|
|Uygulama proxy Bağlayıcısı|Bağlayıcı, ağınız içindeki bir Windows Server üzerinde çalışan hafif bir aracıdır. Bağlayıcı, bulutta ve şirket içi uygulamada bulunan uygulama proxy hizmeti arasındaki iletişimi yönetir. Bağlayıcı yalnızca giden bağlantıları kullanır, bu nedenle herhangi bir gelen bağlantı noktasını açmanız veya DMZ 'e herhangi bir şey yerleştirmeniz gerekmez. Bağlayıcılar, gereken şekilde durum bilgisiz ve buluttan tanıtım bilgileri. Yük Dengeleme ve kimlik doğrulama gibi bağlayıcılar hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcılarını anlama](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory etki alanı hesapları için kimlik doğrulaması yapmak üzere şirket içinde çalışır. Çoklu oturum açma yapılandırıldığında, bağlayıcı, gereken ek kimlik doğrulamasını gerçekleştirmek için AD ile iletişim kurar.|
|Şirket içi uygulama|Son olarak, Kullanıcı şirket içi bir uygulamaya erişebilir.|

Azure AD Uygulama Ara Sunucusu, bulut tabanlı uygulama proxy hizmetinden ve şirket içi bağlayıcıdan oluşur. Bağlayıcı, uygulama ara sunucusu hizmetinden gelen istekleri dinler ve iç uygulamalarla bağlantıları işler. Tüm iletişimlerin TLS üzerinden gerçekleştiğini ve her zaman bağlayıcının uygulama ara sunucusu hizmetine kaynaklandığına dikkat edin. Diğer bir deyişle, iletişimler yalnızca giden ' dur. Bağlayıcı, tüm çağrılar için uygulama ara sunucusu hizmetinde kimlik doğrulamak üzere bir istemci sertifikası kullanır. Bağlantı güvenliği için tek istisna, istemci sertifikasının kurulduğu ilk kurulum adımıdır. Daha fazla [ayrıntı için bkz](application-proxy-security.md#under-the-hood) . uygulama proxy 'si.

### <a name="application-proxy-connectors"></a>Uygulama proxy bağlayıcıları

[Uygulama proxy bağlayıcıları](application-proxy-connectors.md) , bulutta uygulama ara sunucusu hizmetine giden bağlantıyı kolaylaştıran, şirket içinde dağıtılan hafif aracılardır. Bağlayıcıların, arka uç uygulamasına erişimi olan bir Windows Server 'a yüklenmesi gerekir. Kullanıcılar, trafiği aşağıda gösterildiği gibi bağlayıcılar aracılığıyla uygulamalara yönlendiren uygulama proxy 'Si bulut hizmetine bağlanır.

![Azure AD Uygulama Ara Sunucusu ağ bağlantıları](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Bir bağlayıcı ile uygulama proxy hizmeti arasında kurulum ve kayıt aşağıdaki gibi gerçekleştirilir:

1. BT Yöneticisi, 80 ve 443 bağlantı noktalarını giden trafiğe açar ve bağlayıcı, uygulama proxy hizmeti ve Azure AD için gereken çeşitli URL 'lere erişim sağlar.
2. Yönetici Azure portal oturum açar ve bir şirket içi Windows Server 'a bağlayıcı yüklemek için bir yürütülebilir dosya çalıştırır.
3. Bağlayıcı, uygulama proxy 'Si hizmeti için "dinler" a başlar.
4. Yönetici, şirket içi uygulamayı Azure AD 'ye ekler ve kullanıcıların uygulamalarına bağlanması için ihtiyaç duyduğu URL 'Ler gibi ayarları yapılandırır.

Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu dağıtımı planlaması](application-proxy-deployment-plan.md).

Yedeklilik ve ölçek için her zaman birden çok bağlayıcı dağıtmanız önerilir. Bağlayıcılar, hizmetle birlikte, tüm yüksek kullanılabilirliğe sahip görevlerden yararlanın ve dinamik olarak eklenebilir veya kaldırılabilir. Her yeni istek geldiğinde, kullanılabilir bağlayıcılardan birine yönlendirilir. Bir bağlayıcı çalışırken, hizmete bağlanırken etkin kalır. Bir bağlayıcı geçici olarak kullanılamıyorsa, bu trafiğe yanıt vermez. Kullanılmayan bağlayıcılar, etkin olmayan olarak etiketlenebilir ve 10 gün etkin olmama durumunda kaldırılır.

Bağlayıcılar Ayrıca, bağlayıcının daha yeni bir sürümü olup olmadığını öğrenmek için sunucuyu yoklayın. El ile güncelleştirme yapabilseniz de, uygulama proxy 'Si Bağlayıcı Güncelleştiricisi hizmeti çalıştığı sürece bağlayıcılar otomatik olarak güncelleşecektir. Birden çok bağlayıcı içeren kiracılar için, otomatik güncelleştirmeler ortamınızda kapalı kalma süresini engellemek için her bir gruptaki her seferinde bir bağlayıcıyı hedefleyin.

**Note**: uygulama proxy 'si [Sürüm geçmişi SAYFASıNı](application-proxy-release-version-history.md) , güncelleştirmelerin RSS akışına abone olunarak yayımlandığına ilişkin bildirim almak için izleyebilirsiniz.

Her uygulama proxy Bağlayıcısı bir [bağlayıcı grubuna](application-proxy-connector-groups.md)atanır. Aynı bağlayıcı grubundaki bağlayıcılar yüksek kullanılabilirlik ve yük dengeleme için tek bir birim işlevi görür. Yeni gruplar oluşturabilir, Azure portal bunlara bağlayıcılar atayabilir ve ardından belirli uygulamalara özel bağlayıcılar sunacak belirli bağlayıcılar atayabilirsiniz. Yüksek kullanılabilirlik için her bağlayıcı grubunda en az iki bağlayıcı olması önerilir.

Bağlayıcı grupları aşağıdaki senaryoları desteklemeniz gerektiğinde yararlı olur:

* Coğrafi uygulama yayımlama
* Uygulama kesimlemesi/yalıtımı
* Bulutta veya şirket içinde çalışan Web uygulamalarını yayımlama

Bağlayıcılarınızın nereye yükleneceğini ve ağınızı iyileştirerek kullanılacak yeri seçme hakkında daha fazla bilgi için, [Azure Active Directory uygulama ara sunucusu kullanırken ağ topolojisi konularına](application-proxy-network-topology.md)bakın.

## <a name="other-use-cases"></a>Diğer kullanım örnekleri

Bu noktaya kadar, tüm bulut ve şirket içi uygulamalarınız için çoklu oturum açmayı etkinleştirirken şirket içi uygulamaları dışarıdan yayımlamak için uygulama ara sunucusu 'nu kullanmaya odaklandık. Ancak, uygulama proxy 'Si için gereken başka kullanım örnekleri de vardır. Bunlara aşağıdakiler dahildir:

* **REST API 'Leri güvenli bir şekilde yayımlayın**. Şirket içinde çalışan veya buluttaki sanal makinelerde barındırılan iş mantığı veya API 'Ler olduğunda, uygulama proxy 'SI API erişimi için genel bir uç nokta sağlar. API uç noktası erişimi, gelen bağlantı noktalarına gerek kalmadan kimlik doğrulama ve yetkilendirmeyi denetlemenize olanak tanır. Intune kullanan masaüstleri, iOS, MAC ve Android cihazlar için Multi-Factor Authentication ve cihaz tabanlı koşullu erişim gibi Azure AD Premium özellikler aracılığıyla ek güvenlik sağlar. Daha fazla bilgi edinmek için bkz. [yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama](application-proxy-configure-native-client-application.md) ve [Azure Active Directory ve API Management ile OAUTH 2,0 kullanarak API 'yi koruma](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Uzak Masaüstü Hizmetleri** **(RDS)**. Standart RDS dağıtımları açık gelen bağlantılar gerektirir. Ancak, [uygulama proxy 'si Ile RDS dağıtımı](application-proxy-integrate-with-remote-desktop-services.md) , bağlayıcı hizmetini çalıştıran sunucudan kalıcı bir giden bağlantıya sahiptir. Bu şekilde, Uzak Masaüstü Hizmetleri aracılığıyla şirket içi uygulamaları yayımlayarak son kullanıcılara daha fazla uygulama sunabilirsiniz. Ayrıca, RDS 'ye sınırlı bir iki adımlı doğrulama ve koşullu erişim denetimi kümesiyle dağıtımın saldırı yüzeyini de azaltabilirsiniz.
* **WebSockets kullanarak bağlanan uygulamalar yayımlayın**. [Qlik Sense](application-proxy-qlik.md) desteği genel önizlemeye sunuldu ve gelecekte diğer uygulamalara genişletilir.
* **Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini** sağlar. Web uygulamalarını yayımlamak için Azure AD Uygulama Ara Sunucusu kullanabilirsiniz, ancak Azure AD kimlik doğrulama kitaplığı (ADAL) ile yapılandırılmış [yerel istemci uygulamalarını](application-proxy-configure-native-client-application.md) yayımlamak için de kullanılabilir. Yerel istemci uygulamaları bir cihaza yüklendiğinden, Web uygulamalarına bir tarayıcıdan erişildiği için Web Apps 'ten farklıdır.

## <a name="conclusion"></a>Sonuç

Çalışmamız ve kullandığımız araçlar hızlı bir şekilde değişiyor. Daha fazla çalışan, kendi cihazlarını çalışmaya ve hizmet olarak yazılım (SaaS) uygulamalarının kullanımını, kuruluşların verilerini yönetme ve güvenli hale getirme yollarından de gelişmelidir. Şirketler artık kendi duvarları içinde çalışmaz ve kenarlıklarını çevreleyecek bir Moin tarafından korunur. Veriler, hem şirket içi hem de bulut ortamlarında her zamankinden daha fazla konuma hareket eder. Bu evrimi, kullanıcıların üretkenliğini ve işbirliği yapabilmesini artırmaya yardımcı olmuştur, ancak hassas verilerin korunmasını daha zor hale getirir.

Azure AD 'yi kullanarak bir karma birlikte bulunma senaryosunda kullanıcıları yönetme veya yolculuğun buluta başlama süreciyle ilgileniyor olun, Azure AD Uygulama Ara Sunucusu uygulamak, hizmet olarak uzaktan erişim sağlayarak Şirket içi parmak izlerinizin boyutunu azaltmaya yardımcı olabilir.

Kuruluşlar, aşağıdaki avantajlardan yararlanmak için bugün uygulama proxy 'den yararlanmaya başlamalıdır:

* Geleneksel VPN veya diğer şirket içi Web yayımlama çözümlerini ve DMZ yaklaşımını koruma ile ilişkili ek yük olmadan şirket içi uygulamaları dışarıdan yayımlayın
* Tüm uygulamalarda çoklu oturum açma, Microsoft 365 veya diğer SaaS uygulamaları ve şirket içi uygulamalar dahil olmak üzere.
* Azure AD 'nin yetkisiz erişimi engellemek için telemetri Microsoft 365 yararlandığından bulut ölçeğinde güvenlik
* Şirket trafiğinin kimlik doğrulamasının yapıldığından emin olmak için Intune tümleştirmesi
* Merkezileşmeyi Kullanıcı hesabı yönetimi
* En son güvenlik düzeltme eklerine sahip olduğunuzdan emin olmak için otomatik güncelleştirmeler
* Yayımlandıklarında yeni özellikler; SAML çoklu oturum açma için en son destek ve uygulama tanımlama bilgilerinin ayrıntılı yönetimi

## <a name="next-steps"></a>Sonraki adımlar

* Azure AD Uygulama Ara Sunucusu planlama, çalıştırma ve yönetme hakkında bilgi için bkz. [azure ad uygulama ara sunucusu dağıtımı](application-proxy-deployment-plan.md)planlama.
* Canlı bir tanıtımı zamanlamak veya değerlendirme için ücretsiz 90 günlük deneme sürümü almak için bkz. [Enterprise Mobility + Security kullanmaya başlama](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
