---
title: 'Öğretici: Google Cloud (G Suite) Bağlayıcısı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Google Cloud (G Suite) Bağlayıcısı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732135"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Öğretici: Google Cloud (G Suite) Bağlayıcısı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Google Cloud (G Suite) bağlayıcısını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Google Cloud (G Suite) bağlayıcısını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Google Cloud (G Suite) bağlayıcısına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Google Cloud (G Suite) bağlayıcısına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

- Bir Azure AD aboneliği.
- Google Cloud (G Suite) bağlayıcı çoklu oturum açma (SSO) etkin aboneliği.
- Bir Google Apps aboneliği veya Google Cloud Platform aboneliği.

> [!NOTE]
> Bu öğreticideki adımları test etmek için, üretim ortamının kullanılmasını önermiyoruz. Bu belge Yeni Kullanıcı çoklu oturum açma deneyimi kullanılarak oluşturulmuştur. Hala eskisini kullanıyorsanız, kurulum farklı görünür. Yeni deneyimi G-Suite uygulamasının çoklu oturum açma ayarlarındaki etkin bir şekilde etkinleştirebilirsiniz. **Azure AD, kurumsal uygulamalar**' a gidin, **Google Cloud (G Suite) bağlayıcısını** seçin, **Çoklu oturum açma** ' yı seçin ve ardından **Yeni deneyimimizi dene**' ye tıklayın.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izlemeniz gerekir:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

1. **S: Bu tümleştirme, Azure AD ile SSO tümleştirmesi Google Cloud Platform destekliyor mu?**

    C: Evet. Google Cloud Platform ve Google Apps aynı kimlik doğrulama platformunu paylaşır. Bu nedenle, GCP tümleştirmesini yapmak için SSO 'yu Google Apps ile yapılandırmanız gerekir.

2. **S: Azure AD çoklu oturum açma ile uyumlu Kınbook 'Lar ve diğer Chrome cihazları mı var?**
  
    Y: Evet, kullanıcılar, Azure AD kimlik bilgilerini kullanarak Kmebook cihazlarında oturum açabilirler. Kullanıcılardan kimlik bilgilerini iki kez isteyip istememe hakkında bilgi için bu [Google Cloud (G Suite) bağlayıcı destek makalesine](https://support.google.com/chrome/a/answer/6060880) bakın.

3. **S: çoklu oturum açmayı etkinleştirdiğimde, kullanıcılar Azure AD kimlik bilgilerini Google Classroom, GMail, Google Drive, YouTube vb. gibi herhangi bir Google ürününde oturum açmak için kullanabilir.**

    A: kuruluşunuz için etkinleştirmek veya devre dışı bırakmak istediğiniz [Google Cloud (G Suite) bağlayıcısına](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) bağlı olarak Evet.

4. **S: Google Cloud (G Suite) bağlayıcı kullanıcılarınızın yalnızca bir alt kümesi için çoklu oturum açmayı etkinleştirebilir miyim?**

    Y: Hayır, çoklu oturum açmayı açmak hemen, tüm Google Cloud (G Suite) bağlayıcı kullanıcılarınızın Azure AD kimlik bilgileriyle kimlik doğrulaması yapmasını gerektirir. Google Cloud (G Suite) Bağlayıcısı birden çok kimlik sağlayıcısı desteklemediğinden, Google Cloud (G Suite) bağlayıcı ortamınız için kimlik sağlayıcısı aynı anda Azure AD veya Google--olabilir.

5. **S: bir Kullanıcı Windows aracılığıyla oturum açmışsa, parola istenmeden otomatik olarak Google Cloud (G Suite) bağlayıcısının kimliğini doğrular mı?**

    Y: Bu senaryoyu etkinleştirmek için iki seçenek vardır. İlk olarak, kullanıcılar [Azure Active Directory JOIN](../devices/overview.md)aracılığıyla Windows 10 cihazlarında oturum açabilirler. Alternatif olarak, kullanıcılar, bir [Active Directory Federasyon Hizmetleri (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) dağıtımı aracılığıyla Azure AD 'de çoklu oturum açma için etkinleştirilen bir şirket içi Active Directory etki alanına katılmış Windows cihazlarında oturum açabilirler. Her iki seçenek de, Azure AD ve Google Cloud (G Suite) Bağlayıcısı arasında çoklu oturum açmayı etkinleştirmek için aşağıdaki öğreticideki adımları gerçekleştirmenizi gerektirir.

6. **S: "geçersiz e-posta" hata iletisi aldığımda ne yapmam gerekir?**

    Y: Bu kurulum Için, kullanıcıların oturum açabiliyor olması için e-posta özniteliği gereklidir. Bu öznitelik el ile ayarlanamaz.

    E-posta özniteliği, geçerli bir Exchange lisansına sahip herhangi bir kullanıcı için oto doldurulur. Kullanıcı e-posta etkin değilse bu hata, uygulamanın erişim vermek için bu özniteliğin alınması gerektiği için alınır.

    Yönetici hesabıyla portal.office.com adresine giderek Yönetim Merkezi, faturalandırma, abonelikler ' e tıklayabilir, Microsoft 365 aboneliğinizi seçin ve ardından kullanıcılara ata ' ya tıklayabilir, aboneliklerini denetlemek istediğiniz kullanıcıları seçebilir ve sağ bölmede lisansları Düzenle ' ye tıklayın.

    Microsoft 365 lisansı atandıktan sonra, uygulanması birkaç dakika sürebilir. Bundan sonra, Kullanıcı. mail özniteliği yeniden doldurulur ve sorun çözümlenmelidir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Google Cloud (G Suite) Bağlayıcısı **SP** tarafından başlatılan SSO 'yu destekler

* Google Cloud (G Suite) Bağlayıcısı [ **Otomatik** Kullanıcı sağlamayı destekler](./g-suite-provisioning-tutorial.md)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Galeriden Google Cloud (G Suite) bağlayıcısını ekleme

Google Cloud (G Suite) bağlayıcısının tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Google Cloud (G Suite) bağlayıcısını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Google Cloud (G Suite) bağlayıcısını** yazın.
1. Sonuçlar panelinden **Google Cloud (G Suite) bağlayıcısını** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Google Cloud (G Suite) Bağlayıcısı için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Google Cloud (G Suite) bağlayıcısıyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Google Cloud (G Suite) bağlayıcısında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Google Cloud (G Suite) bağlayıcısıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Google Cloud (G Suite) BAĞLAYıCı SSO 'Yu yapılandırın](#configure-google-cloud-g-suite-connector-sso)** .
    1. Google **[Cloud (g Suite) Bağlayıcısı test kullanıcısı oluşturma](#create-google-cloud-g-suite-connector-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Google Cloud (g Suite) bağlayıcısında B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Google Cloud (G Suite) bağlayıcı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **Gmail** için yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın: 

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. **Temel SAML yapılandırması** bölümünde **Google Cloud Platform** için yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın: 
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve tanımlayıcısıyla güncelleştirin. Google Cloud (G Suite) Bağlayıcısı çoklu oturum açma yapılandırmasında varlık KIMLIĞI/tanımlayıcı değeri sağlamıyor; bu nedenle, **etki alanına özel veren** seçeneğinin Işaretini kaldırdığınızda tanımlayıcı değeri olur `google.com` . **Etki alanına özel veren** seçeneğini denetederseniz, olacaktır `google.com/a/<yourdomainname.com>` . **Etki alanına özel veren** seçeneğini denetlemek/işaretini kaldırmak için, öğreticide daha sonra açıklanan **Google Cloud (G SUITE) bağlayıcı SSO 'yu yapılandırın** bölümüne gitmeniz gerekir. Daha fazla bilgi için [Google Cloud (G Suite) bağlayıcı istemci destek ekibine](https://www.google.com/contact/)başvurun.

1. Google Cloud (G Suite) bağlayıcı uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde buna bir örnek gösterilmektedir. **Benzersiz kullanıcı tanımlayıcısının** varsayılan değeri **User. UserPrincipalName** ' dir ancak Google Cloud (G Suite) Bağlayıcısı bunun kullanıcının e-posta adresiyle eşlenmesini bekliyor. Bu şekilde, listeden **User. Mail** özniteliğini kullanabilir veya kuruluşunuzun yapılandırmasına göre uygun öznitelik değerini kullanabilirsiniz.

    ![image](common/default-attributes.png)


1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Google Cloud (G Suite) bağlayıcısını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Google Cloud (G Suite) bağlayıcısına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Google Cloud (G Suite) bağlayıcısını** seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Google Cloud (G Suite) bağlayıcı SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve yönetici hesabınızı kullanarak [Google Cloud (G Suite) bağlayıcı yönetim konsolunda](https://admin.google.com/) oturum açın.

2. **Güvenlik**' e tıklayın. Bağlantıyı görmüyorsanız, ekranın alt kısmındaki **daha fazla denetim** menüsünde gizli olabilir.

    ![Güvenlik ' e tıklayın.][10]

3. **Güvenlik** sayfasında, **Çoklu oturum açma (SSO) seçeneğini belirleyin.**

    ![SSO ' ya tıklayın.][11]

4. Aşağıdaki yapılandırma değişikliklerini gerçekleştirin:

    ![SSO 'yu yapılandırma][12]

    a. **Üçüncü taraf kimlik sağlayıcısıyla bırlıkte SSO ayarla**' yı seçin.

    b. Google Cloud (G Suite) bağlayıcısının **oturum açma sayfası URL 'si** alanında, Azure Portal kopyaladığınız **oturum açma URL** 'si değerini yapıştırın.

    c. Google Cloud (G Suite) bağlayıcısının **oturum kapatma sayfası URL 'si** alanında, Azure Portal kopyaladığınız **oturum açma URL** 'si değerini yapıştırın.

    > [!NOTE]
    > Google Cloud (G Suite), SAML oturum kapatma protokolünü temel alır. Bu nedenle, **oturum kapatma sayfası URL 'si** alanında, aynı değer olarak oturum açma URL 'SI olan SAML Logout URL 'sini kullanmanız gerekir.

    d. Google Cloud (G Suite) Bağlayıcısı ' nda, **doğrulama sertifikası** için Azure Portal indirdiğiniz sertifikayı karşıya yükleyin.   

    e. Azure AD 'deki Yukarıdaki **temel SAML yapılandırması** bölümünde bahsedilen nota göre, **etki alanına özgü sertifikayı kullanma** seçeneğini işaretleyin/işaretini kaldırın.

    f. Google Cloud (G Suite) Bağlayıcısı 'ndaki **parola URL 'Sini Değiştir** alanında, Azure Portal kopyaladığınız **parola URL 'sini Değiştir** değerini yapıştırın.

    örneğin: **Kaydet**’e tıklayın.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Google Cloud (G Suite) bağlayıcı test kullanıcısı oluşturma

Bu bölümün amacı, [Google Cloud (G Suite) Bağlayıcısı 'Nda](https://support.google.com/a/answer/33310?hl=en) B. Simon adlı bir Kullanıcı oluşturmaktır. Kullanıcı Google Cloud (G Suite) Bağlayıcısı 'nda el ile oluşturulduktan sonra, Kullanıcı Microsoft 365 oturum açma kimlik bilgilerini kullanarak oturum açabilir.

Google Cloud (G Suite) Bağlayıcısı otomatik Kullanıcı sağlamayı da destekler. Otomatik Kullanıcı sağlamayı yapılandırmak için, önce [Google Cloud (G Suite) bağlayıcısını otomatik Kullanıcı sağlaması için yapılandırmanız](./g-suite-provisioning-tutorial.md)gerekir.

> [!NOTE]
> Azure AD 'de sağlama, çoklu oturum açmayı test etmeden önce açık bırakılmadığından, kullanıcılarınızın Google Cloud (G Suite) bağlayıcısında zaten mevcut olduğundan emin olun.

> [!NOTE]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Google destek ekibine](https://www.google.com/contact/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Google Cloud (G Suite) bağlayıcı oturum açma URL 'sine yeniden yönlendirilir. 

* Google Cloud (G Suite) bağlayıcı oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Google Cloud (G Suite) bağlayıcı kutucuğuna tıkladığınızda, bu işlem Google Cloud (G Suite) bağlayıcı oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Google Cloud (G Suite) bağlayıcısını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve bu verilerin zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
