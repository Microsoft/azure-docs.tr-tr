---
title: 'Öğretici: Üyeler için Blue erişimiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory (bag) | Microsoft Docs'
description: Üyeler için Azure Active Directory ile mavi erişim arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin (BAE).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: 38765fd6d740c7494cbf7e5a0a38f1d98aecf4a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blue-access-for-members-bam"></a>Öğretici: Üyeler için Blue erişimiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory (bag)

Bu öğreticide, Azure Active Directory (Azure AD) ile üyelerin (BAı) Blue erişimini nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Üyeler için mavi erişimi (BAı) tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Üyeler için mavi erişime (BAE) erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Üyeler (BAE) için mavi erişime otomatik olarak kaydolmalarına olanak tanır.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Üyeler için mavi erişim (bag) çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.


* Üyeler için mavi erişim (BAM) **IDP** tarafından başlatılan SSO 'yu destekler




## <a name="adding-blue-access-for-members-bam-from-the-gallery"></a>Galeriden Üyeler için mavi erişim (bag) ekleme

Üyelerin (BAı) Blue erişiminin Azure AD 'ye tümleştirilmesini yapılandırmak için, galerideki Üyeler (BAE) için mavi erişimi, yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Üyeler için mavi erişim (baz)** yazın.
1. Sonuçlar panelinden **Üyeler Için mavi erişimi (BAE)** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-blue-access-for-members-bam"></a>Üyeler için mavi erişim için Azure AD çoklu oturum açmayı yapılandırma ve test etme (bag)

**B. Simon** adlı bir test kullanıcısı kullanarak, Azure AD SSO 'YU (BAM) Üyeler Için mavi erişimle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında, Üyeler için mavi erişim (BAı) arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'Yu, Üyeler (BAE) için mavi erişimle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Üyeler Için mavi erişimi yapılandırın (BAE) SSO](#configure-blue-access-for-members-bam-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan Üyeler (BAM) için mavi erişimde B. Simon 'a yönelik **[mavi erişimi oluşturun](#create-blue-access-for-members-bam-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Üyeler için mavi erişim (BAI)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`<Custom Domain Value>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMURL>/affwebservices/public/saml2assertionconsumer`

    c. **Ek URL 'Ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMURL>/BAMSSOServlet/sso/BamInboundSsoServlet`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve geçiş durumu ile güncelleştirin. Bu değerleri almak için [Üyeler Için mavi erişim (BAE) istemci desteği ekibine](https://www.bcbstx.com/contact-us) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Üyeler için mavi erişim (BAı) uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki ' a ek olarak, Üyeler için mavi erişim (BAE) uygulaması aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ------------ | --------- |
    | ClientID | `<ClientID>` |
    | UID | `<UID>` |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Üyeler Için mavi erişimi ayarla (BAı)** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Üyeler için mavi erişime (BAM) erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Üyeler Için mavi erişim (BAı)** seçeneğini belirleyin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-blue-access-for-members-bam-sso"></a>Üyeler için Blue erişimi yapılandırma (BAA) SSO

**Üyeler Için mavi erişim (BAE)** tarafında çoklu oturum açma 'yı yapılandırmak için, Indirilen **Federasyon meta verileri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal [Üyeler için Blue erişimi (BAE) destek ekibine](https://www.bcbstx.com/contact-us)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-blue-access-for-members-bam-test-user"></a>Üyeler için mavi erişimi oluştur (BAA) test kullanıcısı

Bu bölümde, Üyeler için mavi erişimde B. Simon adlı bir Kullanıcı oluşturursunuz (BAM). Üyeler için mavi erişim (BAE) platformu ' na Kullanıcı eklemek için, [Üyeler Için mavi erişim (BAE) destek ekibi](https://www.bcbstx.com/contact-us) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Üyeler için mavi erişim (BAE) kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız üyelere (BAı) yönelik mavi erişime otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Üyeler için mavi erişimi (BAA) deneyin](https://aad.portal.azure.com/)