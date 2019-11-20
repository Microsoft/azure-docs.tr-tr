---
title: 'Öğretici Azure Active Directory: Facebook tarafından çalışma alanı ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Facebook tarafından Azure Active Directory ve çalışma alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e98bf83cedc22aa565ba0f98089e9f6f217728
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787643"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Öğretici: Facebook tarafından çalışma alanı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, çalışma alanını Azure Active Directory (Azure AD) ile Facebook ile tümleştirmeyi öğreneceksiniz. Çalışma alanını Facebook ile Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Facebook tarafından çalışma alanına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Facebook tarafından çalışma alanına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

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
* Facebook tarafından çalışma alanı  **[Otomatik Kullanıcı sağlamayı](workplacebyfacebook-provisioning-tutorial.md) destekler**
* Facebook mobil uygulaması 'nın çalışma alanı artık SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Galeriden Facebook tarafından çalışma alanı ekleme

Çalışma alanınızın Facebook ile Azure AD arasında tümleştirilmesini yapılandırmak için, Facebook 'tan Facebook 'tan yönetilen SaaS uygulamaları listenize çalışma alanı eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Facebook ile çalışma alanı** yazın.
1. Sonuçlar panelinden **Facebook tarafından çalışma alanı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Facebook tarafından çalışma alanı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Facebook Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Facebook tarafından çalışma alanındaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Facebook ile çalışma alanıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Facebook SSO Ile çalışma alanı yapılandırma](#configure-workplace-by-facebook-sso)** .
    * Facebook 'a ait çalışma alanında, kullanıcının Azure AD gösterimine bağlı olan Facebook tarafından Iş yerinde B. Simon 'ın bir karşılığı olacak şekilde **[çalışma alanı oluşturun](#create-workplace-by-facebook-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Facebook uygulama tümleştirmesiyle çalışma alanı** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<instancename>.facebook.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Çalışma alanı topluluğlarınızın doğru değerleri için çalışma alanı şirket panosunun kimlik doğrulama sayfasına bakın.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Çalışma alanını Facebook 'a göre ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Facebook tarafından çalışma alanına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Facebook tarafından çalışma alanı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workplace-by-facebook-sso"></a>Çalışma alanını Facebook SSO ile yapılandırma

1. Çalışma alanındaki yapılandırmayı Facebook tarafından otomatik hale getirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya uzantı ekledikten sonra, **Facebook tarafından çalışma alanı 'Nı ayarla** ' ya tıklayın, sizi Facebook uygulamasına göre çalışma alanına yönlendirir. Buradan, Facebook tarafından çalışma alanında oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Çalışma alanını Facebook ile el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Facebook Şirket sitesi tarafından yönetici olarak çalışma alanınızda oturum açın ve aşağıdaki adımları gerçekleştirin:

    > [!NOTE]
    > SAML kimlik doğrulama işleminin bir parçası olarak, çalışma alanı, parametreleri Azure AD 'ye geçirmek için boyut olarak en fazla 2,5 kilobayt olan Sorgu dizelerini kullanabilir.

1. Sol gezinti panelinde **güvenlik** > **kimlik doğrulaması** sekmesi ' ne gidin.

    ![Yönetici paneli](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. **Çoklu oturum açma (SSO)** seçeneğini işaretleyin.
    
    b. **+ Yenı SSO sağlayıcısı ekle**' ye tıklayın.

1. **Kimlik doğrulama** sekmesinde, **Çoklu oturum açma (SSO)** öğesini seçin ve aşağıdaki adımları gerçekleştirin:

    ![Kimlik doğrulama sekmesi](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. **SSO sağlayıcısının adı**' nda, Azureadsso gibi SSO örnek adını girin.

    b. **SAML URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

    c. **SAML veren URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının**değerini yapıştırın.

    d. Azure portal 'den indirilen Not defteri 'nde **Base-64 kodlu sertifikanızı** açın, bu içeriği panonuza kopyalayın ve **SAML sertifikası** metin kutusuna yapıştırın.

    e. Örneğiniz için **hedef kitle URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    f. Örneğiniz için **alıcı URL** 'sini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **URL 'yi oturum aç** metin kutusuna yapıştırın.

    g. Bölümün altına kaydırın ve **test SSO** düğmesine tıklayın. Bu, Azure AD oturum açma sayfası sunulan bir açılan pencerenin görünmesine neden olur. Kimlik doğrulaması için kimlik bilgilerinizi normal olarak girin.

    **Sorun giderme:** Azure AD 'den geri döndürülmekte olan e-posta adresinin, oturum açtığınız çalışma alanı hesabıyla aynı olduğundan emin olun.

    h. Test başarıyla tamamlandıktan sonra sayfanın alt kısmına gidin ve **Kaydet** düğmesine tıklayın.

    i. Artık çalışma alanı kullanan tüm kullanıcılar, kimlik doğrulaması için Azure AD oturum açma sayfasıyla sunulacaktır.

1. **SAML Logout Redirect (isteğe bağlı)**  -

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
>Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [Facebook istemci destek ekibi tarafından çalışma alanına](https://workplace.fb.com/faq/) başvurun

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Facebook tarafından çalışma alanı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Facebook tarafından çalışma alanında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Facebook tarafından çalışma alanı için test SSO 'SU (mobil)

1. Çalışma alanını Facebook mobil uygulamasına göre açın. Oturum aç sayfasında **oturum**aç ' a tıklayın.

    ![Oturum açma](./media/workplacebyfacebook-tutorial/test05.png)

2. İş e-postanızı girin ve **devam**' a tıklayın.

    ![E-posta](./media/workplacebyfacebook-tutorial/test02.png)

3. **Yalnızca bir kez**tıklayın.

    ![Bir kez](./media/workplacebyfacebook-tutorial/test04.png)

4. **Izin ver**' e tıklayın.

    ![Izin ver](./media/workplacebyfacebook-tutorial/test03.png)

5. Son olarak, başarıyla oturum açtıktan sonra uygulama giriş sayfası görüntülenir.    

    ![Giriş sayfası](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Kullanıcı sağlamayı yapılandırma](workplacebyfacebook-provisioning-tutorial.md)

- [Azure AD ile Facebook ile çalışma alanını deneyin](https://aad.portal.azure.com)
