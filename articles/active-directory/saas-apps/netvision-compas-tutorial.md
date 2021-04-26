---
title: 'Öğretici: NetVision Compas ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve NetVision Compas arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519410"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Öğretici: NetVision Compas ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, NetVision Compas 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. NetVision Compas 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de NetVision Compas erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte NetVision Compas için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* NetVision Compas çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* NetVision Compas **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* NetVision Compas 'yi yapılandırdıktan sonra, kuruluşunuzun gizli verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Galeriden NetVision Compas ekleme

NetVision Compas 'yi Azure AD ile tümleştirmeyi yapılandırmak için, galerideki NetVision Compas ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **NetVision Compas** yazın.
1. Sonuçlar panelinden **NetVision Compas** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>NetVision Compas için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu NetVision Compas ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve NetVision Compas içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu NetVision Compas ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[NetVision Compas SSO 'Yu yapılandırın](#configure-netvision-compas-sso)** .
    1. **[NetVision Compas test kullanıcısına](#configure-netvision-compas-test-user)** , NetVision Compas 'in, kullanıcının Azure AD gösterimine bağlı olduğu gibi B. Simon 'ın bir karşılığı olacak şekilde yapılandırın.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **NetVision Compas** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [NetVision Compas istemci desteği ekibine](mailto:contact@net.vision) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve meta veri dosyasını indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)



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

Bu bölümde, NetVision Compas erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **NetVision Compas** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netvision-compas-sso"></a>NetVision Compas SSO 'yu yapılandırma

Bu bölümde, **NetVision Compas**'de SAML SSO 'yu etkinleştirirsiniz.
1. Yönetim hesabı kullanarak **NetVision Compas** 'de oturum açın ve Yönetim alanına erişin.

    ![Yönetici alanı](media/netvision-compas-tutorial/admin.png)

1. **Sistem** alanını bulun ve **kimlik sağlayıcıları**' nı seçin.

    ![Yönetici IDPs](media/netvision-compas-tutorial/admin-idps.png)

1. Azure AD 'yi yeni bir ıDP olarak kaydetmek için **ekleme** eylemini seçin.

    ![IDP ekleme](media/netvision-compas-tutorial/idps-add.png)

1. **Sağlayıcı türü** için **SAML** ' yi seçin.
1. **Görünen ad** ve **Açıklama** alanları için anlamlı değerler girin.
1. **Kullanılabilir kullanıcılar** listesinden seçerek ve ardından **Seçili Ekle** düğmesini seçerek IDP 'ye **NetVision Compas** kullanıcısı atayın. Kullanıcılar, sağlama yordamını takip ederken ıDP 'ye de atanabilir.
1. **Meta veri** SAML seçeneği Için **Dosya Seç** düğmesine tıklayın ve bilgisayarınıza daha önce kaydedilen meta veri dosyasını seçin.
1. **Kaydet**’e tıklayın.

    ![IDP 'yi Düzenle](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>NetVision Compas test kullanıcısı yapılandırma

Bu bölümde, Azure AD 'yi SSO için kullanmak üzere **NetVision Compas** 'de var olan bir kullanıcıyı yapılandırırsınız.
1. Şirketiniz tarafından tanımlanan **NetVision Compas** Kullanıcı sağlama yordamını izleyin veya var olan bir kullanıcı hesabını düzenleyin.
1. Kullanıcının profilini tanımlarken, kullanıcının **e-posta (kişisel)** ADRESININ Azure AD Kullanıcı adı ile eşleştiğinden emin olun: username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.

    ![Kullanıcıyı Düzenle](media/netvision-compas-tutorial/user-config.png)

Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

### <a name="using-the-access-panel-idp-initiated"></a>Erişim panelini (ıDP başlatılan) kullanma.

Erişim panelinde NetVision Compas kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız NetVision Compas 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>NetVision Compas 'ye (SP tarafından başlatılan) doğrudan erişme.

1. **NetVision Compas** URL 'sine erişin. Örneğin, `https://tenant.compas.cloud`.
1. **NetVision Compas** Kullanıcı adını girip Ileri ' **yi** seçin.

    ![Oturum açan kullanıcı](media/netvision-compas-tutorial/login-user.png)

1. **(isteğe bağlı)** Kullanıcı **NetVision Compas** içinde birden çok IDPs atanırsa, kullanılabilir IDPs 'lerin bir listesi sunulur. Daha önce **NetVision Compas** Içinde YAPıLANDıRıLMıŞ Azure AD IDP 'yi seçin.

    ![Oturum açma seçimi](media/netvision-compas-tutorial/login-choose.png)

1. Kimlik doğrulamasını gerçekleştirmek için Azure AD 'ye yönlendirilirsiniz. Kimliği başarıyla doğrulandıktan sonra, SSO 'yu ayarladığınız **NetVision Compas** için otomatik olarak oturum açmış olmanız gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile NetVision Compas 'i deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)