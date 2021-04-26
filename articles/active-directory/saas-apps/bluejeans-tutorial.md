---
title: 'Öğretici: Azure AD için BlueJeans ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure AD için Azure Active Directory ve şema arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: a920abacb0df030f7d38aed11e04a693cfa352a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686606"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Öğretici: Azure AD için BlueJeans ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure AD için şema Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Azure AD 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Azure AD için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure AD çoklu oturum açma (SSO) özellikli abonelik için BlueJeans.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Azure AD için BlueJeans, **SP** tarafından başlatılan SSO 'yu destekler

* Azure AD için BlueJeans [ **Otomatik** Kullanıcı sağlamayı destekler](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Galeriden Azure AD için şema ekleme

Azure AD 'de BlueJeans 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Azure AD için şema ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Azure AD Için BlueJeans** yazın.
1. Sonuçlar panelinden **Azure AD Için BlueJeans** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Azure AD için şema için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Azure AD için şema ile yapılandırın ve test edin. SSO 'nun çalışması için bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Azure AD 'de bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Azure AD için şema ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Azure AD SSO Için BlueJeans 'ı yapılandırın](#configure-bluejeans-for-azure-ad-sso)** .
    1. Azure AD **[test kullanıcısı için şema oluşturma](#create-bluejeans-for-azure-ad-test-user)** -Kullanıcı Azure AD gösterimi ile bağlantılı olan Azure AD Için BlueJeans 'da B. Simon 'un bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Azure AD** uygulama tümleştirmesinin şeması sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.bluejeans.com`

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın:`http://samlsp.bluejeans.com`

    a. **Yanıt URL** 'si metin kutusuna bir URL yazın:`https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > Sign-On URL değeri gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Azure AD istemci desteği ekibine yönelik BlueJeans](https://support.bluejeans.com/contact) ile iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. BlueJeans uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Daha fazlasına ek olarak, şema yanıtında aşağıda gösterilen bazı özniteliklerin daha fazla özniteliğe geri geçirilmesi beklenir. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ---------| --------- |
    | Telefon | Kullanıcı. telephoneNumber |
    | başlık | User. JobTitle |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Azure AD için şema ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure AD 'ye yönelik şema erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Azure AD Için BlueJeans**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-bluejeans-for-azure-ad-sso&quot;></a>Azure AD SSO için şema yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak **Azure AD** Şirket sitesi için şema yöneticinize oturum açın.

2. **Yönetici \> grubu ayarları \> güvenliği**' ne gidin.

    ![Ekran görüntüsü, yönetici sekmesi seçili olan ve Grup ayarı ve güvenlik seçili olan bir tarayıcı penceresinin bir parçasını gösterir.](./media/bluejeans-tutorial/ic785868.png &quot;Yönetici")

3. **Güvenlik** bölümünde aşağıdaki adımları uygulayın:

    ![SAML çoklu oturum açma](./media/bluejeans-tutorial/ic785869.png "SAML çoklu oturum açma")

    a. **SAML çoklu oturum açma** seçeneğini belirleyin.

    b. **Otomatik sağlamayı etkinleştir**' i seçin.

4. Aşağıdaki adımlarla ilerleyin:

    ![Sertifika yolu](./media/bluejeans-tutorial/ic785870.png "Sertifika yolu")

    a. Azure portal indirdiğiniz Base-64 kodlu sertifikayı karşıya yüklemek için **Dosya Seç**' e tıklayın.

    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Parola DEĞIŞTIRME URL** metin kutusuna, Azure Portal kopyaladığınız **parola URL 'sini Değiştir** değerini yapıştırın.

    d. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

5. Aşağıdaki adımlarla ilerleyin:

    ![Değişiklikleri Kaydet](./media/bluejeans-tutorial/ic785874.png "Değişiklikleri Kaydet")

    a. **Kullanıcı kimliği** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    b. **E-posta** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    c. **Değişiklikleri Kaydet**' e tıklayın.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Azure AD test kullanıcısı için şema oluşturma

Bu bölümün amacı, Azure AD için şema olarak B. Simon adlı bir Kullanıcı oluşturmaktır. Azure AD şeması, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](bluejeans-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. **Azure AD** şirket sitenizde yönetici olarak şema olarak oturum açın.

2. **Yönetıcı \> KULLANıCıLARı Yönet \> Kullanıcı Ekle**' ye gidin.

    ![Ekran görüntüsü, yönetici sekmesi seçiliyken, kullanıcıları Yönet ve kullanıcıları Ekle seçiliyken bir tarayıcı penceresinin bir parçasını gösterir.](./media/bluejeans-tutorial/ic785877.png "Yönetici")

    > [!IMPORTANT]
    > **Kullanıcı Ekle** sekmesi yalnızca, **güvenlikte sekmesinde** **otomatik sağlamayı etkinleştir** seçeneği işaretli değilse kullanılabilir.

3. **Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda açıklanan bilgileri girebileceğiniz Kullanıcı ekle bölümünü gösterir.](./media/bluejeans-tutorial/ic785886.png "Kullanıcı Ekleme")

    a. **Ad** metin kutusuna **B** gibi kullanıcının adını girin.

    b. **Soyadı** metin kutusuna, **Simon** gibi kullanıcı adının soyadını girin.

    c. **Azure AD Kullanıcı adı için bir şema seçin** metin kutusuna, **Brittasıon** gibi kullanıcının Kullanıcı adını girin

    d. **Parola oluştur** metin kutusuna parolanızı girin.

    e. **Şirket** metin kutusuna şirketinizi girin.

    f. **E-posta adresi** metin kutusuna kullanıcının e-postasını girin `b.simon@contoso.com` .

    örneğin: **Azure AD toplantısı için şema oluşturma ı. D** metin kutusuna toplantı kimliğinizi girin.

    h. **Bir moderatör geçiş kodu seçin** metin kutusuna geçiş kodunuzu girin.

    i. **Devam**' a tıklayın.

    ![Ekran görüntüsü, Kullanıcı Ekle düğmesi seçiliyken ayarları ve özellikleri görüntüleyebileceğiniz Kullanıcı ekle bölümünü gösterir.](./media/bluejeans-tutorial/ic785887.png "Kullanıcı Ekleme")

    J. **Kullanıcı Ekle**' ye tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesabı oluşturma araçları veya Azure AD Kullanıcı hesapları sağlamak için şema tarafından sunulan API 'Ler tarafından sunulan API 'Ler için başka bir şema kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Azure AD kutucuğunun şeması ' nı tıklattığınızda, SSO 'yu ayarladığınız Azure AD için şema için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Azure AD için BlueJeans 'ı deneyin](https://aad.portal.azure.com/)