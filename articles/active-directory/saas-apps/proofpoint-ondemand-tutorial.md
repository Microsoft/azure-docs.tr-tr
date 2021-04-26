---
title: 'Öğretici: Isteğe bağlı olarak yazım Azure Active Directory tümleştirme | Microsoft Docs'
description: Isteğe bağlı Azure Active Directory ve düzeltme noktası arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: e9beb2c9e265982a9e10f3e6abee6bc3cba4a4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648484"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Öğretici: Isteğe bağlı olarak, denetleme noktası ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Isteğe bağlı olarak, redaksyonun nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Isteğe bağlı olarak bir Redaksyonu tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Istek üzerine yazım denetimi erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesapları ile Isteğe bağlı olarak otomatik olarak oturum açmalarına izin vermek için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Istek üzerine yazım tabanlı çoklu oturum açma (SSO) etkin abonelik.

> [!NOTE]
> MFA veya passwordless kimlik doğrulamasını Azure AD ile kullanıyorsanız, SAML Isteğindeki AuthnContext değerini kapatın. Aksi takdirde, Azure AD, AuthnContext uyuşmazlığından sonra hatayı oluşturur ve belirteci uygulamaya geri göndermez.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Istek üzerine denetleme noktası **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Galeriden Isteğe bağlı olarak Redakz noktası ekleme

Isteğe bağlı olarak, Azure AD 'ye Istek üzerine redaksyonun tümleştirmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Isteğe bağlı olarak Redakz noktası eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **isteğe bağlı** olarak yazım noktası yazın.
1. Sonuçlar panelinden **istek üzerine redakperpoint** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Istek üzerine yazım için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısını kullanarak Azure AD SSO 'yu isteğe bağlı olarak yazım içinde yapılandırın ve test edin. SSO 'nun çalışması için, Isteğe bağlı bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, Istek üzerine yazım noktası ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[İstek üzerine yazım tabanlı SSO 'Yu yapılandırma](#configure-proofpoint-on-demand-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı Isteğe bağlı olarak, yazım noktasında bir B. Simon 'ya sahip olmak için, **[isteğe bağlı test kullanıcısına](#create-proofpoint-on-demand-test-user)** yönelik bir tam kayıt oluştur.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, Isteğe bağlı bulma **noktası** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Istek üzerine yazım tabanlı etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<hostname>.pphosted.com/ppssamlsp`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Isteğe bağlı, istek üzerine istemci desteği ekibine](https://www.proofpoint.com/us/support-services) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **İsteğe bağlı yazım noktası ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Isteğe bağlı olarak, yazım noktasına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **isteğe bağlı olarak Redakta noktası**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-proofpoint-on-demand-sso"></a>Istek üzerine Redaket SSO 'SU yapılandırma

**İsteğe bağlı yazım noktasında** çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den uygun kopyalanmış URL 'leri [isteğe bağlı destek ekibine](https://www.proofpoint.com/us/support-services)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-proofpoint-on-demand-test-user"></a>Isteğe bağlı test kullanıcısına yönelik yazım noktası oluştur

Bu bölümde, Istek üzerine yazım noktasında Britta Simon adlı bir Kullanıcı oluşturacaksınız. Istek üzerine yazım noktasında Kullanıcı eklemek için, isteğe bağlı, [istek üzerine istemci destek ekibi](https://www.proofpoint.com/us/support-services) ile çalışın.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Isteğe bağlı oturum açma URL 'sine yeniden yönlendirilir. 

* Isteğe bağlı oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım üzerinde Isteğe bağlı yazım noktası kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Istek üzerine otomatik olarak bir denetleme noktasında oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Istendiğinde, Istek üzerine yazım denetimi yaptıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).