---
title: 'Öğretici: ILMS ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ILMS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 7289fe2ec1f39679dcec95f1f48a6efa9ed0cdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460351"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Öğretici: ILMS 'Leri Azure Active Directory ile tümleştirme

Bu öğreticide, ILMS 'Leri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. ILMS 'Leri Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ILMS erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* ILMS çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. ILMS **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-ilms-from-the-gallery"></a>Galeriden ILMS ekleme

ILMS 'lerin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize ILMS eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ILMS** yazın.
1. Sonuçlar panelinden **ILMS** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**Britta Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu ILMS ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ILMS içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ILMS 'Ler ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[ILMS SSO 'Yu yapılandırma](#configure-ilms-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[ILMS test kullanıcısı oluşturun](#create-ilms-test-user)** -ILMS 'de kullanıcının Azure AD gösterimine bağlanan Britta Simon 'un bir karşılığı vardır.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **ILMS** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, uygulamayı **IDP** başlatıldı modunda yapılandırmak istiyorsanız, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, ILMS yönetim PORTALı 'nda SAML ayarları ' nın **hizmet sağlayıcı** bölümünde kopyaladığınız **tanımlayıcı** değeri yapıştırın.

    b. **Yanıt URL 'si** metin kutusunda, ILMS YÖNETIM portalındaki SAML ayarları ' nın **hizmet sağlayıcı** bölümünden KOPYALADıĞıNıZ **uç nokta (URL)** değerini aşağıdaki düzene sahip olarak yapıştırın`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, ILMS YÖNETIM portalındaki SAML ayarlarının **hizmet sağlayıcı** bölümünden KOPYALADıĞıNıZ **uç nokta (URL)** değerini şu şekilde yapıştırın`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. JıT sağlamayı etkinleştirmek için, ILMS uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    > [!NOTE]
    > Bu öznitelikleri eşlemek için ILMS 'de **tanınmayan Kullanıcı hesabı oluşturmayı** etkinleştirmeniz gerekir. Öznitelikler yapılandırması hakkında fikir edinmek için [buradaki](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) yönergeleri izleyin.

1. Ayrıca, ILMS uygulaması, daha fazla özniteliğin SAML yanıtına geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği|
    | --------|------------- |
    | bölmenin | User. Departmanı |
    | region | Kullanıcı. durum |
    | bölüm | User. JobTitle |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **ILMS 'Yi ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-ilms-sso"></a>ILMS SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak **ILMS yönetici portalınızda** oturum açın.

2. SAML ayarlarını açmak ve aşağıdaki adımları gerçekleştirmek için **SSO:** **Ayarlar** sekmesinde SAML ' ye tıklayın:

    ![Ekran görüntüsü, S S O: SAML ' yi seçebileceğiniz ı L M S ayarları sekmesini gösterir.](./media/ilms-tutorial/1.png)

3. **Hizmet sağlayıcı** bölümünü genişletin ve **tanımlayıcı** ve **uç nokta (URL)** değerini kopyalayın.

    ![Ekran görüntüsü, değerleri alabileceğiniz SAML ayarlarını gösterir.](./media/ilms-tutorial/2.png) 

4. **Kimlik sağlayıcısı** bölümünde **meta verileri içeri aktar**' a tıklayın.

5. **SAML Imzalama sertifikası** bölümünden Azure Portal Indirilen **Federasyon meta veri** dosyasını seçin.

    ![Ekran görüntüsü, meta veri dosyasını seçebileceğiniz SAML ayarlarını gösterir.](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Tanımayan kullanıcıları tanımak üzere ILMS hesapları oluşturmak için JıT sağlamayı etkinleştirmek istiyorsanız aşağıdaki adımları izleyin:

    a. **Tanınmayan Kullanıcı hesabı oluştur**' a bakın.

    ![Ekran görüntüsü, tanınmayan Kullanıcı hesabını oluşturma seçeneğini gösterir.](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Azure AD 'deki öznitelikleri ILMS öznitelikleri ile eşleyin. Öznitelik sütununda, özniteliklerin adını veya varsayılan değeri belirtin.

    c. **Iş kuralları** sekmesine gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda bilgileri girebileceğiniz Iş kuralları ayarlarını gösterir.](./media/ilms-tutorial/5.png)

    d. Tek oturum açma sırasında mevcut olmayan bölgeler, bölümler ve departmanlar oluşturmak için, **tanınmayan bölgeleri, bölümleri ve departmanları oluşturma ' yı** işaretleyin.

    e. Kullanıcı profilinin her bir çoklu oturum açma işlemiyle güncelleştirilip güncelleştirilmediğini belirtmek için **oturum açma sırasında kullanıcı profilini Güncelleştir '** i işaretleyin.

    f. **Kullanıcı profilindeki zorunlu olmayan alanlar Için boş değerleri Güncelleştir** seçeneği işaretliyse, oturum açma sonrasında boş olan isteğe bağlı profil alanları kullanıcının ILMS profilinin bu alanlar için boş değerler içermesine neden olur.

    örneğin: **Hata bildirim e-postasını gönder** ' i işaretleyin ve hata bildirim e-postasını almak istediğiniz kullanıcının e-postasını girin.

7. Ayarları kaydetmek için **Kaydet** düğmesine tıklayın.

    ![Kaydet düğmesini gösteren ekran görüntüsü.](./media/ilms-tutorial/save.png)

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

Bu bölümde, ILMS 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ILMS**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-ilms-test-user"></a>ILMS test kullanıcısı oluşturma

Uygulama anında Kullanıcı sağlamayı ve kimlik doğrulama kullanıcılarının uygulamada otomatik olarak oluşturulmasını destekler. ILMS yönetim portalı 'nda SAML yapılandırması ayarı sırasında, **tanınmayan Kullanıcı hesabı oluştur** onay kutusunu TıKLADıYSANıZ JIT çalışacaktır.

El ile bir kullanıcı oluşturmanız gerekiyorsa, aşağıdaki adımları izleyin:

1. ILMS şirket sitenizde yönetici olarak oturum açın.

2. Kullanıcı **Kaydet** sayfasını açmak için **kullanıcıları** Kullanıcı sekmesinde **Kaydet** ' e tıklayın.

   ![Ekran görüntüsü, kullanıcıyı Kaydet ' I seçebileceğiniz ı L M S ayarları sekmesini gösterir.](./media/ilms-tutorial/3.png)

3. **Kullanıcı Kaydet** sayfasında, aşağıdaki adımları gerçekleştirin.

    ![Ekran görüntüsü, belirtilen bilgileri girdiğiniz Kullanıcı Kaydet sayfasını gösterir.](./media/ilms-tutorial/create_testuser_add.png)

    a. **Ilk ad** metin kutusuna, Britta gibi ilk adı yazın.

    b. **Soyadı** metin kutusuna Simon gibi son adı yazın.

    c. **E-posta kimliği** metin kutusuna, gibi kullanıcının e-posta adresini yazın BrittaSimon@contoso.com .

    d. **Bölge** açılan kutusunda bölge değerini seçin.

    e. **Bölüm** açılan menüsünde, bölüm için değeri seçin.

    f. **Departman** açılan menüsünde departman değerini seçin.

    örneğin: **Kaydet**’e tıklayın.

    > [!NOTE]
    > Kayıt postası **Gönder** onay kutusunu seçerek kullanıcıya kayıt postası gönderebilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde ILMS kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız ILMS 'Ler için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)