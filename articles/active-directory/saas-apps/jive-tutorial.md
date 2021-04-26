---
title: 'Öğretici: Jive ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Jive arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 6f2d151ac5110a504d17e1c9e2835a339f31da8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Öğretici: Jive ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Jive 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Jive 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Jive erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Jive 'ye otomatik olarak kaydolmasına olanak tanır.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Jive çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Jive, **SP** tarafından başlatılan SSO 'yu destekler.
* Jive [ **Otomatik** Kullanıcı sağlamayı](jive-provisioning-tutorial.md)destekler.

## <a name="add-jive-from-the-gallery"></a>Galeriden Jive ekleme

Jive 'yi Azure AD 'ye tümleştirmeyi yapılandırmak için galerideki Jive 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Jive** yazın.
1. Sonuçlar panelinden **Jive** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Jive için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Jive ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Jive ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Jive ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Jive SSO 'Yu yapılandırma](#configure-jive-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Jive 'de B. Simon 'ın bir karşılığı olacak şekilde **[Jive test kullanıcısı oluşturun](#create-jive-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Jive** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

   a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<instance name>.jivecustom.com`

   b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Jive istemci desteği ekibine](https://www.jivesoftware.com/services-support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Jive ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Jive erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Jive**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-jive-sso"></a>Jive SSO 'yu yapılandırma

1. **Jive** tarafında çoklu oturum açma 'yı yapılandırmak Için, Jive kiracınızda yönetici olarak oturum açın.

1. Üstteki menüden **SAML**' ye tıklayın.

    ![Ekran görüntüsünde, etkin seçilmiş olan SAML sekmesi görüntülenir.](./media/jive-tutorial/jive-2.png)

    a. **Genel** sekmesinde **etkin** ' i seçin.

    b. **Tüm SAML ayarlarını kaydet** düğmesine tıklayın.

1. **IDP meta verileri** sekmesine gidin.

    ![Ekran görüntüsü, SAML sekmesi ı D P meta VERILERININ seçili olduğunu gösterir.](./media/jive-tutorial/jive-3.png)

    a. İndirilen meta veri XML dosyasının içeriğini kopyalayın ve ardından **kimlik sağlayıcısı (ıDP) meta verileri** metin kutusuna yapıştırın.

    b. **Tüm SAML ayarlarını kaydet** düğmesine tıklayın.

1. **Kullanıcı ÖZNITELIĞI eşleme** sekmesini seçin.

    ![Ekran görüntüsünde, Kullanıcı ÖZNITELIĞI EŞLEMESININ seçildiği SAML sekmesi görüntülenir.](./media/jive-tutorial/jive-4.png)

    a. **E-posta** metin kutusunda, **posta** değerinin öznitelik adını kopyalayıp yapıştırın.

    b. **Ilk ad** metin kutusuna, 1 **. değerin öznitelik** adını kopyalayıp yapıştırın.

    c. **Soyadı metin kutusunda** , **Soyadı** değerinin öznitelik adını kopyalayıp yapıştırın.

### <a name="create-jive-test-user"></a>Jive test kullanıcısı oluşturma

Bu bölümün amacı, Jive 'de Britta Simon adlı bir Kullanıcı oluşturmaktır. Jive, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamasını destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](jive-provisioning-tutorial.md) .

Kullanıcı el ile oluşturmanız gerekiyorsa, Jive platformunda kullanıcıları eklemek için [Jive istemci desteği ekibi](https://www.jivesoftware.com/services-support/) ile çalışın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Jive oturum açma URL 'sine yeniden yönlendirilir. 

* Jive oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Jive kutucuğuna tıkladığınızda, bu, Jive oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Jive 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).