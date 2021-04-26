---
title: 'Öğretici: Predictıx sıralaması ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve Predictıx sıralaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
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
ms.openlocfilehash: f48f7cf9507afae31e3c36aef517aab7b7d77ccf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515381"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Öğretici: Predictıx sıralaması ile tümleştirme Azure Active Directory

Bu öğreticide, tahmine TIX sıralamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, tahmine TIX sıralamasına kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla tahmine Tix sıralaması (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini tahmine TIX sıralaması ile yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir tahmine TIX sıralama aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Predictix sıralaması SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-predictix-ordering-from-the-gallery"></a>Galeriden tahmine TIX sıralaması ekleme

Tahmine TIX sıralamasına yönelik Azure AD ile tümleştirmeyi ayarlamak için, Galeriden yönetilen SaaS uygulamaları listenize tahmine Tix sıralaması eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **Predictıx sıralaması** girin. Arama sonuçlarında **Predictıx sıralamasını** seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, Britta Simon adlı bir test kullanıcısı kullanarak tahmine TIX sıralaması ile yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ile tahmine TIX sıralaması içinde karşılık gelen kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açmayı tahmine Tix sıralaması ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında tahmine **[TIX sıralamasını çoklu oturum açmayı yapılandırın](#configure-predictix-ordering-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir tahmine TIX sıralama test kullanıcısı oluşturun](#create-a-predictix-ordering-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açmayı tahmine TIX sıralaması ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), tahmine **TIX sıralama** uygulaması tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları izleyin.

    ![Temel SAML yapılandırması iletişim kutusu](common/sp-identifier.png)

    1. **Oturum açma URL 'si** kutusuna, bu modele bir URL girin:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, bu modele bir URL girin:

        ```https
        https://<companyname-pricing>.dev.ordering.predictix.com
        https://<companyname-pricing>.ordering.predictix.com
        ```

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek oturum açma URL 'sini ve tanımlayıcıyı kullanmanız gerekir. Değerleri almak için [Predictix sıralama destek ekibine](https://www.predix.io/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** iletişim kutusunda gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **indirme** bağlantısını, gereksinimlerinize göre ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. Tahmine **TIX sıralamasını ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    2. **Azure AD tanımlayıcısı**.

    3. **Oturum kapatma URL 'si**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Tahmine TIX sıralamasını çoklu oturum açmayı yapılandırma

Tahmine Tix sıralama tarafında çoklu oturum açmayı yapılandırmak için, indirdiğiniz sertifikayı ve Azure portal kopyaladığınız URL 'Leri tahmine [Tix sıralama desteği ekibine](https://www.predix.io/support/)göndermeniz gerekir. Bu ekip, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlar.

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

Bu bölümde, Predictix sıralamasına erişimi vererek Azure AD çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **predictıx sıralaması**' nı seçin:

    ![Kurumsal uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Predictix sıralaması**' nı seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-predictix-ordering-test-user"></a>Tahmine dayalı sıralama testi kullanıcısı oluşturma

Ardından, Predictix sıralaması içinde Britta Simon adlı bir kullanıcı oluşturmanız gerekir. Kullanıcı eklemek için [Predictix sıralama destek](https://www.predix.io/support/) ekibiyle çalışın. Çoklu oturum açma kullanmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde Predictix sıralama kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Predictix sıralama örneğinde otomatik olarak oturum açmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)