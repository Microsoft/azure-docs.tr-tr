---
title: 'Öğretici: Carbonite uç noktası yedeklemeyle Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Carbonite uç noktası yedeklemesi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456492"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Öğretici: Carbonite uç nokta yedeklemesini Azure Active Directory ile tümleştirme

Bu öğreticide, Carbonite uç nokta yedeklemesini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Carbonite uç noktası yedeklemesini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Carbonite uç noktası yedeklemesine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Carbonite uç noktası yedeklemesine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Carbonite uç noktası yedeklemesi çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Carbonite uç noktası yedeklemesi **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Galeriden Carbonite uç noktası yedeklemesi ekleme

Carbonite uç nokta yedeğinin tümleştirmesini Azure AD ile yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Carbonite uç noktası yedeklemesi eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Carbonite uç noktası yedeklemesi** yazın.
1. Sonuçlar panelinden **Carbonite uç noktası yedeklemesi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Carbonite uç noktası yedeklemesi Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Carbonite uç noktası yedeklemesiyle ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Carbonite uç noktası yedeklemesi ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[Carbonite uç nokta yedekleme SSO 'Su yapılandırma](#configure-carbonite-endpoint-backup-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. **[Carbonite uç nokta yedekleme testi kullanıcısına](#create-carbonite-endpoint-backup-test-user)** , kullanıcının Azure AD gösterimine bağlı olan Carbonite uç noktası yedeğine ait B. Simon 'un bir karşılığı olacak şekilde.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Carbonite uç nokta yedekleme** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki URL 'lerden birini yazın:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki URL 'lerden birini yazın:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna aşağıdaki URL 'lerden birini yazın:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Carpriite uç noktası yedeklemesini ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Carbonite uç nokta yedekleme SSO 'SU yapılandırma

1. Carbonite uç noktası yedeklemesiyle yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **Kurulum Carbonite uç noktası yedeklemesi** ' ne tıklayın, sizi Carbonite uç noktası yedekleme uygulamasına yönlendirir. Buradan, Carbonite uç noktası yedeklemesi 'nde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Carbonite uç noktası yedeklemesini el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Carbonite uç noktası yedeklemesi şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sol bölmeden **Şirket** ' e tıklayın.

    ![Ekran görüntüsü, şirketin seçtiği Carbonite uç noktasını gösterir.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. **Çoklu oturum** açma ' ya tıklayın.

    ![Ekran görüntüsünde çoklu oturum açma seçiliyken şirket görüntülenir.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. **Etkinleştir** ' e tıklayın ve ardından yapılandırmak Için **Ayarları Düzenle** ' ye tıklayın.

    ![Ekran görüntüsü, etkinleştirme ve düzenleme ayarlarına sahip çoklu oturum açma sekmesini gösterir.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. **Çoklu oturum açma** ayarları sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda açıklanan bilgilerle çoklu oturum açma sekmesini gösterir.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. **Kimlik sağlayıcısı adı** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    1. **Kimlik sağlayıcısı URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. Azure portal indirilen **sertifika (base64)** dosyasını karşıya yüklemek Için **Dosya Seç** ' e tıklayın.

    1. **Kaydet**’e tıklayın.

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

Bu bölümde, Carbonite uç noktası yedeklemesine erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Carbonite uç noktası yedeklemesi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite uç noktası yedekleme testi Kullanıcı Oluştur

1. Farklı bir Web tarayıcısı penceresinde, Carbonite uç nokta yedekleme şirket sitenizde yönetici olarak oturum açın.

1. Sol bölmeden **Kullanıcılar** ' a ve ardından **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü, Kullanıcı ve Seçili kullanıcıları Ekle ' nin Carbonite uç noktası sayfasını gösterir.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. **Kullanıcı Ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, burada açıklanan adımları gerçekleştirebileceğiniz Kullanıcı ekleme sayfasını gösterir.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. **E-posta**, **ad**, kullanıcının **Soyadı** ve Kullanıcı için gerekli izinleri kurumsal gereksinimlere göre girin.

    1. **Kullanıcı Ekle**' ye tıklayın.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Carbonite uç nokta yedekleme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Carbonite uç noktası yedeğine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)