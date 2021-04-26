---
title: 'Öğretici: Launchkaranlık ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve başlatma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954811"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Öğretici: Launchkaranlık ile tümleştirme Azure Active Directory

Bu öğreticide, Launchkaranlık 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Launchkaranlık 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Launchkaranlık 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte kullanmaya devam etmek için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

    > [!NOTE]
    > Launchkaranlık Azure Active Directory tümleştirme tek bir yoldur. Tümleştirmeyi yapılandırdıktan sonra, Azure AD 'yi kullanarak Launchkaranlık kullanıcıları, SSO ve hesapları yönetebilirsiniz, ancak Azure 'da kullanıcıları, SSO ve hesapları yönetmek için Launchkaranlık ' **i kullanamazsınız.**

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Koyu çoklu oturum açma etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Launchkaranlık, **IDP** tarafından başlatılan SSO 'yu destekler.
* Launchkaranlık **, tam zamanında** Kullanıcı sağlamayı destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-launchdarkly-from-the-gallery"></a>Galeriden Launchkaranlık ekleme

Başlatma 'nın Azure AD 'ye tümleştirilmesine yönelik tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Launchkaranlık öğesini eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **launchkaranlık** yazın.
1. Sonuçlar panelinden **başlatma** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Azure AD SSO 'yu başlatma için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu launchkaranlık olarak yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Launchkaranlık ile ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Launchkaranlık olarak yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Launchkoyu SSO 'Yu yapılandırma](#configure-launchdarkly-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olarak, launchkaranlık ve üzerinde en fazla B. Simon 'a sahip olmak için, **[launchkaranlık test kullanıcısı oluşturun](#create-launchdarkly-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Launchkaranlık** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`app.launchdarkly.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Değeri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek yanıt URL 'siyle güncellecektir. Uygulamayı **IDP** modunda kullanmak Istiyorsanız, **oturum açma URL 'si** alanını boş bırakmanız gerekir, aksi takdirde **IDP**'den oturum açma işlemi başlatamazsınız. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Launchkoyu 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Launchkaranlık erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Launchkaranlık**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-launchdarkly-sso"></a>Launchkoyu SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Launchkaranlık şirket sitenizde yönetici olarak oturum açın.

2. Sol Gezinti panelinden **Hesap ayarları** ' nı seçin.

    ![Ekran görüntüsünde, üretim altında seçilen hesap ayarları öğesi gösterilir.](./media/launchdarkly-tutorial/configure-1.png)

3. **Güvenlik** sekmesine tıklayın.

    ![Ekran görüntüsü, hesap ayarlarının Güvenlik sekmesini gösterir.](./media/launchdarkly-tutorial/configure-2.png)

4. **SSO 'Yu etkinleştir** ' e tıklayın ve ardından **SAML yapılandırmasını düzenleyin**.

    ![Ekran görüntüsü, S S 'yi ETKINLEŞTIREBILECEĞINIZ ve SAML YAPıLANDıRMASıNı DÜZENLEYEBILECEĞINIZ çoklu oturum açma sayfasını gösterir.](./media/launchdarkly-tutorial/configure-3.png)

5. **SAML yapılandırmanızı Düzenle** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, burada açıklanan değişiklikleri yapabileceğiniz SAML yapılandırmanızı düzenleyin bölümünü gösterir.](./media/launchdarkly-tutorial/configure-4.png)

    a. Örneğiniz için **SAML tüketicisi hizmeti URL 'sini** kopyalayın ve Azure Portal ' de **Launchkoyu etki alanı ve URL 'ler** bölümünde yanıt URL 'si metin kutusuna yapıştırın.

    b. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Azure portal indirilen sertifikayı not defteri 'ne açın, içeriği kopyalayın ve **X. 509.440 sertifikası** kutusuna yapıştırın veya **karşıya yükle**' ye tıklayarak sertifikayı doğrudan karşıya yükleyebilirsiniz.

    d. **Kaydet**’e tıklayın.

### <a name="create-launchdarkly-test-user"></a>Launchkaranlık test kullanıcısı oluşturma

Bu bölümde, Launchkaranlık ' de B. Simon adlı bir Kullanıcı oluşturulur. Launchkaranlık, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Launchkaranlık olarak zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız başlatma için otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım üzerinde Launchkaranlık Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız başlatma için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Launchkaranlık 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve kurtarılmasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).