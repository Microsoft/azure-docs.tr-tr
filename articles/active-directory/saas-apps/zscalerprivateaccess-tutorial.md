---
title: 'Öğretici: Zscaler özel erişimi ile Azure Active Directory tümleştirme (ZPA) | Microsoft Docs'
description: Azure Active Directory ve Zscaler özel erişimi (ZPA) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 58e2a19f2d57eafc7d2967141d584dc7a22fe76c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955678"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Öğretici: Zscaler özel erişimini (ZPA) Azure Active Directory ile tümleştirin

Bu öğreticide, Zscaler özel erişimini (ZPA) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zscaler özel erişimini (ZPA) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler özel erişimine (ZPA) erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler özel erişimi 'ne (ZPA) otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zscaler özel erişimi (ZPA) çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

* Zscaler özel erişimi (ZPA), **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Galeriden Zscaler özel erişimi (ZPA) ekleme

Zscaler özel erişiminin (ZPA) tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Zscaler özel erişimi (ZPA) ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler özel erişimi (ZPA)** yazın.
1. Sonuçlar panelinden **Zscaler özel erişimi (ZPA)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler özel erişimi (ZPA) Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Zscaler özel erişimi (ZPA) içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zscaler özel erişimi (ZPA) ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Zscaler özel erişimi (ZPA) SSO 'Yu yapılandırın](#configure-zscaler-private-access-zpa-sso)** .
    1. **[Zscaler özel erişim (ZPA) test kullanıcısına](#create-zscaler-private-access-zpa-test-user)** , kullanıcının Azure AD gösterimine bağlı olan Zscaler özel erişimi (ZPA) için B. Simon 'a karşılık gelen bir karşılığı olacak şekilde oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler özel erişim (ZPA)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > **Oturum açma URL 'si** değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Zscaler özel erişim (ZPA) istemci desteği ekibine](https://help.zscaler.com/zpa-submit-ticket) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Zscaler özel erişimi (ZPA) ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Zscaler özel erişim (ZPA) erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler özel erişimi (ZPA)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-zscaler-private-access-zpa-sso"></a>Zscaler özel erişim (ZPA) SSO 'yu yapılandırma

1. Zscaler özel erişimi (ZPA) içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, bu **özel erişim (ZPA) kurulum** 'a tıkladığınızda sizi Zscaler özel erişim (ZPA) uygulamasına yönlendirirsiniz. Buradan, Zscaler özel erişimi 'nde (ZPA) oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Zscaler özel erişimi 'ni (ZPA) el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler özel erişim (ZPA) Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Menünün sol tarafında **Yönetim** ' e tıklayın ve **kimlik doğrulama** bölümüne gidin **IDP yapılandırması**' na tıklayın.

    ![Zscaler özel erişim Yöneticisi yönetimi](./media/zscalerprivateaccess-tutorial/administration.png)

5. Sağ üst köşede **IDP Yapılandırması Ekle**' ye tıklayın. 

    ![Zscaler özel erişim Yöneticisi IDP](./media/zscalerprivateaccess-tutorial/metadata.png)

6. **IDP Yapılandırması Ekle** sayfasında, aşağıdaki adımları uygulayın:
 
    ![Zscaler özel erişim Yöneticisi seçme](./media/zscalerprivateaccess-tutorial/select.png)

    a. **IDP meta veri dosyası yükleme** alanına Indirilen meta veri dosyasını Azure AD 'den karşıya yüklemek Için **Dosya Seç** ' e tıklayın.

    b. **IDP meta verilerini** Azure AD 'den okur ve tüm alan bilgilerini aşağıda gösterildiği gibi doldurur.

    ![Zscaler özel erişim Yöneticisi yapılandırması](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Etki **alanları** alanından etki alanınızı seçin.
    
    d. **Kaydet**’e tıklayın.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler özel erişim (ZPA) test kullanıcısı oluşturma

Bu bölümde, Zscaler özel erişimi (ZPA) içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Zscaler özel erişim (ZPA) platformunda kullanıcıları eklemek için lütfen [Zscaler özel erişim (ZPA) destek ekibi](https://help.zscaler.com/zpa-submit-ticket) ile çalışın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zscaler özel erişim (ZPA) oturum açma URL 'sine yeniden yönlendirilir. 

* Zscaler özel erişim (ZPA) oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zscaler özel erişim (ZPA) kutucuğuna tıkladığınızda bu, Zscaler özel erişim (ZPA) oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Zscaler özel erişimini (ZPA) yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).