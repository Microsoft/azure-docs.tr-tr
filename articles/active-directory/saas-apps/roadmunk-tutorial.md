---
title: 'Öğretici: Roadmunk ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory'
description: Azure Active Directory ve Roadmunk arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181734"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Öğretici: Roadmunk ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Roadmunk 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Roadmunk 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Roadmunk 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarını kullanarak Roadmunk ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) için etkinleştirilen bir Roadmunk aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

Roadmunk, *hizmet sağlayıcısı* (SP) ve *kimlik sağlayıcısı* (IDP) tarafından başlatılan SSO 'yu destekler.

## <a name="add-roadmunk-from-the-gallery"></a>Galeriden Roadmunk ekleme

Roadmunk 'i Azure AD 'ye tümleştirmek için, Galeriden, yönetilen SaaS uygulamaları listenize Roadmunk ekleyin:

1. İş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **Roadmunk** yazın.
1. Sonuçlardan **Roadmunk** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Roadmunk için Azure AD SSO 'yu yapılandırma ve test etme

*B. Simon* adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Roadmunk ile yapılandırın ve test edin. SSO iş yapmak için, Roadmunk içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Roadmunk ile yapılandırma ve test etme konusunda genel bir bakış aşağıda verilmiştir:

1. Kullanıcılarınızın bu özelliği kullanabilmesi için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon kullanarak Azure AD SSO 'yu test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD SSO 'yu kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için [ROADMUNK SSO 'Yu yapılandırın](#configure-roadmunk-sso) .
    1. Roadmunk ' de B. Simon 'ın karşılığına kullanıcının Azure AD gösterimine bağlayabilmeniz için [bir Roadmunk test kullanıcısı oluşturun](#create-roadmunk-test-user) .
1. Yapılandırmanın çalıştığından emin olmak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. Azure portal, **Roadmunk** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırması düzenleme simgesini gösteren ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, bir SP meta veri dosyanız varsa ve IDP tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

    a. **Meta veri dosyasını karşıya yükle**' yi seçin.

    ![Karşıya yükleme meta veri dosyası bağlantısını gösteren ekran görüntüsü.](common/upload-metadata.png)

    b. "Roadmunk SSO yapılandırma" yordamının 4. adımında indirdiğiniz meta veri dosyasını seçmek için klasör simgesini seçin. Ardından **Karşıya Yükle**’yi seçin.

    ![Meta veri dosyasının nasıl seçileceğini gösteren ekran görüntüsü.](common/browse-upload-metadata.png)

    Meta veri dosyası karşıya yüklendikten sonra, **temel SAML yapılandırması** bölümünde **tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulur.

    ![Temel SAML yapılandırması bölümünü gösteren ekran görüntüsü. Tanımlayıcı alanı ve yanıt URL 'SI alanı vurgulanır.](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmamışsa, değerleri el ile girin.

1. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla**' yı seçin. **Oturum açma URL 'si** alanında, şunu yazın`https://login.roadmunk.com`

    ![SP tarafından başlatılan mod için bir oturum açma URL 'sinin nereye ayarlanacağını gösteren ekran görüntüsü.](common/metadata-upload-additional-signon.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i bulun. Ardından, sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![SAML imzalama sertifikası için indirme bağlantısını gösteren ekran görüntüsü.](common/metadataxml.png)

1. **Roadmunk ayarla** bölümünde, gerek duyduğunuz URL veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerinin nereye kopyalanacağını gösteren ekran görüntüsü.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal bir test kullanıcısı oluşturacaksınız. *B. Simon* kullanıcısını adlandırın.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Pencerenin üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com` girin.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Roadmunk erişimi vererek Azure SSO 'yu kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Roadmunk**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde, **B. Simon**' u seçin. Sonra iletişim kutusunun alt kısmında **Seç**' i seçin.
1. Kullanıcılara bir rolün atanmasını beklemeniz durumunda, **rol seçin** açılır menüsünde bunu seçin. Bu uygulama için ayarlanmış bir rol yoksa, **varsayılan erişim** rolü seçilidir.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-roadmunk-sso"></a>Roadmunk SSO 'yu yapılandırma

1. Roadmunk Web sitesinde yönetici olarak oturum açın.

1. Sayfanın en altında, Kullanıcı simgesini seçin ve ardından **Hesap ayarları**' nı seçin.

    ![Kullanıcı hesabı ayarlarının nerede seçileceğini gösteren ekran görüntüsü.](./media/roadmunk-tutorial/account.png)

1. **Şirket**  >  **kimlik doğrulama ayarları**' na gidin.

1. **Kimlik doğrulama ayarları** sayfasında, şu adımları izleyin:

    ![Kimlik doğrulama ayarları sayfasını gösteren ekran görüntüsü.](./media/roadmunk-tutorial/saml-sso.png)

    a. **SAML çoklu oturum açma (SSO)** öğesini açın.

    b. 1. **adım** bölümünde meta veri xml dosyasını karşıya yükleyin veya meta verilerin URL 'sini sağlayın.

    c. 2. **adım** bölümünde, Roadmunk meta veri dosyasını indirin ve bilgisayarınıza kaydedin.

    d. SSO kullanarak oturum açmak isterseniz, **Adım 3** bölümünde **yalnızca SAML Sign-In zorla**' yı seçin.

    e. **Kaydet**’i seçin.


### <a name="create-roadmunk-test-user"></a>Roadmunk test kullanıcısı oluştur

1. Roadmunk Web sitesinde yönetici olarak oturum açın.

1. Sayfanın altındaki Kullanıcı simgesini seçin ve ardından **Hesap ayarları**' nı seçin.

    ![Test kullanıcısı için hesap ayarlarının nasıl açılacağını gösteren ekran görüntüsü.](./media/roadmunk-tutorial/account.png)

1. **Kullanıcılar** sekmesini açın ve ardından **kullanıcıyı davet et**' i seçin.

    ![Kullanıcılar sekmesini gösteren ekran görüntüsü. Kullanıcıyı davet et düğmesi vurgulanır. Açık pencerede, e-posta ve rol alanları vurgulanır.](./media/roadmunk-tutorial/create-user.png)

1. Görüntülenen formda, gerekli bilgileri girin ve ardından **davet et**' i seçin.


## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD SSO yapılandırmanızı test edersiniz.

Uygulamalarım portalındaki **Roadmunk** kutucuğunu SEÇTIĞINIZDE, SSO 'Yu ayarladığınız Roadmunk hesabında otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Roadmunk yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi, kuruluşunuzun hassas verilerinin boyutunu gerçek zamanlı olarak korur. Oturum denetimi koşullu erişimden genişletiliyor. 

[Microsoft Cloud App Security kullanarak oturum denetimini nasıl zorlayacağınızı](/cloud-app-security/proxy-deployment-any-app)öğrenin.