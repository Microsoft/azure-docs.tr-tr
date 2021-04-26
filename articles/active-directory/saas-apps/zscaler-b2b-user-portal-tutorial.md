---
title: 'Öğretici: Zscaler B2B Kullanıcı Portalı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zscaler B2B Kullanıcı Portalı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519826"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Öğretici: Zscaler B2B Kullanıcı Portalı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Zscaler B2B Kullanıcı portalını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zscaler B2B Kullanıcı portalını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler B2B Kullanıcı portalına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler B2B Kullanıcı Portalı ' na otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zscaler B2B Kullanıcı Portalı çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Zscaler B2B Kullanıcı portalı **IDP** tarafından başlatılan SSO 'yu destekler

* Zscaler B2B Kullanıcı portalı **, tam zamanında** Kullanıcı sağlamayı destekliyor

* Zscaler B2B Kullanıcı Portalı 'nı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve bu verileri korumayı koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Galeriden Zscaler B2B Kullanıcı Portalı ekleme

Zscaler B2B Kullanıcı portalının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Zscaler B2B Kullanıcı Portalı 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler B2B Kullanıcı Portalı** yazın.
1. Sonuçlar panelinden **Zscaler B2B Kullanıcı Portalı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Zscaler B2B Kullanıcı Portalı için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler B2B Kullanıcı Portalı Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Zscaler B2B Kullanıcı portalında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zscaler B2B Kullanıcı Portalı ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Zscaler B2B Kullanıcı Portalı SSO 'Su yapılandırma](#configure-zscaler-b2b-user-portal-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[ZSCALER B2B Kullanıcı Portalı test kullanıcısı oluşturun](#create-zscaler-b2b-user-portal-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Zscaler B2B Kullanıcı portalında B. Simon 'un bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Zscaler B2B Kullanıcı Portalı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Zscaler B2B Kullanıcı Portalı istemci destek ekibine](https://help.zscaler.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Zscaler B2B Kullanıcı portalını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Zscaler B2B Kullanıcı portalına erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler B2B Kullanıcı Portalı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Zscaler B2B Kullanıcı Portalı SSO 'SU yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve Zscaler B2B Kullanıcı Portalı şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Menünün sol tarafında **Yönetim** ' e tıklayın ve **kimlik doğrulama** bölümüne gidin **IDP yapılandırması**' na tıklayın.

    ![Zscaler özel erişim Yöneticisi yönetimi](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Sağ üst köşede **IDP Yapılandırması Ekle**' ye tıklayın. 

    ![Zscaler özel erişim Yöneticisi IDP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. **IDP Yapılandırması Ekle** sayfasında, aşağıdaki adımları uygulayın:
 
    ![Zscaler özel erişim Yöneticisi seçme](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. **IDP meta veri dosyası yükleme** alanına Indirilen meta veri dosyasını Azure AD 'den karşıya yüklemek Için **Dosya Seç** ' e tıklayın.

    b. **IDP meta verilerini** Azure AD 'den okur ve tüm alan bilgilerini aşağıda gösterildiği gibi doldurur.

    ![Zscaler özel erişim Yöneticisi yapılandırması](./media/zscaler-b2b-user-tutorial/config.png)

    c. Etki **alanları** alanından etki alanınızı seçin.
    
    d. **Kaydet**’e tıklayın.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Zscaler B2B Kullanıcı Portalı test kullanıcısı oluşturma

Bu bölümde, Zscaler B2B Kullanıcı portalında Britta Simon adlı bir Kullanıcı oluşturulur. Zscaler B2B Kullanıcı Portalı, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler B2B Kullanıcı portalında zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Zscaler B2B Kullanıcı Portalı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Zscaler B2B Kullanıcı portalında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Zscaler B2B Kullanıcı portalını deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)