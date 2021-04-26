---
title: 'Öğretici: 123FormBuilder SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve 123FormBuilder SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319177"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Öğretici: 123FormBuilder SSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, 123FormBuilder SSO 'Yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. 123FormBuilder SSO 'Yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, 123FormBuilder SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla 123FormBuilder SSO 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* 123FormBuilder SSO çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* 123FormBuilder SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* 123FormBuilder SSO **, tam zamanında** Kullanıcı sağlamayı destekler.
* 123FormBuilder SSO 'Yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Galeriden 123FormBuilder SSO 'SU ekleme

123FormBuilder SSO 'Yu Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize 123FormBuilder SSO eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **123Formbuilder SSO** yazın.
1. Sonuçlar panelinden **123Formbuilder SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>123FormBuilder SSO için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu 123FormBuilder SSO ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve 123FormBuilder SSO içinde ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'Yu 123FormBuilder SSO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[123FormBuilder SSO 'Yu yapılandırın](#configure-123formbuilder-sso)** .
    * User 'ın Azure AD gösterimine bağlı olan 123FormBuilder SSO 'SU üzerinde B. Simon 'a sahip olmak için **[123FORMBUILDER SSO test kullanıcısı oluşturun](#create-123formbuilder-sso-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **123Formbuilder SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değeri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek URL 'lerden ve tanımlayıcıdan güncelleştirmeniz gerekir.

1. **SAML ile çoklu oturum açma kurulumu** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **123FormBuilder SSO ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, 123FormBuilder SSO erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **123Formbuilder SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-123formbuilder-sso"></a>123FormBuilder SSO 'Yu yapılandırma

1. **123Formbuilder SSO** tarafında çoklu oturum açmayı yapılandırmak için adresine gidin [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) ve aşağıdaki adımları gerçekleştirin:

    ![SSO SAML kimlik sağlayıcısı yapılandırma ekranını gösteren ekran görüntüsü.](./media/123formbuilder-tutorial/submit.png) 

    a. **E-posta** metin kutusuna, gibi kullanıcının e-postasını yazın `B.Simon@Contoso.com` .

    b. **Karşıya yükle** ' ye tıklayın ve Azure Portal adresinden Indirdiğiniz Indirilen meta veri XML dosyasına gözatamazsınız.

    c. **Formu gönder**' e tıklayın.

2. **Microsoft Azure AD çoklu oturum açma-uygulama ayarlarını yapılandırma** aşağıdaki adımları uygulayın:

    ![Tek Sign-On yapılandırma](./media/123formbuilder-tutorial/url3.png)

    a. Uygulamayı **IDP tarafından başlatılan modda** yapılandırmak istiyorsanız, örneğiniz için **tanımlayıcı** değeri KOPYALAYıN ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırın.

    b. Uygulamayı **IDP tarafından başlatılan modda** yapılandırmak istiyorsanız, örneğiniz için **yanıt URL 'si** değerini KOPYALAYıN ve Azure Portal **temel SAML yapılandırması** bölümünde **yanıt URL** metin kutusuna yapıştırın.

    c. Uygulamayı **SP tarafından başlatılan modda** yapılandırmak istiyorsanız, örneğiniz Için **oturum açma URL 'si** değerini KOPYALAYıN ve Azure Portal **temel SAML yapılandırması** bölümündeki URL 'yi **oturum** Aç metin kutusuna yapıştırın.

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO test kullanıcısı oluştur

Bu bölümde, 123FormBuilder SSO 'da Britta Simon adlı bir Kullanıcı oluşturulur. 123FormBuilder SSO, tam zamanında Kullanıcı sağlamayı destekler, varsayılan olarak etkindir. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı 123FormBuilder SSO 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde 123FormBuilder SSO kutucuğuna tıkladığınızda, SSO 'Yu ayarladığınız 123FormBuilder SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile 123FormBuilder SSO 'Yu deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)