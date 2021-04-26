---
title: 'Öğretici: Zendesk ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zendesk arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731827"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Öğretici: Zendesk ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Zendesk 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zendesk 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zendesk 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zendesk 'e otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zendesk çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Zendesk, **SP** tarafından başlatılan SSO 'yu destekler
* Zendesk [ **Otomatik** Kullanıcı sağlamayı destekler](zendesk-provisioning-tutorial.md)


## <a name="adding-zendesk-from-the-gallery"></a>Galeriden Zendesk ekleme

Zendesk 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Zendesk eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zendesk** yazın.
1. Sonuçlar panelinden **Zendesk** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Zendesk için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Zendesk ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Zendesk içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zendesk ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[ZENDESK SSO 'Yu yapılandırın](#configure-zendesk-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Zendesk 'de B. Simon 'a karşılık gelen bir **[Zendesk test kullanıcısı oluşturun](#create-zendesk-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zendesk** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.zendesk.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.zendesk.com`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Zendesk istemci desteği ekibine](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Zendesk uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Zorunlu SAML özniteliği yoktur ancak isteğe bağlı olarak, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden Yönetim yapabilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

    > [!NOTE]
    > Uzantı özniteliklerini, Azure AD 'de olmayan öznitelikleri varsayılan olarak eklemek için kullanabilirsiniz. **Zendesk** 'in kabul ettiği SAML özniteliklerinin tüm listesini almak için [SAML Içinde ayarlayabilirler Kullanıcı öznitelikleri](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) ' ne tıklayın.

1. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

1. **SAML Imzalama sertifikası** bölümünde, **parmak izi değerini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

1. **Zendesk ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Zendesk 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zendesk**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-zendesk-sso"></a>Zendesk SSO 'yu yapılandırma

1. Bu yapılandırmayı **Zendesk** içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Ekran görüntüsü uzantıyı yükler düğmesini gösterir.](./media/target-process-tutorial/install_extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, **Kurulum Zendesk** öğesine tıkladığınızda sizi Zendesk uygulamasına yönlendirirsiniz. Buradan, Zendesk 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Zendesk 'i el ile kurmak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zendesk şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. **Zendesk Yönetim merkezinde** **güvenlik** sekmesinde **güvenlik ayarları** ' na tıklayın.

    ![Ekran görüntüsünde, güvenlik ayarları seçiliyken Zendesk Yönetim Merkezi görüntülenir.](./media/zendesk-tutorial/settings.png "Güvenlik")

1. **Çoklu oturum açma** sayfasına gidin ve **SAML**'da **Düzenle** ' ye tıklayın.

    ![Ekran görüntüsü, Düzenle seçiliyken çoklu oturum açma sayfasını gösterir.](./media/zendesk-tutorial/saml-sso.png "Güvenlik")

1. **SSO** sayfasında aşağıdaki adımları gerçekleştirin.

    ![Çoklu oturum açma](./media/zendesk-tutorial/saml-configuration.png "Çoklu oturum açma")

    a. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Sertifika parmak izi** metin kutusunda, Azure Portal kopyaladığınız sertifikanın **parmak izi** değerini yapıştırın.

    c. **Uzaktan oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    d. **Kaydet**’e tıklayın.

### <a name="create-zendesk-test-user"></a>Zendesk test kullanıcısı oluşturma

Bu bölümün amacı Zendesk 'te Britta Simon adlı bir Kullanıcı oluşturmaktır. Zendesk, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](Zendesk-provisioning-tutorial.md) .

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zendesk oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Zendesk oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zendesk kutucuğuna tıkladığınızda, bu, Zendesk oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Zendesk 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).