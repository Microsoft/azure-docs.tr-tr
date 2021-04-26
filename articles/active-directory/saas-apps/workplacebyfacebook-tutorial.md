---
title: 'Öğretici Azure Active Directory: Facebook tarafından çalışma alanı ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Facebook tarafından Azure Active Directory ve çalışma alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725515"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Öğretici: Facebook tarafından çalışma alanı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, çalışma alanını Azure Active Directory (Azure AD) ile Facebook ile tümleştirmeyi öğreneceksiniz. Çalışma alanını Facebook ile Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Facebook tarafından çalışma alanına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Facebook tarafından çalışma alanına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Facebook çoklu oturum açma (SSO) özellikli abonelik ile çalışma alanı.

> [!NOTE]
> Facebook 'ta iki ürün, çalışma alanı standardı (ücretsiz) ve çalışma alanı Premium (ücretli) vardır. Herhangi bir çalışma alanı Premium kiracısı, SCıM ve SSO tümleştirmesini, maliyet veya lisans için gereken diğer etkileri olmadan yapılandırabilir. SSO ve SCıM, çalışma alanı standart örneklerinde kullanılamaz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Facebook tarafından çalışma alanı **SP** tarafından başlatılan SSO 'yu destekler
* Facebook tarafından çalışma alanı **tam zamanında sağlamayı** destekler
* Facebook tarafından çalışma alanı **[Otomatik Kullanıcı sağlamayı](workplacebyfacebook-provisioning-tutorial.md) destekler**
* Facebook mobil uygulaması 'nın çalışma alanı artık SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Galeriden Facebook tarafından çalışma alanı ekleme

Çalışma alanınızın Facebook ile Azure AD arasında tümleştirilmesini yapılandırmak için, Facebook 'tan Facebook 'tan yönetilen SaaS uygulamaları listenize çalışma alanı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Facebook ile çalışma alanı** yazın.
1. Sonuçlar panelinden **Facebook tarafından çalışma alanı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Facebook tarafından çalışma alanı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Facebook Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Facebook tarafından çalışma alanındaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Facebook ile çalışma alanıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Facebook SSO Ile çalışma alanı yapılandırma](#configure-workplace-by-facebook-sso)** .
    1. Facebook 'a ait çalışma alanında, kullanıcının Azure AD gösterimine bağlı olan Facebook tarafından Iş yerinde B. Simon 'ın bir karşılığı olacak şekilde **[çalışma alanı oluşturun](#create-workplace-by-facebook-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Facebook uygulama tümleştirmesiyle çalışma alanı** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** (çalışma alanında alıcı URL 'si olarak bulunur) metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://.workplace.com/work/saml.php`

    b. **Tanımlayıcıda (VARLıK kimliği)** (Iş yerinde izleyici URL 'si olarak bulunur) metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.workplace.com/company/`

    c. **Yanıt URL 'si** (çalışma alanında onaylama tüketici hizmeti olarak bulunur) metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Çalışma alanı Community 'nizin kimlik doğrulama sayfasına bakın. Bu, bu öğreticide daha sonra açıklanmaktadır.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Çalışma alanını Facebook 'a göre ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Facebook tarafından çalışma alanına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Facebook tarafından çalışma alanı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workplace-by-facebook-sso"></a>Çalışma alanını Facebook SSO ile yapılandırma

1. Çalışma alanındaki yapılandırmayı Facebook tarafından otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya uzantı ekledikten sonra, **Facebook tarafından çalışma alanı 'Nı ayarla** ' ya tıklayın, sizi Facebook uygulamasına göre çalışma alanına yönlendirir. Buradan, Facebook tarafından çalışma alanında oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Çalışma alanını Facebook ile el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Facebook Şirket sitesi tarafından yönetici olarak çalışma alanınızda oturum açın ve aşağıdaki adımları gerçekleştirin:

    > [!NOTE]
    > SAML kimlik doğrulama işleminin bir parçası olarak, çalışma alanı, parametreleri Azure AD 'ye geçirmek için boyut olarak en fazla 2,5 kilobayt olan Sorgu dizelerini kullanabilir.

1. Sol gezinti panelinde **güvenlik**  >  **kimlik doğrulaması** sekmesine gidin.

    ![Yönetici paneli](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. **Çoklu oturum açma (SSO)** seçeneğini işaretleyin.
    
    b. **+ Yenı SSO sağlayıcısı ekle**' ye tıklayın.
    > [!NOTE]
    > Parola oturum açma onay kutusunu da denetlediğinizden emin olun. Yöneticiler, kendinden vazgeçmek için sertifika rollover 'ı gerçekleştirirken bu seçeneğe oturum açma için gerek duyar.

1. **Kimlik doğrulama** sekmesinde, **Çoklu oturum açma (SSO)** öğesini seçin ve aşağıdaki adımları gerçekleştirin:

    ![Kimlik doğrulama sekmesi](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. **SSO sağlayıcısının adı**' nda, Azureadsso gibi SSO örnek adını girin.

    b. **SAML URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **SAML veren URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    d. Azure portal 'den indirilen Not defteri 'nde **Base-64 kodlu sertifikanızı** açın, bu içeriği panonuza kopyalayın ve **SAML sertifikası** metin kutusuna yapıştırın.

    e. Örneğiniz için **hedef kitle URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    f. Örneğiniz için **alıcı URL** 'sini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **URL 'yi oturum aç** metin kutusuna yapıştırın.

    örneğin: Örneğiniz için **ACS (onaylama tüketici hizmeti) URL 'sini** kopyalayıp Azure Portal **temel SAML YAPıLANDıRMASı** bölümünde **yanıt URL** metin kutusuna yapıştırın.

    h. Bölümün altına kaydırın ve **test SSO** düğmesine tıklayın. Bu, Azure AD oturum açma sayfası sunulan bir açılan pencerenin görünmesine neden olur. Kimlik doğrulaması için kimlik bilgilerinizi normal olarak girin.

    **Sorun giderme:** Azure AD 'den geri döndürülmekte olan e-posta adresinin, oturum açtığınız çalışma alanı hesabıyla aynı olduğundan emin olun.

    i. Test başarıyla tamamlandıktan sonra sayfanın alt kısmına gidin ve **Kaydet** düğmesine tıklayın.

    j. Artık çalışma alanı kullanan tüm kullanıcılar, kimlik doğrulaması için Azure AD oturum açma sayfasıyla sunulacaktır.

1. **SAML Logout yeniden yönlendirme (isteğe bağlı)** -

    İsteğe bağlı olarak, Azure AD 'nin oturum kapatma sayfasına işaret etmek için kullanılabilecek bir SAML oturum kapatma URL 'Si yapılandırabilirsiniz. Bu ayar etkinleştirildiğinde ve yapılandırıldığında, Kullanıcı artık çalışma alanı oturumu kapatma sayfasına yönlendirilmeyecektir. Bunun yerine, Kullanıcı SAML Logout yeniden yönlendirme ayarında eklenen URL 'ye yeniden yönlendirilir.

### <a name="configuring-reauthentication-frequency"></a>Yeniden kimlik doğrulama sıklığını yapılandırma

Çalışma alanını, her gün, üç gün, hafta, iki hafta, ay veya hiç bir SAML denetimi isteyecek şekilde yapılandırabilirsiniz.

> [!NOTE]
> Mobil uygulamalarda SAML denetimi için en düşük değer bir hafta olarak ayarlanır.

Ayrıca, düğmesini kullanarak tüm kullanıcılar için bir SAML sıfırlamayı zorunlu kılabilirsiniz: tüm kullanıcılar için SAML kimlik doğrulamasını gerektir.

### <a name="create-workplace-by-facebook-test-user"></a>Facebook test kullanıcısına göre çalışma alanı oluştur

Bu bölümde, Facebook tarafından çalışma alanında B. Simon adlı bir Kullanıcı oluşturulur. Facebook tarafından çalışma alanı, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler.

Bu bölümde sizin için herhangi bir eylem yoktur. Bir kullanıcı Facebook tarafından çalışma alanında yoksa Facebook tarafından çalışma alanına erişmeye çalıştığınızda yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Facebook istemci destek ekibi tarafından çalışma alanına](https://www.workplace.com/help/work/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Facebook oturum açma URL 'SI ile çalışma alanına yönlendirilir. 

* Facebook oturum açma URL 'sine doğrudan çalışma alanına gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'ın Facebook kutucuğunda çalışma alanına tıkladığınızda bu, Facebook oturum açma URL 'SI ile çalışma alanına yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Facebook tarafından çalışma alanı için test SSO 'SU (mobil)

1. Çalışma alanını Facebook mobil uygulamasına göre açın. Oturum aç sayfasında **oturum** aç ' a tıklayın.

    ![Oturum açma](./media/workplacebyfacebook-tutorial/test05.png)

2. İş e-postanızı girin ve **devam**' a tıklayın.

    ![E-posta](./media/workplacebyfacebook-tutorial/test02.png)

3. **Yalnızca bir kez** tıklayın.

    ![Bir kez](./media/workplacebyfacebook-tutorial/test04.png)

4. **Izin ver**' e tıklayın.

    ![Izin ver](./media/workplacebyfacebook-tutorial/test03.png)

5. Son olarak, başarıyla oturum açtıktan sonra uygulama giriş sayfası görüntülenir.    

    ![Giriş sayfası](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Sonraki adımlar

Çalışma alanını Facebook ile yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)