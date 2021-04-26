---
title: 'Öğretici: OpsGenie ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve OpsGenie arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728589"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Öğretici: OpsGenie ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, OpsGenie 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. OpsGenie 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de OpsGenie erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla OpsGenie 'ye otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* OpsGenie çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* OpsGenie, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-opsgenie-from-the-gallery"></a>Galeriden OpsGenie ekleme

OpsGenie tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerideki OpsGenie 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **OpsGenie** yazın.
1. Sonuçlar panelinden **OpsGenie** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>OpsGenie için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu OpsGenie ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve OpsGenie içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu OpsGenie ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[OpsGenie SSO 'Yu yapılandırın](#configure-opsgenie-sso)** .
    1. **[OpsGenie test kullanıcısı oluşturun](#create-opsgenie-test-user)** -kullanıcının Azure AD gösterimine bağlı olan OpsGenie 'de B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **OpsGenie** uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

1. **OpsGenie 'Yi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, OpsGenie erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **OpsGenie**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-opsgenie-sso"></a>OpsGenie SSO 'yu yapılandırma

1. OpsGenie içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, kurulum **OpsGenie** ' ye tıklayarak sizi OpsGenie uygulamasına yönlendirirsiniz. Buradan, OpsGenie 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. OpsGenie 'yi el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, OpsGenie şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar**' a ve ardından **Çoklu oturum açma** sekmesine tıklayın.
   
    ![OpsGenie tek Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. SSO 'yu etkinleştirmek için **etkin**' i seçin.
   
    !["Etkin" onay kutusunun seçili olduğunu gösteren ekran görüntüsü.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. **Sağlayıcı** bölümünde **Azure Active Directory** sekmesine tıklayın.
   
    !["Azure Active Directory" sekmesi seçiliyken "sağlayıcı" bölümünü gösteren ekran görüntüsü.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Azure Active Directory iletişim sayfasında, aşağıdaki adımları uygulayın:
   
    !["Çoklu oturum açmayı etkinleştir" bölümünü, "bir M L 2,0 uç noktası" ve "metadata U R L" ile "çoklu oturum açma" bölümünü gösteren ekran görüntüsü.](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. **Uygulama KIMLIĞI URI** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    a. **Yanıt URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** bölümünde **yanıt URL** metin kutusuna yapıştırın.

    a. **SAML 2,0 uç nokta** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
    
    b. **Meta veri URL 'si:** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.
    
    c. SSO 'yu etkinleştirmek için **Çoklu oturum açmayı etkinleştir** ' i açın.

    d. **SSO ayarlarını uygula**' ya tıklayın.

### <a name="create-opsgenie-test-user"></a>OpsGenie test kullanıcısı oluştur

Bu bölümün amacı, OpsGenie 'de B. Simon adlı bir Kullanıcı oluşturmaktır. 

1. Bir Web tarayıcısı penceresinde, yönetici olarak OpsGenie kiracınızda oturum açın.

2. Sol panelde **Kullanıcılar** ' a tıklayarak kullanıcılar listesine gidin.
   
    ![OpsGenie ayarları](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. **Kullanıcı Ekle**'ye tıklayın.

4. **Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    !["E-posta" ve "tam ad" metin kutuları vurgulanmış "Kullanıcı Ekle" iletişim kutusunu ve "Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. **E-posta** metin kutusuna, Azure Active Directory adreslenen B. Simon e-posta adresini yazın.
   
    b. **Tam ad** metin kutusuna **B. Simon** yazın.
   
    c. **Kaydet**’e tıklayın. 

> [!NOTE]
> B. Simon, profillerini ayarlamaya yönelik yönergeler içeren bir e-posta alır.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız OpsGenie 'ye otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki OpsGenie kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız OpsGenie ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

* OpsGenie 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).