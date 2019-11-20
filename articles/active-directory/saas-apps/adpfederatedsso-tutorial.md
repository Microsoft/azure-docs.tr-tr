---
title: 'Öğretici: ADP ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ADP arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Öğretici: ADP ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ADP 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. ADP 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, ADP erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak ADP 'ye oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* ADP çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ADP **IDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-adp-from-the-gallery"></a>Galeriden ADP ekleme

ADP 'yi Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden ADP 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ADP** yazın.
1. Sonuçlar panelinden **ADP** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>ADP için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu ADP ile yapılandırın ve test edin. SSO 'nun çalışması için, ADP 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ADP ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[ADP SSO 'Yu yapılandırın](#configure-adp-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan ADP 'de B. Simon 'a sahip olmak için, **[ADP test kullanıcısı oluşturun](#create-adp-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **ADP** uygulama tümleştirmesi sayfasında, **Özellikler sekmesine** tıklayın ve aşağıdaki adımları uygulayın: 

    ![Çoklu oturum açma özellikleri](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **Kullanıcıların oturum açma** alanı değerini **Evet**olarak ayarlayın.

    b. **Kullanıcı erişim URL 'sini** kopyalayın ve bunu Öğreticinin ilerleyen kısımlarında açıklanan **oturum açma URL 'sini yapılandırın bölümüne**yapıştırmanız gerekir.

    c. **Kullanıcı Ataması gerekli** alan değerini **Evet**olarak ayarlayın.

    d. **Kullanıcılar Için görünür** alan değerini **Hayır**olarak ayarlayın.

1. [Azure Portal](https://portal.azure.com/), **ADP** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın:`https://fed.adp.com`

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **ADP ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

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

Bu bölümde, B. Simon 'u, ADP 'ye erişim vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ADP**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-adp-sso"></a>ADP SSO 'yu yapılandırma

**ADP** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **meta veri XML** 'sini [ADP Web sitesine](https://adpfedsso.adp.com/public/login/index.fcc)yüklemeniz gerekir.

> [!NOTE]  
> Bu işlem birkaç gün sürebilir.

### <a name="configure-your-adp-services-for-federated-access"></a>Federasyon erişimi için ADP hizmetinizi yapılandırma

>[!Important]
> ADP hizmetinize federasyon erişimi gerektiren çalışanlarınız, ADP hizmeti uygulamasına atanmalıdır ve daha sonra kullanıcıların belirli ADP hizmetine yeniden atanması gerekir.
ADP temsilcinizden onay alındıktan sonra, ADP hizmetinizi yapılandırın ve belirli ADP hizmetine Kullanıcı erişimini denetlemek için kullanıcıları atayın/yönetin.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ADP** yazın.
1. Sonuçlar panelinden **ADP** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.
1. Azure portal, **ADP** uygulama tümleştirme sayfanızda, **Özellikler sekmesine** tıklayın ve aşağıdaki adımları uygulayın:  

    ![Çoklu oturum açma bağlantılı özellikleri](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  **Kullanıcıların oturum açma** alanı değerini **Evet**olarak ayarlayın.

    b.  **Kullanıcı Ataması gerekli** alan değerini **Evet**olarak ayarlayın.

    c.  **Kullanıcılar Için görünür** alan değerini **Evet**olarak ayarlayın.

1. [Azure Portal](https://portal.azure.com/), **ADP** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.

1. **Çoklu oturum açma yöntemi seç** iletişim kutusunda, **bağlı**olarak **mod** ' u seçin. Uygulamanızı **ADP**'e bağlamak için.

    ![Çoklu oturum açma bağlı](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. **Oturum açma URL 'Sini yapılandırma** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açma Prop](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Yukarıdaki **Özellikler sekmesinden** kopyaladığınız **Kullanıcı erişim URL 'sini**yapıştırın (ana ADP uygulamasından).
                                                             
    b. Farklı **geçiş durumu URL 'lerini**destekleyen 5 uygulama aşağıda verilmiştir. Belirli bir uygulama için uygun **geçiş durumu URL 'si** değerini **Kullanıcı erişim URL**'sine el ile eklemeniz gerekir.
    
    * **ADP iş gücü artık**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP iş gücü artık gelişmiş saat**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vanu HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP kurumsal HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. Değişikliklerinizi **kaydedin** .

10. ADP temsilcinizden onay alındıktan sonra, bir veya iki kullanıcıyla testi başlatın.

    a. Federasyon erişimini test etmek için, ADP hizmeti uygulamasına birkaç Kullanıcı atayın.

    b. Kullanıcılar galerideki ADP hizmeti uygulamasına erişirken ve ADP hizmetine erişebillerinde test başarılı olur.
 
11. Başarılı bir testi onaylamada, Federasyon ADP hizmetini ayrı kullanıcılara veya Kullanıcı gruplarına atayın. Bu, öğreticide daha sonra açıklanarak çalışanlarınıza ulaşın.

### <a name="create-adp-test-user"></a>ADP test kullanıcısı oluştur

Bu bölümün amacı, ADP 'de B. Simon adlı bir Kullanıcı oluşturmaktır. ADP hesabındaki kullanıcıları eklemek için [ADP destek ekibi](https://www.adp.com/contact-us/overview.aspx) ile çalışın. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde ADP kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ADP 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile ADP 'yi deneyin](https://aad.portal.azure.com)
