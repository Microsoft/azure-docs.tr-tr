---
title: 'Öğretici: Azure Active Directory aha ile tümleştirme! | Microsoft Belgeleri'
description: Azure Active Directory ve aha arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a39371e7a22334b11be1d1a0a9d28557efe177c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654451"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Öğretici: Aha tümleştirin! Azure Active Directory ile

Bu öğreticide, aha tümleştirmeyi öğreneceksiniz! Azure Active Directory (Azure AD) ile. Aha tümleştirdiğinizde! Azure AD ile şunları yapabilirsiniz:

* Azure AD 'de, aha 'ye erişimi olan denetim!.
* Kullanıcılarınızın, aha 'ye otomatik olarak oturum açabilmesi için etkinleştirin! Azure AD hesaplarıyla.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Aha! Çoklu oturum açma (SSO) etkin abonelik.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Aha! **SP** tarafından başlatılan SSO 'yu destekler
* Aha! **tam zamanında** Kullanıcı sağlamayı destekler

## <a name="add-aha-from-the-gallery"></a>Aha ekleyin! Galeriden

Aha tümleştirmesini yapılandırmak için! Azure AD 'de, aha eklemeniz gerekir! Galeriden, yönetilen SaaS uygulamaları listenize gidin.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, **aha yazın!** yazın.
1. **Aha seçin!** sonuçlar panelinden ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Aha için Azure AD SSO 'yu yapılandırın ve test edin!

Azure AD SSO 'yu aha ile yapılandırın ve test edin! **B. Simon** adlı bir test kullanıcısı kullanılıyor. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir!.

Azure AD SSO 'yu aha ile yapılandırmak ve test etmek için, aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. **[Aha 'Yi yapılandırma! SSO](#configure-aha-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. **[Aha oluşturun! bir Kullanıcı test](#create-aha-test-user)** edin. Bu, kullanıcının Azure AD gösterimine bağlanır.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **aha üzerinde!** Uygulama Tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.aha.io/session/new`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.aha.io`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. [Aha başvurun! ](https://www.aha.io/company/contact)Bu değerleri almak için istemci destek ekibi. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. ' İ **ayarlayın!** bölümüne uygun URL 'leri, gereksiniminize göre kopyalayın.

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

Bu bölümde, B. Simon 'u, aha 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **aha!** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-aha-sso"></a>Aha 'yi yapılandırma! SSO

1. Bu yapılandırma,, bu yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, Kurulum ' a tıklayın **!** Sizi yalnızca aha 'ya yönlendirir! Uygulamanızı. Buradan, bu şekilde, aha 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-8 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Aha 'yi kurmak istiyorsanız! el ile, yeni bir Web tarayıcı penceresi açın ve aha içinde oturum açın! Yönetici olarak Şirket sitesi ve aşağıdaki adımları gerçekleştirin:

4. Üstteki menüde, **Ayarlar**' a tıklayın.

    ![Ayarlar](./media/aha-tutorial/setting.png "Ayarlar")

5. **Hesap**' a tıklayın.

    ![Profil](./media/aha-tutorial/account.png "Profil")

6. **Güvenlik ve çoklu oturum açma**' ya tıklayın.

    ![Güvenlik ve çoklu oturum açma menü seçeneğini vurgulayan ekran görüntüsü.](./media/aha-tutorial/security.png "Güvenlik ve çoklu oturum açma")

7. **Çoklu oturum açma** bölümünde, **kimlik sağlayıcısı** olarak **SAML 2.0**' ı seçin.

    ![Güvenlik ve çoklu oturum açma](./media/aha-tutorial/saml.png "Güvenlik ve çoklu oturum açma")

8. **Çoklu oturum açma** yapılandırması sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma](./media/aha-tutorial/sso.png "Çoklu Oturum Açma")

    a. **Ad** metin kutusuna yapılandırmanız için bir ad yazın.

    b. **Kullanarak yapılandırma** Için **meta veri dosyası**' nı seçin.

    c. İndirilen meta veri dosyanızı karşıya yüklemek için, **Araştır**' a tıklayın.

    d. **Güncelleştir**’e tıklayın.

### <a name="create-aha-test-user"></a>Aha oluşturun! test kullanıcısı

Bu bölümde, B. Simon adlı bir Kullanıcı, aha! içinde oluşturulmuştur. Aha! , varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten aha 'de yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, aha 'a yönlendirilir! Oturum açma akışını başlatabileceğiniz oturum açma URL 'SI. 

* Aha 'ye gidin! Doğrudan oturum açma URL 'SI ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Aha tıkladığınızda! Uygulamamda bulunan kutucuk, bu, aha 'ye yönlendirecektir! Oturum açma URL'si. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Aha 'yi yapılandırdıktan sonra! Kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).