---
title: 'Öğretici: Openatina ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Openatina arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Öğretici: Openatina ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Openatina 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Openatina 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Openatina erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Openatina 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Openatina çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Openatina, **IDP** tarafından başlatılan SSO 'yu destekler
* Openatina **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-openathens-from-the-gallery"></a>Galeriden Openatina ekleme

Openatina 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Openatina 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **openatina** yazın.
1. Sonuçlar panelinden **Openatina** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Openatina için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Openatina Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Openatina 'daki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Openatina ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Openatina SSO 'Yu yapılandırma](#configure-openathens-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Openatina 'da kullanıcının Azure AD gösterimine bağlı olan, Openatina 'da B. Simon 'a karşılık gelen bir buna sahip olmak için **[openatina test kullanıcısı oluşturun](#create-openathens-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **openatina** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, bu öğreticide daha sonra bahsedilen adımlar Için **hizmet sağlayıcı meta veri dosyasını**karşıya yükleyin.

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![openatina karşıya yükleme meta verileri](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![Openatina tarayıcı karşıya yükleme meta verileri](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **temel SAML yapılandırması** bölümünde otomatik olarak doldurulmuş **tanımlayıcı** değeri Al metin kutusu:

    ![Openatina etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-identifier.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Openatina ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Openatina erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Openatina**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-openathens-sso"></a>Openatina SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Openatina şirket sitenizde yönetici olarak oturum açın.

1. **Yönetim** sekmesi altındaki listeden **Bağlantılar** ' ı seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. **SAML 1.1/2.0**' ı seçin ve ardından **Yapılandır** düğmesini seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Yapılandırmayı eklemek için, Azure portal indirdiğiniz Metadata. xml dosyasını karşıya yüklemek için, **Gözden** geçirme düğmesini seçin ve ardından **Ekle**' yi seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. **Ayrıntılar** sekmesi altında aşağıdaki adımları gerçekleştirin.

    ![Çoklu oturum açmayı yapılandırma](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. **Görünen ad eşlemesi**' nde **özniteliği kullan**' ı seçin.

    b. **Görünen ad özniteliği** metin kutusunda `http://schema.microsoft.com/identity/claims/displayname`değeri girin.

    c. **Benzersiz kullanıcı eşlemesi**' nde **özniteliği kullan**' ı seçin.

    d. **Benzersiz kullanıcı özniteliği** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`değeri girin.

    e. **Durum**' da, tüm üç onay kutusunu seçin.

    f. **Yerel hesap oluştur**' da **otomatik olarak**' ı seçin.

    g. **Değişiklikleri Kaydet**' i seçin.

    h. **</> bağlı olan taraf** sekmesinden, **SP meta veri XML** dosyasını indirmek için **meta veri URL 'sini** kopyalayın ve bunu tarayıcıda açın. Bu SP meta veri dosyasını Azure AD 'deki **temel SAML yapılandırması** bölümüne yükleyin.

    ![Çoklu oturum açmayı yapılandırma](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Openatina test kullanıcısı oluşturma

Bu bölümde, Openatina 'da Britta Simon adlı bir Kullanıcı oluşturulur. Openatina, varsayılan olarak etkinleştirilen **tam zamanında Kullanıcı sağlamayı**destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Openatina 'da bir kullanıcı zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Openatina kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Openatina 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Openatina 'yi deneyin](https://aad.portal.azure.com/)