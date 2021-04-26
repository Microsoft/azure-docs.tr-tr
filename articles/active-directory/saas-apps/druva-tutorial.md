---
title: 'Öğretici: Druva ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Druva arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: a7868d702ff3f1190d7f51e4ad7316508d453015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Öğretici: Druva ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Druva 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Druva 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Druva 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Druva otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Druva çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Druva, **IDP** tarafından başlatılan SSO 'yu destekler
* Druva SSO 'yu yapılandırdıktan sonra, kuruluşunuzun gizli verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-druva-from-the-gallery"></a>Galeriden Druva ekleme

Druva tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Druva yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Druva** yazın.
1. Sonuçlar panelinden **Druva** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Druva için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Druva ile yapılandırın ve test edin. SSO 'nun çalışması için, Druva içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Druva ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[DRUVA SSO 'Yu yapılandırın](#configure-druva-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Druva 'de B. Simon 'ya karşılık gelen bir **[Druva test kullanıcısı oluşturun](#create-druva-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Druva** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna dize değerini yazın: `DCP-login` .
    
    b. **Yanıt URL 'si (onaylama tüketici hizmeti URL 'si)** metin kutusuna URL 'yi yazın: `https://cloud.druva.com/wrsaml/consume` .

1. **Kaydet**’e tıklayın.

1. Druva uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Druva uygulaması, yukarıdakine ek olarak, aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | DCP Yönetici konsolundan, tırnak işaretleri olmadan oluşturulan SSO belirteci.  Örneğin: X-XXXXX-XXXX-S-A-M-P-L-E + TXOXKXEXNX =. Azure, kimlik doğrulama belirtecinin çevresine otomatik olarak tırnak işaretleri ekler. |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Druva ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Druva 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Druva**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-druva-sso&quot;></a>Druva SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Druva şirket sitenizde yönetici olarak oturum açın.

1. Sol üst köşedeki Druva logosu ' na tıklayın ve ardından **Druva bulut ayarları**' na tıklayın.

    ![Ayarlar](./media/druva-tutorial/ic795091.png &quot;Ayarlar")

1. **Çoklu oturum açma** sekmesinde **Düzenle**' ye tıklayın.

    !["Düzenle" düğmesinin seçili olduğu "erişim ayarları-çoklu oturum açma" sekmesini gösteren ekran görüntüsü.](./media/druva-tutorial/ic795092.png "Tek Sign-On ayarları")

1. **Tek Sign-On ayarlarını Düzenle** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Tek Sign-On ayarları](./media/druva-tutorial/ic795095.png "Tek Sign-On ayarları")

    1. **Kimlik sağlayıcısı oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. Base-64 kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından **kimlik sağlayıcısı sertifikası** metin kutusuna yapıştırın

       > [!NOTE]
       > Yöneticiler için tek Sign-On etkinleştirmek üzere, **YÖNETICILERIN SSO sağlayıcısı aracılığıyla Druva bulutta oturum açmasını** ve **Druva Cloud Administrators (önerilen) onay kutularına daha güvenli erişim izni vermeyi** seçin. Druva, IDP 'deki herhangi bir hatadan dolayı DCP konsoluna erişebilmeleri için **Yöneticiler Için Failsafe** 'i etkinleştirmenizi önerir. Ayrıca, yöneticilerin DCP konsoluna erişmek için hem SSO hem de DCP parolasını kullanmasını sağlar.

    1. **Kaydet**’e tıklayın. Bu, SSO kullanarak Druva bulut platformuna erişimi sağlar.

### <a name="create-druva-test-user"></a>Druva test kullanıcısı oluştur

Bu bölümde, Druva içinde B. Simon adlı bir Kullanıcı oluşturulur. Druva, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Druva içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Druva kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Druva için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Druva deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)