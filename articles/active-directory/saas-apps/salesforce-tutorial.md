---
title: 'Öğretici: Salesforce ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Salesforce arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63cc4b902c0bd0281228e23076be6e0a18461597
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241431"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Öğretici: Salesforce ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide Salesforce 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Salesforce 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Salesforce erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Salesforce 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Salesforce çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Salesforce, **SP** tarafından başlatılan SSO 'yu destekler

* Salesforce **, tam zamanında** Kullanıcı sağlamayı destekler

* Salesforce [ **Otomatik** Kullanıcı sağlamayı destekler](salesforce-provisioning-tutorial.md)

* Salesforce mobil uygulaması, artık SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

## <a name="adding-salesforce-from-the-gallery"></a>Galeriden Salesforce ekleme

Salesforce 'un Azure AD ile tümleştirilmesini yapılandırmak için, galerideki Salesforce 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Salesforce** yazın.
1. Sonuçlar panelinden **Salesforce** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Salesforce için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Salesforce ile yapılandırın ve test edin. SSO 'nun çalışması için, Salesforce 'daki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Salesforce ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. **[SALESFORCE SSO 'Yu yapılandırma](#configure-salesforce-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Salesforce 'ta B. Simon 'un bir karşılığı olan **[Salesforce test kullanıcısı oluşturun](#create-salesforce-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Salesforce ile yapılandırmak için aşağıdaki adımları uygulayın:

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **Salesforce** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak değeri yazın:

    Kurumsal Hesap: `https://<subdomain>.my.salesforce.com`

    Geliştirici hesabı: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak değeri yazın:

    Kurumsal Hesap: `https://<subdomain>.my.salesforce.com`

    Geliştirici hesabı: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Salesforce istemci desteği ekibine](https://help.salesforce.com/support) başvurun.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Salesforce ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'u tıklatın.
   
    > [!NOTE]
    > Salesforce Kullanıcı öznitelikleri, SAML doğrulaması için büyük/küçük harfe duyarlıdır.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Salesforce 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Salesforce**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-salesforce-sso"></a>Salesforce SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve Salesforce yönetici hesabınızda oturum açın.

2. Sayfanın sağ üst köşesindeki **Ayarlar simgesi** altında **Kurulum** ' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/configure1.png)

3. Gezinti bölmesinde **Ayarlar** ' a gidin, ilgili bölümü genişletmek için **kimlik** ' e tıklayın. Ardından **Çoklu oturum açma ayarları**' na tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-admin-sso.png)

4. **Çoklu oturum açma ayarları** sayfasında **Düzenle** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce hesabınız için çoklu oturum açma ayarlarını etkinleştiremeyebilirsiniz [Salesforce istemci desteği ekibine](https://help.salesforce.com/support)başvurmanız gerekebilir.

5. **SAML etkin**' i seçin ve ardından **Kaydet**' e tıklayın.

      ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-enable-saml.png)

6. SAML çoklu oturum açma ayarlarınızı yapılandırmak için **meta veri dosyasından yeni**' ye tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Azure portal indirdiğiniz meta veri XML dosyasını karşıya yüklemek için **Dosya Seç** ' e tıklayın ve **Oluştur**' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/xmlchoose.png)

8. **SAML çoklu oturum açma ayarları** sayfasında, alanlar otomatik olarak doldurulur ve Kaydet ' e tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/salesforcexml.png)

9. Salesforce ' daki sol gezinti bölmesinde **Şirket ayarları** ' na tıklayarak ilgili bölümü genişletin ve **etki alanım**' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-my-domain.png)

10. **Kimlik doğrulama yapılandırması** bölümüne gidin ve **Düzenle** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. **Kimlik doğrulama yapılandırması** bölümünde, SAML SSO yapılandırmanızın **kimlik doğrulama hizmeti** olarak **Azuresso** ' ı denetleyip **Kaydet**' e tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Birden fazla kimlik doğrulama hizmeti seçilirse, kullanıcılardan Salesforce ortamınızda çoklu oturum açmayı başlatırken, hangi kimlik doğrulama hizmetini kullanarak oturum açmasını istediğinizi seçmeleri istenir. Bunun gerçekleşmesini istemiyorsanız, **diğer tüm kimlik doğrulama hizmetlerini işaretlenmemiş olarak bırakmalısınız**.

### <a name="create-salesforce-test-user"></a>Salesforce test kullanıcısı oluştur

Bu bölümde, Salesforce 'ta B. Simon adlı bir Kullanıcı oluşturulur. Salesforce, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten Salesforce 'ta yoksa, Salesforce 'a erişmeye çalıştığınızda yeni bir tane oluşturulur. Salesforce otomatik Kullanıcı sağlamayı da destekler, [burada](salesforce-provisioning-tutorial.md) otomatik Kullanıcı sağlamayı yapılandırma hakkında daha fazla ayrıntı bulabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Salesforce kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Salesforce 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce için test SSO 'SU (mobil)

1. Salesforce mobil uygulamasını açın. Oturum aç sayfasında, **özel etki alanı kullan**' a tıklayın.

    ![Salesforce mobil uygulaması](media/salesforce-tutorial/mobile-app1.png)

1. **Özel etki alanı** metin kutusuna kayıtlı özel etki alanı adınızı girip **devam**' a tıklayın.

    ![Salesforce mobil uygulaması](media/salesforce-tutorial/mobile-app2.png)

1. Salesforce uygulamasında oturum açmak için Azure AD kimlik bilgilerinizi girin ve **İleri**' ye tıklayın.

    ![Salesforce mobil uygulaması](media/salesforce-tutorial/mobile-app3.png)

1. Aşağıda gösterildiği gibi **erişime Izin ver** sayfasında, Salesforce uygulamasına erişim izni vermek Için **izin ver** ' e tıklayın.

    ![Salesforce mobil uygulaması](media/salesforce-tutorial/mobile-app4.png)

1. Son olarak, başarıyla oturum açtıktan sonra uygulama giriş sayfası görüntülenir.

    ![Salesforce mobil uygulaması @ no__t-1 ![Salesforce mobil uygulama @ no__t-3

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Kullanıcı sağlamayı yapılandırma](salesforce-provisioning-tutorial.md)

- [Azure AD ile Salesforce 'ı deneyin](https://aad.portal.azure.com)
