---
title: 'Öğretici: Adobe Experience Manager ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Adobe Experience Manager arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653363"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Öğretici: Adobe Experience Manager ile tümleştirme Azure Active Directory

Bu öğreticide, Adobe Experience Manager 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Adobe Experience Manager 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Adobe Experience Manager 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak Adobe Experience Manager 'a oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Adobe Experience Manager çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Adobe Experience Manager **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

* Adobe Experience Manager **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Galeriden Adobe Experience Manager ekleme

Adobe Experience Manager Tümleştirmesini Azure AD ile yapılandırmak için galerideki Adobe Experience Manager 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Adobe Experience Manager** yazın.
1. Sonuçlar panelinden **Adobe Experience Manager** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Adobe Experience Manager için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Adobe Experience Manager ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Adobe Experience Manager 'daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Adobe Experience Manager ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Adobe Experience Manager SSO 'Yu yapılandırın](#configure-adobe-experience-manager-sso)** .
    1. Adobe Experience Manager **[test kullanıcısı oluşturun](#create-adobe-experience-manager-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Adobe Experience Manager 'Da Britta Simon 'a sahip olacak.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Adobe Experience Manager** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, AEM sunucunuzda de tanımladığınız benzersiz bir değer yazın.

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Yanıt URL 'SI değerini gerçek yanıt URL 'siyle güncelleştirin. Bu değeri almak için, bu değeri almak üzere [Adobe Experience Manager istemci destek ekibine](https://helpx.adobe.com/support/experience-manager.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna Adobe Experience Manager sunucu URL 'nizi yazın.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Adobe Experience Manager 'ı ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Adobe Experience Manager 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Adobe Experience Manager**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-adobe-experience-manager-sso"></a>Adobe Experience Manager SSO 'yu yapılandırma

1. Başka bir tarayıcı penceresinde **Adobe Experience Manager** Yönetici portalı ' nı açın.

2. **Ayarlar**  >  **güvenlik**  >  **kullanıcıları**' nı seçin.

    ![Adobe Experience Manager 'daki kullanıcılar kutucuğunu gösteren ekran görüntüsü.](./media/adobe-experience-manager-tutorial/user-1.png)

3. **Yönetici** veya diğer ilgili kullanıcıları seçin.

    ![Adminisrator kullanıcısını vurgulayan ekran görüntüsü.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. **Hesap ayarları**  >  **truststore 'ı Yönet**' i seçin.

    ![Hesap ayarları altında TrustStore 'un yönetimini gösteren ekran görüntüsü.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. **Cer dosyasından sertifika ekle**' nin altında, **sertifika dosyası seç**' e tıklayın. Azure portal zaten indirdiğiniz sertifika dosyasına gidin ve seçin.

    ![Sertifika dosyası Seç düğmesini vurgulayan ekran görüntüsü.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Sertifika, TrustStore 'a eklenir. Sertifikanın diğer adını aklınızda edin.

    ![Sertifikanın TrustStore 'a eklendiğini gösteren ekran görüntüsü.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. **Kullanıcılar** sayfasında **kimlik doğrulama-hizmet**' i seçin.

    ![Ekran üzerinde kimlik doğrulama hizmetini vurgulayan sreenshot.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. **Hesap ayarlarını** seçin  >  **anahtar deposu oluşturun/yönetin**. Bir parola sağlayarak anahtar deposu oluşturun.

    ![Anahtar deposunu yönetme vurguların ekran görüntüsü.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Yönetici ekranına geri dönün. Ardından **Ayarlar**  >  **işlemler**  >  **Web Konsolu**' nu seçin.

    ![Ayarlar bölümü içinde Işlemler altında web konsolunu vurgulayan ekran görüntüsü.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Bu, yapılandırma sayfasını açar.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. **Adobe Granite SAML 2,0 kimlik doğrulama işleyicisini** bulun. Sonra **Ekle** simgesini seçin.

    ![Adobe Granite SAML 2,0 kimlik doğrulama Işleyicisini vurgulayan ekran görüntüsü.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Bu sayfada aşağıdaki eylemleri gerçekleştirin.

    ![Tek Sign-On Kaydet düğmesini yapılandırma](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. **Yol** kutusuna, girin **/** .

    b. **IDP URL 'si** kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini girin.

    c. **IDP sertifika diğer adı** kutusuna truststore ' da eklediğiniz **sertifika diğer adı** değerini girin.

    d. Güvenlik tarafından **sağlanmış VARLıK kimliği** kutusuna, Azure Portal yapılandırdığınız benzersiz **Azure AD tanımlayıcı** değerini girin.

    e. **Onaylama tüketici hizmeti URL 'si** kutusuna, Azure Portal yapılandırdığınız **yanıt URL 'si** değerini girin.

    f. **Anahtar deposu parolası** kutusuna keystore 'Da ayarladığınız **parolayı** girin.

    örneğin: **Kullanıcı ÖZNITELIĞI kimliği** kutusuna, büyük/küçük harfe uygun **ad kimliğini** veya başka bir kullanıcı kimliğini girin.

    h. **CRX kullanıcılarını oto oluştur**' u seçin.

    i. **Oturum kapatma URL 'si** kutusuna, Azure Portal aldığınız benzersiz **oturum kapatma URL 'si** değerini girin.

    j. **Kaydet**’i seçin.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager test kullanıcısı oluştur

Bu bölümde, Adobe Experience Manager 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. **Otomatik oluştur CRX kullanıcıları** seçeneğini belirlediyseniz, kullanıcılar başarıyla kimlik doğrulamasından sonra otomatik olarak oluşturulur.

Kullanıcıları el ile oluşturmak istiyorsanız, Adobe Experience Manager platformunda kullanıcıları eklemek için [Adobe Experience Manager Destek ekibi](https://helpx.adobe.com/support/experience-manager.html) ile çalışın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Adobe Experience Manager oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan Adobe Experience Manager oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Adobe Experience Manager 'da otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. My Apps 'teki Adobe Experience Manager kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Adobe Experience Manager 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Adobe Experience Manager 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).