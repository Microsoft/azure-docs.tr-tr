---
title: 'Öğretici: Signagelive ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Signagelive arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160932"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Öğretici: Signagelive ile tümleştirme Azure Active Directory

Bu öğreticide, Signagelive 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Signagelive Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Signagelive 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Signagelive 'de (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Signagelive ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* Signagelive çoklu oturum açma özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Signagelive, SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-signagelive-from-the-gallery"></a>Galeriden Signagelive ekleme

Signagelive tümleştirmesini Azure AD 'ye göre yapılandırmak için, önce galerideki Signagelive öğesini yönetilen SaaS uygulamaları listenize ekleyin.

Galeriden Signagelive eklemek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com)sol bölmedeki **Azure Active Directory** simgesini seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve ardından **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Signagelive**girin. 

     ![Sonuç listesinde Signagelive](common/search-new-app.png)

5. Sonuçlar bölmesinde **Signagelive** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesini seçin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Signagelive ile yapılandırıp test edersiniz.
Çoklu oturum açma için, Signagelive içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD çoklu oturum açmayı Signagelive ile yapılandırmak ve test etmek için önce aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on) .
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [Signagelive çoklu oturum açmayı yapılandırın](#configure-signagelive-single-sign-on) .
3. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
5. Kullanıcının Azure AD gösterimine bağlı olan Signagelive 'de Britta Simon 'a sahip olacak [bir Signagelive test kullanıcısı oluşturun](#create-a-signagelive-test-user) .
6. Yapılandırmanın çalıştığını doğrulamak için [Çoklu oturum açmayı test](#test-single-sign-on) edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Signagelive ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Signagelive** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML** ' yi seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** ' yi seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın:

    ![Signagelive etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** kutusuna şu kalıbı kullanan bir URL girin: `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Signagelive istemci destek ekibine](mailto:support@signagelive.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)** ' i, gereksiniminize göre verilen seçenekten indirmek için **İndir** ' i seçin. Ardından bu dosyayı bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **Signagelive ayarla** bölümünde, IHTIYACıNıZ olan URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-signagelive-single-sign-on"></a>Signagelive çoklu oturum açmayı yapılandırma

Signagelive tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (ham)** ve Azure Portal URL 'leri [Signagelive destek ekibine](mailto:support@signagelive.com)gönderin. SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlandığından emin olurlar.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**yazın.
  
    b. **Kullanıcı adı** alanına "brittasimon@yourcompanydomain.extension" yazın. Örneğin, bu durumda "BrittaSimon@contoso.com" girebilirsiniz.

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri unutmayın.

    d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Signagelive 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Signagelive**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Signagelive**' yi seçin.

    ![Uygulamalar listesindeki Signagelive bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesini seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **Britta Simon**' u seçin. Ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama 'da bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Sonra, ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

### <a name="create-a-signagelive-test-user"></a>Signagelive test kullanıcısı oluşturma

Bu bölümde, Signagelive içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları Signagelive platformuna eklemek için [Signagelive destek](mailto:support@signagelive.com) ekibiyle çalışın. Çoklu oturum açma kullanmadan önce kullanıcıları oluşturmanız ve etkinleştirmeniz gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı Uygulamaps portalını kullanarak test edersiniz.

Uygulamaps portalındaki **Signagelive** kutucuğunu seçtiğinizde, otomatik olarak oturum açmış olmanız gerekir. Uygulamaps portalı hakkında daha fazla bilgi için, bkz. [Uygulamaps portalı nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

