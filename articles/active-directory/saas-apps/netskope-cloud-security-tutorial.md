---
title: 'Öğretici: Netüse Yönetici Konsolu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Netüse Yönetici Konsolu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dec8f8065114b89dfa7bcaceee3f26855953dde2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081871"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Öğretici: Netüse Yönetici Konsolu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Netüse Yönetici Konsolu Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Netüse Yönetici Konsolu 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Netüse Yönetici Konsolu erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, Netüse Yönetici Konsolu için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Netüse Yönetici Konsolu çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Netüse Yönetici Konsolu **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Galeriden Netüse Yönetici Konsolu ekleme

Netüse Yönetici Konsolu tümleştirmesini Azure AD ile yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Netüse Yönetici Konsolu eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **netüse Yönetici Konsolu** yazın.
1. Sonuçlar panelinden **Netüse Yönetici Konsolu** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Netüse Yönetici Konsolu için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netüse Yönetici Konsolu ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Netüse Yönetici Konsolu içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Netüse Yönetici Konsolu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Netüse yönetıcı konsolu SSO 'Yu yapılandırın](#configure-netskope-administrator-console-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Netüse Yönetici Konsolu 'de B. Simon 'a sahip olmak için **[netüse Yönetici Konsolu test kullanıcısı oluşturun](#create-netskope-administrator-console-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **netüse Yönetici Konsolu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `Netskope_<OrgKey>`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri öğreticide ilerleyen kısımlarında bulabilirsiniz.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Oturum açma URL 'SI değerleri gerçek değil. Oturum açma URL 'SI değerini, gerçek oturum açma URL 'SI ile güncelleştirin. Oturum açma URL 'SI değerini almak için [Netüse Yönetici Konsolu istemci desteği ekibine](mailto:support@netskope.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Netüse Yönetici Konsolu uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Daha yukarıya ek olarak, Netüse Yönetici Konsolu uygulaması aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Ad |  Kaynak özniteliği|
    | ---------| --------- |
    | yönetici-rol | Kullanıcı. atandroles |

    > [!NOTE]
    > Azure AD 'de rol oluşturmayı öğrenmek için [buraya](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Netüse Yönetici Konsolu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Netüse Yönetici Konsolu erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Netüse Yönetici Konsolu**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netskope-administrator-console-sso"></a>Netüse Yönetici Konsolu SSO 'yu yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Yönetici Konsolu şirket sitenizde yönetici olarak oturum açın.

1. Sol gezinti bölmesindeki **Ayarlar** sekmesine tıklayın.

    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/config-settings.png)

1. **Yönetim** sekmesini tıklatın.

    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/config-administration.png)

1. **SSO** sekmesi ' ne tıklayın.

    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/config-sso.png)

1. **Ağ ayarları** bölümünde aşağıdaki adımları uygulayın:
    
    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. **Onaylama tüketici hizmeti URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.

    b. **Hizmet sağlayıcısı VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın.

1. **SSO/SLO ayarları** bölümünün altındakı **düzenleme ayarları** ' na tıklayın.

    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. **Ayarlar** açılan penceresinde, aşağıdaki adımları gerçekleştirin;

    ![Netüse Yönetici Konsolu yapılandırması](./media/netskope-cloud-security-tutorial/configuration.png)

    a. **SSO 'Yu etkinleştir**' i seçin.

    b. **IDP URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. İndirilen Base64 kodlamalı sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **IDP sertifika** metin kutusuna yapıştırın.

    e. **SSO 'Yu etkinleştir**' i seçin.

    f. **IDP SLO URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    g. **Gönder**' e tıklayın.

### <a name="create-netskope-administrator-console-test-user"></a>Netüse Yönetici Konsolu test kullanıcısı oluşturma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Yönetici Konsolu şirket sitenizde yönetici olarak oturum açın.

1. Sol gezinti bölmesindeki **Ayarlar** sekmesine tıklayın.

    ![Netüse Yönetici Konsolu kullanıcı oluşturma](./media/netskope-cloud-security-tutorial/config-settings.png)

1. **Etkin platform** sekmesi ' ne tıklayın.

    ![Netüse Yönetici Konsolu kullanıcı oluşturma](./media/netskope-cloud-security-tutorial/user1.png)

1. **Kullanıcılar** sekmesi ' ne tıklayın.

    ![Netüse Yönetici Konsolu kullanıcı oluşturma](./media/netskope-cloud-security-tutorial/add-user.png)

1. **Kullanıcı Ekle**' ye tıklayın.

    ![Netüse Yönetici Konsolu kullanıcı oluşturma](./media/netskope-cloud-security-tutorial/user-add.png)

1. Eklemek istediğiniz kullanıcının e-posta adresini girin ve **Ekle**' ye tıklayın.

    ![Netüse Yönetici Konsolu kullanıcı oluşturma](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Netüse Yönetici Konsolu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Netüse Yönetici Konsolu otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Netüse Yönetici Konsolu deneyin](https://aad.portal.azure.com/)