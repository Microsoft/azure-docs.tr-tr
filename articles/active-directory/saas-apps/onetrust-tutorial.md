---
title: 'Öğretici: OneTrust gizlilik yönetimi yazılımıyla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile OneTrust gizlilik yönetimi yazılımı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648450"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Öğretici: OneTrust gizlilik yönetimi yazılımıyla Azure Active Directory tümleştirme

Bu öğreticide, OneTrust gizlilik yönetimi yazılımını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. OneTrust gizlilik yönetimi yazılımını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de OneTrust gizlilik yönetimi yazılımına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla OneTrust gizlilik yönetimi yazılımında otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini OneTrust gizlilik yönetimi yazılımıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık deneme sürümü edinebilirsiniz.
* OneTrust gizlilik yönetimi yazılımını çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* OneTrust gizlilik yönetimi yazılımı **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

* OneTrust gizlilik yönetimi yazılımı **, tam zamanında** Kullanıcı sağlamasını destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Galeriden OneTrust gizlilik yönetimi yazılımını ekleme

OneTrust gizlilik yönetimi yazılımının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize OneTrust gizlilik yönetim yazılımını eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Onetrust gizlilik yönetimi yazılımını** yazın.
1. Sonuçlar panelinden **Onetrust gizlilik yönetimi yazılımını** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>OneTrust gizlilik yönetimi yazılımı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Onetrust gizlilik yönetimi YAZıLıMıYLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, OneTrust gizlilik yönetimi yazılımında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu OneTrust gizlilik yönetimi yazılımıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[OneTrust gizlilik yönetimi yazılım SSO 'Yu yapılandırın](#configure-onetrust-privacy-management-software-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan bir B. Simon ınonetrust privacy Management yazılımına karşılık gelen, **[OneTrust gizlilik yönetimi yazılım test kullanıcısı oluşturun](#create-onetrust-privacy-management-software-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD SSO 'yu etkinleştirirsiniz.
 
1. Azure portal, **Onetrust gizlilik yönetimi yazılım** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Tek bir Sign-On yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`https://www.onetrust.com/saml2`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.onetrust.com/auth/consumerservice`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

     **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Onetrust gizlilik yönetimi yazılım istemci destek ekibine](mailto:support@onetrust.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **OneTrust gizlilik yönetimi yazılımını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.
1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu işaretleyin ve parolayı aşağı yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, OneTrust gizlilik yönetimi yazılımına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Onetrust gizlilik yönetimi yazılımı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="configure-onetrust-privacy-management-software-sso"></a>OneTrust gizlilik yönetimi yazılım SSO 'SU yapılandırma

**Onetrust gizlilik yönetimi yazılım** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta verileri XML** 'sini ve Azure Portal ' den [onetrust gizlilik yönetimi yazılım destek ekibine](mailto:support@onetrust.com)uygun şekilde kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-onetrust-privacy-management-software-test-user"></a>OneTrust gizlilik yönetimi yazılım test kullanıcısı oluştur

Bu bölümde, OneTrust gizlilik yönetimi yazılımında Britta Simon adlı bir Kullanıcı oluşturulur. OneTrust gizlilik yönetimi yazılımı, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Kullanıcı OneTrust gizlilik yönetimi yazılımında zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa [Onetrust gizlilik yönetimi yazılım destek ekibine](mailto:support@onetrust.com)başvurun.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz OneTrust gizlilik yönetimi yazılım oturum açma URL 'sine yönlendirilir.  
 
* OneTrust gizlilik yönetimi yazılım oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Onetrust gizlilik yönetimi yazılımında otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içinde OneTrust gizlilik yönetimi yazılım kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız OneTrust privacy Management yazılımında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

OneTrust gizlilik yönetimi yazılımını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).