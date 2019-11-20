---
title: 'Öğretici: MS Azure SSO ile uyumluluk ofisi™ için Azure SSO erişimiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve MS Azure SSO Office™ için Azure SSO erişimi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Öğretici: Ahıdex uyumluluk ofisi için MS Azure SSO erişimi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory™

Bu öğreticide, Azure Active Directory (Azure AD) ile Etmx uyumluluk Office™ için MS Azure SSO erişimini nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile, Ahıdex uyumluluk ofisi™ için MS Azure SSO erişimini tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Ahidex uyumluluk Office™ için MS Azure SSO erişimi 'ne erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, Ahidex uyumluluk ofisi™ için MS Azure SSO erişimi 'nin otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* MS Azure SSO, Ahıdex uyumluluk ofisi™ çoklu oturum açma (SSO) özellikli abonelik için erişim.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* I, ETX uyumluluk Office™ için MS Azure SSO erişimi, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Galeriden ETX uyumluluk ofisi™ için MS Azure SSO erişimi ekleme

Azure AD 'ye, etmiş x uyumluluk Office™ yönelik MS Azure SSO erişimi tümleştirmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize, etmekte olan ETX uyumluluk Office™ için MS Azure SSO erişimi eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **MS Azure SSO erişimi Için, Etiıdex uyumluluk ofisi™** yazın.
1. Sonuçlar panelinden 3 **Azure SSO uyumluluk Office™ Için MS Azure SSO erişimi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme, Ahıdex uyumluluk ofisi™ için MS Azure SSO erişimi

**B. Simon**adlı bir test kullanıcısı kullanarak, Azure AD SSO 'Yu Etiidex uyumluluk ofisi™ Için MS Azure SSO erişimiyle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında, Etax uyumluluk Office™ için MS Azure SSO erişimi arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, Ahıdex uyumluluk Office™ için MS Azure SSO erişimiyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. , Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Etiıdex uyumluluk ofisi SSO 'su IÇIN MS Azure SSO erişimi 'Ni yapılandırın](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, **[Etidex uyumluluk ofisi için](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** MS azure SSO™ erişimi için MS Azure SSO erişimi için MS Azure SSO erişimi oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **MS Azure SSO erişimi Için, Ahıdex uyumluluk Office™** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`com.ethidex.prod.<CLIENTID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, [Ahıdex uyumluluk ofisi™ destek ekibine MS Azure SSO erişimi](mailto:support@ethidex.com) ile iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. MS Azure SSO, etmex uyumluluk ofisi™ uygulama uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. MS Azure SSO uyumluluk Office™ için Azure SSO erişimi, **NameIdentifier** 'ın **User. Mail**ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **sertifika (ham)** bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **MS Azure SSO erişimi için Etax uyumluluk ofisi™** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Ahidex uyumluluk Office™ için MS Azure SSO erişimine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Etiıdex uyumluluk Office™ Için MS Azure SSO erişimi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO 'yu, Etiıdex uyumluluk ofisi SSO 'SU için yapılandırma

Azure 'da çoklu oturum açmayı yapılandırmak için, **Etiıdex uyumluluk office™** tarafında, indirilen **sertifikayı (ham)** ve uygun kopyalanmış URL 'Leri Azure Portal, [Ahidex uyumluluk ofisi için MS Azure sso erişimi 'ne göndermeniz gerekir™ destek ekibi](mailto:support@ethidex.com). Bunlar, her iki kenarı da düzgün ayarlandığından SAML SSO bağlantı sağlamak için bu ayarı ayarlayın.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>MS Azure SSO ile uyumlu uyumluluk ofisi test kullanıcısı için erişim oluşturma

Bu bölümde, MS Azure SSO 'da, Ahidex uyumluluk ofisi™ için B. Simon adlı bir Kullanıcı oluşturacaksınız. Ahıdex uyumluluk Office™ platformu için MS Azure SSO erişimi 'ne kullanıcıları eklemek amacıyla, [Etmetix uyumluluk ofisi™ destek ekibi Için MS Azure SSO erişimi](mailto:support@ethidex.com) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde, Etiıdex uyumluluk Office™ için MS Azure SSO Erişimi ' ne tıkladığınızda, SSO 'yu ayarladığınız ETX uyumluluk ofisi™ için MS Azure SSO Erişimi ' ne otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Etiidex uyumluluk ofisi™ için MS Azure SSO erişimi 'ni deneyin](https://aad.portal.azure.com/)

