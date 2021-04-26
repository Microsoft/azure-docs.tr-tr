---
title: 'Öğretici: Trend mikro Web Güvenliği (TMWS) ile Azure AD SSO tümleştirmesi'
description: Azure Active Directory ve trend mikro Web Güvenliği (TMWS) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008404"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Öğretici: Trend mikro Web Güvenliği (TMWS) ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Trend Micro Web Security 'yi (TMWS) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. TMWS 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, TMW erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla bir TMW 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SSO için etkinleştirilen bir TMWS aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

* TMWS, SP tarafından başlatılan SSO 'yu destekler.
* TMWS 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. Microsoft Cloud App Security kullanarak oturum denetimini nasıl zorlayacağınızı öğrenmek için, bkz. [herhangi bir uygulama için koşullu erişim uygulama denetimi ekleme ve dağıtma](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Galeriden TMWS ekleme

TMWS 'nin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden TMWS 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı ile oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar** ' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **eğilim mikro Web Güvenliği (tmws)** girin.
1. Arama sonuçlarında **eğilim mikro Web Güvenliği (TMWS)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>TMWS için Azure AD SSO 'yu yapılandırma ve test etme

B. Simon adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu TMWS ile yapılandırıp test edersiniz. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve TMWS 'deki ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD SSO 'yu TMWS ile yapılandırmak ve test etmek için bu temel adımları tamamlayacaksınız:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. [Azure AD test kullanıcısına](#grant-the-azure-ad-test-user-access-to-tmws) TMW erişimi verin.
    1. [Azure AD 'de Kullanıcı ve grup eşitleme ayarlarını yapılandırın](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. Uygulama tarafında [TMWS SSO 'Yu yapılandırın](#configure-tmws-sso) .
1. Yapılandırmayı doğrulamak için [SSO test](#test-sso) edin.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Trend mikro Web Güvenliği (tmws)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünde **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem düğmesini seçin:

   ![Temel SAML yapılandırma ayarlarını düzenleme](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki kutulara değerler girin:

    a. **Tanımlayıcı (VARLıK kimliği)** kutusunda, aşağıdaki düzende bir URL girin:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. **Yanıt URL** 'si kutusuna şu URL 'yi girin:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Önceki adımdaki tanımlayıcı değeri, girmeniz gereken değer değildir. Gerçek tanımlayıcıyı kullanmanız gerekir. Bu değeri, Azure AD 'nin **kimlik doğrulama yöntemi** sayfasında azure Yönetim **> Dizin Hizmetleri**' nden **Azure yönetim portalı için hizmet sağlayıcı ayarları** bölümüne alabilirsiniz.

1. TMWS, SAML onaylamalarını belirli bir biçimde bekler, bu nedenle SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemeniz gerekir. Bu ekran görüntüsünde varsayılan öznitelikler gösterilmektedir:

    ![Varsayılan öznitelikler](common/default-attributes.png)

1. Önceki ekran görüntüsündeki özniteliklerin yanı sıra, TMWS, SAML yanıtında iki daha fazla özniteliğin geri geçirilmesini bekler. Bu öznitelikler aşağıdaki tabloda gösterilmiştir. Öznitelikler önceden doldurulur, ancak gereksinimlerinizi karşılayacak şekilde değiştirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | 'Le | User. UserPrincipalName |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)**. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için bu sertifika adının yanındaki **İndir** bağlantısını seçin:

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Eğilim mikro Web Güvenliği (TMWS) ayarla** bölümünde, gereksinimlerinize göre uygun URL 'Yi veya URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' ı seçin. **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** kutusuna girin `B.Simon` .  
   1. **Kullanıcı adı** kutusuna **_kullanıcıadı_ @* şirketetkialanı yazın *.* * * * uzantısı. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Azure AD test kullanıcısına TMWS erişimi verme

Bu bölümde, TMWS 'ye erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Trend mikro Web Güvenliği (TMWS)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünde, **Kullanıcılar ve gruplar**' ı seçin:

   ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Azure AD 'de Kullanıcı ve grup eşitleme ayarlarını yapılandırma

1. Sol bölmede **Azure Active Directory**’yi seçin.

1. **Yönet**' in altında **uygulama kayıtları**' yi seçin ve ardından **tüm uygulamalar**' ın altında yeni kurumsal uygulamanızı seçin.

1. **Yönet**’in altında **Sertifikalar ve gizli diziler**’i seçin.

1. **İstemci gizli** dizileri alanında **yeni istemci parolası**' nı seçin.

1. **İstemci parolası Ekle ekranında**, isteğe bağlı olarak bir açıklama ekleyin ve istemci parolası için bir süre sonu süresi seçin ve ardından **Ekle**' yi seçin. Yeni istemci parolası, **istemci** gizli dizileri alanında görüntülenir.

1. İstemci gizli değerini kaydedin. Daha sonra, bunu TMWS 'ye girersiniz.

1. **Yönet** altında **API izinleri**' ni seçin. 

1. **API izinleri** penceresinde **izin Ekle**' yi seçin.

1. **API Izinleri iste** penceresinin **Microsoft apı 'leri** sekmesinde **Microsoft Graph** ve ardından **Uygulama izinleri**' ni seçin.

1. Bu izinleri bulun ve ekleyin: 

    * Group.Read.All
    * User. Read. All

1. **Izin Ekle**' yi seçin. Ayarlarınızın kaydedildiğini doğrulamak için bir ileti görüntülenir. Yeni izinler, **API izinleri** penceresinde görünür.

1. **Izin verme** alanı ' nda, ***yönetici hesabınız* için yönetici onayı ver ' i (varsayılan dizin)** seçin ve ardından **Evet**' i seçin. İstenen izinler için yönetici onayı verildiğini doğrulamak üzere bir ileti görüntülenir.

1. **Genel bakış**'ı seçin. 

1. Sağ bölmede gördüğünüz **uygulama (istemci) kimliğini** ve **Dizin (kiracı) kimliğini** kaydedin. Daha sonra bu bilgileri TMWS 'ye girersiniz. Ayrıca, sağ bölmede gördüğünüz etki alanı adını **yönetmek ve kaydetmek > Azure Active Directory** altında **özel etki alanı adları** ' nı da seçebilirsiniz.

## <a name="configure-tmws-sso"></a>TMWS SSO 'yu yapılandırma

Uygulama tarafında TMWS SSO 'yu yapılandırmak için bu adımları izleyin.

1. Tmws yönetim konsolunda oturum açın ve **Yönetim**  >  **kullanıcıları & kimlik doğrulaması**  >  **Dizin Hizmetleri**' ne gidin.

1. Ekranın üst bölümünde **burayı** seçin.

1. **Kimlik doğrulama yöntemi** SAYFASıNDA **Azure AD**' yi seçin.

1. Kuruluşunuzdaki Azure AD kullanıcılarının, verileri TMWS ile eşitlenmediğinde Web sitelerini TMWS aracılığıyla ziyaret edip etmeyeceğini yapılandırmak için açık veya **kapalı** **'** yı seçin.

    > [!NOTE]
    > Azure AD 'den eşitlenmemiş kullanıcıların kimliği, yalnızca bilinen TMWS ağ geçitleri veya kuruluşunuz için ayrılmış bağlantı noktası aracılığıyla doğrulanabilir.

1. **Kimlik sağlayıcısı ayarları** bölümünde aşağıdaki adımları uygulayın:

    a. **Hizmet URL 'si** kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini girin.

    b. **Oturum açma adı öznitelik** kutusunda, Azure Portal Kullanıcı **talebi adını** **User. onpremisessamaccountname** Source özniteliğiyle girin.

    c. **Ortak SSL sertifikası** kutusunda, Azure Portal indirilen **sertifikayı (base64)** kullanın.

1. **Eşitleme ayarları** bölümünde aşağıdaki adımları uygulayın:

    a. **Kiracı** kutusuna Azure Portal **Dizin (kiracı) kimliğini** veya **özel etki alanı adı** değerini girin.

    b. **Uygulama kimliği** kutusuna Azure Portal **uygulama (istemci) kimliği** değerini girin.

    c. **İstemci parolası** kutusuna Azure Portal **istemci gizli** anahtarını girin.

    d. Azure AD ile el ile veya bir zamanlamaya göre eşitlenecek **eşitleme zamanlaması** ' nı seçin. **El ile**' yi seçerseniz, Active Directory Kullanıcı bilgilerinde her değişiklik yapıldığında, **Dizin Hizmetleri** sayfasına dönüp, tmws 'deki bilgilerin güncel kalması için el ile eşitleme gerçekleştirmeyi unutmayın.

    e. Azure AD hizmeti 'nin başarıyla bağlanıp bağlanamayacağını denetlemek için **Bağlantıyı Sına** ' yı seçin.
    
    f. **Kaydet**’i seçin.
 
 > [!NOTE]
 > TMWS 'yi Azure AD ile yapılandırma hakkında daha fazla bilgi için bkz. [tmws 'de Azure AD ayarlarını yapılandırma](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Test SSO 'SU 

Azure AD hizmetini yapılandırdıktan ve Kullanıcı kimlik doğrulama yöntemi olarak Azure AD 'yi belirttikten sonra, kurulumunuzu doğrulamak için TMWS ara sunucusunda oturum açabilirsiniz. Azure AD oturum açma Hesabınızı doğruladıktan sonra, internet 'i ziyaret edebilirsiniz.

> [!NOTE]
> Tmws, Azure AD portalındaki çoklu oturum açmayı test etmeyi desteklemez, **genel bakış**  >  **Çoklu oturum açma** altında  >  Yeni kurumsal uygulamanızın **SAML testinde çoklu oturum açma ayarı** yapın  >   .

1. Tüm tanımlama bilgilerinin tarayıcısını temizleyin ve ardından tarayıcıyı yeniden başlatın. 

1. Tarayıcınızı TMWS proxy sunucusuna işaret edin. Ayrıntılar için bkz. [PAC dosyalarını kullanarak trafik iletme](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Herhangi bir internet web sitesini ziyaret edin. TMWS, sizi TMWS captive portalına yönlendirdirecektir.

1. Active Directory bir hesap (Biçim: *etki alanı* \\ *sAMAccountName* veya *sAMAccountName* @ *etki alanı*), e-posta adresi veya UPN belirtin ve ardından **oturum aç**' ı seçin. TMWS sizi Azure AD oturum açma penceresine gönderir.

1. Azure AD oturum açma penceresinde Azure AD hesabınızın kimlik bilgilerini girin. Şimdi TMWS 'de oturum açmanız gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile eğilim mikro Web güvenliğini deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle eğilim mikro Web güvenliğini koruma](/cloud-app-security/proxy-intro-aad)