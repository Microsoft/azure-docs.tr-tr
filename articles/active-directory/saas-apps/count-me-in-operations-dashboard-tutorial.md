---
title: 'Öğretici: Me Işlemi sırasında say panosu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı ve Işlem Içi Pano panosunu nasıl Sayalabileceğinizi öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727701"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Öğretici: Işlem Içi Me panosu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Işlem Içi Me panosunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Işlem Içi Me panosunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, bana yönelik Işlemler panosunu say 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Işlem Içi Me panosunu saymak için otomatik olarak oturum açmaya izin ver.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Işlem Içi Pano çoklu oturum açma (SSO) etkin aboneliğini say.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Işlem Içi Me panosu, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Galeriden say-Işlem Içi Pano ekleme

Işlem Içi Me panosunu Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden bana yönelik Işlemler panosunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **bana Işlem Içi Me panosunu say** yazın.
1. Sonuçlar panelinden **bana ait Işlemler panosunu say** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Işlem Içi Me panosu için Azure AD SSO 'Yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, bana yönelik Işlemler panosunu say Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında, Işlem Içi Me panosunu say bölümünde bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'Yu, Işlem Içi beni say panosu ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[beni In-Operations panosu SSO 'su olarak yapılandırın](#configure-count-me-in-operations-dashboard-sso)** .
    1. Kullanıcı tarafından Azure AD temsili ile bağlantılı olan, bana yönelik Işlemler içi bir pano test kullanıcısına göre t. Simon say **[In-Operations oluştur](#create-count-me-in-operations-dashboard-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Işlem Içi Pano** Uygulama tümleştirmesini say sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`api-us.localz.io/<PROJECT_ID>`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [, Işlem içi çalışma panosu istemci desteği ekibinin bağlantı sayısı](mailto:support@localz.co) . Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Işlem Içi say panosu uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki Işlem Içindeki say panosu uygulamasının yanı sıra, aşağıda gösterilen SAML yanıtına daha fazla sayıda özniteliğin geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | ----------- | --------- |
    | atanan roller | Kullanıcı. atandroles |

    > [!NOTE]
    > Işlem Içi Me panosu, uygulamaya atanan kullanıcılar için roller bekliyor. Kullanıcılara uygun roller atanabilmeleri için lütfen bu rolleri Azure AD 'de ayarlayın. Azure AD 'de rolleri nasıl yapılandıracağınızı anlamak için [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)bakın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Işlem Içi Me sayısını ayarla panosu** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Işlem Içi Me panosunu say 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Işlem Içi Me panosunu say**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Say In-Operations, pano SSO 'Yu Yapılandır

**İşlem Içi Me panosu** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den uygun kopyalanmış URL 'Leri, [Işlem içi Pano desteği ekibini say](mailto:support@localz.co)' a göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Bir pano test kullanıcısı In-Operations sayı bana oluştur

Bu bölümde, Işlem Içi Me panosunu say bölümünde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Çalışma zamanı içi Pano platformunda kullanıcıları eklemek için, [Işletimsel işlem Içi Pano Destek ekibi](mailto:support@localz.co) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Işlem Içi Pano oturum açma URL 'sini say ' a yönlendirilir. 

* Hemen sonra Işlem Içi Pano oturum açma URL 'sini doğrudan say ' a gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Yapılacaklar panosunu say kutucuğunu tıklattığınızda, bu Işlem, Işlem Içi Pano oturum açma URL 'sini say 'a yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Işlem Içi say panosunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
