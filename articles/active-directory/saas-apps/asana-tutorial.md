---
title: 'Öğretici: Asana ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Asana arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651017"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Öğretici: Asana ile tümleştirme Azure Active Directory

Bu öğreticide, Asana 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Asana 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Asana 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Asana çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Asana, **SP** tarafından başlatılan SSO 'yu destekler

* Asana, [ **Otomatik** Kullanıcı sağlamayı destekler](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Galeriden Asana ekleyin

Asana 'un Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Asana eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Asana** yazın.
1. Sonuçlar panelinden **Asana** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Asana için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Asana ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Asana 'da ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Asana ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Asana SSO 'Yu yapılandırma](#configure-asana-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Asana 'da B. Simon 'a karşılık gelen, **[Asana test kullanıcısı oluşturun](#create-asana-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Asana** uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Asana etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://app.asana.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://app.asana.com/`

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Asana ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Asana 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Asana**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-asana-sso"></a>Asana SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, Asana uygulamanızda oturum açın. Asana 'da SSO 'yu yapılandırmak için, ekranın sağ üst köşesindeki çalışma alanı adına tıklayarak çalışma alanı ayarlarına erişin. Ardından **\<your workspace name\> Ayarlar**' a tıklayın.

    ![Asana SSO ayarları](./media/asana-tutorial/settings.png)

2. **Kuruluş ayarları** penceresinde, **Yönetim**' e tıklayın. Ardından, SSO yapılandırmasını etkinleştirmek için **ÜYELERIN SAML aracılığıyla oturum açması gerekir** . Aşağıdaki adımları gerçekleştirin:

    ![Tek Sign-On kuruluş ayarlarını yapılandırma](./media/asana-tutorial/save.png)  

    a. **Oturum açma sayfası URL 'si** metin kutusunda, **oturum açma URL**'sini yapıştırın.

    b. Azure portal indirilen sertifikaya sağ tıklayın, ardından not defteri 'ni veya tercih ettiğiniz metin düzenleyicisini kullanarak sertifika dosyasını açın. Başlangıç ve bitiş sertifikası başlığı arasında içeriği kopyalayın ve **X. 509.440 sertifikası** metin kutusuna yapıştırın.

3. **Kaydet**’e tıklayın. Daha fazla yardıma ihtiyacınız varsa [SSO ayarlamak Için Asana kılavuzuna](https://asana.com/guide/help/premium/authentication#gl-saml) gidin.

### <a name="create-asana-test-user"></a>Asana test kullanıcısı oluştur

Bu bölümün amacı, Asana 'da Britta Simon adlı bir Kullanıcı oluşturmaktır. Asana, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamasını destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](asana-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa lütfen aşağıdaki adımları gerçekleştirin:**

Bu bölümde, Asana 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız.

1. **Asana**'da sol paneldeki **takımlar** bölümüne gidin. Artı işareti düğmesine tıklayın.

    ![Azure AD test kullanıcısı oluşturma](./media/asana-tutorial/teams.png)

2. Metin kutusuna **Britta. Simon \@ contoso.com** gibi kullanıcının e-postasını yazın ve ardından **davet et**' i seçin.

3. **Davet gönder**' e tıklayın. Yeni Kullanıcı e-posta hesabına bir e-posta gönderilir. kullanıcının hesabı oluşturması ve doğrulaması gerekir.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Asana oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Asana oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Asana Kutucuğa tıkladığınızda bu, Asana oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Asana 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).