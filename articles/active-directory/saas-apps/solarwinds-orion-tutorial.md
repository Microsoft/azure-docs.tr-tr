---
title: 'Öğretici: SolarWinds Orion ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve SolarWinds Orion arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591393"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Öğretici: SolarWinds Orion ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, SolarWinds Orion 'u Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SolarWinds Orion 'U Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SolarWinds Orion 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SolarWinds Orion 'a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SolarWinds Orion çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SolarWinds Orion **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Galeriden SolarWinds Orion ekleme

SolarWinds Orion 'un Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden SolarWinds Orion 'u yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Solarwinds Orion** yazın.
1. Sonuçlar panelinden **Solarwinds Orion** ' u seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>SolarWinds Orion için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Solarwinds oriwith ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve SolarWinds Orion içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SolarWinds Orion ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Solarwinds ORION SSO 'Yu yapılandırın](#configure-solarwinds-orion-sso)** .
    1. User 'ın Azure AD gösterimine bağlı olan SolarWinds Orion 'da B. Simon 'ya sahip olmak için **[Solarwinds Orion test kullanıcısına oluşturun](#create-solarwinds-orion-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Solarwinds Orion** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Solarwinds Orion istemci destek ekibine](mailto:technicalsupport@solarwinds.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. SolarWinds Orion uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. SolarWinds Orion uygulamasına ek olarak, aşağıda gösterilen SAML yanıtına birkaç özniteliğin daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | ----------- | --------- |
    | FirstName | Kullanıcı. |
    | LastName | User. soyadı |
    | E-posta |Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Solarwinds Orion ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, her ne kadar yeşil 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, her **yaprak yeşil**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-solarwinds-orion-sso"></a>SolarWinds Orion SSO 'yu yapılandırma

1. Solarwinds Orion 'da oturum açın ve **Ayarlar**  ->  **Tüm ayarlar**' a gidin.

    ![Ekran görüntüsü ayarlardan seçilen tüm ayarları gösterir.](./media/solarwinds-orion-tutorial/settings.png)

1. **Kullanıcı hesapları** bölümünde **SAML yapılandırması**' nı seçin.

    ![Ekran görüntüsü Kullanıcı hesaplarından seçilen SAML yapılandırmasını gösterir.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. **KIMLIK sağlayıcısı ekle**' ye tıklayın.

    ![Ekran görüntüsü, KIMLIK sağlayıcısı ekle ' yi seçebileceğiniz SAML yapılandırmasını gösterir.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. **Kimlik sağlayıcısı ekle** sayfasında aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz kimlik sağlayıcısı Ekle sayfasını gösterir.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. **Yapılandır** sekmesine gidin.

    b. **Kimlik sağlayıcısı adı** metin kutusunda, gibi geçerli bir ad verin `My SSO service` .

    c. **SSO hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d.  **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    e. İndirilen **sertifikayı (base64)** Azure Portal Not defteri ' nden açın ve içeriği **X. 509.952 imza sertifikası** metin kutusuna yapıştırın.

    f. **Kaydet**'e tıklayın.

### <a name="create-solarwinds-orion-test-user"></a>Test kullanıcısı üzerinde SolarWinds Orioluştur

1. Solarwinds Orion Web sitesinde oturum açın ve **Ayarlar**  ->  **Tüm ayarlar**' a gidin.

    ![Ekran görüntüsü ayarlardan seçilen tüm ayarları gösterir.](./media/solarwinds-orion-tutorial/settings.png)

1. **Kullanıcı hesapları** bölümünde **hesapları Yönet**' i seçin.

    ![Ekran görüntüsü SAML yapılandırmasını göster seçili.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. **BIREYSEL hesaplar** SEKMESINDE **Yeni Hesap Ekle**' ye tıklayın.

    ![Ekran görüntüsü, hesapları Yönet ' de seçilen yenı hesap Ekle ' ye gösterilir](./media/solarwinds-orion-tutorial/create-user.png)

1. SAML bireysel kullanıcı veya grup oluşturmanız gereken hesap türünü seçin.

    ![Ekran görüntüsü, hesap türünü seçebileceğiniz yeni hesap Ekle ' yi gösterir.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  **Ad kimliği** metin kutusuna, Azure AD 'de olduğu gibi, Kullanıcı adı veya grup adıyla eşleşmesi gereken adı girin.

1.  **İleri** ' ye tıklayın ve ardından sayfayı iletin.

    ![Ekran görüntüsü yeni hesap Ekle ' ye, Azure A 'dan d. d adını girebileceğiniz yeni hesabı gösterir.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SolarWinds Orion oturum açma URL 'sine yönlendirecektir.  

* Oturum açma URL 'sine doğrudan SolarWinds Cuma gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Solarwinds Orion 'da otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım üzerinde SolarWinds Orion kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız SolarWinds Orion için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

SolarWinds Orion 'U yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
