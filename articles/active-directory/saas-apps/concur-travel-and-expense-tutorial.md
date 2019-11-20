---
title: 'Öğretici Azure Active Directory: Concur seyahat ve harcama ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Concur seyahat ve harcama arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Öğretici: Concur seyahat ve harcama ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Concur seyahat ve masrafın Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Concur seyahat ve harcamayı tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Concur seyahat ve gideri erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla gezecek ve ücrettabileceği şekilde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Concur seyahat ve gider aboneliği.
* Concur Kullanıcı hesabınızın altındaki "Şirket Yöneticisi" rolü. [Concur SSO self servis aracına](https://www.concursolutions.com/nui/authadmin/ssoadmin)giderek doğru erişime sahip olup olmadığını test edebilirsiniz. Erişiminiz yoksa lütfen Concur destek veya uygulama Proje Yöneticisi ile iletişime geçin. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu yapılandırıp test edersiniz.

* Concur seyahat ve harcama, **IDP** ve **SP** tarafından başlatılan SSO 'yu destekler
* Concur seyahat ve harcama, hem üretim hem de uygulama ortamında SSO sınamasını destekler 

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, üç bölgenin her biri için sabit bir dize değeridir: US, EMEA ve Çin. Bu nedenle, tek bir Kiracıdaki her bölge için yalnızca bir örnek yapılandırılabilir. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Galeri 'den Concur seyahat ve harcama ekleme

Concur seyahat ve masrafın tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Concur seyahat ve harcama eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Concur seyahat ve harcama** yazın.
1. Sonuçlar panelinden **Concur seyahat ve harcama** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Concur seyahat ve gideri için Azure AD çoklu oturum açma yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Concur seyahat ve harcama ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Concur seyahat ve gider arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Concur seyahat ve masrafıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Concur seyahat ve harcama SSO 'Su yapılandırma](#configure-concur-travel-and-expense-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Concur **[seyahat ve harcama testi kullanıcısı oluşturun](#create-concur-travel-and-expense-test-user)** ve kullanıcının Azure AD gösterimine bağlı olan yarışmayı ve harcamayı Içeren bir B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Concur seyahat ve harcama** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama **IDP** tarafından başlatılan modda önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

    > [!NOTE]
    > Tanımlayıcı (varlık KIMLIĞI) ve yanıt URL 'SI (onaylama tüketici hizmeti URL 'SI) bölgeye özeldir. Lütfen Concur varlığınızın veri merkezine göre ' yi seçin. Concur varlığınızın veri merkezini görmüyorsanız lütfen Concur desteğiyle iletişime geçin. 

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek Için **Kullanıcı özniteliği** için Düzenle/kalem simgesine tıklayın. Benzersiz kullanıcı tanımlayıcısının, Concur User login_id ile eşleşmesi gerekir. Genellikle **User. UserPrincipalName** öğesini **User. Mail**olarak değiştirmelisiniz.

    ![Kullanıcı özniteliğini Düzenle](common/edit-attribute.png)

6. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve meta verileri indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, Concur seyahat ve masrafına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Concur seyahat ve harcama**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-concur-travel-and-expense-sso"></a>Concur seyahat ve harcama SSO 'SU yapılandırma

1. **Concur seyahat ve gider** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini [Concur SSO self servis aracına](https://www.concursolutions.com/nui/authadmin/ssoadmin) yüklemeniz ve "Şirket Yöneticisi" rolüne sahip bir hesapla oturum açmanız gerekir. 

1. **Ekle**'ye tıklayın.
1. IDP 'niz için "Azure AD (US)" gibi özel bir ad girin. 
1. **XML dosyasını karşıya yükle** ' ye tıklayın ve daha önce Indirdiğiniz **Federasyon meta veri XML 'sini** ekleyin
1. Değişikliği kaydetmek için **meta veri Ekle** ' ye tıklayın.

    ![Concur SSO self servis aracı ekran görüntüsü](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Concur seyahat ve harcama testi kullanıcısı oluşturma

Bu bölümde, Concur seyahat ve harcama için B. Simon adlı bir Kullanıcı oluşturacaksınız. Concur seyahat ve gider platformunda kullanıcıları eklemek için Concur destek ekibi ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

> [!NOTE]
> B. Simon 'ın Concur oturum açma kimliği, Azure AD 'de B. Simon 'ın benzersiz tanımlayıcısı ile eşleşmelidir. Örneğin, B. Simon 'un Azure AD benzersiz tanımlayıcı değeri `B.Simon@contoso.com` ' dır. B. Simon 'ın Concur oturum açma kimliği 'nin `B.Simon@contoso.com` olması gerekir. 

## <a name="configure-concur-mobile-sso"></a>Concur Mobile SSO 'yu yapılandırma
Concur Mobile SSO 'yu etkinleştirmek için, Concur destek ekibi **Kullanıcı erişim URL 'si**sağlamanız gerekir. Azure AD 'den **Kullanıcı erişimi URL 'si** almak için aşağıdaki adımları izleyin:
1. **Kurumsal uygulamalara** git
1. **Concur seyahat ve gideri** ' ne tıklayın
1. **Özellikler** 'e tıklayın
1. **Kullanıcı erişim URL 'sini** kopyalayın ve bu URL 'Yi Concur desteği 'ne verin

> [!NOTE]
> SSO 'yu yapılandırmaya yönelik Self Servis seçeneği kullanılamıyor, bu nedenle mobil SSO 'yu etkinleştirmek için Concur destek ekibiyle birlikte çalışın. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Concur seyahat ve harcama kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız yarışmak ve harcamayı otomatik olarak açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile yarışmayı ve gideri deneyin](https://aad.portal.azure.com/)

