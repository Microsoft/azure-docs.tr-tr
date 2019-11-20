---
title: "Öğretici: EAB 'de çoklu oturum açma (SSO) Tümleştirmesi stratejik bir sorun ile Azure Active Directory | Microsoft Docs"
description: Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin ve bu da stratejik bir Ilgi gezin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0c61fcfc-b89b-4f89-9b81-27098047cddb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1185217d6b12d3efd5dedc9faf903f1b365481db
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014072"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Öğretici: EAB 'de çoklu oturum açma (SSO) Tümleştirmesi stratejik bakım ile Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile EAB 'ye yönelik bir stratejik eğitim ile tümleştirmeyi öğreneceksiniz. EAB 'yi tümleştirdiğinizde Azure AD ile ilgili stratejik bir Ilgiyle şunları yapabilirsiniz:

* Azure AD 'de EAB 'ye erişimi olan denetim stratejik bir şekilde gezinmelidir.
* Kullanıcılarınızın Azure AD hesaplarıyla stratejik bir şekilde gezinmelerini sağlamak için kullanıcılarınızın otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* EAB, stratejik bakım çoklu oturum açma (SSO) etkin aboneliğine gider.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* EAB, stratejik bakım gezin **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>EAB 'yi ekleme Galerisi 'nden stratejik bakım gezin

EAB 'nin bir kısmını Azure AD 'ye ekleme hakkında daha fazla şekilde yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize EAB 'ye yönelik bir stratejik bakım ekleyin.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **EAB** ' yi stratejik olarak gezin yazın.
1. Sonuçlar panelinden **stratejik bakım gezin** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Azure AD 'ye yönelik çoklu oturum açmayı yapılandırma ve test etme stratejik bakım

Azure AD SSO 'yu yapılandırın ve test edin EAB, **B. Simon**adlı bir test kullanıcısı kullanarak stratejik bir sorun gider. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve EAB 'deki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu yapılandırmak ve test etmek için EAB ile ilgili ayrıntılı gezinin aşağıdaki yapı taşlarını izleyin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[EAB ' nin stratejik bakım SSO 'su](#configure-eab-navigate-strategic-care-sso)** ' ni yapılandırın.
    1. **[EAB 'ye gidebileceğiniz stratejik bakım test kullanıcısına](#create-eab-navigate-strategic-care-test-user)** bir sahip olmak için b. Simon 'ıN Azure AD gösterimine bağlı olan stratejik bir ilginin bulunduğu
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **EAB 'de stratejik bakım 'a git** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değer almak için [EAB 'ye giderek stratejik bakım istemci destek ekibine](mailto:tech@gradesfirst.com) ulaşın. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde Kopyala düğmesine tıklayarak **uygulama Federasyon meta verileri URL 'sini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, B. Simon, EAB 'ye erişim sağlayarak stratejik bir şekilde oturum açmaya yönelik olarak Azure çoklu oturum açma özelliğini etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **EAB stratejik bakım 'A git**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-eab-navigate-strategic-care-sso"></a>EAB 'yi gezin stratejik bakım SSO 'SU yapılandırma

EAB 'de çoklu oturum açma 'yı yapılandırmak için, **stratejik bakım** ' a gidin, takım ekibi **meta veri URL 'sini** [EAB 'Nin stratejik bakım destek ekibine gitmeniz](mailto:tech@gradesfirst.com)gerekir. Bunlar, her iki kenarı da düzgün ayarlandığından SAML SSO bağlantı sağlamak için bu ayarı ayarlayın.

### <a name="create-eab-navigate-strategic-care-test-user"></a>EAB 'ye gitme stratejik bakım test kullanıcısı oluşturma

Bu bölümde, EAB 'de B. Simon adlı bir Kullanıcı oluşturursunuz. EAB ile çalışma stratejik bakım [destek ekibi](mailto:tech@gradesfirst.com) ' ne giderek Kullanıcı ekleme stratejik bakım platformunda kullanıcıları ekleyin. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde EAB 'ye git ' i tıklattığınızda, SSO 'yu ayarladığınız EAB 'nin stratejik bakım bölümüne otomatik olarak oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile stratejik bir şekilde gezinmenize çalışın](https://aad.portal.azure.com/)

