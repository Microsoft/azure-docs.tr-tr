---
title: 'Öğretici: TOPdesk ile güvenli Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve TOPdesk ile güvenli arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654315"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Öğretici: Topmasa güvenliğine Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile TOPdesk ile güvenli tümleştirme hakkında bilgi edineceksiniz. Azure AD ile TOPdesk-Secure 'i tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Topmasa ile güvenli erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, üst düzey güvenli (çoklu oturum açma) otomatik olarak oturum açmasını sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:
 
 * Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Topoloji güvenli çoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* TOPdesk-Secure **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-topdesk---secure-from-the-gallery"></a>Galeriden Topmasa-Secure ekleme

TOPdesk ile güvenli hale getirmek için Azure AD ile güvenli bir şekilde yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Topmasa-Secure ' ı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **TopDesk-Secure** yazın.
1. Sonuçlar panelinden **Topmasa-Secure** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Azure AD SSO 'yu TOPdesk için yapılandırma ve test etme-güvenli

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre TopDesk ile güvenli bir şekilde yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile TOPdesk-Secure arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, TOPdesk ile güvenli olarak yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    2. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[TOPdesk-Secure SSO 'Yu yapılandırın](#configure-topdesk---secure-sso)** .
    1. Topmasa **[-Secure test kullanıcısı oluşturun](#create-topdesk---secure-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Topmasa 'da güvenli bir Britta Simon 'a sahip olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı TOPdesk ile güvenli olarak yapılandırmak için aşağıdaki adımları uygulayın:

1. Azure portal, **TopDesk-Secure** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

3. **SAML Ile tek Sign-On ayarla** sayfasında, **temel SAML yapılandırması** iletişim kutusunu açmak için kalem simgesi ' ne tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.topdesk.net`

    b. **Tanımlayıcı URL 'si** kutusunda, TopDesk yapılandırmasından alabileceğiniz TopDesk meta veri URL 'sini girin. Bu, aşağıdaki kalıbı kullanmalıdır: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Topmasa-güvenli istemci desteği ekibine](https://www.topdesk.com/us/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **TOPdesk-Secure ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Topmasa-Secure ' a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Topmasa-Secure**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name=&quot;configure-topdesk---secure-sso&quot;></a>TOPdesk-Secure SSO 'yu yapılandırma

1. **Topoloji güvenli** şirket sitenizde yönetici olarak oturum açın.

2. **TopDesk** menüsünde **Ayarlar**' a tıklayın.

    ![Ayarlar](./media/topdesk-secure-tutorial/ic790598.png &quot;Ayarlar")

3. **Oturum açma ayarları**' na tıklayın.

    ![Oturum açma ayarları](./media/topdesk-secure-tutorial/ic790599.png "Oturum açma ayarları")

4. **Oturum açma ayarları** menüsünü genişletin ve ardından **genel**' e tıklayın.

    ![Genel](./media/topdesk-secure-tutorial/ic790600.png "Genel")

5. **SAML oturum açma** Yapılandırması bölümünün **güvenli** bölümünde aşağıdaki adımları uygulayın:

    ![Teknik ayarlar](./media/topdesk-secure-tutorial/ic790855.png "Teknik ayarlar")

    a. Ortak meta veri dosyasını indirmek için **İndir** ' e tıklayın ve ardından bilgisayarınıza yerel olarak kaydedin.

    b. Meta veri dosyasını açın ve ardından **Assertionconsumerservice** düğümünü bulun.

    ![Onaylama tüketici hizmeti](./media/topdesk-secure-tutorial/ic790856.png "Onaylama tüketici hizmeti")

    c. **Assertionconsumerservice** değerini kopyalayın, bu değeri, **TopDesk-Secure etki alanı ve URL 'ler** bölümündeki Yanıt URL metin kutusuna yapıştırın.

6. Bir sertifika dosyası oluşturmak için aşağıdaki adımları gerçekleştirin:

    ![Sertifika](./media/topdesk-secure-tutorial/ic790606.png "Sertifika")

    a. İndirilen meta veri dosyasını Azure portal açın.

    b. **Xsi: Type türünde** **Besleyicisi: Applicationservicetype** olan **RoleDescriptor** düğümünü genişletin.

    c. **X509Certificate** düğümünün değerini kopyalayın.

    d. Kopyalanmış **X509Certificate** değerini bilgisayarınıza yerel olarak bir dosyaya kaydedin.

7. **Genel** bölümünde, **Ekle**' ye tıklayın.

    ![Ekle](./media/topdesk-secure-tutorial/ic790607.png "Ekle")

8. **SAML yapılandırma Yardımcısı** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![SAML yapılandırma Yardımcısı](./media/topdesk-secure-tutorial/ic790608.png "SAML yapılandırma Yardımcısı")

    a. İndirilen meta veri dosyanızı Azure portal karşıya yüklemek için, **Federasyon meta verileri** altında, **Araştır**' a tıklayın.

    b. Sertifika dosyanızı karşıya yüklemek için, **sertifika (RSA)** altında, **Araştır**' a tıklayın.

    c. **Özel anahtar (RSA, PKCS8, der)** için kendi özel anahtarınızı karşıya yükleyebilir veya özel anahtarı almak Için [TopDesk-Secure istemci destek ekibine](https://www.topdesk.com/us/support) başvurabilirsiniz.

    d. TOPdesk destek ekibinden aldığınız logo dosyasını karşıya yüklemek için, **logo simgesi** altında, **Araştır**' a tıklayın.

    e. **Kullanıcı adı öznitelik** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. **Görünen ad** metin kutusuna yapılandırmanız için bir ad yazın.

    örneğin: **Kaydet**’e tıklayın.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk-Secure test kullanıcısı oluşturma

Azure AD kullanıcılarının Topmasa ile güvenli oturum açmasını sağlamak için, bu kullanıcıların Topmasa-Secure ' a sağlanması gerekir.  
Topmasa-Secure durumunda sağlama, el ile gerçekleştirilen bir görevdir.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:

1. **Topoloji güvenli** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Topmasa \> Yeni \> destek dosyaları \> işleci**' ne tıklayın.

    ![Operatör](./media/topdesk-secure-tutorial/ic790610.png "Operatör")

3. **Yeni operatör** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![New Işleci](./media/topdesk-secure-tutorial/ic790611.png "Yeni İşleç")

    a. **Genel** sekmesine tıklayın.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcının soyadı yazın.

    c. **Konum** bölümünde hesap Için bir **site** seçin.

    d. **TopDesk oturum** açma bölümünün **oturum açma adı** metin kutusunda, Kullanıcı için bir oturum açma adı yazın.

    e. **Kaydet**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, TOPdesk-Secure tarafından sunulan diğer tüm Topolojli Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Topmasa güvenli oturum açma URL 'sine yeniden yönlendirilir. 

* Topoloji-güvenli oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da topoloji güvenli Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız en güvenli Topolojimde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

TOPdesk-Secure ' ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).