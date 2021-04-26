---
title: 'Öğretici: Evernote ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Evernote arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: 86a314cd5255c06a70d0f9b28d06e3ac4156fdb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Öğretici: Evernote ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Evernote 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Evernote 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Evernote 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Evernote 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Evernote çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Evernote **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-evernote-from-the-gallery"></a>Galeriden Evernote ekleme

Evernote 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki Evernote 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Evernote** yazın.
1. Sonuçlar panelinden **Evernote** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Evernote için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Evernote ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Evernote içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Evernote ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Evernote SSO 'Yu yapılandırın](#configure-evernote-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Evernote 'ta B. Simon 'a karşılık gelen bir, **[Evernote test kullanıcısı oluşturun](#create-evernote-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Evernote** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Tanımlayıcı** metin kutusuna bir URL yazın:`https://www.evernote.com/saml2`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://www.evernote.com/Login.action`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **İmzalama** seçeneklerini değiştirmek Için, **Düzenle** düğmesine tıklayarak **SAML imzalama sertifikası** iletişim kutusunu açın.

    !["Düzenle" düğmesi seçiliyken "S A M L Imzalama sertifikası" iletişim kutusunu gösteren ekran görüntüsü.](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. **Imzalama seçeneği** için **SAML yanıtı ve onaylama seçeneğini imzala** seçeneğini belirleyin.

    b. **Kaydet**’e tıklayın

1. **Evernote ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Evernote 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Evernote**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-evernote-sso"></a>Evernote SSO 'yu yapılandırma

1. Evernote 'daki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **Kurulum Evernote** 'a tıklayın, sizi Evernote uygulamasına yönlendirir. Buradan, Evernote 'ta oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Evernote 'i el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Evernote şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **' Yönetici Konsolu** 'na gidin

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. **' Yönetici Konsolu**'Ndan **' Güvenlik '** e gidin ve **' çoklu oturum açma** 'yı seçin

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Aşağıdaki değerleri yapılandırın:

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **SSO 'Yu etkinleştir:** SSO varsayılan olarak etkindir (SSO gereksinimini kaldırmak için **Çoklu oturum açmayı devre dışı bırak** ' a tıklayın)

    b. Azure portal 'den **SAML http istek URL 'si** metin kutusuna kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. İndirilen sertifikayı bir not defteri içinde Azure AD 'den açın ve içeriği "başlangıç SERTIFIKASı" ve "BITIŞ SERTIFIKASı" gibi kopyalayın ve **X. 509.440 sertifikası** metin kutusuna yapıştırın. 

    d. **Değişiklikleri Kaydet** 'e tıklayın

### <a name="create-evernote-test-user"></a>Evernote test kullanıcısı oluştur

Azure AD kullanıcılarının, Evernote 'ta oturum açmasını sağlamak için, bu kullanıcıların Evernote 'a sağlanması gerekir.  
Evernote durumunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Evernote şirket sitenizde yönetici olarak oturum açın.

2. **' Yönetici Konsolu '** na tıklayın.

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. **' Yönetici Konsolu '** ndan **' Kullanıcı Ekle '** bölümüne gidin.

    !["Kullanıcı Ekle" seçiliyken "kullanıcılar" menüsünü gösteren ekran görüntüsü.](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Takım üyelerini** **e-posta** metin kutusuna ekleyin, Kullanıcı hesabının e-posta adresini yazın ve **davet et** ' e tıklayın.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Davet gönderildikten sonra, Azure Active Directory hesap sahibi daveti kabul etmek için bir e-posta alır.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Evernote kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Evernote 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Evernote 'ı Azure AD ile deneyin](https://aad.portal.azure.com/)