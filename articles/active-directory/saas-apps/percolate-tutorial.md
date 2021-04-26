---
title: 'Öğretici: Percogeç ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve Percogeç arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522283"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Öğretici: Percogeç ile tümleştirme Azure Active Directory

Bu öğreticide, Percogeç Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, Percogeç 'e kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Percogeç (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Percogeç olarak yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir Percogeç aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Percogeç, SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-percolate-from-the-gallery"></a>Galeriden Percogeç ekleme

Percogeç 'in Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Percogeç eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **Percogeç**' i girin. Arama sonuçlarında **Percogeç** ' i seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısını kullanarak Azure AD çoklu oturum açmayı, Percogeç ile yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ile karşılık gelen kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açmayı, Percogeç ile yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında **[Percogeç çoklu oturum açmayı yapılandırın](#configure-percolate-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir Percogeç test kullanıcısı oluşturun](#create-a-percolate-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açmayı Percogeç olarak yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **percogeç** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, uygulamayı IDP tarafından başlatılan modda yapılandırmak için herhangi bir işlem yapmanız gerekmez. Uygulama zaten Azure ile tümleştirildi.

    ![Percogeç etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/preintegrated.png)

5. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla** ' yı seçin ve **oturum açma URL 'si** kutusuna şunu girin **https://percolate.com/app/login** :

   !["Ek U r ls 'yi ayarla" seçeneğinin seçili olduğunu gösteren ekran görüntüsü](common/metadata-upload-additional-signon.png)
6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak için **Kopyala** simgesini seçin. Bu URL 'YI kaydedin.

    ![Uygulama Federasyon meta veri URL 'sini Kopyala](common/copy-metadataurl.png)

7. **En geç ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-percolate-single-sign-on"></a>Percogeç çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcısı penceresinde yönetici olarak Percogeç ' e oturum açın.

2. Giriş sayfasının sol tarafında **Ayarlar**' ı seçin:
    
    ![Ayarlar'ı seçin](./media/percolate-tutorial/configure01.png)

3. Sol bölmede, **kuruluş** altında **SSO** ' yı seçin:

    ![Kuruluş altında SSO seçin](./media/percolate-tutorial/configure02.png)

    1. **Oturum açma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. **VARLıK kimliği** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    1. Not defteri 'nde, Azure portal indirdiğiniz Base-64 kodlu sertifikayı açın. İçeriğini kopyalayıp **x509 sertifikaları** kutusuna yapıştırın.

    1. **E-posta özniteliği** kutusuna **emaadresi** girin.

    1. **Kimlik sağlayıcısı meta veri URL 'si** kutusu isteğe bağlı bir alandır. Azure portal bir **uygulama Federasyon meta veri URL 'si** kopyaladıysanız, bu kutuya yapıştırabilirsiniz.

    1. **AuthNRequests imzalı olmalıdır mi?** listesinde **Hayır**' ı seçin.

    1. **SSO otomatik sağlamayı etkinleştir** listesinde **Hayır**' ı seçin.

    1. **Kaydet**’i seçin.

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

Bu bölümde, Percogeç 'e erişimi vererek Azure AD çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **percogeç**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Percogeç**' i seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Sol bölmeden seçili "kullanıcılar ve gruplar" i gösteren ekran görüntüsü.](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ve gruplar'ı seçin](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-percolate-test-user"></a>Bir Percogeç test kullanıcısı oluşturma

Azure AD kullanıcılarının, Percogeç oturum açmasını sağlamak için bunları Percogeç 'e eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:

1. Yönetici olarak Percogeç 'de oturum açın.

2. Sol bölmede, **kuruluş** altındaki **Kullanıcılar** ' ı seçin. **Yeni kullanıcıları** seçin:

    ![Yeni Kullanıcı Seç](./media/percolate-tutorial/configure03.png)

3. **Kullanıcı oluştur** sayfasında, aşağıdaki adımları uygulayın.

    ![Kullanıcı Oluştur sayfası](./media/percolate-tutorial/configure04.png)

    1. **E-posta** kutusuna kullanıcının e-posta adresini girin. Örneğin, brittasimon@contoso.com.

    1. **Tam ad** kutusuna kullanıcının adını girin. Örneğin, **Brittasıon**.

    1. **Kullanıcı oluştur**' u seçin.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde Percogeç kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Percogeç örneğinde otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)