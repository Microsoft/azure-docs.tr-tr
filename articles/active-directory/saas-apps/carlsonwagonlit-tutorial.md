---
title: 'Öğretici: Carlson Wagonaydınlatmalı seyahat ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Carlson Wagonaydınlatmalı seyahat arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 86c2d9585abd546acb3efb22256d3f3b70bfb972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674404"
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Öğretici: Carlson Wagonaydınlatmalı seyahat ile Azure Active Directory tümleştirme

Bu öğreticide, Carlson Wagonaydınlatmalı Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Carlson Wagonaydınlatmalı yolculuğu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Hlson Wagonaydınlatmalı geziye erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Carlson Wagonaydınlatmalı yolculuğuna (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Carlson Wagonaydınlatmalı seyahat ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Carlson Wagonaydınlatmalı gezi çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Carlson Wagonaydınlatmalı gezi **IDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Galeriden Carlson Wagonaydınlatma ekleme

Carlson Wagonaydınlatmalı seyahatin Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Carlson Wagonaydınlatmalı yolculuğu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Carlson Wagonaydınlatma eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Carlson wagonaydınlatma** yazın, sonuç panelinden **Carlson wagonaydınlatma** ' yı seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde Carlson Wagonaydınlatma yolculuğu](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, Carlson Wagonaydınlatmalı seyahatle Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Carlson Wagonaydınlatmalı yolculuğu içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma işlemini Carlson Wagonaydınlatmalı yolculuğuyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Carlson Wagonaydınlatmalı yolculuğu yapılandırma tek oturum açma](#configure-carlson-wagonlit-travel-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Carlson wagonaydınlatma testi kullanıcısı oluşturun](#create-carlson-wagonlit-travel-test-user)** . Carlson Wagonaydınlatmalı gezon 'ın, kullanıcının Azure AD gösterimine bağlı yolculuğu için bir karşılığı.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Carlson Wagonaydınlatma ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Carlson Wagonaydınlatmalı seyahat** uygulaması tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Carlson Wagonaydınlatmalı seyahat etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-identifier.png)

    **Tanımlayıcı** metin kutusuna değeri yazın:`cwt-stage`

5. **SAML Ile tek Sign-On ayarla** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Ayarlama Carlson Wagonaydınlatma** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-carlson-wagonlit-travel-single-sign-on"></a>Carlson Wagonaydınlatmalı seyahat tek Sign-On yapılandırma

**Carlson Wagonaydınlatmalı seyahat** tarafında çoklu oturum açma 'yı yapılandırmak için, Indirilen **Federasyon meta verileri XML** 'sini ve Azure Portal ' den [Carlson wagonaydınlatmalı seyahat destek ekibine](https://www.mycwt.com/traveler-help/)doğru kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** yazın.

    b. **Kullanıcı adı** alanına **brittasıon \@ yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Carlson Wagonaydınlatmalı geziye erişim vererek Britta Simon 'un Azure çoklu oturum açma özelliğini kullanmasını sağlayabilirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Carlson wagonaydınlatma**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Carlson Wagonaydınlatma**' yı seçin.

    ![Uygulamalar listesindeki Carlson Wagonaydınlatma bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-carlson-wagonlit-travel-test-user"></a>Carlson Wagonaydınlatmalı seyahat testi Kullanıcı Oluştur

Bu bölümde, Carlson Wagonaydınlatmalı yolculuğu içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Carlson [Wagonaydınlatmalı seyahat destek ekibi](https://www.mycwt.com/traveler-help/) ile çalışarak, Carlson wagonaydınlatma platformunda kullanıcıları ekleyin. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Carlson wagonaydınlatma kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Carlson Wagonaydınlatmalı yolculuğa otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)