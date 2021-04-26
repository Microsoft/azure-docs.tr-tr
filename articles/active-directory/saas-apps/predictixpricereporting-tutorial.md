---
title: 'Öğretici: Predictıx fiyat raporlama ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ile tahmine Tix fiyat raporlama arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 801197a171b5be2cfe46c249398381e1b25857ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515364"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Öğretici: Predictıx fiyat raporlama ile tümleştirme Azure Active Directory

Bu öğreticide, tahmine TIX fiyat raporlamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, tahmine TIX fiyat raporlamaya kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla tahmine TIX fiyat raporlama (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini tahmine TIX fiyat raporlama ile yapılandırmak için şunlar gerekir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme](https://azure.microsoft.com/pricing/free-trial/) aboneliği için kaydolabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir tahmine TIX fiyat raporlama aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Predictix fiyat raporlama SP tarafından başlatılan SSO 'yu destekler.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Galeriden tahmine TIX fiyat raporlaması ekleme

Tahmine TIX fiyat raporlama 'nın Azure AD 'ye tümleştirmesini ayarlamak için, Galeriden, yönetilen SaaS uygulamaları listenize tahmine Tix fiyat raporlama eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna tahmine **TIX fiyat raporlama** girin. Arama sonuçlarında **Predictix fiyat raporlama** ' yı seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, Britta Simon adlı bir test kullanıcısı kullanarak tahmine TIX fiyat raporlama ile yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ile tahmine TIX fiyat raporlama 'da ilgili Kullanıcı arasında bir ilişki kurmanız gerekir.

Tahmine TIX fiyat raporlama ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında tahmine **[TIX fiyat raporlama çoklu oturum açmayı yapılandırın](#configure-predictix-price-reporting-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir tahmine TIX fiyat raporlama test kullanıcısı oluşturun](#create-a-predictix-price-reporting-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açmayı tahmine Tix fiyat raporlama ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), tahmine **TIX fiyat raporlama** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları izleyin.

    ![Temel SAML yapılandırması iletişim kutusu](common/sp-identifier.png)

    1. **Oturum açma URL 'si** kutusuna, bu modele bir URL girin:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, bu modele bir URL girin:

        ```https
        https://<companyname-pricing>.predictix.com
        https://<companyname-pricing>.dev.predictix.com
        ```

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek oturum açma URL 'sini ve tanımlayıcıyı kullanmanız gerekir. Değerleri almak için [Predictix fiyat raporlama destek ekibine](https://www.infor.com/company/customer-center/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** iletişim kutusunda gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **indirme** bağlantısını, gereksinimlerinize göre ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. Tahmine **TIX fiyat raporlamasını ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Tahmine TIX fiyat raporlama çoklu oturum açmayı yapılandırma

Predictix fiyat raporlama tarafında çoklu oturum açmayı yapılandırmak için, indirdiğiniz sertifikayı ve Azure portal kopyaladığınız URL 'Leri tahmine [Tix fiyat raporlama desteği ekibine](https://www.infor.com/company/customer-center/)göndermeniz gerekir. Bu ekip, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlar.

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

Bu bölümde, Azure AD çoklu oturum açma özelliğini kullanarak tahmine TIX fiyat raporlamaya erişim izni vererek Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **predictix fiyat raporlama**' yı seçin.

    ![Kurumsal uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Predictıx fiyat raporlama**' yı seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-predictix-price-reporting-test-user"></a>Tahmine TIX fiyat raporlama test kullanıcısı oluşturma

Ardından, Predictix fiyat raporlaması ' nda Britta Simon adlı bir kullanıcı oluşturmanız gerekir. Kullanıcı eklemek için tahmine [TIX fiyat raporlama destek](https://www.infor.com/company/customer-center/) ekibiyle çalışın. Çoklu oturum açma kullanmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde Predictix fiyat raporlama kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Predictix fiyat raporlama örneğinde otomatik olarak oturum açmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)