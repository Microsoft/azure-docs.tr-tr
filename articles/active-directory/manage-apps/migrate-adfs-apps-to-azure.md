---
title: Uygulamalar AD FS'den Azure AD'ye taşıyın. | Microsoft Docs
description: Bu makalede, kuruluşların uygulamalar Federasyon SaaS uygulamalarına odaklanarak, Azure AD'ye taşıma anlamalarına yardımcı olmak için tasarlanmıştır.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c77b03c6e1f2240059d884b051e00b01836d714
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724014"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Uygulamalar AD FS'den Azure AD'ye taşıma 

Bu makalede uygulamalarını AD FS'den Azure Active Directory (Azure AD) taşıma anlamanıza yardımcı olur. Özellikle federasyon SaaS uygulamalarına odaklanır.

Bu makalede adım adım yönergeler sağlanmaz. Şirket içi yapılandırmaların Azure AD'ye nasıl dönüştürüldüğünü anlayarak geçişi başarmanıza yardımcı olmak için kavramsal rehberlik sağlanır. Ayrıca yaygın senaryoları da kapsar.

## <a name="introduction"></a>Giriş

Kullanıcı hesaplarının bulunduğu şirket içi bir dizininiz varsa, büyük olasılıkla en az bir veya iki uygulamanız vardır. Bu uygulamalar da, kullanıcıların söz konusu kimliklerle oturum açarak erişmeleri için yapılandırılmıştır.

Ayrıca siz de çoğu kuruluş gibiyseniz, büyük olasılıkla bulut uygulamaları ve kimliklerini benimseme yolunda ilerliyorsunuz demektir. Belki de Office 365 ve Azure AD Connect ile hızla yol alıyorsunuzdur. Tüm iş yükleri için değil yalnızca önemli bazı iş yükleri için bulut tabanlı SaaS uygulamalarını kurmuş da olabilirsiniz.  

Birçok kuruluşun doğrudan Active Directory Federation Service (AD FS) gibi bir şirket içi oturum açma hizmetine federasyon oluşturan SaaS veya özel iş kolu (LOB) uygulamaları, ayrıca Office 365 ve Azure AD tabanlı uygulamaları vardır. Bu kılavuzda nedenini açıklar ve uygulamalarınızı Azure AD'ye taşıma.

> [!NOTE]
> Bu kılavuzda SaaS uygulaması yapılandırma ve geçişi hakkında ayrıntılı bilgi ve özel LOB uygulamaları hakkında üst düzey bilgi sağlanır. Gelecekte özel LOB uygulamalarına yönelik daha ayrıntılı bir kılavuz sağlanması planlanmaktadır.

![Bağlı uygulamaları doğrudan şirket içine](media/migrate-adfs-apps-to-azure/migrate1.png)

![Azure AD Federasyon oluşturan uygulamalar](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Uygulamaları Azure AD'ye taşınıyor nedenleri

Zaten AD FS, Ping veya başka bir şirket içi kimlik doğrulama sağlayıcısı kullanan bir kuruluş için uygulamaları Azure AD'ye taşınıyor, aşağıdaki avantajları sağlar:

- **Daha güvenli erişim**

  - Azure multi-Factor Authentication dahil ayrıntılı uygulama başına erişim denetimlerini yapılandırın [Azure AD koşullu erişim](../active-directory-conditional-access-azure-portal.md). İlkeler, aynı bugün Office 365'le uyguluyor olabileceğiniz şekilde SaaS uygulamalarına ve özel uygulamalara da uygulanabilir.
  - Tehditleri algılamak ve riskli trafiği tanımlayan makine öğrenme ve buluşsal yöntemler temelinde oturum açma bilgilerini korumaya yardımcı olmak için, [Azure AD Kimlik Koruması](../active-directory-identityprotection.md)'ndan yararlanın.

- **Azure AD B2B işbirliği**

  SaaS uygulamalarında oturum açma işlemi Azure AD temelinde yapıldıktan sonra, [Azure AD B2B işbirliğiyle](../b2b/what-is-b2b.md) iş ortaklarına bulut kaynakları için erişim verebilirsiniz.

- **Azure AD'nin daha kolay yönetici deneyimi ve ek özellikleri**
  
  SaaS uygulamaları için bir kimlik sağlayıcısı olan Azure AD, aşağıdakiler gibi ek özellikleri destekler:
  - Uygulama başına belirteç imzalama sertifikaları.
  - [Yapılandırılabilir sertifika sona erme tarihleri](manage-certificates-for-federated-single-sign-on.md).
  - Azure AD kimlikleri temelinde kullanıcı hesaplarının (önemli Azure Market uygulamalarında) [otomatik sağlanması](user-provisioning.md).

- **Şirket içi bir kimlik sağlayıcısının avantajlarını koruma**
  
  Azure AD'nin avantajlarını elde ederken, kimlik doğrulaması için şirket içi çözümünüzü kullanmaya devam edebilirsiniz. Bu şekilde, şirket için Multi-Factor Authentication çözümleri, günlük ve denetim gibi avantajlar da yitirilmez.

- **Şirket içi kimlik sağlayıcısının devre dışı bırakılmasına yardımcı olma**
  
  Şirket içi kimlik doğrulaması ürün devre dışı bırakmak istediğiniz kuruluşlarda, uygulamaları Azure AD'ye taşınıyor, kolay geçiş'göz önünden işinin bir kısmını alarak sağlar.

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Şirket içi uygulama türlerini Azure AD'deki uygulama türleriyle eşleme

Uygulamaların çoğu, kullandıkları oturum açma türüne göre birkaç kategoriden birine girer. Bu kategoriler, uygulamanın Azure AD'de nasıl temsil edildiğini belirler.

Kısacası, SAML 2.0 uygulamaları Market'teki Azure AD uygulama galerisi yoluyla veya Market dışı uygulamalar olarak Azure AD ile tümleştirilebilir. OAuth 2.0 veya OpenID Connect kullanan uygulamalar Azure AD ile benzer biçimde, *uygulama kayıtları* olarak tümleştirilebilir. Daha ayrıntılı bilgi için okumaya devam edin.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federasyon SaaS uygulamaları - özel LOB uygulamaları

Federasyon uygulamaları, şu kategorilere giren uygulamaları içerir:

- SaaS uygulamaları 
    - Kullanıcılarınız Salesforce, ServiceNow veya Workday gibi SaaS uygulamalarında oturum açıyorsa ve AD FS veya Ping gibi bir şirket içi kimlik sağlayıcısıyla tümleştiriyorsanız, SAS uygulamaları için federasyon oturum açma işlemini kullanıyorsunuz demektir.
    - Uygulamalar federasyon oturum açma işlemi için genellikle SAML 2.0 protokolünü kullanır.
    - Bu kategoriye giren uygulamalar Azure AD ile Market'teki veya Market dışındaki uygulamalar şeklinde kurumsal uygulamalar olarak tümleştirilebilir.
- Özel LOB uygulamaları
    - Bu terim kuruluşunuzun içinde geliştirilen veya veri merkezinize yüklenmiş standart paketli ürün olarak sağlanan SaaS dışı uygulamaları belirtir. Bu kategori, SharePoint uygulamalarını ve Windows Identity Foundation üzerinde oluşturulan uygulamaları içerir.
    - Uygulamalar federasyon oturum açma işlemi için SAML 2.0, WS-Federasyon, OAuth veya OpenID Connect kullanabilir.
    - OAuth 2.0 veya OpenID Connect veya WS-Federasyon kullanan özel uygulamalar Azure AD ile uygulama kayıtları olarak tümleştirilebilir. SAML 2.0 veya WS-Federasyon kullanan özel uygulamalar kurumsal uygulamaların içinde Market dışı uygulamalar olarak tümleştirilebilir.

### <a name="non-federated-apps"></a>Federasyon olmayan uygulamalar

Federasyon olmayan uygulamaları, Azure AD Uygulama Ara Sunucusu ve bununla ilgili özellikleri kullanılarak Azure AD ile tümleştirebilirsiniz. Federasyon olmayan uygulamalar şunlardır:

- Doğrudan Active Directory'yle Windows Tümleşik Kimlik Doğrulaması kullanan uygulamalar. Bu uygulamaları, [Azure AD Uygulama Ara Sunucusu](application-proxy-add-on-premises-application.md) üzerinden Azure AD'yle tümleştirebilirsiniz.
- Bir aracı yoluyla çoklu oturum açma sağlayıcınızla tümleştirilen ve yetkilendirme için üst bilgi kullanan uygulamalar. Oturum açma işlemi için yüklü bir aracıyı kullanan ve üst bilgiye dayalı yetkilendirme yapan şirket içi uygulamaları, [Azure AD için Ping Access](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/) ile Azure AD Uygulama Ara Sunucusu üzerinden Azure AD tabanlı oturum açma için yapılandırılabilir.

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Şirket içi federasyon uygulamalarını Azure AD'ye çevirme

AD FS ile Azure AD'nin çalışmaları birbirine benzer; dolayısıyla güven yapılandırma, oturum açma ve oturum kapatma URL'leri ve tanımlayıcı gibi kavramlar her ikisi için de geçerlidir. Bununla birlikte, geçişi yaparken bazı küçük farklılıkları anlamanız gerekir.

Aşağıdaki tabloda, çevirinize yardımcı olmak için AD FS, Azure AD ve SaaS uygulamalarında paylaşılan bazı önemli fikirler eşleştirilir.

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Azure AD veya AD FS'de uygulamaları gösterme

Geçiş işlemi, uygulamanın şirket içinde nasıl yapılandırıldığını değerlendirerek ve bu yapılandırmayı Azure AD'ye eşleyerek başlar. Aşağıdaki tabloda, AD FS bağlı olan taraf yapılandırma öğelerinin Azure AD'de bunlara karşılık gelen öğelerle eşlemesini bulabilirsiniz.

- AD FS terimi: Bağlı olan taraf veya bağlı olan taraf güveni.
- Azure AD terimi: Kurumsal uygulama veya uygulama kaydı (uygulamanın türüne) bağlı olarak.

|Uygulama yapılandırma öğesi|Açıklama|AD FS yapılandırmasındaki konum|Azure AD yapılandırmasında buna karşılık gelen konum|SAML belirteç öğesi|
|-----|-----|-----|-----|-----|
|Uygulama oturum açma URL'si|Bu uygulamanın oturum açma sayfasının URL'si. Burası, kullanıcının SP tarafından başlatılmış bir SAML akışında uygulamada oturum açmak için gittiği yerdir.|Yok|Azure AD'de oturum açma URL'si Azure Portal'ın içinde, uygulamanın **Çoklu oturum açma** özelliklerinde oturum açma URL'si olarak yapılandırılır.</br></br>(Oturum açma URL'sini görmek için **Gelişmiş URL ayarlarını göster**'i seçmeniz gerekebilir.)|Yok|
|Uygulama yanıt URL'si|Kimlik sağlayıcısının (IdP) perspektifinden uygulamanın URL'si. Burası, kullanıcı IdP'de oturum açtıktan sonra kullanıcının ve belirtecin gönderildiği yerdir.</br></br> Bazen "SAML onay belgesi tüketici uç noktası" olarak da adlandırılır.|Uygulamanın AD FS bağlı olan taraf güveninde bulunur. Bağlı olan tarafa sağ tıklayın, **Özellikler**'i seçin ve sonra da **Uç Noktalar** sekmesini seçin.|Azure AD'de yanıt URL'si Azure Portal'ın içinde, uygulamanın **Çoklu oturum açma** özelliklerinde yanıt URL'si olarak yapılandırılır.</br></br>(Yanıt URL'sini görmek için **Gelişmiş URL ayarlarını göster**'i seçmeniz gerekebilir.)|SAML belirtecindeki **Destination** öğesiyle eşlenir.</br></br> Örnek değer: `https://contoso.my.salesforce.com`|
|Uygulama oturumu kapatma URL'si|Kullanıcı uygulamada oturumunu kapattığında, IdP'nin kullanıcı oturumu açtığı diğer tüm uygulamalarda da oturumu kapatmak için “oturum kapatma temizleme” isteklerinin gönderildiği URL.|AD FS Yönetimi'nde, **Bağlı Olan Taraf Güvenleri**'nin altında bulunur. Bağlı olan tarafa sağ tıklayın, **Özellikler**'i seçin ve sonra da **Uç Noktalar** sekmesini seçin.|Yok. Azure AD, tüm uygulamalarda oturumun kapatılması anlamına gelen “çoklu oturum kapatma” işlemini desteklemez. Kullanıcının yalnızca Azure AD oturumunu kapatır.|Yok|
|Uygulama tanımlayıcısı|IdP’nin perspektifinden uygulamanın tanımlayıcısı. Tanımlayıcı olarak çoğunlukla oturum açma URL değeri kullanılır (ama her zaman kullanılmaz).</br></br> Bazen uygulama bunu “varlık kimliği" olarak adlandırır.|AD FS'de, bu bağlı olan taraf kimliğidir. Bağlı olan taraf güvenine sağ tıklayın, **Özellikler**'i seçin ve sonra da **Tanımlayıcılar** sekmesini seçin.|Azure AD'de, tanımlayıcı Azure Portal'ın içinde uygulamanın **Çoklu oturum açma** özelliklerinde, **Etki Alanı ve URL'ler** altında Tanımlayıcı olarak yapılandırılır. (**Gelişmiş URL ayarlarını göster** onay kutusunu seçmeniz gerekebilir.)|SAML belirtecindeki **Audience** öğesine karşılık gelir.|
|Uygulama federasyon meta verileri|Uygulamanın federasyon meta verilerinin konumu. IdP bunu, uç noktalar veya şifreleme sertifikaları gibi belirli yapılandırma ayarlarını otomatik olarak güncelleştirmek için kullanır.|Uygulamanın federasyon meta veri URL'si, uygulamaya ilişkin AD FS bağlı olan taraf güveninde bulunur. Güvene sağ tıklayın, **Özellikler**'i seçin ve ardından **İzleme** sekmesini seçin.|Yok. Azure AD uygulama federasyon meta verilerini doğrudan kullanmayı desteklemez.|Yok|
|Kullanıcı tanımlayıcısı/**NameID**|Azure AD'den veya AD FS'den kullanıcı tanımlayıcınızı uygulamanıza benzersiz olarak göstermek için kullanılan öznitelik.</br></br> Bu öznitelik normalde kullanıcının UPN'si veya e-posta adresidir.|AD FS'de, bunu bağlı olan tarafta bir talep kuralı olarak bulabilirsiniz. Çoğu durumda, talep kuralı "nameidentifier" ile biten bir türde bir talep gönderir.|Azure AD'de, kullanıcı tanımlayıcısını Azure Portal'ın içinde uygulamanın **Çoklu oturum açma** özelliklerindeki **Kullanıcı Öznitelikleri** başlığının altında bulabilirsiniz.</br></br>Varsayılan olarak UPN kullanılır.|IdP'den uygulamaya SAML belirtecindeki **NameID** öğesi olarak iletilir.|
|Uygulamaya gönderilecek diğer talepler|Kullanıcı tanımlayıcısı/**NameID** bilgisinin yanı sıra, IdP'den uygulamaya yaygın olarak başka talep bilgileri de gönderilir. Bu bilgilere örnek olarak ad, soyadı, e-posta adresi ve kullanıcının üye olduğu gruplar verilebilir.|AD FS'de, bunu bağlı olan tarafta diğer talep kuralları olarak bulabilirsiniz.|Azure AD'de, Azure Portal'ın içinde uygulamanın **Çoklu oturum açma** özelliklerindeki **Kullanıcı Öznitelikleri** başlığının altında bulabilirsiniz. **Görünüm**'ü seçin ve diğer tüm kullanıcı özniteliklerini düzenleyin.|Yok|

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>SaaS uygulamasında Azure AD'yi kimlik sağlayıcısı olarak gösterme
Geçiş kapsamında, uygulamayı Azure AD'ye (şirket içi kimlik sağlayıcısı yerine) işaret edecek şekilde yapılandırmanız gerekir. Bu bölüm, özel/LOB uygulamalarına değil SAML protokolü kullanan SaaS uygulamalarına odaklanır. Bununla birlikte, buradaki kavramlar özel LOB uygulamalarını da kapsayacak şekilde genişletilebilir.

Genel hatlarıyla, birkaç önemli nokta SaaS uygulamasını Azure AD'ye gösterir:

- Kimlik sağlayıcısını veren: https&#58;//sts.windows.net/{kiracı-kimliği}/
- Kimlik sağlayıcısı oturum açma URL'si: https&#58;//login.microsoftonline.com/{kiracı-kimliği}/saml2
- Kimlik sağlayıcısı oturum kapatma URL'si: https&#58;//login.microsoftonline.com/{kiracı-kimliği}/saml2 
- Federasyon meta verilerinin konumu: https&#58;//login.windows.net/{kiracı-kimliği}/federationmetadata/2007-06/federationmetadata.xml?appid={uygulama-kimliği} 

{kiracı-kimliği} öğesini, Azure Portal'da **Azure Active Directory** > **Özellikler** altında **Dizin Kimliği** olarak bulunan kiracı kimliğinizle değiştirin. {uygulama-kimliği} öğesini, uygulamanın özellikleri altında **Uygulama Kimliği** olarak bulunan uygulama kimliğinizle değiştirin.

Aşağıdaki tabloda, uygulamada SSO ayarlarını yapılandırmaya yönelik önemli IdP yapılandırma öğeleri ve bunların AD FS ve Azure AD'deki değerleri veya konumları açıklanır. Tablonun referans çerçevesi, kimlik doğrulama isteklerini nereye göndereceğini ve alınan belirteçleri nasıl doğrulayacağını bilmesi gereken SaaS uygulamasıdır.

|Yapılandırma öğesi|Açıklama|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>oturum açma </br>URL'si|Uygulamanın perspektifinden IdP'nin oturum açma URL'si (kullanıcının oturum açmak için yeniden yönlendirildiği konum).|AD FS oturum açma URL'si AD FS federasyon hizmeti adının arkasına “/adfs/ls/” eklenerek oluşturulur. Örneğin: https&#58;//fs.contoso.com/adfs/ls/|Azure AD için buna karşılık gelen değer, {kiracı-kimliği} öğesinin kiracı kimliğinizle değiştirildiği desene uyar. Bu değeri Azure Portal'da, **Azure Active Directory** > **Özellikler** altında **Dizin Kimliği** olarak bulabilirsiniz.</br></br>SAML-P protokolünü kullanan uygulamalar için: https&#58;//login.microsoftonline.com/{kiracı-kimliği}/saml2 </br></br>WS-Federasyon protokolünü kullanan uygulamalar için: https&#58;//login.microsoftonline.com/{kiracı-kimliği}/wsfed|
|IdP </br>oturumu kapatma </br>URL'si|Uygulamanın perspektifinden IdP'nin oturumu kapatma URL'si (uygulamada oturumu kapatmayı seçen kullanıcının yeniden yönlendirildiği konum).|AD FS için, oturumu kapatma URL'si oturum açma URL'siyle aynı olabileceği gibi, aynı URL'nin sonuna “wa=wsignout1.0” eklenmiş hali de olabilir. Örneğin: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Azure AD'de buna karşılık gelen değer, uygulamanın SAML 2.0 oturumu kapatma işlemini destekleyip desteklemediğine bağlıdır.</br></br>Uygulama SAML oturumu kapatma işlemini destekliyorsa, değer {kiracı-kimliği} öğesinin kiracı kimliğiyle değiştirildiği desene uyar. Bunu Azure Portal'da, **Azure Active Directory** > **Özellikler** altında **Dizin Kimliği** olarak bulabilirsiniz: https&#58;//login.microsoftonline.com/{kiracı-kimliği}/saml2</br></br>Uygulama SAML oturumu kapatma işlemini desteklemiyorsa: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Belirteç </br>imzalama </br>sertifika|IdP'nin verilen belirteçleri imzalamak için özel anahtarını kullandığı sertifika. Belirtecin, uygulamanın güvenmek üzere yapılandırıldığı IdP'den geldiğini doğrular.|AD FS belirteç imzalama sertifikası AD FS Yönetimi'nde **Sertifikalar**'ın altında bulabilirsiniz.|Azure AD'de, belirteç imzalama sertifikasını Azure Portal'ın içinde uygulamanın **Çoklu oturum açma** özelliklerindeki **SAML İmzalama Sertifikası** başlığı altında bulabilirsiniz. Sertifikayı buradan indirip uygulamaya yükleyebilirsiniz.</br></br> Uygulamanın birden çok sertifikası varsa, tüm sertifikaları federasyon meta veri XML dosyasında bulabilirsiniz.|
|Tanımlayıcı/</br>“veren”|Uygulamanın perspektifinden IdP'nin tanımlayıcısı (bazen “veren kimliği” olarak da adlandırılır).</br></br>SAML belirtecinde, değer **Issuer** öğesi olarak gösterilir.|AD FS için tanımlayıcı genellikle AD FS Yönetimi'nde **Hizmet** > **Federasyon Hizmeti Özelliklerini Düzenle**'nin altında yer alan federasyon hizmeti tanımlayıcısıdır. Örneğin: http&#58;//fs.contoso.com/adfs/services/trust|Azure AD için buna karşılık gelen değer, {kiracı-kimliği} değerinin kiracı kimliği ile değiştirildiği desene uyar. Bu değeri Azure Portal'da, **Azure Active Directory** > **Özellikler** altında **Dizin Kimliği** olarak bulabilirsiniz: https&#58;//sts.windows.net/{kiracı-kimliği}/|
|IdP </br>federasyon </br>meta veriler|IdP'nin genel kullanıma açık federasyon meta verilerinin konumu. (Bazı uygulamalar federasyon meta verilerini yönetici yapılandırma URL'lerine, tanımlayıcıya ve bağımsız olarak belirteç imzalama sertifikasına alternatif olarak kullanılır)|AD FS federasyon meta verileri URL'sini AD FS Yönetimi altında bulabilirsiniz **hizmet** > **uç noktaları** > **meta verileri**  >   **Türü: Federasyon meta verileri**. Örneğin: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Azure AD'de buna karşılık gelen değer şu desene uyar: https&#58;//login.microsoftonline.com/{KiracıEtkiAlanıAdı}/FederationMetadata/2007-06/FederationMetadata.xml. {KiracıEtkiAlanıAdı} değerinin yerine kiracınızın “contoso.onmicrosoft.com” biçimindeki adı kullanılır. </br></br>Daha fazla bilgi için bkz. [Federasyon meta verileri](../develop/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>SaaS uygulamaları taşıma

Taşıma SaaS uygulamalarını AD fs'den veya başka bir kimlik sağlayıcısından Azure AD'ye bir el ile bugün işlemidir. Uygulamaya özgü yönergeler için [Market'te bulunan SaaS uygulamalarını tümleştirme konusundaki öğreticilerin listesine bakın](../saas-apps/tutorial-list.md).

Tümleştirme öğreticilerinde bir yeşil alan tümleştirmesi yaptığınız varsayılır. Uygulamalarınızı planlar, değerlendirir, yapılandırır ve bunların tam geçişini yaparken, geçişe özgü birkaç önemli kavramı bilmeniz gerekir:  

- Bazı uygulamalar kolayca geçirilebilir. Özel talepler gibi daha karmaşık gereksinimleri olan uygulamalar Azure AD ve/veya Azure AD Connect'te ek yapılandırma gerektirebilir.
- En yaygın senaryolarda, uygulama için yalnızca **NameID** talebi ve diğer yaygın kullanıcı tanımlayıcısı talepleri gerekir. Ek taleplerin gerekip gerekmediğini saptamak için, AD FS'den veya üçüncü taraf kimlik sağlayıcınızdan hangi talepleri gönderdiğinizi inceleyin.
- Ek taleplerin gerektiğini saptadıktan sonra, bunların Azure AD'de sağlandığından emin olun. Gerekli bir özniteliğin, örneğin **samAccountName** özniteliğinin Azure AD'ye eşitlendiğinden emin olmak için Azure AD Connect eşitleme yapılandırmasını gözden geçirin.
- Öznitelikler Azure AD'de kullanılabilir olduktan sonra, bu öznitelikleri verilen belirteçlere talep olarak dahil etmek için Azure AD'de talep verme kuralları ekleyebilirsiniz. Bu kuralları Azure AD'de uygulamanın **Çoklu oturum açma** özelliklerinde eklersiniz.

### <a name="assess-what-can-be-moved"></a>Nelerin taşınabileceğini değerlendirin

SAML 2.0 uygulamaları Market'teki Azure AD uygulama galerisi yoluyla veya Market dışı uygulamalar olarak Azure AD ile tümleştirilebilir.  

Bazı yapılandırmalar Azure AD'de yapılandırırken ek adımlar gerektirir ve bazı yapılandırmalar da henüz desteklenmemektedir. Neleri taşıyabileceğinizi saptamak için, uygulamalarınızdan her birinin geçerli yapılandırmasına bakın. Özellikle şunlara dikkat edin:

- Yapılandırılmış talep kuralları (verme aktarım kuralları).
- SAML **NameID** biçimi ve özniteliği.
- Verilen SAML belirteci sürümleri.
- Verme yetkilendirme kuralları veya erişim denetim ilkeleri veya Multi-Factor Authentication (ek kimlik doğrulaması) kuralları gibi diğer yapılandırmalar.

#### <a name="what-can-be-moved-today"></a>Bugün nelerin taşınabileceğini

Bugün kolayca taşıyabilirsiniz uygulamalar standart yapılandırma öğelerini ve taleplerini kullanan SAML 2.0 uygulamalarıdır. Bu uygulamalar şunlardan oluşabilir:

- Kullanıcı asıl adı.
- E-posta adresi.
- Verilen ad.
- Soyadı.
- Azure AD posta özniteliği, posta öneki, çalışan kimliği, 1-15 uzantı öznitelikleri veya şirket içi **SamAccountName** özniteliği dahil olmak üzere SAML **NameID** gibi alternatif öznitelik. Daha fazla bilgi için bkz. [NameIdentifier talebini düzenleme](../develop/active-directory-saml-claims-customization.md).
- Özel talepler. Desteklenen talep eşlemeleri hakkında bilgi için bkz. [Azure Active Directory'de talep eşlemesi](../develop/active-directory-claims-mapping.md) ve [Azure Active Directory'de kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

Özel taleplere ve **NameID** öğelerine ek olarak, geçiş işlemi kapsamında Azure AD'de başka yapılandırma adımları gerektiren yapılandırmalar:

- AD FS'de özel yetkilendirme veya Multi-Factor Authentication kuralları. Bunları kullanarak yapılandırma [Azure AD koşullu erişim](../active-directory-conditional-access-azure-portal.md) özelliği.
- Birden çok SAML uç noktası olan uygulamalar. Bunları Azure AD'de PowerShell kullanarak yapılandırırsınız. (Bu özellik portalda sağlanmaz.)
- SAML sürüm 1.1 belirteçlerini gerektiren SharePoint uygulamaları gibi WS-Federasyon uygulamaları. Bunları PowerShell kullanarak el ile yapılandırmanız gerekir.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Bugün Azure AD'de desteklenmeyen uygulamalar ve yapılandırmalar

Aşağıdaki özellikleri gerektiren uygulamalar bugün geçirilemez. Bu özellikleri gerektiren uygulamalarınız varsa, yorumlar bölümünde geri bildirim sağlayın.

- Protokol özellikleri:
    - Tüm oturum açılan uygulamalarda SAML Çoklu Oturum Kapatma (SLO) desteği.
    - WS-Trust ActAs deseni desteği.
    - SAML yapı çözümlemesi.
    - İmzalı SAML isteklerinde imza doğrulaması. İmzalı isteklerin kabul edildiğine ama imzanın doğrulanmadığına dikkat edin.
- Belirteç özellikleri:
    - SAML belirteci şifreleme.
    - SAML protokol yanıtları için SAML sürüm 1.1 belirteçleri.
- Belirteç içindeki talep özellikleri:
    - Şirket içi grup adlarını talep olarak verme.
    - Azure AD dışındaki depolardan gelen talepler.
    - Karmaşık talep verme dönüştürme kuralları. Desteklenen talep eşlemeleri hakkında bilgi için bkz. [Azure Active Directory'de talep eşlemesi](../develop/active-directory-claims-mapping.md) ve [Azure Active Directory'de kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).
    - Dizin uzantılarını talep olarak verme.
    - **NameID** biçiminin özel belirtimi.
    - Çok değerli öznitelikler verme.

> [!NOTE]
> Azure AD, bu alanda özellikler eklenerek sürekli geliştirilmektedir. Bu makaleyi düzenli aralıklarla güncelleştiriyoruz.

### <a name="configure-azure-ad"></a>Azure AD'yi yapılandırma

#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>SaaS uygulaması için çoklu oturum açma (SSO) ayarlarını yapılandırma

Azure AD'de SAML oturum açma ayarlarını (uygulamanızın gerektirdiği gibi) uygulamanın **Çoklu oturum açma** özelliklerinde, **Kullanıcı Öznitelikleri**'nin altında yapılandırırsınız.

!["Kullanıcı Öznitelikleri" bölümüyle çoklu oturum açma özellikleri](media/migrate-adfs-apps-to-azure/migrate3.png)

Güvenlik belirtecinde talep olarak gönderilecek öznitelikleri görmek için **Diğer tüm kullanıcı özniteliklerini görüntüle ve düzenle**’yi seçin.

![Talep olarak gönderebilirsiniz özniteliklerinin listesini gösterir](media/migrate-adfs-apps-to-azure/migrate4.png)

Düzenlemek için belirli bir öznitelik satırını seçin veya yeni öznitelik eklemek için **Öznitelik ekle**’yi seçin.

!["Özniteliği Düzenle" bölmesi gösterir](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Uygulamaya kullanıcı atama

Azure AD'deki kullanıcıların SaaS uygulamasında oturum açabilmesi için, onlara erişim vermelisiniz.

Azure AD portalında kullanıcıları atamak için, SaaS uygulamasının sayfasına göz atın ve kenar çubuğunda **Kullanıcılar ve gruplar**'ı seçin. Kullanıcı veya grup eklemek için, bölmenin en üstündeki **Kullanıcı ekle**'yi seçin.

!["Kullanıcılar ve gruplar" altında "Kullanıcı ekle" düğmesi](media/migrate-adfs-apps-to-azure/migrate6.png)

!["Atama Ekle" bölmesi gösterir](media/migrate-adfs-apps-to-azure/migrate7.png)

Erişimi doğrulamak için, kullanıcıların oturum açtıklarında [erişim panelinde](../user-help/active-directory-saas-access-panel-introduction.md) SaaS uygulamasını görüyor olması gerekir. Erişim panelini https://myapps.microsoft.com adresinde bulabilirler. Bu örnekte, kullanıcının hem Salesforce hem de ServiceNow'a erişimi başarıyla atanmıştır.

![Salesforce ve ServiceNow uygulamalarını içeren örnek erişim paneli](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>SaaS uygulamasını yapılandırma

Şirket içi federasyondan Azure AD'ye tam geçiş işlemi, üzerinde çalıştığınız SaaS uygulamasının birden çok kimlik sağlayıcısını destekleyip desteklemediğine bağlıdır. İşte birden çok IdP desteği hakkında sık sorulan bazı sorular:

   **S: Bir uygulama birden çok Idp'yi desteklemesi ne anlama geliyor?**

   Y: Birden çok Idp'yi destekleyen SaaS uygulamaları oturum açma deneyimindeki tüm işlemeden önce yeni IDP (bizim durumumuzda Azure AD içinde) hakkında bilgileri girmenize olanak tanır. Yapılandırma bittikten sonra, Azure AD'ye işaret etmek için uygulamanın kimlik doğrulama yapılandırmasına geçebilirsiniz.

   **S: SaaS uygulamasının birden çok Idp'yi desteklemesi neden önemlidir?**

   Y: Birden çok IDP desteklenmiyorsa, yöneticinin bu sırada Azure AD'yi uygulamanın yeni Idp'si yapılandırması bir hizmet veya bakım kesintisi olarak kısa bir zaman penceresi kenara ayırmanız gerekir. Bu kesinti sırasında, kullanıcılara hesaplarında oturum açamayacakları bildirilmelidir.

   Uygulama birden çok IdP'yi destekliyorsa, ek IdP önceden yapılandırılabilir. Bundan sonra yönetici Azure tam geçişi sırasında IdP'yi değiştirebilir.

   Uygulama birden çok IdP'yi destekliyorsa ve oturum açma için kimlik doğrulamasını birden çok IdP'nin eşzamanlı olarak işlemesini seçerseniz, kullanıcıya oturum açma sayfasında kimlik doğrulaması için IdP seçme olanağı sağlanır.

#### <a name="example-support-for-multiple-identity-providers"></a>Örnek: Birden çok kimlik sağlayıcıları için destek

Örneğin Salesforce'ta, IDP yapılandırmasını **Settings** > **Company Settings** > **My Domain** > **Authentication Configuration** altında bulabilirsiniz.

![Salesforce uygulamasında "Authentication Configuration" bölümü](media/migrate-adfs-apps-to-azure/migrate9.png)

Yapılandırma daha önce **Identity** > **Single sign-on settings** altında oluşturulduğundan, kimlik doğrulama yapılandırması için IdP'nizi değiştirebilirsiniz. Örneğin, bunu AD FS'den Azure AD'ye geçirebilirsiniz.

![Kimlik doğrulama hizmeti olarak Azure AD'yi seçme](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>İsteğe bağlı: Azure AD'de kullanıcı sağlamayı yapılandırma

Azure AD'nin SaaS uygulaması için kullanıcı sağlamayı doğrudan işlemesini istiyorsanız, bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı sağlamayı ve kullanıcı sağlamasını kaldırmayı otomatikleştirme](user-provisioning.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaları Azure Active Directory ile yönetme](what-is-application-management.md)
- [Uygulamalara erişimi yönetme](what-is-access-management.md)
- [Azure AD Connect federasyonu](../hybrid/how-to-connect-fed-whatis.md)
