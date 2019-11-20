---
title: 'Öğretici: Atlasduyi bulutu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Atlasduyi bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f901bd37a54d12dc2afe805f71c7b018a277e34
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969620"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Öğretici: Atlasduyi bulutunu Azure Active Directory tümleştirin

Bu öğreticide, Atlasme bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Atlasme bulutunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Atlasme bulutuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte bulunan bulut için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Atlasme bulutu çoklu oturum açma (SSO) etkin aboneliği.
* Atlasme bulut ürünleri için Security Assertion Markup Language (SAML) çoklu oturum açmayı etkinleştirmek için, Atlasbir erişim ayarlamanız gerekir. [Atlasduyi erişimi]( https://www.atlassian.com/enterprise/cloud/identity-manager)hakkında daha fazla bilgi edinin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

* Atlasme bulutu **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Galeriden Atlasme bulutu ekleme

Atlasduyi bulutu 'nın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize atlasduyisi bulutu eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **atlasduyisi bulutu** yazın.
1. Sonuçlar panelinden **Atlasduyi bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, Atlasbir Cloud Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Atlasduyi bulutu 'ndaki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Atlasduyi bulutu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Atlasduyi Cloud SSO 'Yu yapılandırma](#configure-atlassian-cloud-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Atlasbir **[Cloud test kullanıcısı oluşturun](#create-atlassian-cloud-test-user)** -kullanıcının Azure AD gösterimine bağlı olan atlasduyi bulutu 'nda B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Atlasme Cloud** Application Integration sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://auth.atlassian.com/saml/<unique ID>`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. **Ek URL 'Ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Yukarıdaki değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu gerçek değerleri, daha sonra öğreticinin **yapılandırma Atlasme bulutu çoklu oturum açma** bölümünde açıklanan **ATLASDUYI bulutu SAML yapılandırma** ekranından alırsınız.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Atlasme bulut Yöneticisi portalına oturum açabilmek için kullandığınız örnekten değeri yapıştırın.

    ![Çoklu oturum açmayı yapılandırma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Atlasme bulut uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Atlasme Cloud uygulaması, **NameIdentifier** 'ın **User. Mail**ile eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Atlasme bulutu 'Nı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

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

Bu bölümde, Atlasme bulutuna erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Atlasduyi bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-atlassian-cloud-sso"></a>Atlasduyi bulutu SSO 'yu yapılandırma

1. Atlasduyi bulutu 'ndaki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, **Kurulum Atlasme bulutu** ' na tıklayın, sizi atlasme bulut uygulamasına yönlendirir. Buradan, Atlasduyi bulutu 'nda oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Atlasesi bulutu 'nı el ile kurmak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Atlasme bulutu şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Çoklu oturum açmayı yapılandırmadan önce etki alanınızı doğrulamanız gerekir. Daha fazla bilgi için bkz. [Atlasme etki alanı doğrulama](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) belgesi.

1. Sol bölmede **güvenlik** > **SAML çoklu oturum açma**' yı seçin. Daha önce yapmadıysanız, Atlasme Identity Manager 'a abone olun.

    ![Çoklu oturum açmayı yapılandırma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. **SAML Yapılandırması Ekle** penceresinde şunları yapın:

    ![Çoklu oturum açmayı yapılandırma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. **Kimlik sağlayıcısı VARLıK kimliği** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure ad tanımlayıcısını** yapıştırın.

    b. **Kimlik sağlayıcısı SSO URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.

    c. İndirilen sertifikayı bir. txt dosyasında Azure portal açın, değeri ( *Başlangıç sertifikası* ve *son sertifika* satırları olmadan) kopyalayın ve ardından **genel x509 sertifikası** kutusuna yapıştırın.

    d. **Yapılandırmayı kaydet**' e tıklayın.

1. Doğru URL 'Leri ayarlamış olduğunuzdan emin olmak için aşağıdakileri yaparak Azure AD ayarlarını güncelleştirin:

    ![Çoklu oturum açmayı yapılandırma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. SAML penceresinde, **SP KIMLIK kimliğini** kopyalayın ve ardından Azure Portal, atlasme bulutu **temel SAML yapılandırması**altında, **tanımlayıcı** kutusuna yapıştırın.

    b. SAML penceresinde, **SP onaylama tüketici hizmeti URL 'sini** kopyalayın ve ardından Azure Portal, Atlasme bulutu **temel SAML yapılandırması**altında, **yanıt URL 'si** kutusuna yapıştırın. Oturum açma URL 'SI, Atlasme bulutunuzun kiracı URL 'sidir.

    > [!NOTE]
    > Mevcut bir müşteriyseniz, Azure portal **SP KIMLIK kimliği** ve **SP onaylama tüketici hizmeti URL 'si** değerlerini güncelleştirdikten sonra **Evet, güncelleştirme yapılandırması**' nı seçin. Yeni bir müşteriyseniz, bu adımı atlayabilirsiniz.

### <a name="create-atlassian-cloud-test-user"></a>Atlasduyi bulutu test kullanıcısı oluşturma

Azure AD kullanıcılarının atlasi bulutu 'nda oturum açmasını etkinleştirmek için aşağıdakileri yaparak Atlasme bulutunda Kullanıcı hesaplarını el ile sağlayın:

1. **Yönetim** bölmesinde, **Kullanıcılar**' ı seçin.

    ![Atlasme bulut kullanıcıları bağlantısı](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Atlasduyi bulutu 'nda bir kullanıcı oluşturmak için **kullanıcıyı davet et**' i seçin.

    ![Atlasduyi bulutu kullanıcısı oluşturma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. **E-posta adresi** kutusuna kullanıcının e-posta adresini girin ve ardından uygulama erişimini atayın.

    ![Atlasduyi bulutu kullanıcısı oluşturma](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Kullanıcıya e-posta Daveti göndermek için **kullanıcıları davet et**' i seçin. Kullanıcıya bir e-posta daveti gönderilir ve daveti kabul ettikten sonra Kullanıcı sistemde etkin olur.

> [!NOTE]
> **Kullanıcılar bölümünde** **toplu oluştur** düğmesini seçerek de kullanıcıları toplu olarak oluşturabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Atlascu bulutu kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Atlascu bulutu 'nda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Atlasme bulutu 'nı deneyin](https://aad.portal.azure.com/)