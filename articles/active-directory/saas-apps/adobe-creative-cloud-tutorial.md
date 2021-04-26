---
title: 'Öğretici: Adobe Creative Cloud ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Adobe Creative Cloud arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 553f77cdb42aa0adb230ee3efd7bec7e9fbfa972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653380"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Öğretici: Adobe Creative Cloud ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Adobe Creative Cloud Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Adobe Creative Cloud Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Adobe Creative Cloud erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Adobe Creative Cloud için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik Adobe Creative Cloud.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Adobe Creative Cloud **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Galeriden Adobe Creative Cloud ekleme

Adobe Creative Cloud tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Adobe Creative Cloud yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Adobe Creative Cloud** yazın.
1. Sonuçlar panelinden **Adobe Creative Cloud** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Adobe Creative Cloud için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Adobe Creative Cloud ile yapılandırın ve test edin. SSO 'nun çalışması için, Adobe Creative Cloud bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Adobe Creative Cloud ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Adobe CREATIVE Cloud SSO 'Yu yapılandırın](#configure-adobe-creative-cloud-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Adobe Creative Cloud bir B. Simon 'ya sahip olmak için **[Adobe Creative Cloud test kullanıcısı oluşturun](#create-adobe-creative-cloud-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Adobe Creative Cloud** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://adobe.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. **Adobe Cloud SSO 'Yu yapılandırma** bölümünde 4. adım hakkında yer alan yönergeleri izleyin. Bu durumda, **Federasyon meta VERI XML dosyasını** açabilir ve bundan sonra BIR varlık kimliği değeri alabilir ve bunu Azure AD yapılandırmasında bir tanımlayıcı değeri olarak koyabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Adobe Creative Cloud uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Yukarıdaki Adobe Creative Cloud uygulama, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    |----- | --------- |
    | FirstName | Kullanıcı. |
    | LastName | User. soyadı |
    | E-posta | Kullanıcı. Mail |

    > [!NOTE]
    > Kullanıcı, SAML yanıtında doldurulacak bir e-posta talebi değeri için geçerli bir Microsoft 365 ExO lisansına sahip olmaları gerekir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon veri XML**'i bulun ve ardından **İndir** ' i seçerek xml meta veri dosyasını indirin ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Adobe Creative Cloud ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Adobe Creative Cloud erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Adobe Creative Cloud**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir sistem yöneticisi olarak [Adobe Yönetici Konsolu](https://adminconsole.adobe.com) 'nda oturum açın.

1. Üst gezinti çubuğundaki **Ayarlar** ' a gidin ve ardından **kimlik**' i seçin. Dizinlerin listesi açılır. İstediğiniz Federal dizini seçin.

1. **Dizin ayrıntıları** sayfasında **Yapılandır**' ı seçin.

1. Varlık KIMLIĞINI ve ACS URL 'sini (onaylama tüketici hizmeti URL 'SI veya yanıt URL 'si) kopyalayın. URL 'Leri Azure portal uygun alanlara girin.

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **Uygulama ayarlarını yapılandır** Iletişim kutusunda **tanımlayıcı** Için sıze sağlanmış olan varlık kimliği değerini kullanın.

    b. **Uygulama ayarlarını yapılandır** iletişim kutusunda, **yanıt URL** 'SINI Adobe size sağlayan ACS URL 'Si (onaylama tüketici hizmeti URL 'si) değerini kullanın.

1. Sayfanın alt kısmındaki Azure portal indirdiğiniz **Federasyon VERILERI XML** dosyasını karşıya yükleyin. 

    ![Federasyon verileri XML dosyası](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IDP meta veri XML 'i")

1. **Kaydet**’i seçin.

### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud test kullanıcısı oluştur

Azure AD kullanıcılarının Adobe Creative Cloud oturum açmasını sağlamak için, Adobe Creative Cloud sağlanması gerekir. Adobe Creative Cloud durumda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. Yönetici olarak [Adobe Yönetici Konsolu](https://adminconsole.adobe.com) sitesinde oturum açın.

2. Kullanıcıları, Adobe konsolunun içinde Federasyon KIMLIĞI olarak ekleyin ve bir ürün profiline atayın. Kullanıcı ekleme hakkında ayrıntılı bilgi için bkz. [Adobe Yönetici Konsolu 'nda Kullanıcı ekleme](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. Bu noktada, e-posta adresinizi/UPN 'nizi Adobe oturum açma formuna yazın, sekme tuşuna basın ve Azure AD 'ye geri Federasyon oluşturmanız gerekir:
   * Web erişimi: www \. adobe.com > oturum açma
   * Masaüstü uygulaması yardımcı programı içinde > oturum açma
   * Uygulama içinde > yardım > oturum açma

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Adobe Creative Cloud oturum açma URL 'sine yönlendirecektir. 

* Doğrudan Adobe Creative Cloud oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın Adobe Creative Cloud kutucuğuna tıkladığınızda bu, Adobe Creative Cloud oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Adobe Creative Cloud yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app)