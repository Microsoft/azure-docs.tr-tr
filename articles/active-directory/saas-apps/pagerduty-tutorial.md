---
title: 'Öğretici: Pagerharcı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile Pagerharcı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 9a3117b64c516120f8556b7b63b24e5ef906f973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Öğretici: Pagerharcı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Pagerharcı tümleştirmeyi öğreneceksiniz. Pagerharcı 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Pagerharcı 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Pagerharcı 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Pagerharcı çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> MFA veya passwordless kimlik doğrulamasını Azure AD ile kullanıyorsanız, SAML Isteğindeki AuthnContext değerini kapatın. Aksi takdirde, Azure AD, AuthnContext uyuşmazlığından sonra hatayı oluşturur ve belirteci uygulamaya geri göndermez.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Pagerharcı, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-pagerduty-from-the-gallery"></a>Galeriden Pagerharcı ekleme

Pagerharcı 'in Azure AD ile tümleştirilmesini yapılandırmak için, galerisindeki Pagerharcı 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **pagerharcı** yazın.
1. Sonuçlar panelinden **Pagerharcı** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Pagerharcı için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak pagerharcı Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Pagerharcı içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Pagerharcı ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Pagerharcı SSO 'Yu yapılandırma](#configure-pagerduty-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Pagerharcı **[test kullanıcısı oluşturun](#create-pagerduty-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan pagerharcı 'de B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Pagerharcı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.pagerduty.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.pagerduty.com`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Pagerharcı istemci destek ekibine](https://www.pagerduty.com/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Pagerharcı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Pagerharcı 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Pagerharcı**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-pagerduty-sso&quot;></a>Pagerharcı SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Pagerharcı şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Hesap ayarları**' na tıklayın.

    ![Hesap ayarları](./media/pagerduty-tutorial/ic778535.png &quot;Hesap ayarları")

3. **Çoklu oturum açma**' ya tıklayın.

    ![Çoklu oturum açma](./media/pagerduty-tutorial/ic778536.png "Çoklu oturum açma")

4. **Çoklu oturum açmayı etkinleştir (SSO)** sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı etkinleştir](./media/pagerduty-tutorial/ic778537.png "Çoklu oturum açmayı etkinleştir")

    a. Not defteri 'nde Azure portal indirilen Base-64 kodlu sertifikanızı açın, bu içeriği panonuza kopyalayın ve **X. 509.440 sertifika** metin kutusuna yapıştırın
  
    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.
  
    c. **Logout URL** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'sini** yapıştırın.

    d. **Kullanıcı adına Izin ver/parola oturum açma** seçeneğini belirleyin.

    e. **Tam kimlik doğrulama bağlamı karşılaştırması** onay kutusunu seçin.

    f. **Değişiklikleri Kaydet**’e tıklayın.

### <a name="create-pagerduty-test-user"></a>Pagerharcı test kullanıcısı oluşturma

Azure AD kullanıcılarının, Pagerharcı 'de oturum açmasını sağlamak için bunların Pagerharcı 'e sağlanması gerekir. Pagerharcı söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

> [!NOTE]
> Azure Active Directory Kullanıcı hesapları sağlamak için Pagerharcı tarafından sunulan diğer tüm Pagerharcı Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Pagerharcı** kiracınızda oturum açın.

2. Üstteki menüde **Kullanıcılar**' a tıklayın.

3. **Kullanıcı Ekle**' ye tıklayın.
   
    ![Kullanıcı Ekleme](./media/pagerduty-tutorial/ic778539.png "Kullanıcı Ekle")

4.  **Ekibinize davet et** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Takımınızı davet etme](./media/pagerduty-tutorial/ic778540.png "Takımınızı davet etme")

    a. **B. Simon** gibi kullanıcının **adını ve soyadını** yazın. 
   
    b. **B. Simon \@ contoso.com** gibi kullanıcının **e-posta** adresini girin.
   
    c. **Ekle**' ye ve ardından **davet gönder**' e tıklayın.
   
    > [!NOTE]
    > Tüm eklenen kullanıcılar, bir Pagerharcı hesabı oluşturmak için davet alır.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Pagerharcı oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Pagerharcı oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Pagerharcı kutucuğuna tıkladığınızda, bu işlem Pagerharcı oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Pagerharcı yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).