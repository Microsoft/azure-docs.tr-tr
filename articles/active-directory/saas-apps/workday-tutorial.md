---
title: 'Öğretici: Workday ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Workday arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181382"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Öğretici: Workday ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Workday 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Workday 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Workday 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Workday 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Workday çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Workday, **SP** tarafından başlatılan SSO 'yu destekler.

* Workday mobil uygulaması, artık SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. ' Nin nasıl yapılandırılacağı hakkında daha fazla bilgi için lütfen [Bu](workday-mobile-tutorial.md) bağlantıyı izleyin.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-workday-from-the-gallery"></a>Galeriden Workday ekleme

Workday 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden iş Workday 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Workday** yazın.
1. Sonuçlar panelinden **Workday** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Workday için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Workday Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Workday 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Workday ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Workday 'ı yapılandırın](#configure-workday)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Workday 'de B. Simon 'a karşılık gelen bir iş **[günü test kullanıcısı oluşturun](#create-workday-test-user)** .
3. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Workday** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://impl.workday.com/<tenant>/login-saml.htmld`

    c. **Oturum kapatma URL** 'si metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI, yanıt URL 'si ve oturum kapatma URL 'SI ile güncelleştirin. Yanıt URL 'niz için bir alt etki alanı olmalıdır; örneğin: www, WD2, WD3, WD3-Impl, wd5, wd5-Impl).
    > `http://www.myworkday.com`Çalışma, ancak değil gibi bir şey kullanılıyor `http://myworkday.com` . Bu değerleri almak için [Workday istemci destek ekibine](https://www.workday.com/en-us/partners-services/services/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Workday uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Workday uygulaması, **NameIdentifier** 'ın **User. Mail**, **UPN** vb. ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

    > [!NOTE]
    > Burada ad KIMLIĞINI UPN (User. UserPrincipalName) ile varsayılan olarak eşleştirdik. SSO 'nun başarıyla çalışması için, ad KIMLIĞINI Workday hesabınızdaki (e-postanız, UPN, vb.) gerçek Kullanıcı KIMLIĞIYLE eşlemeniz gerekir.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **İmzalama** seçeneklerini gereksiniminize göre değiştirmek Için, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![Sertifika](common/edit-certificate.png) 

    ![SAML Imzalama sertifikası](./media/workday-tutorial/signing-option.png)

    a. Imza seçin **onay IÇIN SAML yanıtı ve onayını Imzala** **seçeneğini** belirleyin.

    b. **Kaydet**’e tıklayın

1. Workday 'yi **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Workday 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Workday**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workday"></a>Workday yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, iş günü Şirket sitenizde yönetici olarak oturum açın.

1. **Arama kutusunda** , ana sayfanın sol üst tarafında bulunan **kiracı kurulumunu Düzenle – güvenlik** adlı arama yapın.

    ![Kiracı güvenliğini Düzenle](./media/workday-tutorial/IC782925.png "Kiracı güvenliğini Düzenle")


1. **SAML kurulumu** bölümünde aşağıdaki adımları uygulayın:

    ![SAML kurulumu](./media/workday-tutorial/IC782926.png "SAML kurulumu")

    a.  **SAML kimlik doğrulamasını etkinleştir**' i seçin.

    b.  **Satır ekle**' ye tıklayın.

1. **SAML kimlik sağlayıcıları** bölümünde, lütfen yeni oluşturulan satır için aşağıdaki eylemleri gerçekleştirin.

    a. Aşağıda gösterilen alanlar için aşağıdaki eylemleri gerçekleştirin.

    ![SAML kimlik sağlayıcıları 1](./media/workday-tutorial/IC7829271.png "SAML Kimlik Sağlayıcıları")

    * **Kimlik sağlayıcısı adı** metin kutusuna bir sağlayıcı adı yazın (örneğin: *Spinitilik SSO*).

    * Azure portal, **Workday 'Yi ayarlama** bölümünde, **Azure AD tanımlayıcı** değerini kopyalayın ve ardından **sertifikayı verenin** metin kutusuna yapıştırın.

    * İndirilen **sertifikayı** Azure Portal Not defteri ' nden açın ve içeriği **x. 509.952 Certificate** metin kutusuna yapıştırın.

    b. Aşağıda gösterilen alanlar için aşağıdaki eylemleri gerçekleştirin.

    ![SAML kimlik sağlayıcıları 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML Kimlik Sağlayıcıları")

    * **IDP tarafından başlatılan oturum kapatma** onay kutusunu seçin.

    * **Logout Response URL** metin kutusuna yazın **http://www.workday.com** .

    * **Oturum kapatma isteği URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    * **SP tarafından başlatılan** onay kutusuna tıklayın.

    * **Hizmet sağlayıcı kimliği** metin kutusuna yazın **http://www.workday.com** .


    * **SP tarafından başlatılan kimlik doğrulama Isteğini söndür**' ı seçin.

    c. Aşağıda gösterilen alanlar için aşağıdaki eylemleri gerçekleştirin.

    ![SAML kimlik sağlayıcıları 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML Kimlik Sağlayıcıları")

    * Azure portal, **Workday 'Yi ayarlama** bölümünde, **oturum açma URL 'si** değerini kopyalayın ve sonra **IDP SSO hizmeti URL 'si** metin kutusuna yapıştırın.

    * **Ortamlar Için kullanılan** metin kutusunda, açılan listeden uygun ortam adlarını seçin.

1. Aşağıdaki görüntüde aşağıdaki adımları gerçekleştirin.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML Kimlik Sağlayıcıları")

    a. **Hizmet sağlayıcı kimliği (kullanım dışı olacak)** metin kutusuna yazın **http://www.workday.com** .

    b. **IDP SSO hizmeti URL 'si (kullanım dışı olacak)** metin kutusunda, **oturum açma URL 'si** değerini yazın.

    c. **SP tarafından başlatılan kimlik doğrulama Isteğini söndür (kullanım dışı olacaktır)** seçeneğini belirleyin.

    d. **Kimlik doğrulama Isteği Imza yöntemi** için **SHA256** öğesini seçin.

    e. **Tamam**'a tıklayın.

    > [!NOTE]
    > Lütfen bir çoklu oturum açmayı doğru şekilde ayarlamış olduğunuzdan emin olun. Yanlış kurulumla çoklu oturum açmayı etkinleştirdiğinizde, uygulamayı kimlik bilgilerinizle giremeyebilirsiniz ve kilitlenmeyebilirsiniz. Bu durumda, Workday, kullanıcıların normal Kullanıcı adı ve parolasını şu biçimde kullanarak oturum açabilbileceği bir yedekleme günlüğü URL 'si sağlar: [Workday URL 'SI]/Login.Flex? Redirect = n

### <a name="create-workday-test-user"></a>Workday test kullanıcısı oluşturma

1. Workday şirket sitenizde yönetici olarak oturum açın.

1. Sağ üst köşedeki **profil** ' e tıklayın, **giriş** ' i seçin ve **uygulamalar** sekmesinde **Dizin** ' e tıklayın. 

1. **Dizin** sayfasında, Görünüm sekmesinde **çalışanları bul** ' u seçin.

    ![Çalışanları bul](./media/workday-tutorial/user-directory.png)

1.  **Çalışanları bul** sayfasında, sonuçlardan Kullanıcı ' yı seçin.

1. Aşağıdaki sayfada, **iş > çalışan güvenliği** ' ni seçin ve **Workday hesabının** **ad kimliği** değeri olarak Azure Active Directory ile eşleşmesi gerekir.

    ![Çalışan güvenliği](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Workday test kullanıcısı oluşturma hakkında daha fazla bilgi için lütfen [Workday istemci destek ekibine](https://www.workday.com/en-us/partners-services/services/support.html)başvurun.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Workday oturum açma URL 'sine yeniden yönlendirilir. 

2. Workday oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

3. Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde Workday kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Workday 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Workday 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)