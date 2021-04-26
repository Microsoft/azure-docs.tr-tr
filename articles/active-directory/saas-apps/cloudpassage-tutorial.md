---
title: 'Öğretici: Cloudpaszu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve Cloudpasla arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: b720f7e49fc0679de5c3f430122bab05d5b706f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455782"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Öğretici: Cloudpaszu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Cloudpaszu Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Cloudpaszu 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cloudpasna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cloudj 'de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cloudpaszu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cloudpaszu, **SP** tarafından başlatılan SSO 'yu destekliyor

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-cloudpassage-from-the-gallery"></a>Galeriden Cloudpaszu ekleme

Cloudpaszu 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Cloudpaszu ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **cloudpaszu** yazın.
1. Sonuçlar panelinden **Cloudpaszu** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Cloudpaszu için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Cloudpaszu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Cloudpaszu içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cloudpaszu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Cloudpaszu SSO 'Yu yapılandırın](#configure-cloudpassage-sso)** .
    1. Cloudpaszu **[test kullanıcısı oluşturun](#create-cloudpassage-test-user)** -kullanıcının Azure AD gösterimine bağlı olan cloudpasm 'de B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **cloudpaszu** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

     a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://portal.cloudpassage.com/saml/init/accountid`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın: `https://portal.cloudpassage.com/saml/consume/accountid` . Bu özniteliğin değerini, Cloudpaszu portalınızın **Çoklu oturum açma ayarları** bölümünde **SSO kurulum belgeleri** ' ne tıklayarak alabilirsiniz.

    ![Ekran görüntüsünde, S S O kurulum belgeleri bağlantısı adlı Cloudpaszu portalı gösterilmektedir.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Cloudpaszu istemci destek ekibine](https://www.cloudpassage.com/company/contact/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Cloudpaszu uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Cloudpaszu uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ---------------| --------------- |
    | FirstName |Kullanıcı. |
    | Soyadı |User. soyadı |
    | e-posta |Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Cloudpasu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Cloudpaszu 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Cloudpaszu**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cloudpassage-sso"></a>Cloudpaszu SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, Cloudpaszu şirket sitenizde yönetici olarak oturum açın.

1. Üstteki menüde **Ayarlar**' a ve ardından **site yönetimi**' ne tıklayın. 
   
    ![Ekran görüntüsü, site yönetimi seçiliyken Cloudpaszu sitesini gösterir.][12]

1. **Kimlik doğrulama ayarları** sekmesine tıklayın. 
   
    ![Ekran görüntüsü, kimlik doğrulama ayarları sekmesi seçili olarak Cloudpaszu sitesini gösterir.][13]

1. **Çoklu oturum açma ayarları** bölümünde aşağıdaki adımları uygulayın: 
   
    ![Ekran görüntüsü, bu adımda bilgileri girebileceğiniz çoklu oturum açma ayarları bölümünü gösterir.][14]

    a. **Çoklu oturum açmayı etkinleştir (SSO) (SSO kurulum belgeleri)** onay kutusunu seçin.
    
    b. **Azure ad tanımlayıcısını** **SAML veren URL** metin kutusuna yapıştırın.
  
    c. **Oturum açma URL** 'sini **SAML Endpoint URL** metin kutusuna yapıştırın.
  
    d. **Logout URL** 'sini **Logout giriş sayfası** metin kutusuna yapıştırın.
  
    e. İndirilen sertifikanızı Not defteri 'nde açın, indirilen sertifikanın içeriğini panonuza kopyalayın ve **x 509 sertifika** metin kutusuna yapıştırın.
  
    f. **Kaydet**’e tıklayın.

### <a name="create-cloudpassage-test-user"></a>Cloudpaszu test kullanıcısı oluştur

Bu bölümün amacı, Cloudpaszu içinde B. Simon adlı bir Kullanıcı oluşturmaktır.

**Cloudpaszu içinde B. Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. **Cloudpaszu** şirket sitenizde yönetici olarak oturum açın. 

1. Üstteki araç çubuğunda, **Ayarlar**' a ve ardından **site yönetimi**' ne tıklayın. 
   
    ![Ekran görüntüsünde site yönetimi seçiliyken Cloudpaszu gösterilmektedir.][22] 

1. **Kullanıcılar** sekmesine tıklayın ve ardından **Yeni Kullanıcı Ekle**' ye tıklayın. 
   
    ![Ekran görüntüsü, kullanıcılar sekmesi seçiliyken Cloudpaszu site yönetimini ve yeni kullanıcı ekleme seçeneğini gösterir.][23]

1. **Yeni Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın: 
   
    ![Ekran görüntüsü, Kullanıcı bilgilerini belirtebileceğiniz Yeni Kullanıcı ekle bölümünü gösterir.][24]
    
    a. **Ilk ad** metin kutusuna Britta yazın. 
  
    b. **Soyadı** metin kutusuna Simon yazın.
  
    c. **Kullanıcı adı** metin kutusunda, **e-posta** metin kutusu ve **yeniden yazma e-posta** metin kutusunu, Azure AD 'de Britta Kullanıcı adı yazın.
  
    d. **Erişim türü** olarak, **Halo portalı erişimini etkinleştir**' i seçin.
  
    e. **Ekle**'ye tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Cloudpaszu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Cloudpaszu ' nda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Cloudpaszu 'yi deneyin](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png