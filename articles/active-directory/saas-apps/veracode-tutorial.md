---
title: 'Öğretici: Veracode | ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ile Veracode arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Öğretici: Veracode ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Veracode 'u Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Veracode 'u Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Veracode erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir Veracode çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Veracode, kimlik sağlayıcısı tarafından başlatılan SSO ve Just-In-Time Kullanıcı sağlamasını destekler.

## <a name="add-veracode-from-the-gallery"></a>Galeriden Veracode ekleyin

Veracode 'un Azure AD ile tümleştirilmesini yapılandırmak için Galeriden yönetilen SaaS uygulamaları listenize Veracode ekleyin.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna "Veracode" yazın.
1. Sonuçlar panelinden **Veracode** ' u seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Veracode için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Veracode ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD SSO 'yu Veracode ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    * B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    * Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Veracode SSO 'Yu yapılandırın](#configure-veracode-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Veracode 'da B. Simon 'a karşılık gelen bir **[Veracode test kullanıcısı oluşturun](#create-veracode-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Veracode** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. **Kaydet**’i seçin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)**. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Indirme bağlantısı vurgulanmış şekilde SAML Imzalama sertifikası bölümünün ekran görüntüsü](common/certificatebase64.png)

1. Veracode, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Kullanıcı özniteliklerinin & talepler bölümünün ekran görüntüsü](common/default-attributes.png)

1. Veracode, SAML yanıtına daha fazla özniteliğin geri geçirilmesini de bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ---------------| --------------- |
    | FirstName |Kullanıcı. |
    | Soyadı |User. soyadı |
    | e-posta |Kullanıcı. Mail |

1. **Kurulum Veracode** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Leri vurgulanmış şekilde ayarlama Veracode bölümünün ekran görüntüsü](common/copy-configuration-urls.png)

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

Bu bölümde, Veracode 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Veracode**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-veracode-sso&quot;></a>Veracode SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Veracode şirket sitenizde oturum açın.

1. Üstteki menüden **Ayarlar**  >  **Yöneticisi**' ni seçin.
   
    ![Ayarlar simgesi ve yönetici vurgulanmış şekilde Veracode yönetiminin ekran görüntüsü](./media/veracode-tutorial/admin.png &quot;Yönetim")

1. **SAML** sekmesini seçin.

1. **Kuruluş SAML ayarları** bölümünde aşağıdaki adımları uygulayın:

    ![Kuruluş SAML ayarları bölümünün ekran görüntüsü](./media/veracode-tutorial/saml.png "Yönetim")

    a.  **Veren** için, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    b. **Onaylama Imza sertifikası** için Azure Portal indirilen sertifikanızı karşıya yüklemek Için **Dosya Seç** ' i seçin.

    c. **Kendi kendine kayıt** Için, **kendinden kaydolmayı etkinleştir**' i seçin.

1. **Kendi kendine kayıt ayarları** bölümünde aşağıdaki adımları gerçekleştirin ve ardından **Kaydet**' i seçin:

    ![Çeşitli seçenekler vurgulanmış şekilde kendi kendine kayıt ayarları bölümünün ekran görüntüsü](./media/veracode-tutorial/save.png "Yönetim")

    a. **Yeni Kullanıcı etkinleştirmesi** Için **etkinleştirme gerekli değil**' i seçin.

    b. **Kullanıcı veri güncelleştirmeleri** Için **tercih Veracode Kullanıcı verileri**' ni seçin.

    c. **SAML öznitelik ayrıntıları** için aşağıdakileri seçin:
      * **Kullanıcı rolleri**
      * **İlke Yöneticisi**
      * **Geçirenlerin**
      * **Güvenlik lideri**
      * **Yönetici**
      * **Gönderenin**
      * **Oluşturucu**
      * **Tüm tarama türleri**
      * **Takım üyelikleri**
      * **Varsayılan takım**

### <a name="create-veracode-test-user"></a>Veracode test kullanıcısı oluşturma

Bu bölümde, Veracode 'da B. Simon adlı bir Kullanıcı oluşturulur. Veracode, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Veracode 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için Veracode tarafından sunulan diğer herhangi bir diğer Veracode Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Veracode 'da otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Veracode kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Veracode 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Veracode 'u yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).