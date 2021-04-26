---
title: 'Öğretici Azure Active Directory: yazılım AG bulutu ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ile Software AG bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Öğretici: Software AG bulutu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, yazılım AG bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Software AG bulutu 'nı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de yazılım AG bulutuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Software AG bulutuna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Yazılım AG bulutu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yazılım AG bulutu **SP** tarafından başlatılan SSO 'yu destekler
* Yazılım AG bulutu **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Galeriden yazılım AG bulutu ekleme

Software AG bulutu 'nın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yazılım AG bulutu 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Software AG bulutu** yazın.
1. Sonuçlar panelinden **Software AG bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Yazılım AG bulutu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, yazılım AG bulutu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, yazılım AG bulutu 'ndaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Software AG bulutu ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Yazılım ağ bulutu SSO 'Yu yapılandırma](#configure-software-ag-cloud-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Yazılım **[AG bulutu test kullanıcısı oluşturun](#create-software-ag-cloud-test-user)** -bu, Kullanıcı Azure AD gösterimine bağlı olan yazılım AG bulutu 'nda B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **yazılım AG bulutu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [yazılım AG bulutu istemci destek ekibine](mailto:support@softwareag.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Yazılım AG bulutu 'Nı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, yazılım AG bulutuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yazılım AG bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-software-ag-cloud-sso"></a>Yazılım AG bulutu SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yazılım AG bulutu Web sitesinde yönetici olarak oturum açın.

1.  **Yönetime** tıklayın

    ![Yazılım AG bulutu yönetimini yapılandırma](./media/software-ag-cloud-tutorial/admin.png)

1. **Çoklu oturum açma > kimlik sağlayıcısı ekleme '** ye gidin

    ![Yazılım AG bulutu kimlik sağlayıcısını yapılandırma](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![Yazılım AG bulutu yapılandırma adımları](./media/software-ag-cloud-tutorial/saml-1.png)

    a. **Kimlik sağlayıcısı görünen adı** metin kutusunda, adı gibi verin `azure ad` .

    b. **Yazılım AG bulutu yeniden yönlendirme URI 'si metin kutusunda kullanılacak kimlik sağlayıcısı benzersiz tanımlayıcısı** ' nda, kimlik sağlayıcısı için benzersiz bir ad girin. **Yazılım AG bulutu yeniden yönlendirme URI 'si** alanı YENILENIR ve URI ile doldurulur. Bu URI 'yi kopyalayın ve tanımlanan desenlere göre Azure portal **VARLıK kimliğini** ve diğer bilgileri yapılandırmak için kullanın.

    c. **Federasyon meta VERI XML** dosyasını **kimlik sağlayıcısı yapılandırması** ' na içeri aktarın ve **İleri**' ye tıklayın.

    d. **Yapılandırma** sayfasına gidin ve alanları gerektiği gibi tamamlayın.

### <a name="create-software-ag-cloud-test-user"></a>Yazılım AG bulutu test kullanıcısı oluştur

Bu bölümde, yazılım AG bulutu 'nda Britta Simon adlı bir Kullanıcı oluşturulur. Yazılım AG bulutu, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten yazılım AG bulutu 'nda yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı Aşağıdaki seçenekle test edersiniz. 

Microsoft Azure, yazılım AG bulutu 'nda bir sağlayıcı olarak yapılandırıldığı varsayılarak, öğesine gidin `www.softwareag.cloud` ve oturum aç düğmesine tıklayın ve ortam adını girin. Sonraki ekranda, "oturum aç <IDP NAME> " bağlantısına tıklayın ve kimlik bilgilerini girin. Kimliği doğrulandıktan sonra, oturum açılır ve yazılım AG bulutu giriş sayfasına yönlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Software AG bulutu 'nı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).