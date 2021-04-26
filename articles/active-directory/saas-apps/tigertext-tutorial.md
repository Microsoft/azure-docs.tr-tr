---
title: 'Öğretici: TigerConnect Secure Messenger ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve TigerConnect güvenli Messenger arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182164"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Öğretici: TigerConnect Secure Messenger ile Azure Active Directory tümleştirme

Bu öğreticide, TigerConnect Secure Messenger 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

TigerConnect Secure Messenger 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, TigerConnect güvenli Messenger erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla güvenli Messenger (çoklu oturum açma) ile bağlantı kurmak için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini TigerConnect Secure Messenger ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* Çoklu oturum açma özellikli bir Tıgerconnect güvenli Messenger aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edin ve TigerConnect Secure Messenger 'ı Azure AD ile tümleştirin.

* TigerConnect güvenli Messenger, **SP** tarafından başlatılan SSO 'yu destekler
* TigerConnect güvenli Messenger 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infiliğini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Galeriden Tıgerconnect Secure Messenger ekleniyor

TigerConnect Secure Messenger 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Tıgerconnect Secure Messenger eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Tigerconnect Secure Messenger** yazın.
1. Sonuçlar panelinden **Tıgerconnect güvenli Messenger** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre TigerConnect Secure Messenger Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz. Çoklu oturum açma için, bir Azure AD kullanıcısı ve TigerConnect güvenli Messenger 'daki ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD çoklu oturum açmayı, TigerConnect Secure Messenger ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    * Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    * Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Tıgerconnect Secure Messenger SSO 'Yu yapılandırın](#configure-tigerconnect-secure-messenger-sso)** .
    * Tigerconnect **[güvenli Messenger test kullanıcısı oluşturun](#create-a-tigerconnect-secure-messenger-test-user)** ve bu sayede, Britta Simon ADLı Azure AD kullanıcısına bağlı olan Tıgerconnect güvenli Messenger 'Da Britta Simon adlı bir kullanıcı oluşturun.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı TigerConnect Secure Messenger ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Tigerconnect güvenli Messenger** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** kutusuna bir URL girin:

       `https://home.tigertext.com`

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **Tanımlayıcı (VARLıK kimliği)** değeri gerçek değil. Bu değeri gerçek tanımlayıcıyla güncelleştirin. Değeri almak için, [Tigerconnect Secure Messenger destek ekibine](mailto:prosupport@tigertext.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölmesinde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarla** bölmesinde, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Federasyon meta verileri XML indirme seçeneği](common/metadataxml.png)

1. **TigerConnect güvenli Messenger ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, TigerConnect güvenli Messenger erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Tigerconnect Secure Messenger**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>TigerConnect Secure Messenger SSO 'yu yapılandırma

TigerConnect güvenli Messenger tarafında çoklu oturum açmayı yapılandırmak için, indirilen Federasyon meta veri XML 'sini ve Azure portal kopyalanmış URL 'Leri, [Tigerconnect Secure Messenger destek ekibine](mailto:prosupport@tigertext.com)göndermeniz gerekir. TigerConnect Secure Messenger ekibi, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlayacaktır.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Bir Tıgerconnect Secure Messenger test kullanıcısı oluşturma

Bu bölümde, TigerConnect Secure Messenger 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Tigerconnect güvenli Messenger ' da Kullanıcı olarak Britta Simon eklemek için [Tigerconnect güvenli Messenger destek ekibi](mailto:prosupport@tigertext.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı My Apps portalını kullanarak test edersiniz.

Uygulamalarım portalındaki **Tigerconnect güvenli Messenger** ' ı seçtiğinizde, çoklu oturum açma 'Yı ayarladığınız Tigerconnect Secure Messenger aboneliğinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [My Apps Portalındaki uygulamaları erişme ve kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory tümleştirme öğreticilerinin listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Güvenli Messenger 'ı Azure AD ile bağlamayı deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)