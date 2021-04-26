---
title: 'Öğretici: Perception Birleşik Devletler Azure Active Directory tümleştirme (UltiPro olmayan) | Microsoft Docs'
description: Azure Active Directory ve Perception Birleşik Devletler (non-UltiPro) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515534"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Öğretici: Perception Birleşik Devletler Azure Active Directory tümleştirme (UltiPro olmayan)

Bu öğreticide, Perception Birleşik Devletler (non-UltiPro) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile Perception Birleşik Devletler Tümleştirme (non-UltiPro), aşağıdaki avantajları sağlar:

* Azure AD 'de, Perception Birleşik Devletler erişimi olan (UltiPro olmayan) denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Birleşik Devletler (non-UltiPro) (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Perception Birleşik Devletler (non-UltiPro) ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Perception Birleşik Devletler (UltiPro olmayan) çoklu oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Perception Birleşik Devletler (non-UltiPro) **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Galeriden Perception Birleşik Devletler (UltiPro olmayan) ekleme

Perception Birleşik Devletler (UltiPro olmayan) tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Perception Birleşik Devletler (UltiPro olmayan) eklemeniz gerekir.

**Galeriden Perception Birleşik Devletler (non-UltiPro) eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **perception Birleşik Devletler (UltiPro olmayan)** yazın, sonuç panelinden **Perception Birleşik Devletler (UltiPro olmayan)** öğesini seçin ve ardından uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde Perception Birleşik Devletler (UltiPro olmayan)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak perception Birleşik Devletler (UltiPro olmayan) ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Perception Birleşik Devletler (UltiPro olmayan) ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, Perception Birleşik Devletler (non-UltiPro) ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Perception Birleşik Devletler (UltiPro olmayan) çoklu oturum açmayı yapılandırın](#configure-perception-united-states-non-ultipro-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Perception Birleşik Devletler (UltiPro olmayan) ile bir Britta Simon 'ın bir karşılığı olan **[perception Birleşik Devletler (UltiPro olmayan) test kullanıcısı oluşturun](#create-perception-united-states-non-ultipro-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, Perception Birleşik Devletler (UltiPro) ile birlikte yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Perception Birleşik Devletler (UltiPro olmayan)** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Perception Birleşik Devletler (UltiPro olmayan) etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`https://perception.kanjoya.com/sp`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. **Perception Birleşik Devletler (non-UltiPro)** uygulaması, **Azure AD tanımlayıcı** değerini, URI kodlamalı olarak **ayarlanan Perception Birleşik Devletler (ultipro olmayan)** bölümünden alacağınız <entity_id> olarak gerektirir. URI kodlamalı değeri almak için aşağıdaki bağlantıyı kullanın: **http://www.url-encode-decode.com/** .

    d. URI kodlamalı değeri aldıktan sonra, aşağıda belirtildiği gibi **yanıt URL** 'siyle birleştirin.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Yukarıdaki değeri **yanıt URL** metin kutusuna yapıştırın.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Ayarlama algılama Birleşik Devletler (UltiPro olmayan)** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Perception Birleşik Devletler (UltiPro olmayan) tek Sign-On yapılandırma

1. Başka bir tarayıcı penceresinde, Perception Birleşik Devletler (UltiPro olmayan) Şirket sitenizde yönetici olarak oturum açın.

2. Ana araç çubuğunda **Hesap ayarları**' na tıklayın.

    ![Ana araç çubuğundan "hesap ayarları" nın seçili olduğunu gösteren ekran görüntüsü.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. **Hesap ayarları** sayfasında, aşağıdaki adımları uygulayın:

    ![Perception Birleşik Devletler (UltiPro olmayan) Kullanıcı](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. **Şirket adı** metin kutusuna **şirketin** adını yazın.
    
    b. **Hesap adı** metin kutusuna **hesabın** adını yazın.

    c. **Varsayılan Reply-To e-posta** metin kutusuna geçerli **e-postayı** yazın.

    d. **SAML 2,0** olarak **SSO kimlik sağlayıcısını** seçin.

4. **SSO yapılandırma** sayfasında, aşağıdaki adımları uygulayın:

    ![Perception Birleşik Devletler (UltiPro olmayan) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. **SAML NameID türünü** **e-posta** olarak seçin.

    b. **SSO yapılandırma adı** metin kutusuna **yapılandırmanızın** adını yazın.
    
    c. **Kimlik sağlayıcısı adı** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın. 

    d. **SAML etki alanı metin** kutusunda, gibi etki alanını girin @contoso.com .

    e. **Meta VERI XML** dosyasını karşıya yüklemek Için **yeniden yükle** 'ye tıklayın.

    f. **Güncelleştir**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın brittasimon@yourcompanydomain.extension . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Perception Birleşik Devletler (non-UltiPro) erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Perception Birleşik Devletler (UltiPro olmayan)** öğesini seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Perception Birleşik Devletler (non-UltiPro)** öğesini seçin.

    ![Uygulamalar listesindeki Perception Birleşik Devletler (UltiPro olmayan) bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Perception Birleşik Devletler (UltiPro olmayan) test kullanıcısı oluşturma

Bu bölümde, Perception Birleşik Devletler (UltiPro olmayan) bölümünde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları Perception Birleşik Devletler (UltiPro olmayan) platformda eklemek için [perception Birleşik Devletler (UltiPro olmayan) destek ekibi](https://www.ultimatesoftware.com/Contact/ContactUs) ile çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Perception Birleşik Devletler (UltiPro olmayan) kutucuğuna tıkladığınızda, SSO 'Yu ayarladığınız Perception Birleşik Devletler (non-UltiPro) otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)