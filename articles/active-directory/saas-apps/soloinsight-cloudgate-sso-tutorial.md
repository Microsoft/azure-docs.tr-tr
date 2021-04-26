---
title: 'Öğretici: Soloinsight-CloudGate SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Soloinsight-CloudGate SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 193d779a2e9246fe36af4828251a15d593e80ac7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514784"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Öğretici: Azure Active Directory Soloinsight-CloudGate SSO 'yu tümleştirme

Bu öğreticide, Soloinsight-CloudGate SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Soloinsight-CloudGate SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Soloinsight-CloudGate SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SSO 'yu Soloinsight-CloudGate için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Soloinsight-CloudGate SSO çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Soloinsight-CloudGate SSO, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Galeriden Soloinsight-CloudGate SSO ekleme

Soloinsight-CloudGate SSO 'yu Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Soloinsight-CloudGate SSO eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Soloinsight-cloudgate SSO** yazın.
1. Sonuçlar panelinden **Soloinsight-CloudGate SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**Britta Simon** adlı bir test kullanıcısı kullanarak Azure AD sso 'YU Soloinsight-CloudGate SSO ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Soloinsight-CloudGate SSO içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Soloinsight-CloudGate SSO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Soloinsight-CloudGate SSO 'Yu yapılandırın](#configure-soloinsight-cloudgate-sso)** .
3. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı Soloinsight-CloudGate SSO 'da Britta Simon 'a sahip olmak için **[Soloinsight-CloudGate SSO test kullanıcısı oluşturun](#create-soloinsight-cloudgate-sso-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Soloinsight-cloudgate SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.sigateway.com/login`

    1. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Bu değerler gerçek değildir. Bu değerleri, öğreticinin **yapılandırma Soloinsight-CloudGate SSO çoklu oturum** açma bölümünde daha sonra açıklanan gerçek oturum açma URL 'Si ve tanımlayıcı ile güncelleştirin.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Soloinsight-CloudGate SSO 'Yu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO 'yu yapılandırma

1. Yapılandırma Soloinsight-CloudGate SSO içinde otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, Kurulum ' a tıklayın **Soloinsight-CloudGate SSO** sızı Soloinsight-CloudGate SSO uygulamasına yönlendirirler. Buradan Soloinsight-CloudGate SSO 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-8 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Soloinsight-CloudGate SSO 'yu el ile ayarlamak istiyorsanız, yeni bir Web tarayıcısı penceresi açın ve Soloinsight-CloudGate SSO şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Temel SAML yapılandırılırken Azure portal yapıştırılacak değerleri almak için, kimlik bilgilerinizi kullanarak CloudGate web portalında oturum açın ve ardından aşağıdaki yol **giriş>yönetim>sistem>ayarları**' nda bulunan SSO ayarlarına erişin.

    ![CloudGate SSO ayarları](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML tüketicisi URL 'SI**

    * **SAML tüketicisi URL 'si** ve **yeniden yönlendirme URL** 'si alanlarında bulunan bağlantıları kopyalayın ve **tanımlayıcı (varlık kimliği)** ve **yanıt URL 'si** alanları için Azure Portal **temel SAML yapılandırması** bölümüne yapıştırın.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML Imzalama sertifikası**

    * Azure portal SAML Imza sertifikası listesinden indirilen sertifika (base64) dosyasının kaynağına gidin ve sağ tıklayın. Listeden **Notepad + + seçeneğiyle Düzenle** ' yi seçin. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Sertifikayı sertifika (base64) Notepad + + dosyasına kopyalayın.

        ![Sertifika kopyası](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * İçeriği CloudGate Web portalı SSO ayarları **sertifikası** alanına yapıştırın ve Kaydet düğmesine tıklayın.

        ![Sertifika Portalı](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Varsayılan Grup**

    * CloudGate Web portalındaki **varsayılan grup** seçeneğinin açılır listesinden **İş Yöneticisi** ' ni seçin.

        ![Varsayılan Grup](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD tanımlayıcısı ve oturum açma URL 'SI**

    * Azure portal kopyalanmış **oturum açma URL 'si** , **Soloinsight-CloudGate SSO** yapılandırması, cloudgate Web portalı SSO ayarları bölümüne girilir.

    * CloudGate Web portalı **ad oturum açma URL 'si** alanındaki Azure Portal **oturum açma URL 'si** bağlantısını yapıştırın.

    * CloudGate Web portalı **ad tanımlayıcı** alanındaki Azure Portal **Azure AD tanımlayıcı** bağlantısını yapıştırın

        ![Ad oturum açma](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

Bu bölümde, Soloinsight-CloudGate SSO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Soloinsight-CloudGate SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO test kullanıcısı oluşturma

Bir test kullanıcısı oluşturmak için, CloudGate Web portalınızın ana menüsünde **çalışanlar** ' ı seçin ve yeni çalışan Ekle formunu doldurun. Test kullanıcısına atanacak olan yetkili düzeyi **Iş Yöneticisi** , tüm gerekli alanlar doldurulduktan sonra **Oluştur** ' a tıklayın.

![Çalışan testi](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Soloinsight-CloudGate SSO kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Soloinsight-CloudGate SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)