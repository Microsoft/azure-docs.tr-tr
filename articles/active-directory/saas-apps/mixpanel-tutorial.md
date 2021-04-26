---
title: 'Öğretici: Mixpanel ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Mixpanel arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: dfd262c1dc7aa2e6cfa6ae8835210086dd45e4f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521248"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Öğretici: Mixpanel ile tümleştirme Azure Active Directory

Bu öğreticide, Mixpanel 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Mixpanel 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Mixpanel 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Mixpanel (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Mixpanel ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Mixpanel çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Mixpanel, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-mixpanel-from-the-gallery"></a>Galeriden Mixpanel ekleme

Mixpanel 'in Azure AD ile tümleştirilmesini yapılandırmak için galerisinden Mixpanel 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Mixpanel eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Mixpanel** yazın, sonuç panelinden **Mixpanel** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde Mixpanel](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre Mixpanel ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Mixpanel 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Mixpanel ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Mixpanel çoklu oturum açmayı yapılandırma](#configure-mixpanel-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Mixpanel **[test kullanıcısı oluşturun](#create-mixpanel-test-user)** -Mixpanel 'de kullanıcının Azure AD gösterimine bağlanmış bir Britta Simon 'un karşılığı.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Mixpanel ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Mixpanel** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Mixpanel etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://mixpanel.com/login/`

    > [!NOTE]
    > Lütfen [https://mixpanel.com/register/](https://mixpanel.com/register/) oturum açma kimlik bilgilerinizi ayarlamak için kaydolun ve KIRACıNıZıN SSO ayarlarını etkinleştirmek Için [Mixpanel destek ekibine](mailto:support@mixpanel.com) başvurun. Ayrıca, Mixpanel destek takımınızdan gerekliyse oturum açma URL 'SI değerini de alabilirsiniz. 

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Mixpanel ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-mixpanel-single-sign-on"></a>Mixpanel tek Sign-On yapılandırma

1. Farklı bir tarayıcı penceresinde, Mixpanel uygulamanızda yönetici olarak oturum açın.

2. Sayfanın alt kısmında, sol köşedeki küçük **dişli** simgesine tıklayın. 
   
    ![Mixpanel tek Sign-On](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. **Erişim güvenliği** sekmesine tıklayın ve ardından **Ayarları Değiştir**' e tıklayın.
   
    ![Ekran görüntüsü, ayarları değiştirebileceğiniz erişim güvenliği sekmesini gösterir.](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. **Sertifikanızı değiştirin** iletişim sayfasında, indirilen sertifikanızı karşıya yüklemek Için **Dosya Seç** ' e tıklayın ve ardından **İleri**' ye tıklayın.
   
    ![Ekran görüntüsü, sertifika dosyası seçebileceğiniz sertifikayı değiştirme iletişim kutusunu gösterir.](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Kimlik **doğrulama URL 'Sini değiştirin** iletişim kutusundaki kimlik doğrulama URL 'si metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın ve ardından **İleri**' ye tıklayın.
   
    ![Ekran görüntüsü, oturum açma U r L 'nizi kopyalayabileceğiniz kimlik doğrulama U R L bölmesini gösterir.](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. **Bitti**’ye tıklayın.

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

Bu bölümde, Mixpanel 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon ' u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Mixpanel**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Mixpanel**' i seçin.

    ![Uygulamalar listesindeki Mixpanel bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-mixpanel-test-user"></a>Mixpanel test kullanıcısı oluşturma

Bu bölümün amacı, Mixpanel 'de Britta Simon adlı bir Kullanıcı oluşturmaktır. 

1. Mixpanel şirket sitenizde yönetici olarak oturum açın.

2. Sayfanın alt kısmındaki sol köşedeki az dişli düğmesine tıklayarak **Ayarlar** penceresini açın.

3. **Takım** sekmesine tıklayın.

4. **Takım üyesi** metin kutusunda, Azure 'Da Britta e-posta adresini yazın.
   
    ![Ekran görüntüsü, davet etmek için bir adres eklediğiniz takım sekmesini gösterir.](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. **Davet et**' e tıklayın. 

> [!Note]
> Kullanıcı, profili ayarlamak için bir e-posta alır.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Mixpanel kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Mixpanel ' de otomatik olarak oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)