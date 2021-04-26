---
title: F5 VPN ile Azure AD güvenli karma erişimi | Microsoft Docs
description: Parola daha az VPN için F5 BIG-IP ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory için öğretici
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730896"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Parola açısından daha az VPN için F5 BIG-IP ile çoklu oturum açma tümleştirmesi Azure Active Directory öğreticisi

Bu öğreticide, güvenli karma erişim (SHA) için Azure Active Directory (AD) ile F5's büyük IP tabanlı güvenli yuva katmanı sanal özel ağ (SSL-VPN) çözümünü tümleştirmeyi öğrenin.

BÜYÜK IP SSL VPN 'yi Azure AD ile tümleştirmek, aşağıdakiler de dahil olmak üzere [birçok önemli avantaj](f5-aad-integration.md)sağlar:

- [Azure AD ön kimlik doğrulama ve yetkilendirme](../../app-service/overview-authentication-authorization.md) aracılığıyla geliştirilmiş sıfır güven

- [VPN hizmetine parola-daha az kimlik doğrulama](https://www.microsoft.com/security/business/identity/passwordless)

- Tek bir denetim düzleminden kimlikleri ve erişimi yönetme- [Azure Portal](https://portal.azure.com/#home)

Bu harika değer eklemesine rağmen, klasik VPN, güvenilen bir ağ çevre kavramı üzerinde tahmine açık kalır. Şirket varlıkları artık kurumsal bir veri merkezinin duvarlarıyla sınırlandırmadıklarından, ancak sabit sınır olmadan çok bulut ortamlarında, bu model artık gerçek bir sıfır güven itimi elde etmek için geçerli değildir. Bu nedenle, müşterilerimizin [uygulama temelinde erişimi yönetme konusunda](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)daha fazla kimliğe dayalı bir yaklaşıma geçmeyi düşünmelerini öneririz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu senaryoda, SSL-VPN hizmetinin büyük IP APM örneği bir SAML servis sağlayıcısı (SP) olarak yapılandırılır ve Azure AD, güvenilen SAML ıDP 'nin ön kimlik doğrulaması sağlar. Azure AD 'den çoklu oturum açma (SSO), büyük IP APM 'ye talep tabanlı kimlik doğrulama yoluyla sağlanır ve sorunsuz bir VPN erişim deneyimi sağlar.

![Görüntü SSL-VPN mimarisini gösterir](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Bu kılavuz boyunca başvurulan tüm örnek dizeler veya değerler gerçek ortamınız için olanlarla değiştirilmelidir.

## <a name="prerequisites"></a>Önkoşullar

Önceki deneyim veya F5 BIG-IP bilgisi gerekli değildir, ancak şunları yapmanız gerekir:

- Azure AD [ücretsiz aboneliği](https://azure.microsoft.com/trial/get-started-active-directory/) veya üzeri

- Kullanıcı kimlikleri [, şirket içi dizinlerinden](../hybrid/how-to-connect-sync-whatis.md) Azure AD 'ye eşitlenmelidir.

- Azure AD uygulama Yöneticisi [izinlerine](../roles/permissions-reference.md#application-administrator) sahip bir hesap

- BÜYÜK IP 'den gelen ve giden istemci trafiğinin yönlendirilmesini içeren mevcut bir büyük IP altyapısı veya [büyük IP sanal sürümünü Azure 'a dağıtın](f5-bigip-deployment-guide.md).

- BÜYÜK IP yayınlanan VPN hizmeti için bir kaydın, test sırasında ortak DNS 'de veya bir test istemcisinin localhost dosyasında bulunması gerekir

- BÜYÜK IP 'nin HTTPS üzerinden hizmet yayımlamak için gereken SSL sertifikalarıyla sağlanması gerekir.

[F5 büyük IP terminolojisi](https://www.f5.com/services/resources/glossary) ile alıştırarak kendiniz de öğreticide başvurulan çeşitli bileşenleri anlamanıza yardımcı olur.

>[!NOTE]
>Bu kılavuzdaki yönergeler ve Azure portal gördükleriniz arasında herhangi bir değer bulursanız Azure sürekli gelişmektedir. Ekran görüntüleri, büyük IP v15, ancak v 13.1 ' den görece benzer şekilde kalır.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD Galerisi 'nden F5 BIG-IP ekleyin

BÜYÜK IP arasında bir SAML Federasyonu güveni ayarlamak, Azure AD büyük IP 'nin yayımlanan VPN hizmetine erişim vermeden önce Azure AD 'ye ön kimlik doğrulama ve [koşullu erişim](../conditional-access/overview.md) olanağı sağlar.

1. Uygulama yöneticisi haklarına sahip bir hesap kullanarak Azure AD portalında oturum açın

2. Sol gezinti bölmesinden **Azure Active Directory hizmetini** seçin

3. **Kurumsal uygulamalara** gidin ve üst şeritte **Yeni uygulama**' yı seçin.

4. Galeride F5 araması yapın ve **F5 büyük IP APM Azure AD tümleştirmesi**' ni seçin.

5. Uygulama için bir ad girin, ardından kiracınıza eklenmesini sağlamak için **ekle/oluştur** ' u ekleyin. Kullanıcı, adı Azure ve Office 365 uygulama portallarında bir simge olarak görebilir. Ad, belirli bir hizmeti yansıtmalıdır. Örneğin, VPN.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

1. Yeni F5 uygulama özelliklerinizi görünümünde,   >  **Çoklu oturum açmayı** Yönet ' e gidin.

2. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin. Hayır ' ı seçerek çoklu oturum açma ayarlarını kaydetme isteğini atlayın **, daha sonra Kaydet '** i seçin.

3. **SAML ile çoklu oturum açmayı ayarla** menüsünde, aşağıdaki ayrıntıları sağlamak IÇIN **temel SAML yapılandırması** kalem simgesini seçin:

   - Önceden tanımlanmış **tanımlayıcı URL** 'SINI, büyük IP yayınlanan hizmetinizin URL 'siyle değiştirin. Örneğin, `https://ssl-vpn.contoso.com`

   - SAML uç noktası yolu da dahil olmak üzere **yanıt URL 'si** metin kutusuyla aynısını yapın. Örneğin, `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - Bu yapılandırmada tek başına uygulama, Azure AD 'nin büyük IP SAML hizmetine yönlendirmeden önce kullanıcıyı bir SAML onaylama işlemi ile verdiği bir ıDP tarafından başlatılan modda çalışır. IDP tarafından başlatılan modu desteklemeyen uygulamalar için, büyük IP SAML hizmeti için **oturum açma URL 'sini** belirtin. Örneğin, `https://ssl-vpn.contoso.com`.

   - Logout URL 'Si için, yayımlanan hizmetin ana bilgisayar üst bilgisi tarafından önceden bekletilen büyük IP APM çoklu oturum kapatma (SLO) uç noktasını girin. Örneğin, `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   SLO URL 'SI sağlamak, Kullanıcı oturumu kapattıktan sonra büyük IP ve Azure AD 'nin her iki ucunda da bir kullanıcı oturumunun sonlandırılmasını sağlar. BÜYÜK-IP APM Ayrıca belirli bir uygulama URL 'SI çağrılırken tüm oturumları sonlandırma [seçeneği](https://support.f5.com/csp/article/K12056) sağlar.

![Görüntüde temel SAML yapılandırması gösteriliyor](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>TMOS V16 'den SAML SLO uç noktası/SAML/SP/profile/Redirect/SLO olarak değiştirildi

4. SAML yapılandırma menüsünden çıkmadan önce **Kaydet** ' i SEÇIN ve SSO test istemi ' ni atlayın.

Azure AD, bunları büyük IP APM kimlik doğrulaması için kullanıcılara sunacak olduğundan, **Kullanıcı öznitelikleri & talepler** bölümünün özelliklerini gözlemleyin.

![Görüntü, Kullanıcı öznitelikleri taleplerini gösterir](media/f5-sso-vpn/user-attributes-claims.png)

BÜYÜK IP yayınlanan hizmetinizdeki diğer belirli talepleri ekleyebilirsiniz, ancak varsayılan kümesine ek olarak tanımlanan tüm talepler yalnızca Azure AD 'de varsa doldurulmuş öznitelikler olarak verilmek üzere de verilebilir. Aynı şekilde, Dizin [rolleri veya grup](../hybrid/how-to-connect-fed-group-claims.md) üyelikleri, bir talep olarak verilebilmesi IÇIN Azure AD 'de bir kullanıcı nesnesine karşı tanımlamayı de gerektirir.

![Görüntü, Federasyon meta verileri indirme bağlantısını gösterir](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD tarafından oluşturulan SAML imzalama sertifikalarının üç yıl boyunca bir ömrü vardır; bu nedenle Azure AD yayımlanmış Kılavuzu kullanılarak yönetilmesi gerekir.

### <a name="azure-ad-authorization"></a>Azure AD yetkilendirmesi

Varsayılan olarak, Azure AD yalnızca bir hizmete erişim izni verilen kullanıcılara belirteç verir.

1. Hala uygulamanın yapılandırma görünümünde **Kullanıcılar ve gruplar** ' ı seçin.

2. **+ Kullanıcı Ekle** ' yi seçin ve atama Ekle menüsünde **Kullanıcılar ve gruplar ' ı** seçin.

3. **Kullanıcılar ve gruplar** iletişim kutusunda VPN 'e erişim yetkisi olan kullanıcı gruplarını ekleyin ve ardından ata ' yı **seçin**  >   .

![Görüntü Kullanıcı bağlantısı eklemeyi gösterir ](media/f5-sso-vpn/add-user-link.png)

4. Bu işlem, SAML Federasyonu güveninin Azure AD bölümünü tamamlar. BÜYÜK IP APM artık SSL-VPN hizmetini yayımlamak ve SAML ön kimlik doğrulaması için güveni tamamlamaya yönelik bir özellik kümesiyle yapılandırmak üzere ayarlanabilir.

## <a name="big-ip-apm-configuration"></a>BÜYÜK-IP APM yapılandırması

### <a name="saml-federation"></a>SAML Federasyonu

Aşağıdaki bölümde, Azure AD ile VPN hizmetini federasyona eklemek için gereken büyük IP SAML hizmet sağlayıcısı ve karşılık gelen SAML ıDP nesneleri oluşturulur.

1.   >  **Federasyon**  >  **SAML hizmeti sağlayıcısı**  >  **Yerel SP Hizmetleri** ' ne gidin ve **Oluştur** ' u seçin.

![Görüntüde büyük IP SAML yapılandırması gösteriliyor](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Daha önce Azure AD 'de tanımladığınız bir **ad** ve aynı **varlık kimliği** değerini ve uygulamaya bağlanmak IÇIN kullanılacak ana bilgisayar FQDN 'sini girin

![Görüntü yeni SAML SP hizmeti oluşturmayı gösterir](media/f5-sso-vpn/create-new-saml-sp.png)

   SP **adı** ayarları yalnızca, varlık KIMLIĞI yayımlanan URL 'nin ana bilgisayar adı bölümüyle tam eşleşme değilse veya normal ana bilgisayar adı tabanlı URL biçiminde değilse gereklidir. Varlık KIMLIĞI ise, yayımlanan uygulamanın dış şemasını ve ana bilgisayar adını belirtin `urn:ssl-vpn:contosoonline` .

3. Aşağı kaydırarak yeni **SAML SP nesnesini** seçin ve **IDP bağlayıcıları bağla/ciltten çıkar**' ı seçin.

![Görüntü, yerel SP hizmeti ile Federasyon oluşturmayı gösterir](media/f5-sso-vpn/federation-local-sp-service.png)

4. **Yeni IDP Bağlayıcısı oluştur** ' u seçin ve açılan menüden **meta** verilerden seçin

![Görüntüde yeni ıDP Bağlayıcısı oluştur görüntülenir](media/f5-sso-vpn/create-new-idp-connector.png)

5. Daha önce indirdiğiniz Federasyon meta verileri XML dosyasına gidin ve dış SAML ıDP 'yi temsil edecek APM nesnesi için bir **kimlik sağlayıcı adı** sağlayın

6. Yeni bir **satır ekle** ' yi seçerek yenı Azure AD dış IDP bağlayıcısını seçin.

![Görüntü dış ıDP bağlayıcısını gösterir](media/f5-sso-vpn/external-idp-connector.png)

7. SAML SP nesnesini SAML ıDP nesnesine bağlamak için **Güncelleştir** ' i seçin ve ardından **Tamam**' ı seçin.

![Görüntü, SP kullanarak SAML ıDP 'yi gösterir](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>WebTop yapılandırması

Aşağıdaki adımlar, SSL-VPN ' i büyük IP 'nin özel Web portalı aracılığıyla kullanıcılara sunulacak şekilde etkinleştirir.

1.   >  **Webol**  >  **WebTop listelerine** erişin ve **Oluştur**' u seçin.

2. Portala bir ad verin ve türü **tam** olarak ayarlayın. Örneğin, `Contoso_webtop`.

3. Kalan tercihleri ayarlayın ve ardından **bitti**' yi seçin.

![Görüntü WebTop yapılandırmasını gösterir](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN yapılandırması

VPN özelliği, her biri genel hizmetin farklı bir yönünü denetleyen birkaç öğeden oluşur.

1. **Erişim**  >  **bağlantısı/VPN**  >  **ağ erişimi (VPN)**  >  **IPv4 Kiralama havuzlarına** gidin ve **Oluştur**' u seçin.

2. VPN istemcilerine ayrılan IP adresleri havuzu için bir ad girin. Örneğin, Contoso_vpn_pool

3. Türü **IP adres aralığı** olarak ayarlayın ve ardından **Ekle** ve **bitti**' yi seçerek bir başlangıç ve bitiş IP 'si sağlayın.

![Görüntü VPN yapılandırmasını gösterir](media/f5-sso-vpn/vpn-configuration.png)

Bir ağ erişim listesi, VPN havuzundan IP ve DNS ayarları, Kullanıcı yönlendirme izinleri ve ayrıca gerekirse uygulamalar başlatabilir hizmeti sağlar.

1. **Erişim**  >  **bağlantısı/VPN: ağ erişimi (VPN)**  >  **ağ erişim listeleri** ' ne gidin ve **Oluştur**' u seçin.

2. VPN erişim listesi ve açıklamalı alt yazı için bir ad girin, örneğin, contoso-VPN, sonrasında **tamamlandı**.

![Görüntü ağ erişim listesinde VPN yapılandırmasını gösterir](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Üst şeritte **ağ ayarları** ' nı seçin ve aşağıdaki ayarları ekleyin:

   • **Desteklenen IP sürümü**: IPv4

   • **IPv4 kiralama havuzu**: daha önce oluşturulan VPN havuzunu seçin; örneğin, Contoso_vpn_pool

![Görüntüde contoso VPN havuzu görüntülenir](media/f5-sso-vpn/contoso-vpn-pool.png)

   Istemci ayarları seçenekleri, bir VPN oluşturulduğunda istemci trafiğinin nasıl yönlendirildiği konusunda kısıtlamalar uygulamak için kullanılabilir.

4. **Tamamlandı** ' ı seçin ve ayarları eklemek için DNS/konaklar sekmesine gidin:

   • **IPv4 birincil ad sunucusu**: ortamınızın DNS sunucusunun IP 'si

   • **DNS varsayılan etki alanı soneki**: Bu belirli VPN bağlantısının etki alanı son eki. Örneğin, contoso.com

![Görüntüde varsayılan etki alanı son eki gösterilir](media/f5-sso-vpn/domain-suffix.png)

[F5 makalesinde](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) , diğer ayarları tercihlerinize göre ayarlama hakkında ayrıntılar sunulmaktadır.

VPN hizmetinin desteklemesi gereken her VPN istemci türünün ayarlarını yapılandırmak için artık büyük IP bağlantı profili gereklidir. Örneğin, Windows, OSX ve Android.

1. **Erişim**  >  **bağlantısı/VPN**  >  **bağlantı**  >  **profillerine** gidin ve **Ekle**' yi seçin.

2. Bir profil adı girin ve üst profili **/Common/connectivity** olarak ayarlayın, örneğin Contoso_VPN_Profile.

![Görüntü yeni bağlantı profili oluştur ' a gösteriliyor](media/f5-sso-vpn/create-connectivity-profile.png)

F5's [belgeleri](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) , istemci desteği hakkında daha fazla ayrıntı sağlar.

## <a name="access-profile-configuration"></a>Erişim profili yapılandırması

VPN nesneleri yapılandırıldığında, SAML kimlik doğrulaması için hizmeti etkinleştirmek üzere bir erişim ilkesi gerekir.

1. **Erişim**  >  **profilleri/ilkeleri**  >  **erişim profillerine gidin (oturum başına ilkeler)** ve **Oluştur** ' u seçin.

2. Profil adı ve profil türü için **Tümü** seçin, örneğin Contoso_network_access

3. **Kabul edilen diller** listesine en az bir dil eklemek için aşağı kaydırın ve **bitti** ' yi seçin

![Görüntüde genel özellikler gösteriliyor](media/f5-sso-vpn/general-properties.png)

4. Görsel ilke Düzenleyicisi 'nin ayrı bir tarayıcı sekmesinde başlatılması için yeni erişim profilinin Per-Session Ilkesi alanında **Düzenle** ' yi seçin.

![Görüntü oturum başına ilkeyi gösterir](media/f5-sso-vpn/per-session-policy.png)

5. Oturum aç ' a tıklayın **+** ve açılır menüden **kimlik doğrulama**  >  **SAML** kimlik doğrulaması  >  **Ekle öğesini** seçin.

6. SAML kimlik doğrulaması SP yapılandırması ' nda, daha önce oluşturduğunuz VPN SAML SP nesnesini ve ardından **Kaydet**' i seçin.

![Görüntüde SAML kimlik doğrulaması gösterilmektedir](media/f5-sso-vpn/saml-authentication.png)

7. **+** SAML kimlik doğrulaması 'Nın başarılı dalı için seçin.

8. Atama sekmesinden **Gelişmiş kaynak ata** ' yı ve ardından **öğe Ekle** ' yi seçin.

![Görüntü avans kaynağı atamasını gösterir](media/f5-sso-vpn/advance-resource-assign.png)

9. Açılır pencerede **yeni giriş** ' i ve ardından **Ekle/Sil**' i seçin.

10. Alt pencerede **ağ erişimi** ' ni seçin ve daha önce oluşturulan ağ erişim profilini seçin

![Görüntü yeni ağ erişim girişi eklemeyi gösterir](media/f5-sso-vpn/add-new-entry.png)

11. **WebTop** sekmesine geçin ve daha önce oluşturulan WebTop nesnesini ekleyin.

![Görüntü WebTop nesnesi eklemeyi gösterir](media/f5-sso-vpn/add-webtop-object.png)

12. **Güncelleştir** ' i ve ardından **Kaydet**' i seçin.

13. Başarılı dalı, sonra **Kaydet** olarak **değiştirmek için üst** reddetme kutusunda bağlantıyı seçin.

![Görüntü yeni görsel ilke düzenleyicisini gösterir](media/f5-sso-vpn/vizual-policy-editor.png)

14. **Erişim Ilkesi Uygula** ' yı seçerek bu ayarları işleyin ve görsel ilke Düzenleyicisi sekmesini kapatın.

![Görüntü yeni erişim ilkesi yöneticisini gösterir](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>VPN hizmetini yayımlama

Tüm ayarlar yerinde olduğunda APM artık, VPN 'ye bağlanan istemcileri dinlemek için bir ön uç sanal sunucusu gerektirir.

1. **Yerel trafik**  >  **sanal sunucuları**  >  **sanal sunucu listesini** seçin ve **Oluştur**' u seçin.

2. VPN sanal sunucusu için bir **ad** sağlayın (örneğin, **VPN_Listener**).

3. Sanal sunucuya, istemci trafiği almak için yönlendirmenin bulunduğu kullanılmamış bir **IP hedef adresi** sağlayın

4. Hizmet bağlantı noktasını **443 HTTPS** olarak ayarlayın ve durumun **etkin** olduğundan emin olun

![Görüntüde yeni sanal sunucu gösteriliyor](media/f5-sso-vpn/new-virtual-server.png)

5. **Http profilini** http olarak ayarlayın ve önkoşulların bir parçası olarak SAĞLADıĞıNıZ ortak SSL SERTIFIKASı Için SSL profilini (istemci) ekleyin.

![Görüntü, SSL profilini gösterir](media/f5-sso-vpn/ssl-profile.png)

6. Erişim Ilkesi altında, **erişim profilini** ve **bağlantı profilini** oluşturulan VPN nesnelerini kullanacak şekilde ayarlayın.

![Görüntü erişim ilkesini gösterir](media/f5-sso-vpn/access-policy.png)

7. Bittiğinde **bitti** ' yi seçin.

8.  SSL-VPN hizmetiniz artık doğrudan URL 'SI veya Microsoft 'un uygulama portalları aracılığıyla SHA aracılığıyla yayımlanır ve erişilebilir.

## <a name="additional-resources"></a>Ek kaynaklar

- [Parolaların sonu, parolasız go](https://www.microsoft.com/security/business/identity/passwordless)

- [Koşullu Erişim nedir?](../conditional-access/overview.md)

- [Uzak çalışmayı etkinleştirmek için Microsoft sıfır güven çerçevesi](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure AD ile tam uygulama tümleştirmesi için beş adım](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Sonraki adımlar

Uzak bir Windows istemcisinde bir tarayıcı açın ve **büyük IP VPN hizmetinin** URL 'sine gidin. Azure AD kimlik doğrulamasından geçtikten sonra, büyük IP Web üst portalı ve VPN başlatıcısı 'nı görürsünüz.

![Görüntü VPN başlatıcısı 'nı gösterir](media/f5-sso-vpn/vpn-launcher.png)

VPN kutucuğunu seçmek, büyük IP uç istemcisini yükler ve SHA için yapılandırılmış bir VPN bağlantısı kurar.
F5 VPN uygulaması, Azure AD koşullu erişim 'de de hedef kaynak olarak görünür olmalıdır. Koşullu erişim ilkeleri oluşturmaya yönelik [kılavuzumuza](../conditional-access/concept-conditional-access-policies.md) bakın ve ayrıca Azure AD [parola-daha az kimlik doğrulama](https://www.microsoft.com/security/business/identity/passwordless)için kullanıcıları etkinleştirin.