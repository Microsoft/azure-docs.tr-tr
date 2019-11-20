---
title: 'Öğretici Azure Active Directory: Velpıc SAML ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Velpıc SAML arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Öğretici: Velpıc SAML ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Velpıc SAML Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Velpıc SAML 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Velpıc SAML erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Velpıc SAML için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Velpıc SAML çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Velpıc SAML **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-velpic-saml-from-the-gallery"></a>Galeriden Velpıc SAML ekleme

Velpıc SAML tümleştirmesini Azure AD 'ye yapılandırmak için, Galeriden Velpıc SAML 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **velpıc SAML** yazın.
1. Sonuçlar panelinden **Velpıc SAML** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Velpıc SAML için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak VELPıC SAML Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Velpıc SAML içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Velpıc SAML ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Velpıc SAML SSO 'Yu yapılandırın](#configure-velpic-saml-sso)** .
    1. Velpıc **[SAML test kullanıcısı oluşturma](#create-velpic-saml-test-user)** -kullanıcının Azure AD gösterimine bağlı olan velpıc SAML 'da B. Simon 'a karşılık gelen bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **velpıc SAML** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<sub-domain>.velpicsaml.net`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Lütfen Velpıc SAML tarafında SSO eklentisini yapılandırdığınızda, bir oturum açma URL 'sinin Velpıc SAML ekibi tarafından sağlandığını ve tanımlayıcı değerin kullanılabilir olacağını lütfen unutmayın. Bu değeri Velpıc SAML uygulama sayfasından kopyalamanız ve buraya yapıştırmanız gerekir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Velpıc kümesi Ayarla SAML** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'u tıklatın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Velpıc SAML erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Velpıc SAML**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-velpic-saml-sso"></a>Velpıc SAML SSO 'yu yapılandırma

1. Velpıc SAML 'daki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **set up VELPıC SAML** ' ye tıklayın, sizi velpıc SAML uygulamasına yönlendirirler. Buradan, Velpıc SAML 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-8 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Velpıc SAML 'yi el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Velpıc SAML şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Yönet** sekmesine tıklayın ve oturum açma için yeni eklenti oluşturmak üzere **Eklentiler** düğmesine tıklabilmeniz gereken **tümleştirme** bölümüne gidin.

    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_1.png)

5. **' Pluginadd '** düğmesine tıklayın.
    
    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_2.png)

6. Eklenti Ekle sayfasında **SAML** kutucuğuna tıklayın.
    
    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_3.png)

7. Yeni SAML eklentisinin adını girip **' Ekle '** düğmesine tıklayın.

    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_4.png)

8. Ayrıntıları aşağıdaki gibi girin:

    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_5.png)

    a. **Ad** metın kutusuna SAML eklentisinin adını yazın.

    b. **Veren URL** metin kutusuna, Azure Portal **oturum açma penceresini yapılandırma** penceresinden kopyaladığınız **Azure ad tanımlayıcısını** yapıştırın.

    ,. **Sağlayıcı meta veri yapılandırması** Azure Portal ' den Indirdiğiniz meta veri xml dosyasını karşıya yükleyin.

    TID. **' Otomatik olarak yeni kullanıcı oluştur '** onay kutusunu etkinleştirerek SAML 'yi zamanında sağlama seçeneğini de belirleyebilirsiniz. Bir Kullanıcı Velpıc 'de yoksa ve bu bayrak etkinleştirilmemişse, Azure oturumu başarısız olur. Bayrak etkinleştirilirse, Kullanıcı oturum açma sırasında otomatik olarak Velpıc olarak sağlanır. 

    A. Metin kutusundan **Çoklu oturum açma URL 'sini** kopyalayın ve Azure Portal yapıştırın.
    
    vadeli. **Kaydet**'e tıklayın.

### <a name="create-velpic-saml-test-user"></a>Velpıc SAML test kullanıcısı oluşturma

Uygulamanın tam zamanında Kullanıcı sağlamasını desteklediği için bu adım genellikle gerekli değildir. Otomatik Kullanıcı sağlama etkinleştirilmemişse, el ile Kullanıcı oluşturma aşağıda açıklandığı şekilde yapılabilir.

Velpıc SAML şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:
    
1. Yönet sekmesine tıklayın ve kullanıcılar bölümüne gidin ve ardından yeni düğmesine tıklayarak Kullanıcı ekleyin.

    ![Kullanıcı Ekle](./media/velpicsaml-tutorial/velpic_7.png)

2. **"Yeni Kullanıcı Oluştur"** iletişim sayfasında aşağıdaki adımları gerçekleştirin.

    ![kullanıcı](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. **Ilk ad** metin kutusuna B adını yazın.

    b. **Soyadı** metin kutusunda, Simon 'ın son adını yazın.

    ,. **Kullanıcı adı** metin kutusuna B. Simon Kullanıcı adını yazın.

    TID. **E-posta** metin kutusuna B.Simon@contoso.com hesabının e-posta adresini yazın.

    A. Bilgilerin geri kalanı isteğe bağlıdır, gerekirse onu doldurabilirsiniz.
    
    vadeli. **Kaydet**' e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

1. Erişim panelinde Velpıc SAML kutucuğuna tıkladığınızda, Velpıc SAML uygulamasının oturum açma sayfasını almalısınız. Oturum açma sayfasındaki **' Azure AD Ile oturum aç '** düğmesini görmeniz gerekir.

    ![Eklentisindeki](./media/velpicsaml-tutorial/velpic_6.png)

1. Azure AD hesabınızı kullanarak Velpıc 'de oturum açmak için **' Azure AD Ile oturum aç '** düğmesine tıklayın.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Velpıc SAML 'yi deneyin](https://aad.portal.azure.com/)

