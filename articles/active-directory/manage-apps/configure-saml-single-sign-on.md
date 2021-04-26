---
title: Azure Active Directory 'de uygulamalar için SAML tabanlı çoklu oturum açmayı (SSO) anlayın
description: Azure Active Directory 'de uygulamalar için SAML tabanlı çoklu oturum açmayı (SSO) anlayın
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374497"
---
# <a name="understand-saml-based-single-sign-on"></a>SAML tabanlı çoklu oturum açmayı anlama

Uygulama yönetiminde [hızlı başlangıç serisinde](view-applications-portal.md) , Azure AD 'yi bir uygulama Için kimlik sağlayıcısı (IDP) olarak kullanmayı öğrendiniz. Bu makale, çoklu oturum açma için SAML tabanlı seçenek hakkında daha fazla ayrıntıya gider. 


## <a name="before-you-begin"></a>Başlamadan önce

Azure AD 'nin kimlik sağlayıcısı (IDP) olarak kullanılması ve çoklu oturum açma (SSO) yapılandırması, kullanılan uygulamaya bağlı olarak basit veya karmaşık olabilir. Bazı uygulamalar yalnızca birkaç eylem ile yapılandırılabilir. Başkalarının derinlemesine yapılandırılması gerekir. Bilgi almak için uygulama yönetiminde [hızlı başlangıç serisini](view-applications-portal.md) gözden geçirebilirsiniz. Eklemekte olduğunuz uygulama basittir, büyük olasılıkla bu makaleyi okumanız gerekmez. Eklemekte olduğunuz uygulama SAML tabanlı SSO için özel yapılandırma gerektiriyorsa, bu makale sizin için de kullanılır.

[Hızlı başlangıç serisinde](add-application-portal-setup-sso.md), çoklu oturum açmayı yapılandırma hakkında bir makale vardır. Bu uygulamada, bir uygulamanın SAML yapılandırma sayfasına erişmeyi öğreneceksiniz. SAML yapılandırması sayfası beş bölümden oluşur. Bu bölümler, bu makalede ayrıntılı olarak ele alınmıştır.

> [!IMPORTANT] 
> **Kurumsal uygulamalarda** bir uygulama Için gezintide **Çoklu oturum açma** seçeneğinin mevcut olmadığı bazı senaryolar vardır. 
>
> Uygulama **uygulama kayıtları** kullanılarak kaydedilmişse, çoklu oturum açma özelliği varsayılan olarak OIDC OAuth kullanacak şekilde yapılandırılır. Bu durumda, **Çoklu oturum açma** seçeneği, **Kurumsal uygulamalar** altındaki gezinmede gösterilmez. Özel uygulamanızı eklemek için **uygulama kayıtları** kullandığınızda, bildirim dosyasındaki seçenekleri yapılandırırsınız. Bildirim dosyası hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulama bildirimi](../develop/reference-app-manifest.md). SSO standartları hakkında daha fazla bilgi edinmek için bkz. [Microsoft Identity platform kullanarak kimlik doğrulama ve yetkilendirme](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Bir uygulamanın başka bir kiracıda barındırıldığı veya hesabınızın gerekli izinleri (genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi) yoksa, **Çoklu oturum açma** 'nın gezinmede eksik olduğu diğer senaryolar. İzinler Ayrıca **Çoklu oturum** açmayı açabiliyor ancak kaydedemeyeceksiniz bir senaryoya neden olabilir. Azure AD Yönetim rolleri hakkında daha fazla bilgi için bkz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . (.


## <a name="basic-saml-configuration"></a>Temel SAML yapılandırması

Uygulama satıcısından değerleri almalısınız. Değerleri el ile girebilir veya alanların değerini ayıklamak için bir meta veri dosyası yükleyebilirsiniz.

> [!TIP]
> Birçok uygulama zaten Azure AD ile çalışacak şekilde önceden yapılandırılmıştır. Bu uygulamalar, Azure AD kiracınıza bir uygulama eklerken gözatabilmeniz gereken uygulamalar galerisinde listelenmiştir. [Hızlı başlangıç serisi](add-application-portal-setup-sso.md) , süreç boyunca size yol gösterir. Galerideki uygulamalar için ayrıntılı, adım adım yönergeler bulacaksınız. Adımlara erişmek için, hızlı başlangıç serisinde açıklandığı şekilde uygulamanın SAML yapılandırması sayfasındaki bağlantıya tıklayabilir veya [SaaS uygulama yapılandırma öğreticilerinde](../saas-apps/tutorial-list.md)tüm uygulama yapılandırma öğreticilerinin listesine gidebilirsiniz.

| Temel SAML yapılandırma ayarı | SP ile başlatılan | idP ile başlatılan | Açıklama |
|:--|:--|:--|:--|
| **Tanımlayıcı (Varlık Kimliği)** | Bazı uygulamalar için gereklidir | Bazı uygulamalar için gereklidir | Uygulamayı benzersiz olarak tanımlar. Azure AD, kimliği, SAML belirtecinin hedef kitle parametresi olarak uygulamaya gönderir. Uygulamanın doğrulaması bekleniyordu. Bu değer ayrıca uygulama tarafından sağlanan SAML meta verilerinde Varlık Kimliği olarak da görünür. Şu kalıbı kullanan bir URL girin: ' https:// <subdomain> . contoso.com ' *Bu değeri, uygulama tarafından gönderilen **Authisteyner** (SAML isteği) içinde **veren** öğesi olarak bulabilirsiniz.* |
| **Yanıt URL'si** | Gerekli | Gerekli | Uygulamanın SAML belirtecini almayı beklediği konumu belirtir. Yanıt URL'si, Onay Belgesi Tüketici Hizmeti (ACS) URL'si olarak da bilinir. Birden çok yanıt URL 'si belirtmek için ek yanıt URL 'si alanlarını kullanabilirsiniz. Örneğin, birden çok alt etki alanları için ek yanıt URL 'Leri gerekebilir. Ya da, sınama amaçları için aynı anda birden çok yanıt URL 'Si (yerel ana bilgisayar ve genel URL 'Ler) belirtebilirsiniz. |
| **Oturum açma URL 'SI** | Gerekli | Belirtmeyin | Kullanıcı bu URL'yi açtığında hizmet sağlayıcısı kimlik doğrulaması ve oturum açma için Azure AD'ye yönlendirir. Azure AD, uygulamayı Microsoft 365 veya Azure AD My uygulamalarından başlatmak için URL 'YI kullanır. Boş olduğunda, Kullanıcı Microsoft 365, Azure AD Uygulamalarım veya Azure AD SSO URL 'sinden uygulamayı başlattığında Azure AD, IDP tarafından başlatılan bir oturum açma işlemi yapar.|
| **Geçiş Durumu** | İsteğe Bağlı | İsteğe Bağlı | Uygulamaya kimlik doğrulaması tamamlandıktan sonra kullanıcının yönlendirileceği yeri belirtir. Genellikle değer uygulamanın geçerli bir URL 'sidir. Ancak, bazı uygulamalar bu alanı farklı şekilde kullanır. Daha fazla bilgi için uygulama satıcısına danışın.
| **Oturum kapatma URL 'SI** | İsteğe Bağlı | İsteğe Bağlı | SAML oturum kapatma yanıtlarını uygulamaya geri göndermek için kullanılır.

## <a name="user-attributes-and-claims"></a>Kullanıcı öznitelikleri ve talepler 

Kullanıcı uygulamanın kimliğini doğruladığında Azure AD, uygulamayı benzersiz bir şekilde tanımlayan kullanıcı hakkında bilgi (veya talepler) içeren bir SAML belirteci uygulamayı yayınlar. Varsayılan olarak, bu bilgiler kullanıcının Kullanıcı adı, e-posta adresi, adı ve soyadı bilgilerini içerir. Örneğin, uygulama belirli talep değerleri veya Kullanıcı adı dışında bir **ad** biçimi gerektiriyorsa, bu talepleri özelleştirmeniz gerekebilir. 

> [!IMPORTANT]
> Birçok uygulama önceden yapılandırılmıştır ve uygulama galerisinde, Kullanıcı ve grup taleplerini ayarlamaya endişelenmeniz gerekmez. [Hızlı başlangıç serisi](add-application-portal.md) , uygulama ekleme ve yapılandırma konusunda size yol gösterir.


**Benzersiz kullanıcı tanımlayıcı (ad kimliği)** tanımlayıcı değeri, gerekli bir taleple ve önemlidir. Varsayılan değer *User. UserPrincipalName*' dir. Kullanıcı tanımlayıcısı, uygulama içinde her kullanıcıyı benzersiz olarak tanımlar. Örneğin e-posta adresi hem kullanıcı adı hem de benzersiz tanıtıcı olarak kullanılıyorsa değeri *user.mail* olarak ayarlayın.

SAML taleplerini özelleştirme hakkında daha fazla bilgi edinmek için bkz. [nasıl yapılır: kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

Yeni talepler ekleyebilirsiniz, Ayrıntılar için [uygulamaya özel talepler ekleme](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) veya grup taleplerini ekleme bölümüne bakın. [Grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md)konusuna bakın.


> [!NOTE]
> SAML belirtecini Azure AD 'den uygulamanıza özelleştirmenin ek yolları için aşağıdaki kaynaklara bakın.
>- Azure portal aracılığıyla özel Roller oluşturmak için, bkz. [rol taleplerini yapılandırma](../develop/active-directory-enterprise-app-role-management.md).
>- Talepleri PowerShell aracılığıyla özelleştirmek için bkz. [Customize Claim-PowerShell](../develop/active-directory-claims-mapping.md).
>- Uygulamanız için isteğe bağlı talepler yapılandırmak üzere uygulama bildirimini değiştirmek için bkz. [isteğe bağlı talepler yapılandırma](../develop/active-directory-optional-claims.md).
>- Belirteç kullanım belirteçleri, erişim belirteçleri, oturum belirteçleri ve KIMLIK belirteçlerine yönelik belirteç ömür ilkelerini ayarlamak için bkz. [belirteç yaşam sürelerini yapılandırma](../develop/active-directory-configurable-token-lifetimes.md). Ya da Azure AD koşullu erişimi aracılığıyla kimlik doğrulaması oturumlarını kısıtlamak için bkz. [kimlik doğrulama oturumu yönetimi özellikleri](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>SAML imzalama sertifikası

Azure AD, uygulamanın gönderdiği SAML belirteçlerini imzalamak için bir sertifika kullanır. Azure AD ile uygulama arasındaki güveni yapılandırmak için bu sertifikaya ihtiyacınız vardır. Sertifika biçimi hakkında daha fazla bilgi için bkz. uygulamanın SAML belgeleri. Daha fazla bilgi için bkz. SAML belirtecindeki [Federasyon çoklu oturum açma](manage-certificates-for-federated-single-sign-on.md) ve [Gelişmiş sertifika imzalama seçenekleri](certificate-signing-options.md)için sertifikaları yönetme.

> [!IMPORTANT]
> Birçok uygulama zaten önceden yapılandırılmış ve uygulama galerisinde, sertifikalara ek olarak ihtiyacınız yoktur. [Hızlı başlangıç serisi](add-application-portal.md) , uygulama ekleme ve yapılandırma konusunda size yol gösterir.

Azure AD 'den, etkin Sertifikayı Base64 veya ham biçimde doğrudan bir **SAML Ile tek Sign-On BIR SAML** sayfasından indirebilirsiniz. Ayrıca, uygulama meta verileri XML dosyasını indirerek ya da uygulama Federasyon meta verileri URL 'sini kullanarak etkin sertifikayı edinebilirsiniz. Sertifikalarınızı görüntülemek, oluşturmak veya indirmek (etkin veya devre dışı) için aşağıdaki adımları izleyin.

Bir sertifikayı doğrulamak için denetlenecek bazı yaygın şeyler şunlardır: 
   - *Doğru sona erme tarihi.* Son kullanma tarihini üç yıla kadar gelecek şekilde yapılandırabilirsiniz.
   - *Doğru sertifika için etkin durumu.* Durum **etkin** değilse, durumu **etkin** olarak değiştirin. Durumu değiştirmek için sertifikanın satırına sağ tıklayın ve **sertifikayı etkin yap**' ı seçin.
   - *Doğru imzalama seçeneği ve algoritması.*
   - *Doğru bildirim e-posta adresleri (es).* Etkin sertifika sona erme tarihi yaklaştığında Azure AD, bu alanda yapılandırılan e-posta adresine bir bildirim gönderir.

Bazen sertifikayı indirmeniz gerekebilir. Bunu kaydettiğiniz yerde dikkatli olun! Sertifikayı indirmek için Base64 biçimi, ham biçim veya Federasyon meta veri XML seçeneklerinden birini seçin. Azure AD Ayrıca, uygulama **Federasyon meta veri URL 'sini** de sağlar `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Uygulama, kullanılırken işlenen XML 'de bulunan bayt sırası Işaretçisini işleyebilmelidir https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Bayt sırası işareti, yazdırılamaz bir ASCII karakteri» ¿olarak temsil edilir ve onaltılı düzende XML verilerini gözden geçirirken EF BB BF olarak gösterilir.

Sertifika değişiklikleri yapmak için Düzenle düğmesini seçin. **SAML Imzalama sertifikası** sayfasında yapabileceğiniz birkaç şey vardır:
   - Yeni bir sertifika oluşturun: **Yeni sertifika**' yı seçin, **sona erme tarihini** seçin ve ardından **Kaydet**' i seçin. Sertifikayı etkinleştirmek için bağlam menüsünü (**...**) seçin ve **sertifikayı etkin yap**' ı seçin.
   - Özel anahtar ve PFX kimlik bilgileri içeren bir sertifikayı karşıya yükleyin: **sertifikayı Içeri aktar** ' ı seçin ve sertifikaya gidin. **PFX parolasını** girin ve ardından **Ekle**' yi seçin.  
   - Gelişmiş Sertifika imzalamayı yapılandırın. Bu seçenekler hakkında daha fazla bilgi için bkz. [Gelişmiş sertifika imzalama seçenekleri](certificate-signing-options.md).
   - Etkin sertifikanın sona erme tarihi yaklaştığında ek kişilere bildir: **bildirim e-posta adresleri** alanlarına e-posta adreslerini girin.

## <a name="set-up-the-application-to-use-azure-ad"></a>Uygulamayı Azure AD kullanacak şekilde ayarlama

**Ayarla \<applicationName>** bölümünde, Azure AD 'yi bir SAML kimlik sağlayıcısı olarak kullanacak şekilde uygulamada yapılandırılması gereken değerler listelenmiştir. Uygulamalar Web sitesinde yapılandırma sayfasında değerleri ayarlarsınız. Örneğin, GitHub yapılandırıyorsanız github.com sitesine gidip değerleri ayarlarsınız. Uygulama zaten önceden yapılandırıldıysa ve Azure AD galerisinde, **adım adım yönergeleri görüntülemek** için bir bağlantı bulacaksınız. Aksi takdirde, yapılandırdığınız uygulamanın belgelerini bulmanız gerekecektir. 

**Oturum açma URL 'si** ve **oturum kapatma URL 'Si** değerleri, Azure AD kiracısı için SAML istek işleme uç noktası olan aynı uç noktaya çözümlenir. 

**Azure AD tanımlayıcısı** , uygulamaya verilen SAML belirtecindeki **veren** 'in değeridir.

## <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Uygulamanızı bir SAML tabanlı kimlik sağlayıcısı olarak Azure AD kullanacak şekilde yapılandırdıktan sonra, hesabınız için çoklu oturum açma çalışıp çalışmadığını görmek için ayarları test edebilirsiniz. 

**Test** ' i seçin ve şu anda oturum açmış kullanıcıyla veya başka biri olarak test etme seçeneğini belirleyin. 

Oturum açma işlemi başarılı olursa, SAML uygulamanıza Kullanıcı ve grup atamaya hazırsınız demektir. Tebrikler!

Bir hata iletisi görünürse, aşağıdaki adımları izleyin:

1. Hatayla ilgili bilgileri kopyalayıp **Hata neye benziyor?** kutusuna yapıştırın.

    ![Çözüm rehberliği alın](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **Çözüm kılavuzunu al**' ı seçin. Kök nedeni ve çözüm kılavuzu görüntülenir.  Bu örnekte, Kullanıcı uygulamaya atanmamıştır.

3. Çözüm kılavuzunu okuyun ve ardından sorunu düzeltmeye çalışın.

4. Testi başarıyla tamamlana kadar tekrarlayın.

Daha fazla bilgi için bkz. [Azure Active Directory içindeki uygulamalarda SAML tabanlı çoklu oturum açma hatalarını ayıklama](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
- [Uygulamaya Kullanıcı veya Grup atama](./assign-user-or-group-access-portal.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Çoklu Oturum Açma SAML protokolü](../develop/single-sign-on-saml-protocol.md)
