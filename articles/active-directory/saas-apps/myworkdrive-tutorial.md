---
title: 'Öğretici: MyWorkDrive ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile MyWorkDrive arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: c733873a5545bb01f03abd8178ed2f371e2dd3aa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074079"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Öğretici: MyWorkDrive 'ı Azure Active Directory ile tümleştirme

Bu öğreticide, MyWorkDrive 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. MyWorkDrive 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de MyWorkDrive erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla MyWorkDrive 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* MyWorkDrive çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. MyWorkDrive **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-myworkdrive-from-the-gallery"></a>Galeriden MyWorkDrive ekleme

MyWorkDrive 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden MyWorkDrive ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **myworkdrive** yazın.
1. Sonuçlar panelinden **Myworkdrive** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**Britta Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu MyWorkDrive ile yapılandırın ve test edin. SSO 'nun çalışması için, MyWorkDrive içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu MyWorkDrive ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[MyWorkDrive SSO 'Yu yapılandırın](#configure-myworkdrive-sso)** .
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
5. Kullanıcının Azure AD gösterimine bağlı olan MyWorkDrive 'da Britta Simon 'un bir karşılığı olacak şekilde **[myworkdrive test kullanıcısı oluşturun](#create-myworkdrive-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **myworkdrive** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki alana ait değerleri girin:

    **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Kendi şirketinizin MyWorkDrive sunucusu ana bilgisayar adını girin: örn.
    > 
    > Yanıt URL'si: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Oturum açma URL 'SI:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Bu değerler için kendi ana bilgisayar adınızı ve SSL sertifikanızı ayarlama konusunda emin değilseniz, [Myworkdrive destek ekibine](mailto:support@myworkdrive.com) başvurun.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** panonuza kopyalamak için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>MyWorkDrive SSO 'yu yapılandırma

1. MyWorkDrive içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, **Setup MyWorkDrive** öğesine tıkladığınızda sizi myworkdrive uygulamasına yönlendirirsiniz. Buradan, MyWorkDrive 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-4 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. MyWorkDrive 'ı el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak MyWorkDrive ' da oturum açın.

1. Yönetim panelindeki MyWorkDrive sunucusunda **kuruluş** ' a tıklayın ve aşağıdaki adımları uygulayın:

    ![Yönetici](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. **SAML/ADFS SSO**'yu etkinleştirin.

    b. **SAML seçin-Azure AD**

    c. **Azure Uygulama Federasyon meta verileri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL** 'sinin değerini yapıştırın.

    d. **Kaydet**’e tıklayın

    > [!NOTE]
    > Daha fazla bilgi için, [Myworkdrive Azure AD desteği makalesini](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)inceleyin.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, MyWorkDrive 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Myworkdrive**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive test kullanıcısı oluştur

Bu bölümde, MyWorkDrive 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Myworkdrive platformunda kullanıcıları eklemek için [myworkdrive destek ekibi](mailto:support@myworkdrive.com) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde MyWorkDrive kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız MyWorkDrive sürücüsünde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)