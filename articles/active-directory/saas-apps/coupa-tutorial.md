---
title: 'Öğretici: Azure Active Directory ile tümleştirme Microsoft Docs'
description: Azure Active Directory ve kupona arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653014"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Öğretici: bağa ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir bağa tümleştirmeyi öğreneceksiniz. Bir Azure AD ile bağa tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, bir kupona erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Kupona, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-coupa-from-the-gallery"></a>Galeriden bir bağa ekleyin

Kupona 'nın bir Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize bir Kupone eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **bir kupona** yazın.
1. Sonuçlar panelinden **bir kupona** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Bir kupona için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'su Ile bir kupona yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu bir kupona ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[bir APA SSO 'Yu yapılandırın](#configure-coupa-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan bir B. Simon ınbağon 'ya sahip olmak üzere, bir e-ç **[test kullanıcısı oluşturun](#create-coupa-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **bağa** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.coupahost.com`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Bu değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri almak için, [bir istemci desteği ekibine](https://success.coupa.com/Support/Contact_Us?) başvurun.

    b. **Tanımlayıcı** kutusuna URL 'yi yazın:

    | Ortam  | URL |
    |:-------------|----|
    | Korumalı alan | `sso-stg1.coupahost.com`|
    | Üretim | `sso-prd1.coupahost.com`|
    | | |

    c. **Yanıt URL** 'si metin kutusuna URL 'yi yazın:

    | Ortam | URL |
    |------------- |----|
    | Korumalı alan | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Üretim | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Set up bağa** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, kupona 'ya erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **kupona**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-coupa-sso&quot;></a>Kupona SSO 'yu yapılandırma

1. Bir yönetici olarak, bağa şirket sitenizde oturum açın.

2. **Kurulum \> güvenlik denetimi**' ne gidin.

    ![Güvenlik denetimleri](./media/coupa-tutorial/setup.png &quot;Güvenlik Denetimleri")

3. Bu **kimlik bilgilerini kullanarak oturum açın** bölümünde aşağıdaki adımları uygulayın:

    ![Bağa SP meta verileri](./media/coupa-tutorial/login.png "Bağa SP meta verileri")

    a. **SAML kullanarak oturum aç '** ı seçin.

    b. Azure portal indirilen meta verileri karşıya yüklemek için, **Araştır** ' a tıklayın.

    c. **Kaydet**’e tıklayın.

### <a name="create-coupa-test-user"></a>Bağa test kullanıcısı oluşturma

Azure AD kullanıcılarının bir kupona 'da oturum açmasını sağlamak için, bunların bir kupona içinde sağlanması gerekir.  

* Kupona durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Bağa** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde, **Kurulum**' a ve ardından **Kullanıcılar**' a tıklayın.

    ![Kullanıcılar](./media/coupa-tutorial/user.png "Kullanıcılar")

3. **Oluştur**’a tıklayın.

    ![Kullanıcı Oluşturma](./media/coupa-tutorial/create.png "Kullanıcı Oluştur")

4. **Kullanıcı oluştur** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı ayrıntıları](./media/coupa-tutorial/details.png "Kullanıcı ayrıntıları")

    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory hesabının **oturum açma**, **ad**, ad, **Soyadı**, **tek Sign-On kimliği**, **e-posta** özniteliklerini yazın.

    b. **Oluştur**’a tıklayın.

    >[!NOTE]
    >Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır.
    >

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için, herhangi bir diğer bağa Kullanıcı hesabı oluşturma aracını veya bir bağa tarafından sunulan API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz bir oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan bağa oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki bir kupone Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız kupona 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Kupona 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app)