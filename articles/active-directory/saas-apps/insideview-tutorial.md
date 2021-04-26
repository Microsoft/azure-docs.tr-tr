---
title: 'Öğretici: InsideView ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ile dışarıdan görüntüleme arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460113"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Öğretici: InsideView ile tümleştirme Azure Active Directory

Bu öğreticide, Iç görünümü Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi kullanarak, dışarıdan görüntüleme erişimi olduğunu denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla dışarıdan görüntüleme (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini InsideView ile yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir InsideView aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* InsideView, IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-insideview-from-the-gallery"></a>Galeriden InsideView ekleme

InsideView 'ın Azure AD 'ye tümleştirmesini ayarlamak için Galeriden yönetilen SaaS uygulamaları listenize InsideView eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **InsideView** yazın. Arama sonuçlarında **InsideView** ' ı seçin ve ardından **Ekle**' yi seçin.

    ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısı kullanarak InsideView ile Azure AD çoklu oturum açmayı yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için bir Azure AD kullanıcısı ile InsideView içinde ilgili Kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açmayı, InsideView ile yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında **[Çoklu oturum açma görünümü yapılandırma](#configure-insideview-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir InsideView test kullanıcısı oluşturun](#create-an-insideview-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açma 'yı InsideView ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), Uygulama tümleştirmesini dışarıdan görüntüleme sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın.

    ![Temel SAML yapılandırması iletişim kutusu](common/idp-reply.png)

    **Yanıt URL 'si** kutusuna, bu modele bir URL girin:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Bu değer bir yer tutucudur. Asıl yanıt URL 'sini kullanmanız gerekir. Değeri almak için [InsideView destek ekibine](mailto:support@insideview.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** iletişim kutusunda gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)**' ın yanındaki **İndir** bağlantısını seçin ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **Iç görünümü ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-insideview-single-sign-on"></a>Dışarıdan görüntülemeyi çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcısı penceresinde, dışarıdan görüntüleme şirket sitenizde yönetici olarak oturum açın.

1. Pencerenin üst kısmında **yönetici**, **Tekoturum açma ayarları**' nı seçin ve **SAML ekleyin**.
   
   ![SAML çoklu oturum açma ayarları](./media/insideview-tutorial/ic794135.png "SAML çoklu oturum açma ayarları")

1. **Yenı SAML Ekle** bölümünde aşağıdaki adımları uygulayın.

    ![Yeni bir SAML bölümü ekleme](./media/insideview-tutorial/ic794136.png "Yeni bir SAML bölümü ekleme")

    1. **STS adı** kutusuna yapılandırmanız için bir ad girin.

    1. **SAMLP/WS-beslenir Istek noktası** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. Azure portal indirdiğiniz ham sertifikayı açın. Sertifikanın içeriğini panoya kopyalayın ve ardından içeriği **STS sertifikası** kutusuna yapıştırın.

    1. **CRM Kullanıcı kimliği eşleme** kutusuna girin **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. **CRM e-posta eşleme** kutusuna girin **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. **CRM adı eşleme** kutusuna yazın **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    1. **CRM soyadı eşleme** kutusuna girin **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .  

    1. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory** ' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin:

    ![Tüm kullanıcılar'ı seçin](common/users.png)

2. Pencerenin üst kısmındaki **Yeni Kullanıcı** ' yı seçin:

    ![Yeni Kullanıcı Seç](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. **Ad** kutusuna **Brittasıon** yazın.
  
    1. **Kullanıcı adı** kutusuna **BrittaSimon@ girin \<yourcompanydomain> . \<extension>** (Örneğin, BrittaSimon@contoso.com .)

    1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda değer ' i yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak dışarıdan görüntüleme erişimi vererek Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **InsideView**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **InsideView**' u seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-an-insideview-test-user"></a>Bir InsideView test kullanıcısı oluşturma

Azure AD kullanıcılarının dışarıdan oturum açmasını sağlamak için bunları InsideView 'a eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

InsideView 'da Kullanıcı veya kişi oluşturmak için, [InsideView destek ekibine](mailto:support@insideview.com)başvurun.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, InsideView tarafından sunulan herhangi bir kullanıcı hesabı oluşturma aracını veya API 'YI kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde dışarıdan görünüm kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız InsideView örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [Apps Portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)