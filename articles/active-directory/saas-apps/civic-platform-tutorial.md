---
title: 'Öğretici: Cıvic platformuyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Cıvic platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455978"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Öğretici: Cıvic platformunu Azure Active Directory ile tümleştirme

Bu öğreticide, Cıvic platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Cıvic platformunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Cıvic platformuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cıvic platformunda otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Cıvic platformu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cıvic platformu, **SP** tarafından başlatılan SSO 'yu destekler





## <a name="adding-civic-platform-from-the-gallery"></a>Galeriden Cıvic platformu ekleme

Cıvic platformunun tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Cıvic platformunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cıvic platformu** yazın.
1. Sonuçlar panelinden **Cıvic platformu** ' nu seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Cıvic platformuyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Cıvic platformunda ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Cıvic platformuyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Cıvic platform SSO 'Yu yapılandırın](#configure-civic-platform-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Kullanıcı Azure AD gösterimine bağlı olan Cıvic platformunda B. Simon 'a sahip olmak için **[Cıvic platform test kullanıcısı oluşturun](#create-civic-platform-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Cıvic platform** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.accela.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın:`civicplatform.accela.com`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Bu değeri, gerçek oturum açma URL 'siyle güncelleştirin. Bu değeri almak için [Cıvic platform istemci destek ekibine](mailto:skale@accela.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Ekran görüntüsü, l olduğunuz uygulama Federasyon meta verilerini kopyalayabileceğiniz SAML Imzalama sertifikası sayfasını gösterir.](common/copy-metadataurl.png)

1.   >  Azure AD 'de Azure Active Directory **uygulama kayıtları** gidin, uygulamanızı seçin.

1. **Dizin (kiracı) kimliğini** kopyalayın ve Not defteri 'nde depolayın.

    ![Dizini (kiracı KIMLIĞI) kopyalayın ve uygulama kodunuzda depolayın](media/civic-platform-tutorial/directoryid.png)

1. **Uygulama kimliğini** kopyalayın ve Not defteri 'nde depolayın.

   ![Uygulama (istemci) KIMLIĞINI Kopyala](media/civic-platform-tutorial/applicationid.png)

1.   >  Azure AD 'de Azure Active Directory **uygulama kayıtları** gidin, uygulamanızı seçin. **Sertifikalar & parolaları**' nı seçin.

1. Istemci gizli dizileri **-> yeni istemci parolası**' nı seçin.

1. Gizli anahtar ve süre için bir açıklama sağlayın. İşiniz bittiğinde **Ekle**' yi seçin.

   > [!NOTE]
   > İstemci gizliliğini kaydettikten sonra, istemci parolasının değeri görüntülenir. Anahtarı daha sonra alamadığı için bu değeri kopyalayın.

   ![Daha sonra geri alamadığı için gizli değeri kopyalayın](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Cıvic platform SSO 'yu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve Atlasme bulut şirket sitenizde yönetici olarak oturum açın.

1. **Standart seçeneklere** tıklayın.

    ![Ekran görüntüsü Atlasme bulut sitesini, yönetici araçları altında kullanıma hazır standart seçeneklere sahip olarak gösterir.](media/civic-platform-tutorial/standard-choices.png)

1. Standart bir seçim **ssoconfig** oluşturun.

1. **Ssoconfig** ve Gönder için arama yapın.

    ![Ekran görüntüsünde, ad s s o config girilen standart seçimler araması gösterilmektedir.](media/civic-platform-tutorial/sso-config.png)

1. Kırmızı nokta ' a tıklayarak SSOCONFIG ' i genişletin.

    ![Ekran görüntüsünde, bulunan S S O YAPıLANDıRMAYA git standart seçimleri gösterilmektedir.](media/civic-platform-tutorial/sso-config01.png)

1. Aşağıdaki adımda SSO ile ilgili yapılandırma bilgilerini sağlayın:

    ![Ekran görüntüsünde, S S O YAPıLANDıRMASı için standart seçimler öğesi düzenleme gösterilmektedir.](media/civic-platform-tutorial/sso-config02.png)

    1. **ApplicationId** alanına, Azure Portal KOPYALADıĞıNıZ **uygulama kimliği** değerini girin.

    1. **ClientSecret** alanına, Azure Portal kopyaladığınız **gizli** değeri girin.

    1. **Directoryıd** alanına, Azure Portal kopyaladığınız **Dizin (kiracı) kimliği** değerini girin.

    1. Idpname girin. Örn:- `Azure` .

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

Bu bölümde, Cıvic platformuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Cıvic platformu**' nu seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-civic-platform-test-user"></a>Cıvic platform test kullanıcısı oluştur

Bu bölümde, Cıvic platformunda B. Simon adlı bir Kullanıcı oluşturacaksınız. Cıvic [Platform istemci destek ekibine](mailto:skale@accela.com)kullanıcıları eklemek Için cıvic platform destek ekibi ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Cıvic platform kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Cıvic platformunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)