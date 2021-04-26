---
title: 'Öğretici: alan kimliğiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve alan kimliği arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453544"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Öğretici: alan kimliğiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, alan kimliğini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Alan kimliğini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de alan kimliğine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla alan kimliğinde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Alan kimliği çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Alan kimliği ıDP tarafından başlatılan SSO 'yu destekler.
* Alan kimliğini yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanma ve savunma sürecinde koruma sağlar. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Galeriden alan kimliği Ekle

Alan kimliğinin Azure AD ile tümleştirilmesini yapılandırmak için galerideki alan kimliğini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulamayı eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **alan kimliği** yazın.
1. Sonuçlar panelinden **alan kimliği** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Alan kimliği için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu alan kimliğiyle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve alan kimliği içindeki ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu alan kimliğiyle yapılandırmak ve test etmek için aşağıdaki adımları izleyin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [alan KIMLIĞI SSO 'Yu yapılandırın](#configure-field-id-sso) .
    1. Alan kimliği 'nde, kullanıcının Azure AD gösterimine bağlı olarak B. Simon 'a karşılık gelen bir [alan kimliği test kullanıcısı oluşturun](#create-a-field-id-test-user) .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **alan kimliği** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. Sonra **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kurşun kalem simgesi vurgulanmış şekilde SAML sayfası ile tek Sign-On ayarlama ekran görüntüsü](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

   a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://<tenantname>.fieldid.com/fieldid`

   b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [alan kimliği destek ekibine](mailto:support@ecompliance.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde Kopyala simgesini seçerek **uygulama Federasyon meta verileri URL 'sini** kopyalayın. Bu dosyayı bilgisayarınıza kaydedin.

    ![SAML Imzalama sertifikasının, kopya simgesi vurgulanmış ekran görüntüsü](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** için girin `B.Simon` .  
   1. **Kullanıcı adı** için username@companydomain.extension (örneğin, `B.Simon@contoso.com` ) girin.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda gösterilen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, alan kimliğine erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **alan kimliği**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar vurgulanmış şekilde Yönet bölümünün ekran görüntüsü](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekleme ekranının ekran görüntüsü](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmında **Seç** ' i seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-field-id-sso"></a>Alan kimliği SSO 'yu Yapılandır

Alan kimliği tarafında çoklu oturum açma 'yı yapılandırmak için, **uygulama Federasyon meta veri URL 'Sini** [alan kimliği destek ekibine](mailto:support@ecompliance.com)gönderin. SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlandığından emin olurlar.

### <a name="create-a-field-id-test-user"></a>Alan kimliği test kullanıcısı oluşturma

Bu bölümde, alan kimliğinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Alan kimliği platformunda kullanıcıları eklemek için [alan kimliği destek ekibi](mailto:support@ecompliance.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim paneli ' ni kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde alan kimliği kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız alan kimliğinde otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile alan kimliğini deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle alan kimliğini koruma](/cloud-app-security/proxy-intro-aad)