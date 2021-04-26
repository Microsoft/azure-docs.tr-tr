---
title: 'Öğretici: Samsung KNOX ve Business Services ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Samsung KNOX ve Iş hizmetleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 330c02f15c0818f0a5c69088757c92a91a523589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Öğretici: Samsung KNOX ve Business Services ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Samsung KNOX ve Iş hizmetlerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Samsung KNOX ve Business Services 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Samsung KNOX ve Iş hizmetlerine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Samsung KNOX ve Iş hizmetlerinde otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir Samsung KNOX hesabı.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Samsung KNOX ve Iş Hizmetleri **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Galeriden Samsung KNOX ve Business Services ekleme

Samsung KNOX ve Business Services 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Samsung KNOX ve Business Services 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Samsung KNOX ve Business Services** yazın.
1. Sonuçlar panelinden **Samsung KNOX ve Iş Hizmetleri** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Samsung KNOX ve Iş Hizmetleri için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Samsung KNOX ve Business Services Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, [SamsungKnox.com](https://samsungknox.com/)Içinde BIR Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Samsung KNOX ve Business Services ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Samsung KNOX ve Business SERVICES SSO 'Yu yapılandırma](#configure-samsung-knox-and-business-services-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Samsung KNOX **[ve Business Services test kullanıcısı oluşturun](#create-samsung-knox-and-business-services-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan Samsung KNOX ve Iş hizmetlerinde B. Simon 'a sahip olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Samsung KNOX ve Business Services** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    * **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://www.samsungknox.com`
    * **Yanıt URL 'si (onaylama tüketici hizmeti URL 'si)** metin kutusuna URL 'yi yazın:`https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![Temel SAML yapılandırma değerleri](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

Bu bölümde, Samsung KNOX ve Business Services erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Samsung KNOX ve Iş Hizmetleri**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Samsung KNOX ve Business Services SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak [SamsungKnox.com](https://samsungknox.com/) 'de oturum açın.

1. Sağ üst köşedeki **avatar** öğesine tıklayın.

    ![Samsung KNOX avatarı](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Sol kenar çubuğunda **ACTIVE DIRECTORY ayarları** ' na tıklayın ve aşağıdaki adımları gerçekleştirin.

    ![ACTIVE DIRECTORY AYARLARı](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, Azure Portal girdiğiniz **tanımlayıcı** değeri yapıştırın.

    b. **Uygulama Federasyon meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    c. **Ad SSO 'Ya Bağlan**' a tıklayın.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Samsung KNOX ve Business Services test kullanıcısı oluşturma

Bu bölümde, Samsung KNOX ve Iş hizmetlerinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Samsung KNOX kuruluşunuza bir alt yönetici veya test kullanıcısı davet etme yönergeleri için [Knox configure](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) veya [Knox Mobile kayıt](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) Yöneticisi kılavuzlarını inceleyin. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz [SamsungKnox.com](https://samsungknox.com/)'a yönlendirilir. 

* Doğrudan [SamsungKnox.com](https://samsungknox.com/) adresine gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım bölümünde Samsung KNOX ve Iş Hizmetleri kutucuğuna tıkladığınızda, bu [SamsungKnox.com](https://samsungknox.com/)'e yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Samsung KNOX ve Business Services 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).