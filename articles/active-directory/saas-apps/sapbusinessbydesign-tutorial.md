---
title: 'Öğretici: SAP Business ByDesign ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve SAP Business ByDesign arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654349"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Öğretici: SAP Business ByDesign ile Azure Active Directory tümleştirme

Bu öğreticide SAP Business ByDesign 'ın Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. SAP Business ByDesign 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP Business ByDesign 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP Business ByDesign ' a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SAP Business ByDesign çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SAP Business ByDesign **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Galeriden SAP Business ByDesign ekleme

SAP Business ByDesign 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden SAP Business ByDesign 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **SAP Business Bydesign** yazın.
1. Sonuçlar panelinden **SAP Business ByDesign** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test KULLANıCıSı kullanarak SAP Business Bydesign Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP Business ByDesign içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SAP Business ByDesign ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[SAP Business ByDesign SSO 'Yu yapılandırma](#configure-sap-business-bydesign-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. SAP Business Bydesign **[test kullanıcısı oluşturma](#create-sap-business-bydesign-test-user)** -kullanıcının Azure AD gösterimine bağlı olan SAP Business Bydesign 'Ta Britta Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP Business ByDesign** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<servername>.sapbydesign.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [SAP Business ByDesign Client destek ekibine](https://www.sap.com/products/cloud-analytics.support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. SAP Business ByDesign uygulaması, SAML onaylamalarını belirli bir biçimde bekler. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image1](common/edit-attribute.png)

6. **Ad tanımlayıcı değerini** düzenlemek için **Düzenle** simgesine tıklayın.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. **Kullanıcı taleplerini Yönet** bölümünde aşağıdaki adımları uygulayın:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. **Kaynak** olarak **dönüşüm** ' i seçin.

    b. **Dönüştürme** açılan listesinde **Extractmailprefix ()** öğesini seçin.

    > [!NOTE]
    > Varsayılan olarak ByD, kullanıcı eşlemesi için **belirtilmemiş** NameID biçimini kullanır. ByD Kullanıcı diğer adında SAML-onaylarının NameID 'sini eşleştirir. Ek olarak ByD, **Emapostaadı** ad kimlik biçimini destekler. Bu durumda ByD, BYD çalışan iletişim verilerinin ByD Kullanıcı e-posta adresi üzerinde SAM onaylama 'nın NameID 'sini eşleştirir. Daha fazla ayrıntı için [Bu SAP bloguna](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)başvurabilirsiniz.

8. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

9. **SAP Business ByDesign 'ı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, SAP Business ByDesign 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SAP Business ByDesign**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.

1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-business-bydesign-sso"></a>SAP Business ByDesign SSO 'yu yapılandırma

1. SAP Business ByDesign Portal 'da yönetici haklarıyla oturum açın.

2. **Uygulama ve Kullanıcı yönetimi ortak görevi** ' ne gidin ve **kimlik sağlayıcısı** sekmesine tıklayın.

3. **Yeni kimlik sağlayıcısı** ' na tıklayın ve Azure Portal indirdiğiniz meta veri xml dosyasını seçin. Meta verileri içeri aktararak, sistem gerekli imza sertifikasını ve şifreleme sertifikasını otomatik olarak karşıya yükler.

    ![Tek Sign-On1 yapılandırma](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. **Onaylama tüketici hizmeti URL 'SINI** SAML isteğine dahil etmek Için, **onaylama tüketici hizmeti URL 'sini dahil et**' i seçin.

5. **Çoklu oturum açmayı etkinleştir**' e tıklayın.

6. Yaptığınız değişiklikleri kaydedin.

7. **Sistemim** sekmesine tıklayın.

    ![Tek Sign-On2 yapılandırma](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. **Azure AD oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    ![Tek Sign-On3 yapılandırma](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Çalışanın, **El Ile kimlik sağlayıcı seçimi** seçerek Kullanıcı kimliği ve parola veya SSO ile oturum açma arasında el ile seçim yapıp kullanamayacağını belirtin.

10. **SSO URL 'si** bölümünde, çalışan tarafından sistemde oturum açmaya yönelik olarak kullanılması gereken URL 'yi belirtin.
    Çalışanlara gönderilen URL açılır listesinde, aşağıdaki seçenekler arasından seçim yapabilirsiniz:

    **SSO olmayan URL**

    Sistem, çalışana yalnızca normal sistem URL 'sini gönderir. Çalışan, SSO kullanarak yeniden oturum alamaz ve bunun yerine parola veya sertifika kullanmalıdır.

    **SSO URL 'SI**

    Sistem yalnızca SSO URL 'sini çalışana gönderir. Çalışan SSO 'yu kullanarak oturum açabilir. Kimlik doğrulama isteği IDP aracılığıyla yeniden yönlendirildi.

    **Otomatik seçim**

    SSO etkin değilse, Sistem normal sistem URL 'sini çalışana gönderir. SSO etkinse, sistem, çalışanın bir parolaya sahip olup olmadığını denetler. Bir parola varsa, çalışana hem SSO URL 'si hem de SSO olmayan URL gönderilir. Ancak, çalışanın parolası yoksa, çalışana yalnızca SSO URL 'SI gönderilir.

11. Yaptığınız değişiklikleri kaydedin.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business ByDesign test kullanıcısı oluşturma

Bu bölümde, SAP Business ByDesign 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. SAP Business ByDesign platformunda kullanıcıları eklemek için lütfen [SAP Business Bydesign Client destek ekibi](https://www.sap.com/products/cloud-analytics.support.html) ile çalışın. 

> [!NOTE]
> Lütfen NameID değerinin SAP Business ByDesign platformunda username alanıyla eşleştiğinden emin olun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SAP Business ByDesign oturum açma URL 'sine yeniden yönlendirilir. 

2. SAP Business ByDesign oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

3. Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SAP Business ByDesign kutucuğuna tıkladığınızda bu, SAP Business ByDesign oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

* SAP Business ByDesign 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimleri uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).