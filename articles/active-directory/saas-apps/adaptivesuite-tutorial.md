---
title: 'Öğretici: Uyarlamalı Öngörüler ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Uyarlamalı Öngörüler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649096"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Öğretici: Uyarlamalı öngörüleri Azure Active Directory tümleştirin

Bu öğreticide, uyarlamalı öngörüleri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Uyarlamalı öngörüleri Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Uyarlamalı Öngörüler 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Uyarlamalı Öngörüler 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Uyarlamalı Öngörüler çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Uyarlamalı Öngörüler **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-adaptive-insights-from-the-gallery"></a>Galeriden Uyarlamalı Öngörüler ekleyin

Uyarlamalı Öngörüler 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Uyarlamalı Öngörüler eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Uyarlamalı Öngörüler** yazın.
1. Sonuçlar panelinden **Uyarlamalı Öngörüler** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Uyarlamalı Öngörüler için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, uyarlamalı Öngörüler Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, uyarlamalı Öngörüler içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Uyarlamalı Öngörüler ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Uyarlamalı Öngörüler SSO 'Yu yapılandırma](#configure-adaptive-insights-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Uyarlamalı **[Öngörüler test kullanıcısı oluşturun](#create-adaptive-insights-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Uyarlamalı içgörüler 'de B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Uyarlamalı Öngörüler** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Uyarlamalı Öngörüler 'in **SAML SSO ayarları** sayfasından tanımlayıcıyı (varlık kimliği) ve yanıt URL 'si değerlerini elde edebilirsiniz.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Uyarlamalı Öngörüler ayarlama** bölümünde, uygun URL 'leri gereksinime göre kopyalayın.

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

Bu bölümde, uyarlamalı Öngörüler 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Uyarlamalı Öngörüler**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name=&quot;configure-adaptive-insights-sso&quot;></a>Uyarlamalı Öngörüler SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, uyarlamalı Öngörüler Şirket sitenizde yönetici olarak oturum açın.

2. **Yönetim** bölümüne gidin.

    ![Gezinti panelinde yönetim için önemli ekran görüntüsü.](./media/adaptivesuite-tutorial/administration.png &quot;Yönetici")

3. **Kullanıcılar ve roller** bölümünde **SAML SSO ayarları**' na tıklayın.

    ![SAML SSO ayarlarını yönetme](./media/adaptivesuite-tutorial/settings.png "SAML SSO ayarlarını yönetme")

4. **SAML SSO ayarları** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![SAML SSO ayarları](./media/adaptivesuite-tutorial/saml.png "SAML SSO ayarları")

    a. **Kimlik sağlayıcısı adı** metin kutusuna yapılandırmanız için bir ad yazın.

    b. Azure portal ' dan kopyalanmış **Azure AD tanımlayıcı** değerini **KIMLIK sağlayıcısı varlık kimliği** metin kutusuna yapıştırın.

    c. Azure portal ' dan kopyalanmış **oturum açma URL 'si** değerini **kimlik sağlayıcısı SSO URL 'si** metin kutusuna yapıştırın.

    d. Azure portal ' den kopyalanmış **oturum kapatma URL 'si** değerini **özel oturum kapatma URL 'si** metin kutusuna yapıştırın.

    e. İndirilen sertifikanızı karşıya yüklemek için **Dosya Seç**' e tıklayın.

    f. Aşağıdakiler için aşağıdakileri seçin:

     * **SAML Kullanıcı kimliği**, **kullanıcının Uyarlamalı Öngörüler Kullanıcı adını** seçin.

     * **SAML Kullanıcı kimliği konumu**, **ilgilinin NameID içinde Kullanıcı kimliği**' ni seçin.

     * **SAML NameID biçimi**, **e-posta adresi** seçin.

     * **SAML 'Yi etkinleştirin**, **SAML SSO ve doğrudan Uyarlamalı** içgörüler oturum açma seçeneğini belirleyin.

    örneğin: **Uyarlamalı Öngörüler SSO URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** ve **yanıt URL 'si** metin kutularına yapıştırın.

    h. **Kaydet**’e tıklayın.

### <a name="create-adaptive-insights-test-user"></a>Uyarlamalı Öngörüler test kullanıcısı oluşturma

Azure AD kullanıcılarının Uyarlamalı Öngörüler 'de oturum açmasını sağlamak için bunların Uyarlamalı Öngörüler 'e sağlanması gerekir. Uyarlamalı Öngörüler söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Uyarlamalı Öngörüler** şirket sitenizde yönetici olarak oturum açın.

2. **Yönetim** bölümüne gidin.

   ![Yönetici](./media/adaptivesuite-tutorial/administration.png "Yönetici")

3. **Kullanıcılar ve roller** bölümünde **Kullanıcılar**' a tıklayın.

   ![Kullanıcı Ekle](./media/adaptivesuite-tutorial/users.png "Kullanıcı Ekleme")

4. **Yeni Kullanıcı** bölümünde aşağıdaki adımları gerçekleştirin:

   ![Gönder](./media/adaptivesuite-tutorial/new.png "Gönder")

   a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory kullanıcısına ilişkin **adı**, **Kullanıcı** adını, **e-postayı** ve **parolayı** yazın.

   b. Bir **rol** seçin.

   c. **Gönder**' e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, uyarlamalı içgörüler tarafından sunulan diğer Uyarlamalı içgörüler Kullanıcı hesabı oluşturma araçlarını veya API 'Lerini kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Uyarlamalı Öngörüler ' de otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Uyarlamalı Öngörüler kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Uyarlamalı öngörülere otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Uyarlamalı öngörüleri yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).