---
title: 'Öğretici: Cloud akademik My-SSO ile SSO tümleştirmesi Azure Active Directory'
description: Bu öğreticide, Azure Active Directory ile bulut akademik My-SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729843"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Öğretici: bulut akademik My-SSO ile çoklu oturum açma tümleştirmesi Azure Active Directory

Bu öğreticide Cloud akademik My-SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Cloud akademik My-SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Cloud akademik My-SSO ile kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcılarınızın Azure AD hesaplarıyla bulut akademik My-SSO ' da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli bir bulut akademik My-SSO aboneliğim.

## <a name="tutorial-description"></a>Öğretici açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

* Bulut akademik My-SSO, **SP** tarafından başlatılan SSO 'yu destekler
* Cloud akademik My-SSO **, yalnızca zamanında** Kullanıcı sağlamasını destekler

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Galeriden bulut akademik My-SSO 'SU Ekle

Cloud akademik My-SSO ile Azure AD arasındaki tümleştirmeyi yapılandırmak için, Galeriden bulut akademik My-SSO ' u yönetilen SaaS uygulamaları listenize eklemeniz gerekir:

1. Azure portal bir iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cloud AKADEMIK My-SSO** yazın.
1. Sonuçlar panelinde **Cloud akademik My-SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Cloud akademik My-SSO için Azure AD SSO 'yu yapılandırma ve test etme

Azure AD SSO 'yu, **B. Simon** adlı bir test kullanıcısı kullanarak Cloud akademik My-SSO ile yapılandırıp test edersiniz. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Cloud akademik My-SSO ' daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cloud akademik My-SSO ile yapılandırmak ve test etmek için şu üst düzey adımları tamamlayacaksınız:

1. Kullanıcılarınızın özelliğini kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Kullanıcının Azure AD çoklu oturum açma özelliğini kullanmasını sağlamak için **[Test kullanıcısına erişim Izni verin](#grant-access-to-the-test-user)** .
1. Uygulama tarafında **[bulut akademik My-SSO için çoklu oturum açmayı yapılandırın](#configure-single-sign-on-for-cloud-academy)** .
    1. Kullanıcının Azure AD gösterimine karşılık gelen bir **[bulut akademik My-SSO test kullanıcısı oluşturun](#create-a-cloud-academy-test-user)** .
1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. Azure portal, **Cloud akademik My-SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünde **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem düğmesini seçin:

   ![Temel SAML yapılandırmasını düzenlemenin kalem düğmesini gösteren ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusuna aşağıdaki URL 'lerden birini yazın:
    
    | Oturum açma URL'si |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. **Yanıt URL 'si** metin kutusuna aşağıdaki URL 'lerden birini yazın:
    
    | Yanıt URL'si |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak için Kopyala düğmesini seçin. URL 'YI kaydedin.

    ![Uygulama Federasyon meta veri URL 'SI için Kopyala düğmesini gösteren ekran görüntüsü.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' ı seçin. **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları uygulayın:
   1. **Ad** kutusuna **B. Simon** girin.  
   1. **Kullanıcı adı** kutusuna \<username> @ \<companydomain> .. yazın. \<extension> Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="grant-access-to-the-test-user"></a>Test kullanıcısına erişim izni verme

Bu bölümde, bu kullanıcıya bulut akademik mi-SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Cloud akademik My-SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünde, **Kullanıcılar ve gruplar**' ı seçin:
1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin:
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Bulut akademik My için çoklu oturum açmayı yapılandırma

1. Farklı bir tarayıcı penceresinde, bulut akademik My-SSO şirket sitesinde yönetici olarak oturum açın.

1. Şirketinizin adını seçin ve ardından açılan menüdeki **ayarlar & tümleştirmeler** ' i seçin:

    ![Ayarlar & tümleştirmeler seçeneğini gösteren ekran görüntüsü.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. **Ayarlar & tümleştirmeler** sayfasında, **tümleştirmeler** sekmesinde **SSO** kartını seçin:

    ![Tümleştirme sekmesindeki SSO kartını gösteren ekran görüntüsü.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Bu sayfada aşağıdaki adımları uygulayın:

    ![Inegrations > SSO sayfasını gösteren ekran görüntüsü.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. **VARLıK kimliği URL 'si** kutusuna, Azure Portal KOPYALADıĞıNıZ varlık kimliği değerini girin.

    b. **SSO URL 'si** kutusunda, Azure Portal kopyaladığınız oturum açma URL 'si değerini yapıştırın.

    c. İndirilen Base64 sertifikasını Not defteri 'ndeki Azure portal açın. İçeriğini **sertifika** kutusuna yapıştırın.

    d. **Ad kimliği biçimi** kutusunda varsayılan değeri tutun: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. **Kaydet**’i seçin.

    > [!NOTE]
    > Cloud akademik My-SSO yapılandırma hakkında daha fazla bilgi için bkz. [Çoklu oturum açmayı ayarlama](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Bir bulut akademik test kullanıcısı oluşturma

Bu bölümde, bulut akademik My-SSO 'da Britta Simon adlı bir Kullanıcı oluşturulmuştur. Cloud akademik My-SSO, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı bulut akademik My-SSO 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz bulut akademik oturum açma URL 'sine yeniden yönlendirilir. 

* Cloud akademik My-SSO oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Cloud akademik My-SSO kutucuğuna tıkladığınızda bu, bulut akademik oturum açma URL 'SI ile yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Cloud akademik My-SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).