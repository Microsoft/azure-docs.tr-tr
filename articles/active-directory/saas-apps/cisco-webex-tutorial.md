---
title: 'Öğretici Azure Active Directory: Cisco WebEx toplantılarıyla çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Cisco WebEx Toplantıları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026257"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Öğretici: Cisco WebEx toplantılarıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Cisco WebEx toplantılarının Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Cisco WebEx toplantılarını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cisco WebEx toplantılarına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco WebEx toplantılarına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cisco WebEx Toplantıları çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cisco WebEx Toplantıları **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

* Cisco WebEx Toplantıları **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Galeriden Cisco WebEx Toplantıları ekleme

Cisco WebEx toplantılarının Azure AD 'de tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Cisco WebEx Toplantıları eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cisco WebEx Toplantıları** yazın.
1. Sonuçlar panelinden **Cisco WebEx Toplantıları** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Cisco WebEx toplantıları için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Cisco WebEx toplantılarıyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için Cisco WebEx toplantılarında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cisco WebEx toplantılarıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. **[Cisco WebEx Toplantıları SSO 'Su yapılandırma](#configure-cisco-webex-meetings-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Cisco WebEx toplantılarında B. Simon 'a sahip olmak için **[Cisco WebEx Toplantıları test kullanıcısı oluşturun](#create-cisco-webex-meetings-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Cisco WebEx Toplantıları** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **Service Provider meta veri** dosyasını aşağıdaki gibi karşıya yükleyerek **IDP** tarafından başlatılan modda uygulamayı yapılandırabilirsiniz:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    c. Hizmet sağlayıcı meta veri dosyasını karşıya yükleme işleminin başarıyla tamamlanmasından sonra, **tanımlayıcı** ve **yanıt URL** değerleri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur.

    >[!Note]
    >Service Provider meta verileri dosyasını, Öğreticinin ilerleyen kısımlarında açıklanan **Cisco WebEx Toplantıları SSO bölümünü yapılandırma** bölümünden alacaksınız. 

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:  

    a. **Temel SAML yapılandırması** bölümünde Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)
    
    b. **Oturum aç URL** metin kutusuna şu kalıbı kullanarak URL 'yi yazın: ` https://<customername>.my.webex.com`

5. Cisco WebEx Toplantıları uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

6. Daha fazlasına ek olarak, Cisco WebEx Toplantıları uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Kullanıcı öznitelikleri iletişim kutusundaki Kullanıcı talepleri bölümünde aşağıdaki tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin: 

    | Adı | Kaynak özniteliği|
    | ---------------|  --------- |
    |   FirstName    | Kullanıcı. |
    |   Soyadı    | User. soyadı |
    |   e-posta       | Kullanıcı. Mail |
    |   'sini    | Kullanıcı. Mail |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinden, açılan listeden o satır için gösterilen öznitelik değerini seçin.

    f. **Kaydet** düğmesine tıklayın.

4. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Cisco WebEx toplantılarını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
    1. **Ad** alanına `B.Simon` girin.  
    1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
    1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
    1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Cisco WebEx toplantılarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Cisco WebEx Toplantıları**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco WebEx Toplantıları SSO 'SU yapılandırma

1. Yönetim kimlik bilgilerinizle `https://<customername>.webex.com/admin` URL 'sine gidin.

2. **Ortak site ayarları** ' na gidin ve **SSO yapılandırması**' na gidin.
 
    ![Çoklu oturum açmayı yapılandırma](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. **WebEx yönetimi** sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. **Federasyon Protokolü**olarak **SAML 2,0** ' ı seçin.

    b. Azure portal 'ten indirdiğiniz meta veri dosyasını karşıya yüklemek için **SAML meta verilerini Içeri aktar** bağlantısına tıklayın.

    c. Hizmet sağlayıcısı meta veri dosyasını indirmek için **dışarı aktar** düğmesine tıklayın ve Azure Portal ÜZERINDEKI **temel SAML yapılandırması** bölümüne yükleyin.

    d. **Authcontextclassref** metin kutusunda `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` yazın ve Azure AD ile MFA 'yı etkinleştirmek istiyorsanız `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` gibi iki değer yazın

    e. **Otomatik hesap oluşturmayı**seçin.

    >[!NOTE]
    >**Tam zamanında** Kullanıcı sağlamayı etkinleştirmek Için **Otomatik hesap oluşturmayı**denetlemeniz gerekir. Bu SAML belirteci özniteliklerinin yanı sıra SAML yanıtında geçirilmesi gerekir.

    f. **Kaydet** düğmesine tıklayın.

    >[!NOTE]
    >Bu yapılandırma yalnızca e-posta biçiminde WebEx UserID kullanan müşteriler içindir.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco WebEx Toplantıları test kullanıcısı oluşturma

Bu bölümün amacı, Cisco WebEx toplantılarında B. Simon adlı bir Kullanıcı oluşturmaktır. Cisco WebEx Toplantıları, varsayılan olarak etkinleştirilen **tam zamanında** sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Cisco WebEx toplantılarında zaten mevcut değilse, Cisco WebEx toplantılarına erişmeye çalıştığınızda yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Cisco WebEx Toplantıları kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Cisco WebEx toplantılarında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ServiceNow 'ı Azure AD ile deneyin](https://aad.portal.azure.com)
