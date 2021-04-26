---
title: 'Öğretici: bağlantı noktası \ bağlantı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve, Ntakct arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736993"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile yerleşik olarak tümleştirme

Bu öğreticide, u 'nin Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. U 'nin Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, o Taktacct erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla şirket içinde yerleşik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* , Tek oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Ise Intacct, **IDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-sage-intacct-from-the-gallery"></a>Galeriden NtAcct ekleme

Nıntacct 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **bağla Intacct** yazın.
1. Sonuçlar panelinden, **It** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>It Intacct için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Azure AD SSO 'Yu, It Intacct ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile bağlantılı Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, It Intacct ile yapılandırmak ve test etmek için aşağıdaki adımları izleyin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, o **[Intacct SSO 'Yu yapılandırın](#configure-sage-intacct-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, nıntacct 'da B. Simon 'a sahip olmak için, o şirket içi **[test kullanıcısı oluşturun](#create-sage-intacct-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **yerleşik uygulama tümleştirmesi** sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Yanıt URL** 'si metin kutusuna bir URL yazın:`https://www.intacct.com/ia/acct/sso_response.phtml`

1. E Intacct uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, e Intacct uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Kullanıcı öznitelikleri & talepler** iletişim kutusunda aşağıdaki tabloda GÖSTERILDIĞI gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Öznitelik adı  |  Kaynak özniteliği|
    | ---------------| --------------- |
    | Şirket Adı | **Şirket Içi şirket KIMLIĞI** |
    | name | Değer, öğreticide daha sonra **açıklanacak olan IT Intacct test kullanıcısına** girdiğiniz **nıntacct kullanıcı kimliğiyle** aynı olmalıdır |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın veya seçin.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. J şirket içinde **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, B. Simon 'u, e-postayla erişim izni vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **zu Intacct**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-sage-intacct-sso&quot;></a>Zu Intacct SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, şirket içi şirket sitenizde yönetici olarak oturum açın.

1. **Şirket** sekmesine tıklayın ve ardından **Şirket bilgileri**' ne tıklayın.

    ![Şirket](./media/intacct-tutorial/ic790037.png &quot;Şirket")

1. **Güvenlik** sekmesine tıklayın ve ardından **Düzenle**' ye tıklayın.

    ![Güvenlik](./media/intacct-tutorial/ic790038.png "Güvenlik")

1. **Çoklu oturum açma (SSO)** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma](./media/intacct-tutorial/ic790039.png "Çoklu oturum açma")

    a. **Çoklu oturum açmayı etkinleştir '** i seçin.

    b. **Kimlik sağlayıcısı türü** olarak **SAML 2,0**' i seçin.

    c. **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    d. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Base-64** kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından **sertifika** kutusuna yapıştırın.

    f. **Kaydet**’e tıklayın.

### <a name="create-sage-intacct-test-user"></a>It Intacct test kullanıcısı oluştur

Azure AD kullanıcılarını, o şekilde çalışır durumda oturum açabilirler. böylece, bu kişiler, It 'nin içine sağlanması gerekir. O Ntakct için, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı hesaplarını sağlamak için aşağıdaki adımları uygulayın:**

1. E- **Tacct** kiracınızda oturum açın.

1. **Şirket** sekmesine tıklayın ve ardından **Kullanıcılar**' a tıklayın.

    ![Kullanıcılar](./media/intacct-tutorial/ic790041.png "Kullanıcılar")

1. **Ekle** sekmesine tıklayın.

    ![Ekle](./media/intacct-tutorial/ic790042.png "Ekle")

1. **Kullanıcı bilgileri** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda bilgileri girebileceğiniz Kullanıcı bilgileri bölümünü gösterir.](./media/intacct-tutorial/ic790043.png "Kullanıcı bilgileri")

    a. Kullanıcı **kimliği**, **Soyadı**, **adı, ad**, **e-posta adresi**, **başlık** ve **Kullanıcı bilgileri** bölümüne sağlamak istediğiniz bir Azure AD hesabının **telefonunu** girin.

    > [!NOTE]
    > Yukarıdaki ekran görüntüsünde **Kullanıcı kimliğinin** ve Azure Portal Içindeki **Kullanıcı öznitelikleri** bölümünde **ad** özniteliğiyle eşlenen **kaynak öznitelik** değerinin aynı olması gerekir.

    b. Sağlamak istediğiniz bir Azure AD hesabının **yönetici ayrıcalıklarını** seçin.

    c. **Kaydet**’e tıklayın. 
    
    d. Azure AD hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

1. **Çoklu oturum açma** sekmesi ' ne tıklayın ve aşağıda yer alan **Federasyon SSO kullanıcı kimliğinin** ve Azure Portal içindeki Kullanıcı öznitelikleri bölümünde ile eşlenmiş **kaynak öznitelik** değerinin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` aynı olması gerekir. 

    ![Ekran görüntüsü, Federal S S O Kullanıcı g/ç 'sini girebileceğiniz Kullanıcı bilgileri bölümünü gösterir.](./media/intacct-tutorial/ic790044.png "Kullanıcı bilgileri")

> [!NOTE]
> Azure AD Kullanıcı hesaplarını sağlamak için, diğer şirket Içi Kullanıcı hesabı oluşturma araçlarını veya,,, m/

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız yerleşik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' daki e Intacct kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız yerleşik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Şirket \ ' i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).