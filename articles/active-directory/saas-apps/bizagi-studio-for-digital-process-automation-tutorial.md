---
title: 'Öğretici Azure Active Directory: dijital Işlem otomasyonu için BizAgi ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Dijital Işlem otomasyonu için Azure Active Directory ile Bizagı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Öğretici: dijital Işlem otomasyonu için BizAgi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Digital Process Automation Hizmetleri veya sunucusu için Bizagı tümleştirme hakkında bilgi edineceksiniz. Azure AD ile dijital Işlem otomasyonu için Bizagı 'yi tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, dijital Işlem Otomasyonu Hizmetleri veya sunucusu için bir BizAgi projesine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla dijital Işlem AutomationServices veya Server için Bizagı projesine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Automation Services veya Server kullanan bir BizAgi projesi. 
* SAML onaylama imzaları için kendi sertifikalarınızı vardır. Bu sertifikaların P12 veya PFX biçiminde oluşturulması gerekir.
* Bizagi projesinden oluşturulan XML biçiminde bir meta veri dosyası olmalıdır. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Automation Services veya Server kullanarak bir BizAgi projesinde Azure AD SSO 'yu yapılandırıp test edersiniz.

* Bizagi for Digital Process Automation, **SP** tarafından başlatılan SSO 'yu destekler
* Bizagi 'yi dijital Işlem otomasyonu için yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve bir şekilde korunmasını koruyan oturum denetimleri uygulayabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Galeriden dijital Işlem otomasyonu için Bizagı ekleme

Azure AD 'de dijital Işlem otomasyonu için Bizagı tümleştirmesini yapılandırmak için, Galeriden dijital Işlem otomasyonu için Bizagı 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **dijital Işlem Otomasyonu Için bizagı** yazın.
1. Sonuçlar panelinden **dijital Işlem Otomasyonu Için Bizagı** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Dijital Işlem otomasyonu için Bizagı için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak dijital işlem otomasyonu IÇIN Azure AD SSO 'Yu BizAgi ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Bizagi projesindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Dijital Işlem otomasyonu için BizAgi ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Digital Process AUTOMATION SSO Için Bizagı 'Yi yapılandırma](#configure-bizagi-for-digital-process-automation-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Digital Process Automation test kullanıcısına yönelik Bizagı](#create-bizagi-for-digital-process-automation-test-user)** için, kullanıcının Azure AD gösterimine bağlı olan dijital işlem otomasyonu Için BizAgi 'de B. Simon 'a karşılık gelen bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **BizAgi for Digital Process Automation** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **Meta veri dosyasını karşıya yükle** seçeneğinde Bizagı meta veri dosyasını karşıya yükleyin.
1. Yapılandırmayı gözden geçirin. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda BizAgi PROJENIZIN URL 'sini yazın:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna Bizagı PROJENIZIN URL 'sini yazın:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Digital Process Automation destek ekibine yönelik Bizagı](mailto:jarvein.rivera@bizagi.com) ile iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)
    
    Bu meta veri URL 'SI, BizAgi projenizin kimlik doğrulama seçeneklerinde kayıtlı olmalıdır.
    
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, benzersiz kullanıcı tanımlayıcısını düzenlemek için **talepler & Kullanıcı öznitelikleri** için Düzenle/kalem simgesine tıklayın.
    
    Kullanıcı. mail olarak benzersiz kullanıcı tanımlayıcısı ayarlayın.

### <a name="create-an-azure-ad-test"></a>Azure AD testi oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, dijital Işlem otomasyonu için Bizagı 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **dijital Işlem Otomasyonu Için Bizagı**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Digital Process Automation SSO için Bizagı yapılandırma

**Dijital Işlem Otomasyonu Için BizAgi** 'da çoklu oturum açmayı yapılandırmak Için, [dijital işlem Otomasyonu destek ekibi Için Bizagı](mailto:jarvein.rivera@bizagi.com)'ye **uygulama Federasyon meta verileri URL 'sini** göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Dijital Işlem Otomasyonu test kullanıcısı için Bizagı oluşturma

Bu bölümde, dijital Işlem otomasyonu için BizAgi 'de Britta Simon adlı bir Kullanıcı oluşturacaksınız. Digital Process Automation platformu için [BizAgi](mailto:jarvein.rivera@bizagi.com) 'de kullanıcıları eklemek için BizAgi ile birlikte çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde dijital Işlem için Bizagı Otomasyonu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız dijital Işlem otomasyonu için Bizagı portalında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile dijital Işlem otomasyonu için Bizagı 'yi deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)