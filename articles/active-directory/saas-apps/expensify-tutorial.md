---
title: 'Öğretici: Expensesıfy ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Expensesıfy arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 5c68453f7f7d638877683920a17022cf3d29bdc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597819"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile Expensesıfy tümleştirme

Bu öğreticide, Expensesıfy 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Expensesıfy 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Expensesıfy 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Expensesıfy 'ye otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Expensesıfy çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Expensesıfy, **SP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-expensify-from-the-gallery"></a>Galeriden Expensesıfy ekleyin

Expensesıfy 'yi Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Expensesıfy eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **expensesıfy** yazın.
1. Sonuçlar panelinden **Expensesıfy** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Azure AD SSO 'yu Expensesıfy için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Expensesıfy ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Expensesıfy içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Expensesıfy ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Expensesıfy SSO 'Yu yapılandırın](#configure-expensify-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Expensesıfy 'de B. Simon 'ın bir karşılığı olacak şekilde, **[pahalı bir test kullanıcısı oluşturun](#create-expensify-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Expensesıfy** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://www.expensify.com/authentication/saml/login`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://www.expensify.com`

    c. b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Bu değeri gerçek yanıt URL 'siyle güncelleştirin. Bu değeri almak için [Expensesıfy istemci destek ekibine](mailto:help@expensify.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **meta veri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Expensesıfy 'Yi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Expensesıfy 'ye erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Expensesıfy**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-expensify-sso"></a>Expensesıfy SSO 'yu yapılandırma

Expensesıfy 'de SSO 'yu etkinleştirmek için önce uygulamada **etki alanı denetimini** etkinleştirmeniz gerekir. Uygulamada, [burada](https://help.expensify.com/domain-control)listelenen adımlar aracılığıyla etki alanı denetimini etkinleştirebilirsiniz. Ek destek için, [Expensesıfy istemci desteği ekibi](mailto:help@expensify.com)ile çalışın. Etki alanı denetimi etkinleştirildikten sonra aşağıdaki adımları izleyin:

![Tek Sign-On yapılandırma](./media/expensify-tutorial/domain-control.png)

1. Expensesıfy uygulamanızda oturum açın.

2. Sol bölmede, **Ayarlar** ' a tıklayın ve **SAML**' ye gidin.

3. **SAML oturum açma** seçeneğini **etkin** olarak değiştirin.

4. Azure AD 'den indirilen Federasyon meta verilerini Not defteri 'nde açın, içeriği kopyalayın ve ardından **kimlik sağlayıcısı meta verileri** metin kutusuna yapıştırın.

### <a name="create-expensify-test-user"></a>Expensesıfy test kullanıcısı oluşturma

Bu bölümde, Expensesıfy 'de B. Simon adlı bir Kullanıcı oluşturacaksınız. Expensesıfy platformunda kullanıcıları eklemek için [Expensesıfy istemci destek ekibi](mailto:help@expensify.com) ile çalışın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Expensesıfy oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Expensesıfy oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım üzerinde Expensesıfy kutucuğuna tıkladığınızda bu, Expensesıfy oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Expensesıfy 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).