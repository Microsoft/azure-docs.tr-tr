---
title: 'Öğretici Azure Active Directory: JAMF Pro ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve JAMF Pro arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 7aaaa854ef7d0fd44bdccbe07cb38112475be367
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727637"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Öğretici: JAMF Pro ile SSO tümleştirmesi Azure Active Directory

Bu öğreticide, JAMF Pro 'Yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. JAMF Pro 'Yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* JAMF Pro 'ya kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcılarınıza Azure AD hesaplarıyla JAMF Pro 'Yu otomatik olarak oturum açın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin olan bir JAMF Pro aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

* JAMF Pro, **SP tarafından başlatılan** ve **IDP tarafından başlatılan** SSO 'yu destekler.

## <a name="add-jamf-pro-from-the-gallery"></a>Galeriden JAMF Pro ekleyin

JAMF Pro tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden JAMF Pro 'Yu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. İş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna *JAMF Pro* girin.
1. Sonuçlar panelinden **JAMF Pro** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>JAMF Pro için Azure AD 'de SSO 'Yu yapılandırma ve test etme

B. Simon adlı bir test kullanıcısı kullanarak JAMF Pro ile Azure AD SSO 'Yu yapılandırın ve test edin. SSO 'nun çalışması için, JAMF Pro 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Bu bölümde, JAMF Pro ile Azure AD SSO 'Yu yapılandırıp test edersiniz.

1. Kullanıcılarınızın bu özelliği kullanabilmesi için [Azure AD 'de SSO 'Yu yapılandırın](#configure-sso-in-azure-ad) .
    1. Azure AD SSO 'yu, B. Simon hesabı ile test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. B. Simon 'un Azure AD 'de SSO kullanabilmesi için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için [JAMF Pro 'DA SSO 'Yu yapılandırın](#configure-sso-in-jamf-pro) .
    1. JAMF Pro 'da kullanıcının Azure AD gösterimine bağlı olan B. Simon 'a karşılık gelen bir [JAMF Pro test kullanıcısı oluşturun](#create-a-jamf-pro-test-user) .
1. Yapılandırmanın çalıştığını doğrulamak için [SSO yapılandırmasını test](#test-the-sso-configuration) edin.

## <a name="configure-sso-in-azure-ad"></a>Azure AD 'de SSO 'yu yapılandırma

Bu bölümde, Azure portal Azure AD SSO 'yu etkinleştirirsiniz.

1. Azure portal, **JAMF Pro** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Tek bir Sign-On yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML Yapılandırması sayfasını düzenleyin.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP tarafından başlatılan** modda yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki formülü kullanan bir URL girin:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki formülü kullanan bir URL girin:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. **Ek URL 'Ler ayarla**' yı seçin. Uygulamayı **SP tarafından başlatılan** modda yapılandırmak Istiyorsanız, **oturum açma URL 'si** metin kutusuna AŞAĞıDAKI formülü kullanan bir URL girin: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. JAMF Pro portalındaki **Çoklu oturum açma** bölümünden gerçek tanımlayıcı değerini alacaksınız, bu da Öğreticinin ilerleyen kısımlarında açıklanmıştır. Tanımlayıcı değerden gerçek alt etki alanı değerini ayıklayabilir ve bu alt etki alanı bilgilerini oturum açma URL 'SI ve yanıt URL 'SI olarak kullanabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen formüllere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümüne gidin, **Kopyala** düğmesini seçerek **App Federasyon meta verileri URL 'sini** kopyalayın ve ardından bilgisayarınıza kaydedin.

    ![SAML Imzalama sertifikası indirme bağlantısı](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmede **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.
   1. **Kullanıcı adı** alanına [ad] @ [companydomain] yazın. [uzantı]. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, JAMF Pro 'ya B. Simon erişimi verirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **JAMF Pro**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-sso-in-jamf-pro"></a>JAMF Pro 'da SSO 'Yu yapılandırma

1. JAMF Pro 'da yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**' ı seçerek **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklersiniz.

    ![Uygulamalarım güvenli oturum açma tarayıcı uzantısı sayfası](common/install-myappssecure-extension.png)

2. Uzantıyı tarayıcıya ekledikten sonra **JAMF Pro 'Yu ayarla**' yı seçin. JAMF Pro uygulaması açıldığında, oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı uygulamayı otomatik olarak yapılandırır ve 3 ile 7 arasındaki adımları otomatikleştirin.

    ![JAMF Pro 'da kurulum yapılandırma sayfası](common/setup-sso.png)

3. JAMF Pro 'Yu el ile ayarlamak için yeni bir Web tarayıcı penceresi açın ve JAMF Pro şirket sitenizde yönetici olarak oturum açın. Ardından, aşağıdaki adımları uygulayın.

4. Sayfanın sağ üst köşesindeki **Ayarlar simgesini** seçin.

    ![JAMF Pro 'da ayarlar simgesini seçin](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **Çoklu oturum açma** seçeneğini belirleyin.

    ![JAMF Pro 'da tek Sign-On seçin](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **Çoklu oturum açma** sayfasında, aşağıdaki adımları uygulayın.

    ![JAMF Pro 'da tek Sign-On sayfası](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **Düzenle**'yi seçin.

    b. **Tek Sign-On kimlik doğrulamasını etkinleştir** onay kutusunu seçin.

    c. **Kimlik sağlayıcısı** açılan menüsünden **Azure** seçeneğini belirleyin.

    d. **VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** BÖLÜMÜNDEKI **tanımlayıcı (varlık kimliği)** alanına yapıştırın.

    > [!NOTE]
    > `<SUBDOMAIN>`Azure Portal 'Teki **temel SAML yapılandırması** bölümünde oturum açma URL 'SINI ve yanıt URL 'sini doldurmak için alanındaki değeri kullanın.

    e. **Kimlik sağlayıcısı meta veri kaynağı** açılan menüsünden **meta veri URL 'sini** seçin. Görüntülenen alanda, Azure portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    f. Seçim Belirteç süre sonu değerini düzenleyin veya "SAML belirteci süre sonunu devre dışı bırak" seçeneğini belirleyin.

7. Aynı sayfada, **Kullanıcı eşleme** bölümüne gidin. Ardından, aşağıdaki adımları uygulayın.

    ![JAMF Pro 'daki tek Sign-On sayfasının Kullanıcı eşleme bölümü.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. **Kimlik sağlayıcısı kullanıcı eşlemesi** Için **NameID** seçeneğini belirleyin. Varsayılan olarak, bu seçenek **NameID** olarak ayarlanır, ancak özel bir öznitelik tanımlayabilirsiniz.

    b. **JAMF Pro kullanıcı eşlemesi** Için **e-posta** ' yı seçin. JAMF Pro, IDP tarafından önce kullanıcılar ve ardından gruplar tarafından gönderilen SAML özniteliklerini eşleştirir. Bir Kullanıcı JAMF Pro 'ya erişmeyi denediğinde, JAMF Pro, kimlik sağlayıcısından Kullanıcı hakkında bilgi alır ve tüm JAMF Pro Kullanıcı hesaplarıyla eşleşir. Gelen Kullanıcı hesabı bulunmazsa, JAMF Pro bunu grup adıyla eşleştirmeye çalışır.

    c. Değeri `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` **KIMLIK sağlayıcısı grubu öznitelik adı** alanına yapıştırın.

    d. Aynı sayfada, **güvenlik** bölümüne gidin ve **kullanıcıların tek Sign-On kimlik doğrulamasını atlamasına izin ver**' i seçin. Sonuç olarak, kullanıcılar kimlik doğrulaması için kimlik sağlayıcısı oturum açma sayfasına yönlendirilmez ve bunun yerine JAMF Pro 'da doğrudan oturum açabilir. Bir Kullanıcı, kimlik sağlayıcısı aracılığıyla JAMF Pro 'ya erişmeyi denediğinde, IDP tarafından başlatılan SSO kimlik doğrulaması ve yetkilendirme oluşur.

    e. **Kaydet**’i seçin.

### <a name="create-a-jamf-pro-test-user"></a>JAMF Pro test kullanıcısı oluşturma

Azure AD kullanıcılarının JAMF Pro 'da oturum açması için, bu kullanıcıların JAMF Pro 'da sağlanması gerekir. JAMF Pro 'da sağlama işlemi el ile gerçekleştirilen bir görevdir.

Bir kullanıcı hesabı sağlamak için aşağıdaki adımları uygulayın:

1. JAMF Pro şirket sitenizde yönetici olarak oturum açın.

2. Sayfanın sağ üst köşesindeki **Ayarlar** simgesini seçin.

    ![JAMF Pro 'da ayarlar simgesi](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **Grupları & JAMF Pro Kullanıcı hesapları**' nı seçin.

    ![JAMF Pro ayarlarındaki JAMF Pro Kullanıcı hesapları & gruplar simgesi](./media/jamfprosamlconnector-tutorial/user1.png)

4. **Yeni**'yi seçin.

    ![JAMF Pro Kullanıcı hesapları & grupları sistem ayarları sayfası](./media/jamfprosamlconnector-tutorial/user2.png)

5. **Standart hesap oluştur**' u seçin.

    ![JAMF Pro Kullanıcı hesapları & grupları sayfasında standart hesap oluştur seçeneği](./media/jamfprosamlconnector-tutorial/user3.png)

6. **Yeni hesap** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![JAMF Pro sistem ayarlarındaki yeni hesap kurulum seçenekleri](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **Kullanıcı adı** alanına `Britta Simon` , test kullanıcısının tam adını girin.

    b. Kuruluşunuza uygun **ERIŞIM düzeyi**, **ayrıcalık KÜMESI** ve **erişim durumu** seçeneklerini belirleyin.

    c. **Tam ad** alanına girin `Britta Simon` .

    d. **E-posta adresi** alanına Britta Simon hesabının e-posta adresini girin.

    e. **Parola** alanına kullanıcının parolasını girin.

    f. **Parolayı Doğrula** alanına, kullanıcının parolasını yeniden girin.

    örneğin: **Kaydet**’i seçin.

## <a name="test-the-sso-configuration"></a>SSO yapılandırmasını test etme

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz JAMF Pro oturum açma URL 'sine yeniden yönlendirilir.  

* JAMF Pro oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız JAMF Pro 'da otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki JAMF Pro kutucuğuna tıkladığınızda, SP modunda yapılandırılmışsa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'Yu ayarladığınız JAMF Pro 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

JAMF Pro 'Yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).