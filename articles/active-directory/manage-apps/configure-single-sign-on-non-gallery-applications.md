---
title: SAML çoklu oturum açma-Galeri olmayan uygulamalar-Microsoft Identity platform | Microsoft Docs
description: Microsoft Identity platformunda (Azure AD) Galeri olmayan uygulamalara çoklu oturum açma (SSO) yapılandırma
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dcc2d6fc252f288f15e2583012798b4d0e9cee6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169430"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Galeri olmayan uygulamalarda SAML tabanlı çoklu oturum açmayı yapılandırma

Azure AD kurumsal uygulamalarınıza [Galeri uygulaması](add-gallery-app.md) veya [Galeri olmayan bir Web uygulaması](add-non-gallery-app.md) eklediğinizde, sunulan çoklu oturum açma seçeneklerinden biri, [SAML tabanlı çoklu oturum](what-is-single-sign-on.md#saml-sso)açma seçenekleridir. SAML protokollerinden birini kullanarak kimlik doğrulaması yapan uygulamalar için mümkün olan her zaman SAML 'yi seçin. SAML çoklu oturum açma ile Azure AD, kullanıcının Azure AD hesabını kullanarak uygulamanın kimliğini doğrular. Azure AD oturum açma bilgileri uygulamaya bir bağlantı protokolü üzerinden iletişim kurar. Kullanıcıları, SAML talepleriniz içinde tanımladığınız kurallara göre belirli uygulama rollerine eşleyebilirsiniz. Bu makalede, Galeri dışı bir uygulama için SAML tabanlı çoklu oturum açma 'nın nasıl yapılandırılacağı açıklanır. 

> [!NOTE]
> Galeri uygulaması ekleniyor mu? [SaaS uygulama öğreticileri listesinde](../saas-apps/tutorial-list.md) adım adım kurulum yönergelerini bulun

SAML dışı bir uygulama için kod yazmadan çoklu oturum açmayı yapılandırmak için bir aboneliğiniz veya Azure AD Premium olması gerekir ve uygulamanın SAML 2,0 ' i desteklemesi gerekir. Azure AD sürümleri hakkında daha fazla bilgi için [Azure AD fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)ziyaret edin.

## <a name="before-you-begin"></a>Başlamadan önce

Uygulama Azure AD kiracınıza eklenmemişse, bkz. [Galeri dışı bir uygulama ekleme](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Adım 1. Temel SAML yapılandırmasını düzenleme

1. [Azure Portal](https://portal.azure.com) bir bulut uygulaması Yöneticisi veya Azure AD kiracınız için bir uygulama Yöneticisi olarak oturum açın.

2. **Kurumsal uygulamalar** > **Azure Active Directory** gidin ve listeden uygulamayı seçin. 
   
   - Uygulamayı aramak için, **uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve ardından **Uygula**' yı seçin. Arama kutusuna uygulamanın adını girin ve sonra sonuçlardan uygulamayı seçin.

3. **Yönet** bölümünde **Çoklu oturum açma**' yı seçin. 

4. **SAML**'yi seçin. **SAML Ile çoklu oturum açmayı ayarlama sayfası açılır** .

   ![1\. adım temel SAML yapılandırmasını düzenleme](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Temel SAML yapılandırma seçeneklerini düzenlemek için, **temel SAML yapılandırması** bölümünün sağ üst köşesindeki **Düzenle** simgesini (bir kurşun kalem) seçin.

1. Aşağıdaki ayarları girin. Uygulama satıcısından değerleri almalısınız. Değerleri el ile girebilir veya alanların değerini ayıklamak için bir meta veri dosyası yükleyebilirsiniz.

    | Temel SAML yapılandırma ayarı | SP ile başlatılan | idP ile başlatılan | Açıklama |
    |:--|:--|:--|:--|
    | **Tanımlayıcı (varlık KIMLIĞI)** | Bazı uygulamalar için gereklidir | Bazı uygulamalar için gereklidir | Uygulamayı benzersiz olarak tanımlar. Azure AD, kimliği, SAML belirtecinin hedef kitle parametresi olarak uygulamaya gönderir. Uygulamanın doğrulaması bekleniyordu. Bu değer ayrıca uygulama tarafından sağlanan SAML meta verilerinde Varlık Kimliği olarak da görünür. Şu kalıbı kullanan bir URL girin: ' https://<subdomain>. contoso.com ' *Bu değeri, uygulama tarafından gönderilen authisteyesindeki (SAML isteği ) **veren** öğesi olarak bulabilirsiniz.* |
    | **Yanıt URL'si** | Gerekli | Gerekli | Uygulamanın SAML belirtecini almayı beklediği konumu belirtir. Yanıt URL'si, Onay Belgesi Tüketici Hizmeti (ACS) URL'si olarak da bilinir. Birden çok yanıt URL 'si belirtmek için ek yanıt URL 'si alanlarını kullanabilirsiniz. Örneğin, birden çok alt etki alanları için ek yanıt URL 'Leri gerekebilir. Ya da, sınama amaçları için aynı anda birden çok yanıt URL 'Si (yerel ana bilgisayar ve genel URL 'Ler) belirtebilirsiniz. |
    | **Oturum Açma URL'si** | Gerekli | Belirtmeyin | Kullanıcı bu URL'yi açtığında hizmet sağlayıcısı kimlik doğrulaması ve oturum açma için Azure AD'ye yönlendirir. Azure AD, uygulamayı Office 365 veya Azure AD erişim panelinden başlatmak için URL 'YI kullanır. Boş olduğunda Azure AD, bir Kullanıcı uygulamayı Office 365, Azure AD erişim paneli veya Azure AD SSO URL 'sinden başlattığında IDP tarafından başlatılan oturum açma işlemini gerçekleştirir.|
    | **Geçiş durumu** | İsteğe bağlı | İsteğe bağlı | Uygulamaya kimlik doğrulaması tamamlandıktan sonra kullanıcının yönlendirileceği yeri belirtir. Genellikle değer uygulamanın geçerli bir URL 'sidir. Ancak, bazı uygulamalar bu alanı farklı şekilde kullanır. Daha fazla bilgi için uygulama satıcısına danışın.
    | **Oturum kapatma URL 'SI** | İsteğe bağlı | İsteğe bağlı | SAML oturum kapatma yanıtlarını uygulamaya geri göndermek için kullanılır.

Daha fazla bilgi için bkz. [Çoklu oturum açma SAML Protokolü](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>2\. Adım. Kullanıcı özniteliklerini ve taleplerini yapılandırma 

Kullanıcı uygulamanın kimliğini doğruladığında Azure AD, uygulamayı benzersiz bir şekilde tanımlayan kullanıcı hakkında bilgi (veya talepler) içeren bir SAML belirteci uygulamayı yayınlar. Varsayılan olarak, bu bilgiler kullanıcının Kullanıcı adı, e-posta adresi, adı ve soyadı bilgilerini içerir. Örneğin, uygulama belirli talep değerleri veya Kullanıcı adı dışında bir **ad** biçimi gerektiriyorsa, bu talepleri özelleştirmeniz gerekebilir. Galeri uygulamaları için gereksinimler [uygulamaya özgü öğreticilerde](../saas-apps/tutorial-list.md)açıklanmıştır veya uygulama satıcısına danışabilirsiniz. Kullanıcı özniteliklerini ve taleplerini yapılandırmaya yönelik genel adımlar aşağıda açıklanmıştır.

1. **Kullanıcı öznitelikleri ve talepler** bölümünde sağ üst köşedeki **düzenleme** simgesini (bir kurşun kalem) seçin.

   ![2\. adım kullanıcı özniteliklerini ve taleplerini yapılandırma](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. **Ad tanımlayıcı değerini**doğrulayın. Varsayılan değer *User. PrincipalName*' dır. Kullanıcı tanımlayıcısı, uygulama içinde her kullanıcıyı benzersiz olarak tanımlar. Örneğin e-posta adresi hem kullanıcı adı hem de benzersiz tanıtıcı olarak kullanılıyorsa değeri *user.mail* olarak ayarlayın.

3. **Ad tanımlayıcı değerini**değiştirmek Için **ad tanımlayıcı değer** alanı için **düzenleme** simgesini (bir kurşun kalem) seçin. Gerektiğinde tanımlayıcı biçiminde ve kaynakta uygun değişiklikleri yapın. Ayrıntılar için bkz. [NameID 'Yi düzenlemeyle](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). İşiniz bittiğinde değişiklikleri kaydedin. 
 
4. Grup taleplerini yapılandırmak için **talep alanında döndürülen gruplar** için **Düzenle** simgesini seçin. Ayrıntılar için bkz. [Grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md).

5. Talep eklemek için sayfanın en üstünde **yeni talep Ekle** ' yi seçin. **Adı** girin ve uygun kaynağı seçin. **Öznitelik** kaynağını seçerseniz, kullanmak istediğiniz **kaynak özniteliğini** seçmeniz gerekir. **Çeviri** kaynağını seçerseniz, kullanmak istediğiniz **dönüştürme** ve **parametre 1** ' i seçmeniz gerekir. Ayrıntılar için bkz. [uygulamaya özgü talepler ekleme](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). İşiniz bittiğinde değişiklikleri kaydedin. 

6. **Kaydet**’i seçin. Yeni talep tabloda görüntülenir.

   > [!NOTE]
   > SAML belirtecini Azure AD 'den uygulamanıza özelleştirmenin ek yolları için aşağıdaki kaynaklara bakın.
   >- Azure portal aracılığıyla özel Roller oluşturmak için, bkz. [rol taleplerini yapılandırma](../develop/active-directory-enterprise-app-role-management.md).
   >- Talepleri PowerShell aracılığıyla özelleştirmek için bkz. [Customize Claim-PowerShell](../develop/active-directory-claims-mapping.md).
   >- Uygulamanız için isteğe bağlı talepler yapılandırmak üzere uygulama bildirimini değiştirmek için bkz. [isteğe bağlı talepler yapılandırma](../develop/active-directory-optional-claims.md).
   >- Belirteç kullanım belirteçleri, erişim belirteçleri, oturum belirteçleri ve KIMLIK belirteçlerine yönelik belirteç ömür ilkelerini ayarlamak için bkz. [belirteç yaşam sürelerini yapılandırma](../develop/active-directory-configurable-token-lifetimes.md). Ya da Azure AD koşullu erişimi aracılığıyla kimlik doğrulaması oturumlarını kısıtlamak için bkz. [kimlik doğrulama oturumu yönetimi özellikleri](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>3\. Adım. SAML imzalama sertifikasını yönetme

Azure AD, uygulamanın gönderdiği SAML belirteçlerini imzalamak için bir sertifika kullanır. Azure AD ile uygulama arasındaki güveni ayarlamak için bu sertifikaya ihtiyacınız vardır. Sertifika biçimi hakkında daha fazla bilgi için bkz. uygulamanın SAML belgeleri. Daha fazla bilgi için bkz. SAML belirtecindeki [Federasyon çoklu oturum açma](manage-certificates-for-federated-single-sign-on.md) ve [Gelişmiş sertifika imzalama seçenekleri](certificate-signing-options.md)için sertifikaları yönetme.

Azure AD 'den, etkin Sertifikayı Base64 veya ham biçimde doğrudan, **SAML Ile çoklu oturum açma 'yı kullanarak** yükleyebilirsiniz sayfasından indirebilirsiniz. Alternatif olarak, uygulama meta verileri XML dosyasını indirerek ya da uygulama Federasyon meta verileri URL 'sini kullanarak etkin sertifikayı edinebilirsiniz. Sertifikalarınızı görüntülemek, oluşturmak veya indirmek (etkin veya devre dışı) için aşağıdaki adımları izleyin.

1. **SAML Imzalama sertifikası** bölümüne gidin. 

   ![3\. adım SAML imzalama sertifikasını yönetme](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Sertifikanın şu olduğunu doğrulayın:

   - *İstenen sona erme tarihi.* Son kullanma tarihini üç yıla kadar gelecek şekilde yapılandırabilirsiniz.
   - *İstenen sertifika için etkin durumu.* Durum **etkin**değilse, durumu **etkin**olarak değiştirin. Durumu değiştirmek için, istenen sertifikanın satırına sağ tıklayın ve **sertifikayı etkin yap**' ı seçin.
   - *Doğru imzalama seçeneği ve algoritması.*
   - *Doğru bildirim e-posta adresleri (es).* Etkin sertifika sona erme tarihi yaklaştığında Azure AD, bu alanda yapılandırılan e-posta adresine bir bildirim gönderir.

2. Sertifikayı indirmek için Base64 biçimi, ham biçim veya Federasyon meta veri XML seçeneklerinden birini seçin. Azure AD Ayrıca `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`biçimindeki uygulamaya özgü meta verilere erişebileceğiniz **uygulama Federasyon meta veri URL 'sini** de sağlar.

3. Bir sertifikayı yönetmek, oluşturmak veya içeri aktarmak için, **SAML Imzalama sertifikası** bölümünün sağ üst köşesindeki **Düzenle** simgesini (bir kurşun kalem) seçin.

   ![SAML imzalama sertifikası](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Aşağıdaki eylemlerden herhangi birini gerçekleştirin:

   - Yeni bir sertifika oluşturmak için **Yeni sertifika**' yı seçin, **sona erme tarihi**' ni seçin ve ardından **Kaydet**' i seçin. Sertifikayı etkinleştirmek için bağlam menüsünü ( **...** ) seçin ve **sertifikayı etkin yap**' ı seçin.
   - Özel anahtar ve PFX kimlik bilgileriyle bir sertifikayı karşıya yüklemek için **sertifikayı Içeri aktar** ' ı seçin ve sertifikaya gidin. **PFX parolasını**girin ve ardından **Ekle**' yi seçin.  
   - Gelişmiş sertifika imzalama seçeneklerini yapılandırmak için aşağıdaki seçenekleri kullanın. Bu seçeneklerin açıklamaları için bkz. [Gelişmiş sertifika imzalama seçenekleri](certificate-signing-options.md) makalesi.
      - **Imzalama seçeneği** açılan LISTESINDE, **SAML yanıtı imzala**, **SAML onayını imzala**veya **SAML yanıtı ve onaylama**imzası ' nı seçin.
      - **Imzalama algoritması** açılan listesinde, **SHA-1** veya **SHA-256**' ı seçin.
   - Etkin sertifikanın sona erme tarihine yaklaştığındaki ek kişilere bildirmek için, **bildirim e-posta adresleri** alanlarına e-posta adreslerini girin.

4. Değişiklik yaptıysanız, **SAML Imzalama sertifikası** bölümünün en üstünde bulunan **Kaydet** ' i seçin. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4\. adımı. Uygulamayı Azure AD kullanacak şekilde ayarlama

**\<ayarla applicationName >** bölümü, Azure AD 'YI bir SAML kimlik sağlayıcısı olarak kullanacak şekilde uygulamada yapılandırılması gereken değerleri listeler. Gerekli değerler uygulamaya göre farklılık gösterir. Ayrıntılar için bkz. uygulamanın SAML belgeleri. Belgeleri bulmak için **\<uygulama adını ayarla >** başlığına gidin ve **adım adım yönergeleri görüntüle**' yi seçin. Belgeler, **oturum açma yapılandırma** sayfasında görüntülenir. Bu sayfa, **\<uygulama adı >** başlığındaki **oturum açma URL**'Sini, **Azure ad tanımlayıcısını**ve oturum **kapatma URL 'si** değerlerini doldurmasında size rehberlik eder.

1. Aşağı kaydırarak **\<ayarla applicationName >** bölümüne gidin. 
   
   ![4\. adım uygulamayı ayarlama](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Bu bölümdeki her satırdaki değeri gereken şekilde kopyalayın ve değeri uygulamaya eklemek için uygulamaya özgü yönergeleri izleyin. Galeri uygulamaları için **adım adım yönergeleri görüntüle**' yi seçerek belgeleri görüntüleyebilirsiniz. 
   - **Oturum açma URL 'si** ve **oturum kapatma URL 'Si** değerleri, Azure AD örneğiniz için SAML istek işleme uç noktası olan aynı uç noktaya çözümlenir. 
   - **Azure AD tanımlayıcısı** , uygulamaya verilen SAML belirtecindeki **veren** 'in değeridir.
2. Tüm değerleri uygun alanlara yapıştırdığınızda **Kaydet**' i seçin.

## <a name="step-5-validate-single-sign-on"></a>5\. adımı. Çoklu oturum açmayı doğrula

Uygulamanızı bir SAML tabanlı kimlik sağlayıcısı olarak Azure AD kullanacak şekilde yapılandırdıktan sonra, hesabınız için çoklu oturum açma çalışıp çalışmadığını görmek için ayarları test edebilirsiniz. 

2. **<applicationName>ile çoklu oturum açmayı doğrula** bölümüne gidin.

   ![5\. adım çoklu oturum açmayı doğrulama](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. **Doğrula**'yı seçin. Test seçenekleri açılır.

4. **Geçerli kullanıcı olarak oturum aç '** ı seçin. 

Oturum açma işlemi başarılı olursa, SAML uygulamanıza Kullanıcı ve grup atamaya hazırsınız demektir.
Bir hata iletisi görünürse, aşağıdaki adımları izleyin:

1. Hatayla ilgili bilgileri kopyalayıp **Hata neye benziyor?** kutusuna yapıştırın.

    ![Çözüm rehberliği alın](media/configure-single-sign-on-portal/error-guidance.png)

2. **Çözüm kılavuzunu al**' ı seçin. Kök nedeni ve çözüm kılavuzu görüntülenir.  Bu örnekte, Kullanıcı uygulamaya atanmamıştır.

3. Çözüm kılavuzunu okuyun ve mümkünse sorunu düzeltemedi.

4. Testi başarıyla tamamlana kadar tekrarlayın.

Daha fazla bilgi için bkz. [Azure Active Directory içindeki uygulamalarda SAML tabanlı çoklu oturum açma hatalarını ayıklama](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaya Kullanıcı veya Grup atama](methods-for-assigning-users-and-groups.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](configure-automatic-user-provisioning-portal.md)
