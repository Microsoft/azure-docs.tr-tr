---
title: 'Öğretici: halka merkezi ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve RingCentral arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 3c8126c051457d740d1a5414a130b4ee3fffcd8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954145"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile halka merkezi tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile halka merkezi tümleştirme hakkında bilgi edineceksiniz. Halka merkezi 'ni Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, halka merkezine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesapları ile RingCentral 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Halka merkezi çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* RingCentral, **IDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-ringcentral-from-the-gallery"></a>Galeriden halka merkezi ekleme

RingCentral 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden halka merkezi 'ni yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **RingCentral** yazın.
1. Sonuçlar panelinden **RingCentral** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Azure AD SSO 'yu halka Merkezi için yapılandırma ve test etme

Azure AD SSO 'yu, **Britta Simon** adlı bir test kullanıcısı kullanarak halka merkezi ile yapılandırın ve test edin. SSO 'nun çalışması için, halka merkezi 'nde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu RingCentral ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[RingCentral SSO 'Yu yapılandırın](#configure-ringcentral-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan halka merkezi 'nde B. Simon 'a sahip olmak için, **[halka merkezi test kullanıcısı oluşturun](#create-ringcentral-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **halka merkezi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    1. **Meta veri dosyasını karşıya yükle**' ye tıklayın.
    1. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.
    1. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur.

    > [!Note]
    > **Hizmet sağlayıcı meta veri dosyasını** , öğreticide daha sonra açıklanan halka merkezi SSO yapılandırma sayfasında alırsınız.

1. **Hizmet sağlayıcısı meta verileri dosyanız** yoksa, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna URL 'lerden birini yazın:
  
    | Tanımlayıcı |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. **Yanıt URL 'si** metin kutusuna URL 'lerden birini yazın:

    | Yanıt URL'si |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, halka merkezine erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **RingCentral**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-ringcentral-sso"></a>Halka merkezi SSO 'yu yapılandırma

1. RemoteApp Central 'daki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya uzantı ekledikten sonra, **RingCentral 'ı ayarla** ' ya tıklayarak sizi halka merkezi uygulamasına yönlendirebilirsiniz. Buradan, halka merkezi 'nde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Halka merkezi 'ni el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve halka Merkezi şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Üstteki **Araçlar**' a tıklayın.

    ![Ekran görüntüsü, halka Merkezi şirket sitesinden seçilen araçları gösterir.](./media/ringcentral-tutorial/ringcentral-1.png)

1. **Çoklu oturum açma '** ya gidin.

    ![Ekran görüntüsü Araçlar menüsünden tek Sign-On seçili olduğunu gösterir.](./media/ringcentral-tutorial/ringcentral-2.png)

1. **Çoklu oturum açma** sayfasında, **SSO yapılandırma** bölümünde, **1. adım** ' da **Düzenle** ' ye tıklayın ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, Düzenle ' yi seçebileceğiniz S S O yapılandırma sayfasını gösterir.](./media/ringcentral-tutorial/ringcentral-3.png)

1. **Çoklu oturum açma ayarlama** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, ı D P meta verilerini karşıya yükleyebileceğiniz tek Sign-On ayarlama sayfasını gösterir.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Azure portal indirdiğiniz meta veri dosyasını karşıya yüklemek için, **Araştır** ' a tıklayın.

    b. Meta verileri karşıya yükledikten sonra, **SSO genel bilgileri** bölümünde değerler otomatik olarak doldurulur.

    c. **Öznitelik eşleme** bölümünde, **e-posta özniteliğini Eşleştir** ' i seçin`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Kaydet**’e tıklayın.

    e. **2. adımda** , **hizmet sağlayıcısı meta veri dosyasını** indirmek için **indir** ' e tıklayın ve Azure Portal **tanımlayıcı** ve **yanıt URL 'si** değerlerini otomatik olarak doldurmak için **temel SAML yapılandırması** bölümüne yükleyin.

    ![Ekran görüntüsü, Indir ' i seçebileceğiniz S S O yapılandırma sayfasını gösterir.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Aynı sayfada, **SSO 'Yu etkinleştir** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, yapılandırmayı bitirebileceğiniz S S O bölümünü etkinleştirir.](./media/ringcentral-tutorial/ringcentral-5.png)

    * **SSO hizmetini etkinleştir**' i seçin.

    * **KULLANıCıLARıN SSO veya halka merkezi kimlik bilgileriyle oturum açmasına Izin ver '** i seçin.

    * **Kaydet**’e tıklayın.

### <a name="create-ringcentral-test-user"></a>Halka merkezi test kullanıcısı oluşturma

Bu bölümde, halka merkezi 'nde Britta Simon adlı bir Kullanıcı oluşturacaksınız. RingCentral platformunda kullanıcıları eklemek için [RingCentral istemci destek ekibi](https://success.ringcentral.com/RCContactSupp) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız halka merkezinde otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın halka merkezi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız halka merkezinde otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

RingCentral 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).