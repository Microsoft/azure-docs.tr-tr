---
title: 'Öğretici: N2F-harcama raporlarıyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve N2F-gider raporları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161294"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Öğretici: N2F-harcama raporlarıyla tümleştirme Azure Active Directory

Bu öğreticide, N2F-gider raporlarının Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
N2F-gider raporlarını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* N2F-harcama raporlarına erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla N2F-harcama raporlarında (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini N2F-harcama raporlarıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* N2F-gider raporları çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* N2F-harcama raporları **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Galeriden N2F-harcama raporları ekleme

N2F-harcama raporlarının tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki N2F-harcama raporları ' nı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden N2F-harcama raporları eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **N2F-harcama raporları**yazın, sonuç panelinden **N2F-harcama raporları** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![N2F-sonuçlar listesindeki gider raporları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test KULLANıCıSıNA göre N2F-harcama raporlarıyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve N2F-harcama raporlarında ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı N2F-harcama raporlarıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[N2F-gider raporlarını yapılandırma çoklu oturum](#configure-n2f---expense-reports-single-sign-on)** açma-uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan N2F-harcama raporlarında, **[N2F-gider raporları test kullanıcısı oluşturun](#create-n2f---expense-reports-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı N2F-harcama raporlarıyla yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **N2F-harcama raporları** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak Istiyorsanız, uygulama zaten Azure ile önceden tümleştirildiği için Kullanıcı herhangi bir adım gerçekleştirmek zorunda değildir.

    ![N2F-gider raporları etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/preintegrated.png)

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![N2F-gider raporları etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusuna bir URL yazın: `https://www.n2f.com/app/`

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

7. **MyPolicies ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F-gider raporlarını çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, N2F-harcama raporları şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar** ' a tıklayın ve ardından açılan listeden **Gelişmiş ayarlar** ' ı seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/configure1.png)

3. **Hesap ayarları** sekmesini seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/configure2.png)

4. **Kimlik doğrulaması** ' nı seçin ve **+ kimlik doğrulama yöntemi ekle** sekmesini seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/configure3.png)

5. Kimlik doğrulama yöntemi olarak **SAML Microsoft Office 365** ' u seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/configure4.png)

6. **Kimlik doğrulama yöntemi** bölümünde aşağıdaki adımları uygulayın:

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/configure5.png)

    a. **VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    b. **Meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    c. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, N2F-harcama raporlarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **N2F-gider raporları**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **N2F-harcama raporları**' nı seçin.

    ![Uygulamalar listesindeki N2F-harcama raporları bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-n2f---expense-reports-test-user"></a>N2F-harcama raporları test kullanıcısı oluşturma

Azure AD kullanıcılarının N2F-harcama raporlarında oturum açmasını sağlamak için, N2F-harcama raporlarında sağlanması gerekir. N2F-harcama raporlarında, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. N2F-harcama raporlarında Şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar** ' a tıklayın ve ardından açılan listeden **Gelişmiş ayarlar** ' ı seçin.

    ![N2F-harcama Kullanıcı ekleme](./media/n2f-expensereports-tutorial/configure1.png)

3. Sol Gezinti panelinden **Kullanıcılar** sekmesini seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/user1.png)

4. **+ Yeni Kullanıcı** sekmesini seçin.

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/user2.png)

5. **Kullanıcı** bölümünde aşağıdaki adımları gerçekleştirin:

    ![N2F-harcama raporları yapılandırması](./media/n2f-expensereports-tutorial/user3.png)

    a. **E-posta adresi** metin kutusuna, **\@contoso.com**gibi kullanıcının e-posta adresini girin.

    b. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta**gibi girin.

    c. **Ad** metin kutusuna, **Brittasıon**gibi kullanıcının adını girin.

    d. **Rol, doğrudan yönetici (N + 1)** seçeneğini belirleyin ve kuruluş gereksiniminize göre **bölme** yapın.

    e. **Doğrula ve davet gönder**' e tıklayın.

    > [!NOTE]
    > Kullanıcı eklerken herhangi bir sorunla karşılaşırsanız lütfen [N2F-harcama raporları destek ekibine](mailto:support@n2f.com) başvurun

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde N2F-harcama raporları kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız N2F-harcama raporlarında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

