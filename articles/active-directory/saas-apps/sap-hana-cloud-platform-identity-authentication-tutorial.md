---
title: 'Öğretici: SAP Cloud Platform Identity Authentication ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP bulut platformu kimlik doğrulaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 196de1cacb977305c778e16182b21c8dcfafe13e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161165"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Öğretici: SAP Cloud Platform Identity Authentication ile tümleştirme Azure Active Directory

Bu öğreticide SAP bulut platformu kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
SAP Cloud Platform kimliği kimlik doğrulamasını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* SAP bulut platformu kimlik doğrulamasına erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP bulut platformu kimlik doğrulaması (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

SAP bulut platformu kimlik doğrulaması ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* SAP Cloud Platform kimlik kimlik doğrulaması çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SAP Cloud Platform kimlik doğrulaması **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

Teknik ayrıntıları kullanmadan önce, bakacağı kavramların anlaşılması çok önemlidir. SAP Cloud Platform kimliği kimlik doğrulaması ve Active Directory Federasyon Hizmetleri (AD FS), SAP bulutu ile korunan SAP uygulamaları ve Hizmetleri ile Azure AD tarafından korunan uygulamalar veya hizmetler arasında SSO uygulamanıza olanak tanır Platform kimliği kimlik doğrulaması.

SAP bulut platformu kimlik doğrulaması şu anda SAP uygulamaları için bir proxy kimlik sağlayıcısı görevi görür. Sırasıyla Azure Active Directory, Bu kurulumda önde gelen kimlik sağlayıcısı olarak davranır. 

Aşağıdaki diyagramda bu ilişki gösterilmektedir:

![Azure AD test kullanıcısı oluşturma](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Bu kurulumla, SAP bulut platformu kimlik doğrulama kiracınız Azure Active Directory güvenilir bir uygulama olarak yapılandırılır.

Bu şekilde korumak istediğiniz tüm SAP uygulamaları ve Hizmetleri, daha sonra SAP bulut platformu kimlik doğrulama yönetim konsolunda yapılandırılır.

Bu nedenle, SAP uygulamalarına ve hizmetlerine erişim verme yetkilendirmesinin SAP Cloud Platform kimlik kimlik doğrulamasında (Azure Active Directory aksine) gerçekleşmesi gerekir.

SAP Cloud Platform kimliği kimlik doğrulamasını Azure Active Directory Marketi aracılığıyla bir uygulama olarak yapılandırarak, ayrı talepler veya SAML onayları yapılandırmanız gerekmez.

> [!NOTE]
> Şu anda yalnızca Web SSO 'SU tarafından test edilmiştir. Uygulamadan API veya API-API iletişimi için gerekli olan akışlar çalışır, ancak henüz sınanmamıştır. Bunlar, sonraki etkinlikler sırasında test edilecek.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Galeriden SAP bulut platformu kimlik doğrulaması ekleme

SAP Cloud Platform kimliği kimlik doğrulamasının Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden SAP bulut platformu kimlik doğrulama kimlik doğrulamasını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden SAP bulut platformu kimlik doğrulaması eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SAP Cloud Platform Identity Authentication**yazın, sonuç panelinden **SAP Cloud Platform kimliği kimlik doğrulaması** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![SAP bulut platformu kimlik doğrulaması sonuçlar listesinde](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak [uygulama adı] ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve [uygulama adı] içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı [uygulama adı] ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[SAP Cloud Platform kimlik doğrulamasını yapılandırma çoklu oturum açma](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. SAP Cloud **[Platform kimlik kimlik doğrulaması test kullanıcısı oluşturma](#create-sap-cloud-platform-identity-authentication-test-user)** -SAP bulut platformu kimlik doğrulamasında kullanıcının Azure AD gösterimine bağlı olan Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı [uygulama adı] ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **SAP Cloud Platform kimlik doğrulama** uygulaması tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **IDP** başlatılmadı modunda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![SAP Cloud Platform kimlik doğrulama etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `<IAS-tenant-id>.accounts.ondemand.com`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [SAP Cloud Platform kimliği kimlik doğrulama istemci destek ekibine](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) başvurun. Tanımlayıcı değerini anlamıyorsanız, [KIRACı SAML 2,0 yapılandırması](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)Ile Ilgili SAP bulut platformu kimlik doğrulama belgelerini okuyun.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![SAP Cloud Platform kimlik doğrulama etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Bu değer gerçek değil. Bu değeri, gerçek oturum açma URL 'siyle güncelleştirin. Lütfen belirli bir iş uygulaması oturum açma URL 'sini kullanın. Şüpheniz varsa [SAP Cloud Platform kimliği kimlik doğrulama istemci desteği ekibine](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) başvurun.

6. SAP Cloud Platform kimlik doğrulama uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image](common/edit-attribute.png)

7. SAP uygulamanız **adı**gibi bir öznitelik beklediğinde, **Kullanıcı öznitelikleri** iletişim kutusunda **Kullanıcı talepleri** bölümüne **FirstName** özniteliğini ekleyin, yukarıdaki görüntüde gösterildiği gibi SAML belirteci özniteliğini yapılandırın ve şunu yapın Aşağıdaki adımlar:

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna FirstName adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinden User. bir öznitelik değeri seçin.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

8. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

9. **SAP Cloud Platform kimlik doğrulamasını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>SAP Cloud Platform kimlik doğrulaması çoklu oturum açmayı yapılandırma

1. Uygulamanız için yapılandırılmış SSO 'yu almak için SAP Cloud Platform kimlik doğrulaması yönetim konsoluna gidin. URL şu düzene sahiptir: `https://<tenant-id>.accounts.ondemand.com/admin`. Ardından [Microsoft Azure AD Ile tümleştirmede](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)SAP Cloud Platform kimlik doğrulama hakkındaki belgeleri okuyun.

2. Azure portal **Kaydet** düğmesini seçin.

3. Yalnızca başka bir SAP uygulaması için SSO eklemek ve etkinleştirmek istiyorsanız aşağıdaki ile devam edin. **GALERIDEN SAP Cloud Platform kimliği kimlik doğrulaması ekleme**bölümündeki adımları yineleyin.

4. Azure portal, **SAP Cloud Platform kimlik doğrulama** uygulaması tümleştirmesi sayfasında, **bağlantılı oturum açma**' yı seçin.

    ![Bağlantılı oturum açmayı Yapılandır](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Yapılandırmayı kaydedin.

> [!NOTE]
> Yeni uygulama, önceki SAP uygulamasının çoklu oturum açma yapılandırmasından yararlanır. SAP bulut platformu kimlik doğrulaması yönetim konsolunda aynı kurumsal kimlik sağlayıcılarını kullandığınızdan emin olun.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, SAP bulut platformu kimlik doğrulaması 'na erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **SAP bulut platformu kimlik doğrulaması**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **SAP Cloud Platform kimlik doğrulaması**' nı seçin.

    ![Uygulamalar listesindeki SAP bulut platformu kimlik doğrulama bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP bulut platformu kimlik doğrulaması test kullanıcısı oluşturma

SAP Cloud Platform kimlik kimlik doğrulamasında bir kullanıcı oluşturmanız gerekmez. Azure AD Kullanıcı deposunda olan kullanıcılar SSO işlevini kullanabilir.

SAP Cloud Platform kimlik doğrulama kimlik doğrulaması, Kimlik Federasyonu seçeneğini destekler. Bu seçenek, uygulamanın, kurumsal kimlik sağlayıcısı tarafından kimliği doğrulanmış kullanıcıların SAP bulut platformu kimlik doğrulama kullanıcı deposunda mevcut olup olmadığını kontrol etmesine izin verir.

Kimlik Federasyonu seçeneği varsayılan olarak devre dışıdır. Kimlik Federasyonu etkinleştirilmişse, yalnızca SAP bulut platformu kimlik doğrulaması 'nda içeri aktarılan kullanıcılar uygulamaya erişebilir.

SAP Cloud Platform kimlik doğrulaması ile kimlik Federasyonu etkinleştirme veya devre dışı bırakma hakkında daha fazla bilgi için, [Kullanıcı Mağazası Ile Kimlik Federasyonu yapılandırma konusundaki "sap Cloud Platform kimlik doğrulaması Ile Kimlik Federasyonu etkinleştirme" konusuna bakın SAP bulut platformu kimlik doğrulaması](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde SAP Cloud Platform kimliği kimlik doğrulama kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP bulut platformu kimlik kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
