---
title: 'Öğretici: ISAMs ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ISAMs arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: e994e39cc916c51e3ad6b00015d710bb422cccc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459705"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-isams"></a>Öğretici: ISAMs ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ISAMs 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. ISAMs 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ISAMs erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ISAMs 'de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* ISAMs çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.


* ISAMs **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* ISAMs 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-isams-from-the-gallery"></a>Galeriden ISAMs ekleme

ISAMs tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden ISAMs 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ISAMs** yazın.
1. Sonuçlar panelinden **ISAMs** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-isams"></a>ISAMs için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak ISAMs Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ISAMs 'deki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ISAMs 'Ler ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[ISAMs SSO 'Yu yapılandırma](#configure-isams-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[ISAMs test kullanıcısı oluşturun](#create-isams-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan ISAMs 'de B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **ISAMs** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.isams.cloud/main/sso/saml2`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.isams.cloud/main/sso/saml2/acs`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.isams.cloud/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [ISAMs istemci desteği ekibine](mailto:support@isams.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, ISAMs 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ISAMs**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-isams-sso"></a>ISAMs SSO 'yu yapılandırma

1. ISAMs 'de yönetici olarak oturum açın.

1. Denetim Masası 'na gidin ve **kimlik doğrulama** modülünü açın.
1. Sağ taraftaki menüden **kimlik sağlayıcıları** ' nı seçin.

    ![Ekran görüntüsü, kimlik sağlayıcılarının seçili olduğu Active Directory yapılandırmayı gösterir.](./media/isams-tutorial/click-identity-provider.png)

1. **Sağlayıcı Ekle** ' yi seçin

    ![Ekran görüntüsü, Sağlayıcı Ekle seçiliyken kimlik sağlayıcılarını gösterir.](./media/isams-tutorial/add-identity-provider.png)


1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan adımları yapabileceğiniz kimlik sağlayıcıları Sihirbazı ' nı gösterir.](./media/isams-tutorial/configure-isams.png)

    a. **Ad** metin kutusuna, gibi geçerli bir ad verin `Saml2 Azure` . Bu, oturum açma sayfasında görünecek olan addır.

    b. Meta veri URL 'SI kutusuna, Azure portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini girin.
    
    c. **Içeri aktar**' a basın.
    
    d. **Etkin Istemci uygulamaları** bölümündeki **uygulamalar** ListBox bölümünde, sağlayıcınızın oturum açma sayfasında görüntülenmesini istediğiniz tüm ISAMs uygulamalarını seçin.

    e. **Kaydet & kapat**' a tıklayın.

### <a name="create-isams-test-user"></a>ISAMs test kullanıcısı oluşturma

1. ISAMs 'de yönetici olarak oturum açın.

2.  **Denetim Masası Ana**  ->  **güvenlik & izinleri**  ->  **Kullanıcı hesapları**  ->  **Kullanıcı seçenekleri & görevler**  ->  **Kullanıcı özelliklerini değiştirme**

    ![Ekran görüntüsü, Kullanıcı hesabı özelliklerini değiştir seçili olan kullanıcı hesapları sayfasını gösterir.](./media/isams-tutorial/modify-user-properties.png)


3. Ortaya çıkan açılan pencerede, **Hesap ayrıntıları** sekmesini seçin ve **Yetkilendirmeyi** yeni oluşturduğunuz kimlik sağlayıcılarınızın kimliğiyle değiştirin.

    ![Ekran görüntüsünde, yetkilendirme için bir değerle hesap ayrıntıları gösterilir.](./media/isams-tutorial/account-details.png)

4. **Kaydet & kapat**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ISAMs kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ISAMs 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile ISAMs 'yi deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)