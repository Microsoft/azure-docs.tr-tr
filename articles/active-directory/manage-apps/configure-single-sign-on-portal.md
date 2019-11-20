---
title: Çoklu oturum açmayı yapılandırma - Azure Active Directory | Microsoft Docs
description: Bu öğreticide Azure portal kullanılarak bir uygulama için Azure Active Directory (Azure AD) ile SAML tabanlı çoklu oturum açma yapılandırması yapılmaktadır.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422610"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>SAML tabanlı çoklu oturum açmayı yapılandırma

Azure AD kurumsal uygulamalarınıza bir uygulama ekledikten sonra, çoklu oturum açma ayarlarını yapılandırırsınız. Bu makalede, Galeri dışı bir uygulama için SAML tabanlı çoklu oturum açma 'nın nasıl yapılandırılacağı açıklanır. 

> [!NOTE]
> Galeri uygulaması ekleniyor mu? [SaaS uygulama öğreticileri listesinde](../saas-apps/tutorial-list.md) adım adım kurulum yönergelerini bulun

Galeri dışı bir uygulama için *kod yazmadan*çoklu oturum açmayı yapılandırmak için bir aboneliğiniz veya Azure AD Premium olması gerekir ve uygulamanın SAML 2,0 ' i desteklemesi gerekir. Azure AD sürümleri hakkında daha fazla bilgi için [Azure AD fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)ziyaret edin.

## <a name="before-you-begin"></a>Başlamadan önce

- Uygulama Azure AD kiracınıza eklenmemişse, bkz. [Galeri uygulaması ekleme](add-gallery-app.md) veya [Galeri dışı bir uygulama ekleme](add-non-gallery-app.md).
- Aşağıdaki ayarlarla ilgili doğru bilgileri almak için uygulama satıcınıza başvurun:

    | Temel SAML yapılandırma ayarı | SP ile başlatılan | idP ile başlatılan | Açıklama |
    |:--|:--|:--|:--|
    | Tanımlayıcı (Varlık Kimliği) | Bazı uygulamalar için gereklidir | Bazı uygulamalar için gereklidir | Çoklu oturum açma yapılandırmasının yapıldığı uygulamayı benzersiz olarak tanımlar. Azure AD, kimliği, SAML belirtecinin hedef kitle parametresi olarak uygulamaya gönderir. Uygulamanın doğrulaması bekleniyordu. Bu değer ayrıca uygulama tarafından sağlanan SAML meta verilerinde Varlık Kimliği olarak da görünür. *Bu değeri, uygulama tarafından gönderilen Authisteynveren ( SAML isteği) öğesi olarak bulabilirsiniz.* |
    | Yanıt URL'si | İsteğe Bağlı | Gerekli | Uygulamanın SAML belirtecini almayı beklediği konumu belirtir. Yanıt URL'si, Onay Belgesi Tüketici Hizmeti (ACS) URL'si olarak da bilinir. |
    | Oturum Açma URL'si | Gerekli | Belirtmeyin | Kullanıcı bu URL'yi açtığında hizmet sağlayıcısı kimlik doğrulaması ve oturum açma için Azure AD'ye yönlendirir. Azure AD, uygulamayı Office 365 veya Azure AD erişim panelinden başlatmak için URL 'YI kullanır. Boş olduğunda, Azure AD, Kullanıcı uygulamayı başlattığında çoklu oturum açmayı başlatmak üzere kimlik sağlayıcısını kullanır.|
    | Geçiş Durumu | İsteğe Bağlı | İsteğe Bağlı | Uygulamaya kimlik doğrulaması tamamlandıktan sonra kullanıcının yönlendirileceği yeri belirtir. Genellikle değer uygulamanın geçerli bir URL 'sidir. Ancak, bazı uygulamalar bu alanı farklı şekilde kullanır. Daha fazla bilgi için uygulama satıcısına danışın.
    | Oturum Kapatma URL'si | İsteğe Bağlı | İsteğe Bağlı | SAML oturum kapatma yanıtlarını uygulamaya geri göndermek için kullanılır.

## <a name="step-1-edit-the-basic-saml-configuration"></a>1\.Adım Temel SAML yapılandırmasını düzenleme

1. [Azure Portal](https://portal.azure.com) bir bulut uygulaması Yöneticisi veya Azure AD kiracınız için bir uygulama Yöneticisi olarak oturum açın.

1. **Azure Active Directory** > **Kurumsal uygulamalar** ' a gidin ve listeden uygulamayı seçin. 
   
   - Uygulamayı aramak için, **uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve ardından **Uygula**' yı seçin. Arama kutusuna uygulamanın adını girin ve sonra sonuçlardan uygulamayı seçin.

1. **Yönet** bölümünde **Çoklu oturum açma**' yı seçin. 

1. **SAML**'yi seçin. **SAML Ile çoklu oturum açmayı ayarlama sayfası açılır** .

1. Temel SAML yapılandırma seçeneklerini düzenlemek için, **temel SAML yapılandırması** bölümünün sağ üst köşesindeki **Düzenle** simgesini (bir kurşun kalem) seçin.

     ![Sertifikaları yapılandırın](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Uygun alanlarda, [başlamadan önce](#before-you-begin) bölümünde açıklanan bilgileri girin.

1. Sayfanın en üstünde **Kaydet**' i seçin.

## <a name="step-2-configure-user-attributes-and-claims"></a>2\.Adım Kullanıcı özniteliklerini ve taleplerini yapılandırma 

Bir uygulama, Kullanıcı oturum açtığında Azure AD 'den aldığı SAML belirtecinde belirli Kullanıcı öznitelikleri veya talepler gerektirebilir. Örneğin, belirli talep URI 'Leri veya talep değerleri gerekli olabilir veya Microsoft Identity platformunda depolanan kullanıcı adından farklı bir **ad** olması gerekebilir. Galeri uygulamaları için gereksinimler [uygulamaya özgü öğreticilerde](../saas-apps/tutorial-list.md)açıklanmıştır veya uygulama satıcısına danışabilirsiniz. Kullanıcı özniteliklerini ve taleplerini yapılandırmaya yönelik genel adımlar aşağıda açıklanmıştır.

1. **Kullanıcı öznitelikleri ve talepler** bölümünde sağ üst köşedeki **düzenleme** simgesini (bir kurşun kalem) seçin.

1. **Ad tanımlayıcı değerini**doğrulayın. Varsayılan değer *User. PrincipalName*' dır. Kullanıcı tanımlayıcısı, uygulama içinde her kullanıcıyı benzersiz olarak tanımlar. Örneğin e-posta adresi hem kullanıcı adı hem de benzersiz tanıtıcı olarak kullanılıyorsa değeri *user.mail* olarak ayarlayın.

1. **Ad tanımlayıcı değerini**değiştirmek Için **ad tanımlayıcı değer** alanı için **düzenleme** simgesini (bir kurşun kalem) seçin. Gerektiğinde tanımlayıcı biçiminde ve kaynakta uygun değişiklikleri yapın. Ayrıntılar için bkz. [NameID 'Yi düzenlemeyle](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). İşiniz bittiğinde değişiklikleri kaydedin. 
 
1. Grup taleplerini yapılandırmak için **talep alanında döndürülen gruplar** için **Düzenle** simgesini seçin. Ayrıntılar için bkz. [Grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md).

3. Talep eklemek için sayfanın en üstünde **yeni talep Ekle** ' yi seçin. **Adı** girin ve uygun kaynağı seçin. **Öznitelik** kaynağını seçerseniz, kullanmak istediğiniz **kaynak özniteliğini** seçmeniz gerekir. **Çeviri** kaynağını seçerseniz, kullanmak istediğiniz **dönüştürme** ve **parametre 1** ' i seçmeniz gerekir. Ayrıntılar için bkz. [uygulamaya özgü talepler ekleme](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). İşiniz bittiğinde değişiklikleri kaydedin. 

4. **Kaydet**’i seçin. Yeni talep tabloda görüntülenir.

   > [!NOTE]
   > SAML belirtecini Azure AD 'den uygulamanıza özelleştirmenin ek yolları için aşağıdaki kaynaklara bakın.
   >- Azure portal aracılığıyla özel Roller oluşturmak için, bkz. [rol taleplerini yapılandırma](../develop/active-directory-enterprise-app-role-management.md).
   >- Talepleri PowerShell aracılığıyla özelleştirmek için bkz. [Customize Claim-PowerShell](../develop/active-directory-claims-mapping.md).
   >- Uygulamanız için isteğe bağlı talepler yapılandırmak üzere uygulama bildirimini değiştirmek için bkz. [isteğe bağlı talepler yapılandırma](../develop/active-directory-optional-claims.md).
   >- Belirteç kullanım belirteçleri, erişim belirteçleri, oturum belirteçleri ve KIMLIK belirteçlerine yönelik belirteç ömür ilkelerini ayarlamak için bkz. [belirteç yaşam sürelerini yapılandırma](../develop/active-directory-configurable-token-lifetimes.md). Ya da Azure AD koşullu erişimi aracılığıyla kimlik doğrulaması oturumlarını kısıtlamak için bkz. [kimlik doğrulama oturumu yönetimi özellikleri](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Adım 3. SAML imzalama sertifikasını yönetme

Azure AD, uygulamanın gönderdiği SAML belirteçlerini imzalamak için bir sertifika kullanır. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında etkin sertifikayı görüntüleyebilir veya indirebilirsiniz. Ayrıca bir sertifikayı güncelleştirebilir, oluşturabilir veya içeri aktarabilirsiniz. Galeri uygulamaları için, sertifika biçimiyle ilgili ayrıntılar, uygulamanın SAML belgelerinde bulunur ( [uygulamaya özgü öğreticilere](../saas-apps/tutorial-list.md)bakın). 

1. **SAML Imzalama sertifikası** bölümüne gidin. Uygulamanın türüne bağlı olarak Sertifikayı Base64 biçiminde, ham biçimde veya Federasyon meta veri XML dosyasında indirme seçeneklerini görürsünüz. Azure AD Ayrıca, `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`uygulama **Federasyon meta veri URL 'sini** de sağlar.

1. Bir sertifikayı yönetmek, oluşturmak veya içeri aktarmak için **SAML Imzalama sertifikası** bölümünün sağ üst köşesindeki **Düzenle** simgesini (bir kurşun kalem) seçin ve ardından aşağıdakilerden birini yapın:

   - Yeni bir sertifika oluşturmak için **Yeni sertifika**' yı seçin, **sona erme tarihi**' ni seçin ve ardından **Kaydet**' i seçin. Sertifikayı etkinleştirmek için bağlam menüsünü ( **...** ) seçin ve **sertifikayı etkin yap**' ı seçin.
   - Özel anahtar ve PFX kimlik bilgileriyle bir sertifikayı karşıya yüklemek için **sertifikayı Içeri aktar** ' ı seçin ve sertifikaya gidin. **PFX parolasını**girin ve ardından **Ekle**' yi seçin.  
   - Gelişmiş sertifika imzalama seçeneklerini yapılandırmak için aşağıdaki seçenekleri kullanın. Bu seçeneklerin açıklamaları için bkz. [Gelişmiş sertifika imzalama seçenekleri](certificate-signing-options.md) makalesi.
      - **Imzalama seçeneği** açılan LISTESINDE, **SAML yanıtı imzala**, **SAML onayını imzala**veya **SAML yanıtı ve onaylama**imzası ' nı seçin.
      - **Imzalama algoritması** açılan listesinde, **SHA-1** veya **SHA-256**' ı seçin.
   - Etkin sertifikanın sona erme tarihine yaklaştığındaki ek kişilere bildirmek için, **bildirim e-posta adresleri** alanlarına e-posta adreslerini girin.

1. **SAML Imzalama sertifikası** bölümünün en üstünde bulunan **Kaydet** ' i seçin. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4\. adımı. Uygulamayı Azure AD kullanacak şekilde ayarlama

**ApplicationName \<> ayarla** bölümünde, Azure AD 'yi bir SAML kimlik sağlayıcısı olarak kullanacak şekilde uygulamada yapılandırılması gereken değerler listelenmiştir. Gerekli değerler uygulamaya göre farklılık gösterir. Ayrıntılar için bkz. uygulamanın SAML belgeleri.

1. Aşağı kaydırarak  **\<> Kurulum ApplicationName** bölümüne gidin. 
2. Bu bölümdeki her satırdaki değeri gereken şekilde kopyalayın ve değeri uygulamaya eklemek için uygulamaya özgü yönergeleri izleyin. Galeri uygulamaları için **adım adım yönergeleri görüntüle**' yi seçerek belgeleri görüntüleyebilirsiniz. 
   - **Oturum açma URL 'si** ve **oturum kapatma URL 'Si** değerleri, Azure AD örneğiniz için SAML istek işleme uç noktası olan aynı uç noktaya çözümlenir. 
   - **Azure AD tanımlayıcısı** , uygulamaya verilen SAML belirtecindeki **veren** 'in değeridir.
1. Tüm değerleri uygun alanlara yapıştırdığınızda **Kaydet**' i seçin.

## <a name="step-5-validate-single-sign-on"></a>5\. adımı. Çoklu oturum açmayı doğrula

Tek bir oturum açma, yönetici için çalışıp çalışmadığını görmek için ayarları test etmeye hazırsınız.  

1. Uygulamanızın çoklu oturum açma ayarlarını açın. 
2. **İle<applicationName> çoklu oturum açmayı doğrula** bölümüne gidin. Bu öğreticide, bu bölüm **GitHub-test ayarlama**olarak adlandırılır.
3. **Test**' i seçin. Test seçenekleri açılır.
4. **Geçerli kullanıcı olarak oturum aç '** ı seçin. 

Oturum açma işlemi başarılı olursa, SAML uygulamanıza Kullanıcı ve grup atamaya hazırsınız demektir.
Bir hata iletisi görünürse, aşağıdaki adımları izleyin:

1. Hatayla ilgili bilgileri kopyalayıp **Hata neye benziyor?** kutusuna yapıştırın.

    ![Çözüm kılavuzunu almak için "hata nasıl görünür?" kutusunu kullanın](media/configure-single-sign-on-portal/error-guidance.png)

1. **Çözüm kılavuzunu al**' ı seçin. Kök nedeni ve çözüm kılavuzu görüntülenir.  Bu örnekte, Kullanıcı uygulamaya atanmamıştır.
1. Çözüm kılavuzunu okuyun ve mümkünse sorunu düzeltemedi.
1. Testi başarıyla tamamlana kadar tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaya Kullanıcı veya Grup atama](methods-for-assigning-users-and-groups.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](configure-automatic-user-provisioning-portal.md)
