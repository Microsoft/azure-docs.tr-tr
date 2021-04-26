---
title: 'Öğretici: SD öğeleriyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SD öğeleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893677"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Öğretici: SD öğeleriyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide SD öğelerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SD öğelerini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SD öğelerine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SD öğelerine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SD öğeleri çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SD öğeleri **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sd-elements-from-the-gallery"></a>Galeriden SD öğeleri ekleme

SD öğelerinin Azure AD ile tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize SD öğeleri eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SD öğeleri** yazın.
1. Sonuçlar panelinden **SD öğeleri** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>SD öğeleri için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, SD ÖĞELERIYLE Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SD öğelerinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SD öğeleriyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[SD öğelerini yapılandırma SSO](#configure-sd-elements-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan SD öğelerinde B. Simon 'un bir karşılığı olan SD **[öğeleri oluşturun](#create-sd-elements-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **SD öğeleri** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [SD öğeleri istemci destek ekibine](mailto:support@sdelements.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. SD öğeleri uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. SD öğeleri uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | --- | --- |
    | e-posta |Kullanıcı. Mail |
    | FirstName |Kullanıcı. |
    | Soyadı |User. soyadı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **SD öğelerini ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, SD öğelerine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SD öğeleri**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sd-elements-sso"></a>SD öğeleri SSO 'SU yapılandırma

1. Çoklu oturum açmayı etkinleştirmek için [SD öğelerinin destek ekibine](mailto:support@sdelements.com) başvurun ve indirilen sertifika dosyasını sağlayın.

1. Farklı bir tarayıcı penceresinde, SD öğeleri kiracınızda yönetici olarak oturum açın.

1. Üstteki menüde, **sistem**' e ve ardından **Çoklu oturum açma**' ya tıklayın.

    ![Açılır listeden "sistem" seçili ve "çoklu oturum açma" gösteren ekran görüntüsü.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. **Tek Sign-On ayarları** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![Tek Sign-On yapılandırma](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. **SSO türü** olarak **SAML**' yi seçin.

    b. **Kimlik sağlayıcısı VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **Kimlik sağlayıcısı tek Sign-On hizmeti** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Kaydet**’e tıklayın.

### <a name="create-sd-elements-test-user"></a>SD öğeleri test kullanıcısı oluştur

Bu bölümün amacı SD öğelerinde B. Simon adlı bir Kullanıcı oluşturmaktır. SD öğelerinde, SD öğelerinin oluşturulması, el ile gerçekleştirilen bir görevdir.

**SD öğelerinde B. Simon oluşturmak için aşağıdaki adımları uygulayın:**

1. Bir Web tarayıcısı penceresinde, SD öğeleri şirket sitenizde yönetici olarak oturum açın.

1. Üstteki menüden **Kullanıcı yönetimi**' ne ve ardından **Kullanıcılar**' a tıklayın.

    !["Kullanıcı Yönetimi" açılır listesinden "kullanıcılar" ın seçili olduğunu gösteren ekran görüntüsü.](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. **Yeni Kullanıcı Ekle**' ye tıklayın.

    !["Yeni Kullanıcı Ekle" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. **Yeni Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![SD öğeleri test kullanıcısı oluşturma](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. **E-posta** metin kutusunda, kullanıcının e-postasını girin **b.simon@contoso.com** .

    b. **Ad** metin kutusuna B gibi kullanıcının adını girin **.**

    c. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını girin.

    d. **Rol** olarak **Kullanıcı**' yı seçin.

    e. **Kullanıcı oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde SD öğeleri kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SD öğelerinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile SD öğelerini deneyin](https://aad.portal.azure.com/)