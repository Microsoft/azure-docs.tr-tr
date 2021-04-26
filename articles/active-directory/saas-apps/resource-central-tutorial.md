---
title: 'Öğretici: Kaynak Merkezi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve kaynak merkezi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586514"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Öğretici: Kaynak Merkezi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide Resource Central 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Kaynak Merkezi 'ni Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de kaynak merkezi 'ne erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla kaynak merkezi 'nde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Kaynak Merkezi çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Kaynak Merkezi, **SP** tarafından başlatılan SSO 'yu destekler

* Kaynak Merkezi **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="add-resource-central-from-the-gallery"></a>Galeriden kaynak merkezi ekleme

Kaynak Merkezi 'nin Azure AD 'ye tümleştirilmesini yapılandırmak için galerideki kaynak merkezi 'ni yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **Kaynak Merkezi**' ni girin.
1. Sonuçlar panelinden **Kaynak Merkezi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Kaynak Merkezi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu kaynak merkezi ile yapılandırın ve test edin. SSO 'nun çalışması için, kaynak merkezi 'nde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu kaynak merkezi ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
    1. Kaynak Merkezi 'nde, kullanıcının Azure AD gösterimine bağlı olan B. Simon 'a sahip olmak için **[Kaynak Merkezi test kullanıcısı oluşturun](#create-resource-central-test-user)** .
1. **[Kaynak MERKEZI SSO 'Yu yapılandırma](#configure-resource-central-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Kaynak Merkezi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması**' nda, aşağıdaki alanlar için değerleri girin:

   1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN_NAME>/ResourceCentral`

   1. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN_NAME>/ResourceCentral`

   1. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Bu değerler değişmez değer değerleri değildir. Bu değerleri, gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'SI değerleriyle güncelleştirin. Bu değerleri almak için [Kaynak Merkezi istemci desteği ekibine](mailto:st@aod.vn) başvurun.  Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası**' nda **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Kaynak Merkezi 'Ni ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin `username@companydomain.extension` . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, kaynak merkezi 'ne erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Kaynak Merkezi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** listesinden **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç**' de bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, **varsayılan erişim** rolü seçili ' i görürsünüz.
1. **Atama Ekle** bölmesinde **ata** düğmesine tıklayın.

### <a name="create-resource-central-test-user"></a>Kaynak Merkezi test kullanıcısı oluştur

Bu bölümde, **Kaynak Merkezi**'nde **B. Simon** adlı bir Kullanıcı oluşturulur.

1. Kaynak Merkezi 'nde **güvenlik**  >  **kişileri**  >  **Yeni**' yi seçin.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Yeni Düğme vurgulanmış şekilde kaynak merkezi 'ndeki kişiler bölmesini gösteren ekran görüntüsü.":::

1. **Kişi ayrıntıları**' nda, **görünen ad** Için **B. Simon** kullanıcısını girin. **SMTP adresi** Için kullanıcının Azure AD Kullanıcı adını girin. Örneğin, `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Kaynak Merkezi 'nde kişi ayrıntıları bölmesini gösteren ekran görüntüsü.":::

## <a name="configure-resource-central-sso"></a>Kaynak Merkezi SSO 'yu yapılandırma

Bu bölümde, **Kaynak Merkezi Sistem Yöneticisi**'nde çoklu oturum açmayı yapılandıracaksınız.

1. Kaynak Merkezi Sistem Yöneticisi ' nde **dış kimlik doğrulaması**' nı seçin.
1.  **Yapılandırmayı Etkinleştir** için **Evet**' i seçin.

    ![Kaynak Merkezi 'nde dış kimlik doğrulaması bölmesinde seçilen yapılandırmayı etkinleştir seçeneğinin gösterildiği ekran görüntüsü.](./media/resource-central/enable.png)

1. **Kimlik doğrulama protokolü**' nde **SAML2**' yi seçin. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Kaynak Merkezi 'nde kimlik doğrulama protokolü için seçili SAML2 gösteren ekran görüntüsü.":::

1. **SAML2 yapılandırması** altında, aşağıdaki alanlar için değerleri girin:

    1. **Tanımlayıcı (VARLıK kimliği)**, **oturum açma URL**'SI, oturum **kapatma URL 'si** ve **Azure AD tanımlayıcısı** için ilgili URL 'leri girin:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Kaynak Merkezi 'nde SAML2 yapılandırma bölmesinin ekran görüntüsü.":::

        **Kaynak Merkezi** bölmesindeki URL 'leri kopyalayın:

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Kaynak Merkezi 'nde kaynak merkezi bölmesini ayarlama ekran görüntüsü.":::

   1. **Dönüş URL 'si** için girin `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. **Sertifika** için sertifikanızı karşıya yükleyin ve parolanızı girin.

   ![Kaynak Merkezi 'nde sertifika bölümünün ekran görüntüsü.](./media/resource-central/cert.png)
   
1. **Kaydet**’i seçin.

1. **Azure portalına** geri dönün. **SAML Imzalama sertifikası**' nda, sertifikanızı karşıya yükleyin ve parolanızı girin.

   ![Azure portal sertifika Içeri aktarma bölmesinin ekran görüntüsü.](./media/resource-central/cert2.png).

1. **Add (Ekle)** seçeneğini belirleyin.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde Azure AD çoklu oturum açma yapılandırmanızı test edersiniz. Çoklu oturum açmayı test etmek için üç seçeneğiniz vardır:

* Azure portal **Bu uygulamayı test et**' i seçin. Bağlantı, oturum açma işlemini başlatabileceğiniz kaynak merkezi oturum açma URL 'sine yeniden yönlendirir.

* Kaynak Merkezi oturum açma URL 'sine doğrudan gidin ve oturum açma işlemini başlatın.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Kaynak Merkezi çoklu oturum açma testi Web sayfasının ekran görüntüsü.":::

* Microsoft 'tan My Apps portalını kullanın. Uygulamalarım portalında kaynak merkezi oturum açma URL 'sine yeniden yönlendirmek için **Kaynak Merkezi** kutucuğunu seçin. Daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Kaynak Merkezi 'ni Azure AD ile çoklu oturum açma için ayarladıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve ölçeğini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
