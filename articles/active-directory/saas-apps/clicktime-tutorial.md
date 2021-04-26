---
title: 'Öğretici: tıklama saati ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile tıklama saati arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 3337d66934c1fe317296cffaa9a663e212cce12d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673190"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Öğretici: tıklama saati ile tümleştirme Azure Active Directory

Bu öğreticide, tıklama saatini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Tıklama saatini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Etkileşimli saate erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla etkileşimli saat (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini tıklama saati ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Tıklama zamanı çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Tıklama saati **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-clicktime-from-the-gallery"></a>Galeriden tıklama saati ekleme

Tıklama saati 'nin Azure AD 'ye tümleştirilmesini yapılandırmak için galerideki tıklama saati ' ni yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden tıklama saati eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **tıklama saati** yazın, sonuç panelinden **tıklama saati** ' ni seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde tıklama saati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre tıklama saati ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve tıklama sırasında ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı tıklama saati ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Tıklama zamanı çoklu oturum açmayı yapılandırma](#configure-clicktime-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Tıklama **[zamanı test kullanıcısı oluşturma](#create-clicktime-test-user)** -kullanıcının Azure AD gösterimine bağlı olan tıklama sırasında Britta Simon 'ın bir karşılığı olacak şekilde.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı tıklama saati ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **tıklama zamanı** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Tıklama saati etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`https://app.clicktime.com/sp/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    ```http
    https://app.clicktime.com/Login/
    https://app.clicktime.com/App/Login/Consume.aspx
    ```

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Tıklama saatini ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-clicktime-single-sign-on"></a>Tıklama saati tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak tıklama saati şirket sitenizde oturum açın.

1. Üstteki araç çubuğunda **Tercihler**' e ve ardından **güvenlik ayarları**' na tıklayın.

1. **Tek Sign-On tercihleri** yapılandırma bölümünde aşağıdaki adımları uygulayın:

    ![Güvenlik ayarları](./media/clicktime-tutorial/tic777280.png "Güvenlik Ayarları")

    a.  **Azure AD** ile tek Sign-On (SSO) kullanarak oturum açmaya **izin ver** ' i seçin.

    b. **Kimlik sağlayıcısı uç noktası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.

    c.  **Not defteri**'nde Azure Portal indirilen **Base-64 kodlu sertifikayı** açın, içeriği kopyalayın ve **X. 509.952 Certificate** metin kutusuna yapıştırın.

    d.  **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.

    b. **Kullanıcı adı** alanına **\@ bricompansıon yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, tıklama saatine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **tıklama zamanı**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **tıklama zamanı**' nı seçin.

    ![Uygulamalar listesindeki tıklama zamanı bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-clicktime-test-user"></a>Tıklama zamanı test kullanıcısı oluştur

Azure AD kullanıcılarının tıklama zamanı 'nda oturum açmasını etkinleştirmek için, bu kullanıcıların tıklama saatine sağlanması gerekir.  
Tıklama zamanı durumunda, sağlama el ile gerçekleştirilen bir görevdir.

> [!NOTE]
> Azure AD Kullanıcı hesaplarını sağlamak için tıklama saati tarafından sunulan başka bir tıklama saati Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Tıklama saati** kiracınızda oturum açın.

1. Üstteki araç çubuğunda **Şirket**' e ve ardından **insanlar**' a tıklayın.

    ![Ekran görüntüsü, şirket ve kişiler seçiliyken tıklama saati kiracısını gösterir.](./media/clicktime-tutorial/tic777282.png "People")

1. **Kişi ekle**' ye tıklayın.

    ![Kişi ekle](./media/clicktime-tutorial/tic777283.png "Kişi ekle")

1. Yeni kişi bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımdaki bilgileri ekleyebileceğiniz kişi ekle bölümünü gösterir.](./media/clicktime-tutorial/tic777284.png "People")

    a.  **Tam ad** metin kutusuna, **Britta Simon** gibi kullanıcının tam adını yazın. 

    b.  **E-posta adresi** metin kutusuna, **brittasıon \@ contoso.com** gibi kullanıcının e-postasını yazın.

    > [!NOTE]
    > İsterseniz, yeni kişi nesnesinin ek özelliklerini ayarlayabilirsiniz.

    c.  **Kaydet**’e tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde tıklama zamanı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız tıklama saatine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)