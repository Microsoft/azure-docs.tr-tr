---
title: 'Öğretici: Boomı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Boomı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: c58566c628eedd1dbc3d86ae6a142156cbf31211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585239"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Öğretici: Boomı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Boomı tümleştirme hakkında bilgi edineceksiniz. Boomı 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Boomı 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına izin vermek için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Boomı çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Boomı, **IDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-boomi-from-the-gallery"></a>Galeriden Boomı ekleme

Boomı 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize bir Boomı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **boomı** yazın.
1. Sonuçlar panelinden **Boomı** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Boomı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu boomı ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Boomı içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Boomı ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Boomı SSO 'Yu yapılandırın](#configure-boomi-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan, Boomi 'da bir B. Simon 'ya sahip olmak için, **[boomı test kullanıcısı oluşturun](#create-boomi-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Boomi** uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa ve **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur.

    d. **Oturum açma URL 'sini**(gibi) girin `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}` .

    > [!Note]
    > **Hizmet sağlayıcı meta veri dosyasını** , Öğreticinin ilerleyen kısımlarında açıklanan, **boomı SSO 'yu Yapılandır** bölümünden alacaksınız. **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmazsa, değerleri gereksinimlerinize göre el ile girin.

1. Boomı uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Ekran görüntüsü, kullanıcı özniteliklerinin & taleplerini Kullanıcı. Ise ve Emaadresi Kullanıcı. Mail gibi varsayılan değerlerle gösterir.](common/default-attributes.png)

1. Daha fazlasına ek olarak, Boomı uygulaması aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ---------------|  --------- |
    | FEDERATION_ID | Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Boomi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, B. Simon 'u, Boomi 'ya erişim vererek Azure çoklu oturum açma özelliğini etkinleştirecek şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Boomi**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-boomi-sso"></a>Boomı SSO yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Boomı şirket sitenizde yönetici olarak oturum açın.

1. **Şirket adı** ' na gidin ve **Ayarla**' ya gidin.

1. **SSO seçenekleri** sekmesine tıklayın ve aşağıdaki adımları gerçekleştirin.

    ![Uygulama tarafında tek Sign-On yapılandırma](./media/boomi-tutorial/import.png)

    a. **SAML çoklu oturum açmayı etkinleştir** onay kutusunu işaretleyin.

    b. Azure AD 'den **kimlik sağlayıcısı sertifikasına** indirilen sertifikayı yüklemek Için **içeri aktar** ' a tıklayın.

    c. **Kimlik sağlayıcısı oturum açma URL 'si** metin kutusunda, **oturum açma URL 'SI** değerini Azure AD uygulama yapılandırma penceresinden koyun.

    d. **Federasyon kimliği konumu** Için **FEDERATION_ID öznitelik öğesi** radyo düğmesini seçin.

    e. **Atomsphere meta veri URL 'sini** kopyalayın, tercih ettiğiniz tarayıcı aracılığıyla **meta veri URL** 'sine gidin ve çıktıyı bir dosyaya kaydedin. **Meta veri URL 'sini** Azure Portal **temel SAML yapılandırması** bölümüne yükleyin.

    f. **Kaydet** düğmesine tıklayın.

### <a name="create-boomi-test-user&quot;></a>Boomı test kullanıcısı oluştur

Azure AD kullanıcılarının, Boomı 'da oturum açmasını etkinleştirmek için, bu kullanıcıların Boomı 'da sağlanması gerekir. Boomi durumunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

### <a name=&quot;to-provision-a-user-account-perform-the-following-steps&quot;></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. Bir yönetici olarak Boomı şirket sitenizde oturum açın.

1. Oturum açtıktan sonra **Kullanıcı yönetimi** ' ne gidin ve **Kullanıcılar**' a gidin.

    ![Ekran görüntüsü, kullanıcıların seçtiği Kullanıcı Yönetimi sayfasını gösterir.](./media/boomi-tutorial/user.png &quot;Kullanıcılar")

1. Simge ' ye tıklayın **+**  ve **Kullanıcı rolleri ekle/koru** iletişim kutusu açılır.

    ![Ekran görüntüsü seçili + simgesini gösterir.](./media/boomi-tutorial/add.png "Kullanıcılar")

    ![Ekran görüntüsü, bir Kullanıcı yapılandırdığınız Kullanıcı rollerini Ekle/koru ' yı gösterir.](./media/boomi-tutorial/roles.png "Kullanıcılar")

    a. **Kullanıcı e-posta adresi** metin kutusuna kullanıcının e-postasını yazın B.Simon@contoso.com .

    b. **Ad** metin kutusuna B gibi ilk Kullanıcı adını yazın.

    c. **Soyadı** metin kutusunda, Simon gibi kullanıcı adının soyadını yazın.

    d. Kullanıcının **Federasyon kimliğini** girin. Her kullanıcının hesabı içinde kullanıcıyı benzersiz bir şekilde tanımlayan bir Federasyon KIMLIĞI olmalıdır.

    e. Kullanıcıya **Standart Kullanıcı** rolünü atayın. Yönetici rolünü atamayın çünkü bu, bunlara normal Atmosphere erişimine ek olarak çoklu oturum açma erişimi de verecektir.

    f. **Tamam**'a tıklayın.

    > [!NOTE]
    > Kullanıcı, parolaları kimlik sağlayıcısı aracılığıyla yönetildiğinden, AtomSphere hesabında oturum açmak için kullanılabilecek bir parola içeren bir hoş geldiniz bildirim e-postası almaz. AAD Kullanıcı hesapları sağlamak için, Boomı tarafından sunulan diğer bir Boomı Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Boomi 'da otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Boomı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Boomı 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Boomı 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).