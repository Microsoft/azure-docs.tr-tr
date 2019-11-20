---
title: 'Öğretici: Flaur dosyalarla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve düzme dosyaları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 13012474e34af80c84b034703a3b34f0208036bc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73156276"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Öğretici: Flaur dosyalarla tümleştirme Azure Active Directory

Bu öğreticide, dosyaları Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Flap dosyalarını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, dosyaları DÜZEDEN dosyalara erişimi olan denetimi yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla dosyaları (çoklu oturum açma) düzden otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Düzle dosyalarla birlikte yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Dosyaları Flap çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Flaur dosyaları **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-flatter-files-from-the-gallery"></a>Galeriden dosya ekleme

Dosyaları Azure AD 'ye tümleştirmeyi yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize fladuyur dosyaları eklemeniz gerekir.

**Galeriden dosya eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **dosyaları düzden**yazın, sonuç panelinden **dosyaları düzden** seçin ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesindeki dosyaları düzden](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre flamlar ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisine sahip olan bir ilişki kurulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve bu dosyalarla test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Alev dosyalarını yapılandırma çoklu oturum açma](#configure-flatter-files-single-sign-on)** , uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan dosyaları Flatta dinde Simon 'a sahip olmak için **[Flaur dosyaları oluşturma test kullanıcısı](#create-flatter-files-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, dosyaları Flala birlikte yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **dosyaları alev** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

    ![Dosyaları düzden etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/preintegrated.png)

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Flap dosyalarını ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-flatter-files-single-sign-on"></a>Alev dosyalarını yapılandırma çoklu oturum açma

1. Flamı dosyaları uygulamanızda yönetici olarak oturum açın.

2. **Pano**' ya tıklayın. 
   
    ![Çoklu oturum açmayı yapılandırma](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. **Ayarlar**' a tıklayın ve ardından **Şirket** sekmesinde aşağıdaki adımları gerçekleştirin: 
   
    ![Çoklu oturum açmayı yapılandırma](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **Kimlik doğrulaması IÇIN SAML 2,0 Kullan**' ı seçin.
    
    b. **SAML Yapılandır**öğesine tıklayın.

4. **SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın: 
   
    ![Çoklu oturum açmayı yapılandırma](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. **Etki alanı** metin kutusuna kayıtlı etki alanınızı yazın.
   
   > [!NOTE]
   > Henüz kayıtlı bir etki alanınız yoksa, [support@flatterfiles.com](mailto:support@flatterfiles.com)aracılığıyla Flaur dosyaları destek ekibinize başvurun. 
    
    b. **Kimlik sağlayıcısı URL 'si** metin kutusunda, form Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
   
    c.  Base-64 kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından **kimlik sağlayıcısı sertifikası** metin kutusuna yapıştırın.

    d. **Güncelleştir**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, alev dosyalarına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **dosyaları düzden**seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **dosyaları düzden**Seç ' i seçin.

    ![Uygulamalar listesinde düzme dosyaları bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-flatter-files-test-user"></a>Flaur dosyaları oluştur test kullanıcısı

Bu bölümün amacı, dosyaları alev içinde Britta Simon adlı bir Kullanıcı oluşturmaktır.

**Dosyaları Düzlüme sırasında Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. **Flamı dosyalarınızın** şirket sitenizde yönetici olarak oturum açın.

2. Sol taraftaki Gezinti bölmesinde, **Ayarlar**' a ve ardından **Kullanıcılar** sekmesine tıklayın.
   
    ![Bir alev dosyası kullanıcısı oluşturma](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. **Kullanıcı Ekle**' ye tıklayın. 

4. **Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Bir alev dosyası kullanıcısı oluşturma](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. **Ilk ad** metin kutusuna **Britta**yazın.
   
    b. **Soyadı** metin kutusuna **Simon**yazın. 
   
    c. **E-posta adresi** metin kutusuna, Azure Portal Britta e-posta adresini yazın.
   
    d. **Gönder**' e tıklayın.   


### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde dosyaları Düzle kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız Düzle dosyalarında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

