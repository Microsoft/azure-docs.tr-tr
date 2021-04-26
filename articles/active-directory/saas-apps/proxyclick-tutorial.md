---
title: 'Öğretici: Proxyclick ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve Proxyclick arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522249"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Öğretici: Proxyclick ile tümleştirme Azure Active Directory

Bu öğreticide, Proxyclick 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, Proxyclick 'e kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Proxytıklamalarına (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Proxyclick ile yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme](https://azure.microsoft.com/pricing/free-trial/)için kaydolabilirsiniz.
* Çoklu oturum açma özelliği etkinleştirilmiş bir Proxyclick aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Proxyclick SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-proxyclick-from-the-gallery"></a>Galeriden Proxyclick ekleyin

Proxyclick 'in Azure AD 'ye tümleştirmesini ayarlamak için galerideki yönetim SaaS uygulamaları listenize Proxyclick eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **Proxyclick** yazın. Arama sonuçlarında **Proxyclick ' i** seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, Britta Simon adlı bir test kullanıcısı kullanarak Proxyclick ile yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, Proxyclick içindeki bir Azure AD kullanıcısı ile buna karşılık gelen kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açma 'yı Proxyclick ile yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında **[Proxyclick çoklu oturum açma 'Yı yapılandırın](#configure-proxyclick-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir Proxyclick test kullanıcısı oluşturun](#create-a-proxyclick-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açmayı Proxyclick ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), proxyclick uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, uygulamayı IDP tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın.

    ![Temel SAML yapılandırması iletişim kutusu](common/idp-intiated.png)

    1. **Tanımlayıcı** kutusunda, bu modele bir URL girin:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. **Yanıt URL 'si** kutusuna, bu modele bir URL girin:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla**' yı seçin. **Oturum açma URL 'si** kutusuna, bu modele bir URL girin:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek tanımlayıcıyı, yanıt URL 'sini ve oturum açma URL 'sini kullanmanız gerekir. Bu değerleri almak için adımlar Bu öğreticinin ilerleyen kısımlarında açıklanmıştır.

6. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **indirme** bağlantısını, gereksinimlerinize göre ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Proxyclick 'ı ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick çoklu oturum açma yapılandırma

1. Yeni bir Web tarayıcısı penceresinde, Proxyclick şirket sitesinde yönetici olarak oturum açın.

2. **Hesap & ayarlarını** seçin:

    ![Hesap & ayarlarını seçin](./media/proxyclick-tutorial/configure1.png)

3. **Tümleştirme** bölümüne gidin ve **SAML**' yi seçin:

    ![SAML seçin](./media/proxyclick-tutorial/configure2.png)

4. **SAML** bölümünde aşağıdaki adımları uygulayın.

    ![SAML bölümü](./media/proxyclick-tutorial/configure3.png)

    1. **SAML tüketicisi URL 'si** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** iletişim kutusundaki **yanıt URL 'si** kutusuna yapıştırın.

    1. **SAML SSO yeniden yönlendirme URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** Iletişim kutusundaki **URL 'yi aç** ve **tanımlayıcı** kutularına yapıştırın.

    1. **SAML Istek yöntemi** listesinde **http yeniden yönlendirme**' yi seçin.

    1. **Veren** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    1. **SAML 2,0 ENDPOINT URL** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. Not defteri 'nde, Azure portal indirdiğiniz sertifika dosyasını açın. Bu dosyanın içeriğini **sertifika** kutusuna yapıştırın.

    1. **Değişiklikleri Kaydet**' i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory** ' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin:

    ![Tüm kullanıcılar'ı seçin](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin:

    ![Yeni Kullanıcı Seç](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. **Ad** kutusuna **Brittasıon** yazın.
  
    1. **Kullanıcı adı** kutusuna **BrittaSimon@ girin \<yourcompanydomain> . \<extension>** (Örneğin, BrittaSimon@contoso.com .)

    1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda değer ' i yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Proxyclick 'e erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **proxytıklaı '** nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Proxyclick**' i seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-proxyclick-test-user"></a>Proxyclick test kullanıcısı oluşturma

Azure AD kullanıcılarının Proxytıklamasını oturum açmasını sağlamak için bunları Proxyclick 'e eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:

1. Yönetici olarak Proxyclick şirket sitesinde oturum açın.

1. Pencerenin üst kısmındaki **Iş arkadaşları** ' nı seçin:

    ![Iş arkadaşları seçin](./media/proxyclick-tutorial/user1.png)

1. **Iş arkadaşı ekle**' yi seçin:

    ![Iş arkadaşı ekle 'yi seçin](./media/proxyclick-tutorial/user2.png)

1. **İş arkadaşı ekle** bölümünde aşağıdaki adımları uygulayın.

    ![İş arkadaşı bölümü ekleme](./media/proxyclick-tutorial/user3.png)

    1. **E-posta** kutusuna kullanıcının e-posta adresini girin. Bu durumda, **brittasıon \@ contoso.com**.

    1. **Ilk ad** kutusuna kullanıcının adını girin. Bu durumda, **Britta**.

    1. **Soyadı** kutusuna kullanıcının soyadını girin. Bu durumda, **Simon**.

    1. **Kullanıcı Ekle**' yi seçin.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde Proxyclick kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Proxyclick örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [Apps Portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)