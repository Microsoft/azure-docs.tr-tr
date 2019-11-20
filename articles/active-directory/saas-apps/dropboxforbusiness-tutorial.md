---
title: 'Öğretici: Iş için Dropbox ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Iş için Azure Active Directory ile Dropbox arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 615c10357e099b547008b128ca5f5a773ff1aa14
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158314"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Öğretici: Azure Active Directory Dropbox for Business ile tümleştirin

Bu öğreticide, Dropbox for Business for Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Dropbox for Business for Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Iş için Dropbox 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Iş için Dropbox 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Iş çoklu oturum açma (SSO) özellikli abonelik için Dropbox.

## <a name="scenario-description"></a>Senaryo açıklaması

* Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Iş için Dropbox, **SP** tarafından başlatılan SSO 'yu destekler

* Iş için Dropbox **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Galeriden Iş için Dropbox ekleme

Dropbox for Business 'un Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Iş için Dropbox 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **iş için Dropbox** yazın.
1. Sonuçlar panelinden **Dropbox for Business** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**Britta Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Iş için Dropbox ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Dropbox for Business içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Iş için Dropbox ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Iş SSO 'su Için Dropbox 'U yapılandırın](#configure-dropbox-for-business-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Iş için Dropbox 'ta Britta Simon 'un bir karşılığı olacak şekilde **[iş testi kullanıcısı Için Dropbox 'ı oluşturun](#create-dropbox-for-business-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **iş için Dropbox** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://www.dropbox.com/sso/<id>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir değer yazın: `Dropbox`

    > [!NOTE]
    > Yukarıdaki oturum açma URL 'SI değeri gerçek bir değer değil. Değeri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'siyle güncellecektir.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **İş Için Dropbox 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-dropbox-for-business-sso"></a>Iş SSO 'SU için Dropbox 'ı yapılandırma

1. Dropbox for Business içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yükleyebilirsiniz**' e tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **iş Için kurulum Dropbox** 'a tıkladığınızda sizi Iş için Dropbox 'a yönlendirebilirsiniz. Buradan, Iş için Dropbox 'ta oturum açmak üzere yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-8 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Iş için Dropbox 'ı el ile ayarlamak istiyorsanız yeni bir Web tarayıcısı penceresi açın ve Dropbox for Business kiracısına gidip iş kiracınızda Dropbox 'ta oturum açın. ve aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/ic769509.png "Çoklu oturum açmayı yapılandırma")

4. **Kullanıcı simgesine** tıklayın ve **Ayarlar** sekmesini seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure1.png "Çoklu oturum açmayı yapılandırma")

5. Sol taraftaki Gezinti bölmesinde, **Yönetim Konsolu**' na tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure2.png "Çoklu oturum açmayı yapılandırma")

6. **Yönetim konsolunda**, sol gezinti bölmesinde **Ayarlar** ' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure3.png "Çoklu oturum açmayı yapılandırma")

7. **Kimlik doğrulama** bölümünde **Çoklu oturum açma** seçeneğini belirleyin.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure4.png "Çoklu oturum açmayı yapılandırma")

8. **Çoklu oturum açma** bölümünde aşağıdaki adımları gerçekleştirin:  

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure5.png "Çoklu oturum açmayı yapılandırma")

    a. **Çoklu oturum açma**için açılan listeden bir seçenek olarak **gerekli** ' ı seçin.

    b. **Oturum açma URL 'Si Ekle** ' ye tıklayın ve **kimlik sağlayıcısı oturum açma URL 'si** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın ve **bitti**' yi seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/configure6.png "Çoklu oturum açmayı yapılandırma")

    c. **Sertifikayı karşıya yükle**' ye tıklayın ve ardından Azure Portal indirdiğiniz **Base64 kodlamalı sertifika dosyanıza** gidin.

    d. **Kopyala bağlantısına** tıklayın ve kopyaladığınız değeri Azure Portal **iş etki alanı ve URL 'ler Için Dropbox** 'ın **oturum açma URL 'si** metin kutusuna yapıştırın.

    e. **Kaydet** düğmesine tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Iş için Dropbox 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **iş Için Dropbox**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-dropbox-for-business-test-user"></a>Iş testi kullanıcısı için Dropbox oluştur

Bu bölümde, çalışma için Dropbox 'ta Britta Simon adlı bir Kullanıcı oluşturulur. Iş için Dropbox, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Dropbox for Business içinde henüz bir kullanıcı yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [iş Için Dropbox for Business istemci destek ekibine](https://www.dropbox.com/business/contact) başvurun

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Iş kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Iş için Dropbox 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)