---
title: 'Öğretici: Clarizen One ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Clarizone arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516950"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Öğretici: Clarizen One ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Clarizen 'i tümleştirmeyi öğreneceksiniz. Clarizen birini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Clarizen 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla bir tane açıklığa kavuşturan otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Clarizen One çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Clarizen One, **IDP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-clarizen-one-from-the-gallery"></a>Galeriden Clarizen, birini ekleyin

Clarizen 'nın bir kısmını Azure AD 'ye göre yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Clarizen, bir tane eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Clarizen One** yazın.
1. Sonuçlar panelinden **Clarizen One** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Azure AD SSO 'yu Clarizone için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Clarizen One ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile Clarizen arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Clarizen One ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Clarizen One SSO 'Yu yapılandırma](#configure-clarizen-one-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Clarizen 'da B. Simon 'ın bir karşılığı olan **[Clarizen test kullanıcısı oluşturun](#create-clarizen-one-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Clarizen One** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna değeri yazın:`Clarizen`

    b. **Yanıt URL** 'si metin kutusuna URL 'yi yazın:`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Clarizone 'Yi ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, Clarizen 'ya erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Clarizen One** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-clarizen-one-sso&quot;></a>Clarizen One SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Clarizen bir şirket sitesinde oturum açın.

1. Kullanıcı adına tıklayın ve ardından **Ayarlar**' a tıklayın.

    ![Kullanıcı adınızla &quot;Ayarlar&quot; a tıklanın](./media/clarizen-tutorial/setting.png &quot;Ayarlar")

1. **Genel ayarlar** sekmesine tıklayın. Ardından, **federal kimlik doğrulaması**' nın yanındaki **Düzenle**' ye tıklayın.

    !["Genel ayarlar" sekmesi](./media/clarizen-tutorial/authentication.png "Genel Ayarlar")

1. **Federal kimlik doğrulaması** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    !["Federal kimlik doğrulaması" iletişim kutusu](./media/clarizen-tutorial/federated-authentication.png "Federal kimlik doğrulaması")

    a. **Federal kimlik doğrulamasını etkinleştir**' i seçin.

    b. İndirilen sertifikanızı karşıya yüklemek için **karşıya yükle** ' ye tıklayın.

    c. **Oturum açma URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinde **oturum açma URL 'si** değerini girin.

    d. Oturum kapatma **URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinden **Logout URL 'si** değerini girin.

    e. **Gönderi kullan**' ı seçin.

    f. **Kaydet**’e tıklayın.

### <a name="create-clarizen-one-test-user"></a>Clarizen bir test kullanıcısı oluşturun

Bu bölümün amacı, Clarizen, One adlı bir Kullanıcı oluşturmaktır.

**Kullanıcı el ile oluşturmanız gerekiyorsa lütfen aşağıdaki adımları gerçekleştirin:**

Azure AD kullanıcılarının bir Clarizone 'da oturum açmasını sağlamak için Kullanıcı hesapları sağlamalısınız. Clarizen One durumunda, sağlama el ile gerçekleştirilen bir görevdir.

1. Clarizen bir şirket sitenizde yönetici olarak oturum açın.

2. **Kişiler**' e tıklayın.

    !["Kişiler" i](./media/clarizen-tutorial/people.png "People")

3. **Kullanıcıyı davet et**' e tıklayın.

    !["Kullanıcı davet et" düğmesi](./media/clarizen-tutorial/user.png "Kullanıcıları davet et")

1. **Kişileri davet et** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    !["Kişi davet et" iletişim kutusu](./media/clarizen-tutorial/invite-people.png "Kişileri davet et")

    a. **E-posta** kutusuna Britta Simon hesabının e-posta adresini yazın.

    b. **Davet et**' e tıklayın.

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Clarizen 'da otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Clarizen bir kutucuğa tıkladığınızda, SSO 'yu ayarladığınız Clarizen 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Clarizen One 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).