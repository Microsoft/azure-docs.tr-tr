---
title: 'Öğretici: Citrix ShareFile ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Citrix ShareFile arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 03f2ec7aef1faadcb72d6c7a5a058c7d06596539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729686"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Öğretici: Citrix ShareFile ile tümleştirme Azure Active Directory

Bu öğreticide Citrix ShareFile 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Citrix ShareFile 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Citrix ShareFile erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Citrix ShareFile (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Citrix ShareFile ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık deneme sürümü edinebilirsiniz.
* Citrix ShareFile çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Citrix ShareFile **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Galeriden Citrix ShareFile ekleme

Citrix ShareFile 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için Galeriden Citrix ShareFile öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Citrix ShareFile** yazın.
1. Sonuçlar panelinden **Citrix ShareFile** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Citrix ShareFile için Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre Citrix ShareFile ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Citrix ShareFile içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Citrix ShareFile ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. **[Citrix ShareFile SSO 'Yu yapılandırma](#configure-citrix-sharefile-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. Citrix **[ShareFile test kullanıcısı oluşturun](#create-citrix-sharefile-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan Citrix ShareFile 'Da Britta Simon 'a sahip olmalıdır.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Citrix ShareFile** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.sharefile.com/saml/login`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Citrix ShareFile istemci destek ekibine](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Citrix ShareFile ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Citrix ShareFile erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Citrix ShareFile** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-citrix-sharefile-sso"></a>Citrix ShareFile SSO 'yu yapılandırma

1. **Citrix ShareFile** içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra **Citrix ShareFile ayarla** ' ya tıkladığınızda sizi Citrix ShareFile uygulamasına yönlendirirsiniz. Buradan Citrix ShareFile oturumu açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Citrix ShareFile öğesini el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde Citrix ShareFile şirket sitenizde yönetici olarak oturum açın.

1. **Panoda**, **Ayarlar** ' a tıklayın ve **yönetici ayarları**' nı seçin.

    ![Yönetim](./media/sharefile-tutorial/settings.png)

1. Yönetici ayarları ' **nda güvenlik**  ->  **oturumu & güvenlik ilkesi**' ne gidin.
   
    ![Hesap yönetimi](./media/sharefile-tutorial/settings-security.png "Hesap yönetimi")

1. **Çoklu oturum açma/SAML 2,0 yapılandırma** Iletişim kutusunda **temel ayarlar** altında aşağıdaki adımları gerçekleştirin:
   
    ![Çoklu oturum açma](./media/sharefile-tutorial/saml-configuration.png "Çoklu oturum açma")
   
    a. **SAML etkinleştir**' de **Evet** ' i seçin.

    b. **ShareFile Issuer/ENTITY ID** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** iletişim kutusundaki **tanımlayıcı URL** kutusuna yapıştırın.
    
    c. **IDP veren/VARLıK kimliği** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    d. **X. 509.440 sertifikası** alanının yanındaki **Değiştir** ' e tıklayın ve ardından Azure Portal indirdiğiniz sertifikayı karşıya yükleyin.
    
    e. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
    
    f. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    örneğin: **Isteğe bağlı ayarlarda**, **Kullanıcı adı ve parola** olarak **SP tarafından başlatılan kimlik doğrulama bağlamı** ' nı ve **tam**' ı seçin.

5. **Kaydet**’e tıklayın.

## <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile test kullanıcısı oluştur

1. **Citrix ShareFile** kiracınızda oturum açın.

2. **Kişiler**  ->  **Kullanıcıları Yönet ana**  ->  **Yeni Kullanıcı oluştur**  ->  **çalışanlar oluştur**' a tıklayın.
   
    ![Çalışan oluştur](./media/sharefile-tutorial/create-user.png "Çalışan oluştur")

3. **Temel bilgiler** bölümünde aşağıdaki adımları uygulayın:
   
    ![Temel Bilgiler](./media/sharefile-tutorial/user-form.png "Temel Bilgiler")
   
    a. **Ad** metin kutusuna **Ilk Kullanıcı adını** **Britta** olarak yazın.
   
    b.  **Soyadı** metin kutusunda, Kullanıcı **adını** **Simon** olarak yazın.
   
    c. **E-posta adresi** metin kutusuna Britta Simon 'un e-posta adresini **brittasıon \@ contoso.com** olarak yazın.

4. **Kullanıcı Ekle**'ye tıklayın.
  
    >[!NOTE]
    >Azure AD hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler. Azure AD Kullanıcı hesapları sağlamak için Citrix ShareFile tarafından sunulan diğer Citrix ShareFile Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Citrix ShareFile oturum açma URL 'sine yeniden yönlendirilir.

* Doğrudan Citrix ShareFile oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Citrix ShareFile kutucuğuna tıkladığınızda bu, Citrix ShareFile oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Citrix ShareFile yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).