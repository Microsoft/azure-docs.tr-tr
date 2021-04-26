---
title: 'Öğretici Azure Active Directory: tek oturum açma (SSO) öğesini en güncel hizmet ile tümleştirme | Microsoft Docs'
description: Azure Active Directory ve tek başına hizmeti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651773"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Öğretici: tek oturum açma (SSO) öğesini en güncel hizmet ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile en güncel hizmeti tümleştirmeyi öğreneceksiniz. En güncel hizmeti Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, en yenisi hizmetine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak hizmet vermek için oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* En güncel hizmeti çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* En güncel hizmet **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-freshservice-from-the-gallery"></a>Galeriden en güncel hizmeti ekleme

En güncel hizmeti Azure AD ile tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize en güncel hizmeti eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna her bir **hizmet** yazın.
1. Sonuçlar panelinden yalnızca bir **hizmet** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Azure AD SSO 'yu yalnızca bir hizmet ile yapılandırma ve test etme. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu en güncel hizmetle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, tek başına **[HIZMET SSO 'Yu yapılandırın](#configure-freshservice-sso)** .
    1. Kullanıcı için Azure AD gösterimine bağlı olan, yalnızca bir hizmet **[testi kullanıcısı oluşturun](#create-freshservice-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, en güncel **hizmet** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company-name>.freshservice.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company-name>.freshservice.com`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, en güncel [hizmet istemci destek ekibine](https://support.freshservice.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Azure Portal** **en güncel hizmeti ayarla** bölümünde, GEREKSINIMINIZE göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak en güncel hizmet 'e erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yeniden Seç ' i seçin**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-freshservice-sso"></a>En güncel hizmet SSO 'SU yapılandırma

1. Yapılandırma işlemini otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, bu **hizmeti ayarla** ' ya tıklayın. Buradan, tek bir hizmette oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Alternatif hizmeti el ile ayarlamak istiyorsanız, alternatif hizmet şirket sitenizde yönetici olarak oturum açın.

1. Soldaki menüden **yönetici** ' ye tıklayın ve **Genel ayarlar**' da **Yardım Masası güvenliği** ' ni seçin.

    ![Yönetici](./media/freshservice-tutorial/configure-1.png "Yönetici")

1. **Güvenlik**' te, en **güncel hizmet 360 güvenliğine git ' e** tıklayın.

    ![Güvenlik](./media/freshservice-tutorial/configure-2.png "Güvenlik")

1. **Güvenlik** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu Oturum Açma](./media/freshservice-tutorial/configure-3.png "Çoklu Oturum Açma")
  
    a. **Çoklu oturum açma** için **Açık**' ı seçin.

    b. **Oturum açma yönteminde** **SAML SSO**' yı seçin.

    c. **IDP metin kutusu tarafından belirtilen VARLıK kimliğinde** , Azure Portal KOPYALADıĞıNıZ **varlık kimliği** değeri yapıştırın.

    d. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Imzalama seçeneklerinde**, açılan listeden **yalnızca imzalı onayları** seçin.

    f. **Oturum kapatma URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    örneğin: **Güvenlik sertifikası** metin kutusu ' nda, daha önce edindiğiniz **sertifika (base64)** değerini yapıştırın.
  
    h. **Kaydet**’e tıklayın.


## <a name="create-freshservice-test-user"></a>Yalnızca bir hizmet testi kullanıcısı oluştur

Azure AD kullanıcılarının, tek bir hizmette oturum açmasını sağlamak için, bu kullanıcıların, tek bir hizmet olarak sağlanması gerekir. En güncel hizmet durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Alternatif **hizmet** şirket sitenizde yönetici olarak oturum açın.

2. Soldaki menüden **yönetici**' ye tıklayın.

3. **Kullanıcı yönetimi** bölümünde, **istek sahipleri**' na tıklayın.

    ![Sahiplerini](./media/freshservice-tutorial/create-user-1.png "Sahiplerini")

4. **Yeni Istek sahibi** öğesine tıklayın.

    ![Yeni Istekcılar](./media/freshservice-tutorial/create-user-2.png "Yeni Istekcılar")

5. **Yeni Istek sahibi** bölümünde gerekli alanları girin ve **Kaydet**' e tıklayın.
    ![Yeni Istek sahibi](./media/freshservice-tutorial/create-user-3.png "Yeni Istek sahibi")  

    > [!NOTE]
    > Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır
    >  

    > [!NOTE]
    > Azure AD Kullanıcı hesapları sağlamak için, başka bir kullanıcı hesabı oluşturma aracını veya en güncel hizmet tarafından sunulan API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz en güncel hizmet oturum açma URL 'sine yönlendirecektir. 

* Doğrudan hizmet oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da otomatik olarak hizmet kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız OneDrive hizmetinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

 Tek yapmanız gereken hizmeti yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).