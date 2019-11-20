---
title: 'Öğretici: Coralogix ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Coralogix arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158460"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Öğretici: Coralogix ile tümleştirme Azure Active Directory

Bu öğreticide, Coralogix 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Coralogix 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Coralogix 'e erişimi olan ' i denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Coralogix (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Coralogix ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğeler gereklidir:

- Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
- Bir Coralogix çoklu oturum açma etkin aboneliği. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Coralogix, SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-coralogix-from-the-gallery"></a>Galeriden Coralogix ekleme

Coralogix 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için, önce Galeriden Coralogix 'i yönetilen SaaS uygulamaları listenize ekleyin.

Galeriden Coralogix eklemek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com)sol bölmedeki **Azure Active Directory** simgesini seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Coralogix**yazın. Sonuçlar bölmesinden **Coralogix** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesini seçin.

     ![Sonuçlar listesinde coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısına göre Coralogix ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, Coralogix 'te bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Coralogix ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için, önce aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on) .
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [Coralogix çoklu oturum açmayı yapılandırın](#configure-coralogix-single-sign-on) .
3. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
5. Coralogix 'te kullanıcının Azure AD gösterimine bağlı olan Britta Simon 'ın bir karşılığı olacak şekilde [coralogix test kullanıcısı oluşturun](#create-a-coralogix-test-user) .
6. Yapılandırmanın çalıştığını doğrulamak için [Çoklu oturum açmayı test](#test-single-sign-on) edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Coralogix ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Coralogix** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML** ' yi seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Coralogix etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** kutusunda, aşağıdaki düzene sahıp bir URL girin: `https://<SUBDOMAIN>.coralogix.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL girin, örneğin:
    
    `https://api.coralogix.com/saml/metadata.xml`

    or

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Coralogix istemci desteği ekibine](mailto:info@coralogix.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümündeki desenlere de başvurabilirsiniz.

5. Coralogix uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesini seçin.

    ![image](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, **düzenleme** simgesini kullanarak talepleri düzenleyin. Ayrıca, önceki görüntüde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** öğesini kullanarak talepler ekleyebilirsiniz. Ardından aşağıdaki adımları uygulayın:
    
    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesini** seçin.

    ![resim](./media/coralogix-tutorial/tutorial_usermail.png) ![görüntü](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. **Ad tanımlayıcı biçimi Seç** listesinde, **e-posta adresi**' ni seçin.

    c. **Kaynak özniteliği** listesinden **User. Mail**öğesini seçin.

    d. **Kaydet**’i seçin.

7. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksinimlerinize göre verilen seçeneklerden indirmek için **İndir** ' i seçin. Ardından bu dosyayı bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

8. **Coralogix ayarlama** bölümünde uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-coralogix-single-sign-on"></a>Coralogix çoklu oturum açmayı yapılandırma

**Coralogix** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve Azure Portal URL 'Leri, [coralogix destek ekibine](mailto:info@coralogix.com)gönderin. SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlandığından emin olurlar.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**yazın.
  
    b. **Kullanıcı adı** alanına "brittasimon@yourcompanydomain.extension" yazın. Örneğin, bu durumda "brittasimon@contoso.com" girebilirsiniz.

    c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri unutmayın.

    d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Coralogix 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **coralogix**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Coralogix**' i seçin.

    ![Uygulamalar listesindeki Coralogix bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesini seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' u seçin. Ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

### <a name="create-a-coralogix-test-user"></a>Coralogix test kullanıcısı oluşturma

Bu bölümde, Coralogix 'te Britta Simon adlı bir Kullanıcı oluşturacaksınız. Coralogix platformunda kullanıcıları eklemek için [coralogix destek ekibi](mailto:info@coralogix.com) ile çalışın. Çoklu oturum açma kullanmadan önce kullanıcıları oluşturmanız ve etkinleştirmeniz gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı Uygulamaps portalını kullanarak test edersiniz.

Uygps portalında Coralogix kutucuğunu seçtiğinizde, Coralogix için otomatik olarak oturum açmış olmanız gerekir. Uygulamaps portalı hakkında daha fazla bilgi için, bkz. [Uygulamaps portalı nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

