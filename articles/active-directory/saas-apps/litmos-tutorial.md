---
title: 'Öğretici: Litmos ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Litmos arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: cfdcef2b9adf4e7ce500a9a89a45678a60afffc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Öğretici: Litmos ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Litmos 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Litmos 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Litmos 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Litmos otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Litmos çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Litmos, **IDP** tarafından başlatılan SSO 'yu destekler
* Litmos **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-litmos-from-the-gallery"></a>Galeriden Litmos ekleme

Litmos tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Litmos yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Litmos** yazın.
1. Sonuçlar panelinden **Litmos** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Litmos için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Litmos ile yapılandırın ve test edin. SSO 'nun çalışması için, Litmos içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Litmos ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[LITMOS SSO 'Yu yapılandırın](#configure-litmos-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Litmos 'de B. Simon 'ya karşılık gelen bir **[Litmos test kullanıcısı oluşturun](#create-litmos-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Litmos** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.litmos.com/account/Login`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, öğreticide daha sonra açıklanan gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin veya bu değerleri almak için [Litmos istemci destek ekibine](https://www.litmos.com/contact-us) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Litmos ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Litmos 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Litmos**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-litmos-sso"></a>Litmos SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, Litmos şirket sitenizde yönetici olarak oturum açın.

2. Sol taraftaki Gezinti çubuğunda **hesaplar**' a tıklayın.

    ![Uygulama tarafındaki hesaplar bölümü][22]

3. **Tümleştirmeler** sekmesine tıklayın.

    ![Tümleştirme sekmesi][23]

4. **Tümleştirmeler** sekmesinde **3. taraf tümleştirmelere** gidin ve **SAML 2,0** sekmesi ' ne tıklayın.

    ![SAML 2,0 bölümü][24]

5. **Litmos IÇIN SAML uç noktası** altındaki değeri kopyalayın: Azure Portal ' deki **Litmos etki alanı ve URL 'Ler** bölümünde bulunan **yanıt URL 'si** metin kutusuna yapıştırın.

    ![SAML uç noktası][26]

6. **Litmos** uygulamanızda aşağıdaki adımları gerçekleştirin:

    ![Litmos uygulaması][25]

    a. **SAML etkinleştir**' e tıklayın.

    b. Base-64 kodlu sertifikanızı Not defteri 'nde açın, bu içeriği panonuza kopyalayın ve **SAML X. 509.440 sertifika** metin kutusuna yapıştırın.

    c. **Değişiklikleri Kaydet**’e tıklayın.

### <a name="create-litmos-test-user"></a>Litmos test kullanıcısı oluştur

Bu bölümün amacı, Litmos ' de Britta Simon adlı bir Kullanıcı oluşturmaktır. Litmos uygulaması tam zamanında sağlamayı destekler. Bu, erişim paneli kullanılarak uygulamaya erişim denemesi sırasında gerektiğinde bir kullanıcı hesabının otomatik olarak oluşturulduğu anlamına gelir.

**Litmos ' de Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. Farklı bir tarayıcı penceresinde, Litmos şirket sitenizde yönetici olarak oturum açın.

2. Sol taraftaki Gezinti çubuğunda **hesaplar**' a tıklayın.

    ![Uygulama tarafındaki hesaplar bölümü][22]

3. **Tümleştirmeler** sekmesine tıklayın.

    ![Tümleştirmeler sekmesi][23]

4. **Tümleştirmeler** sekmesinde **3. taraf tümleştirmelere** gidin ve **SAML 2,0** sekmesi ' ne tıklayın.

    ![SAML 2.0][24]

5. **AutoGenerate kullanıcıları** seçin
  
    ![Kullanıcıları otomatik üret][27]

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Litmos kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Litmos için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Litmos deneyin](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png