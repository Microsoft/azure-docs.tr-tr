---
title: 'Öğretici: SailPoint ıdentitynow ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SailPoint ıdentityarasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/04/2020
ms.author: jeedes
ms.openlocfilehash: 04e1dbdd0a84fd8950ca2ebe05b1389767ed77c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sailpoint-identitynow"></a>Öğretici: Saılpoint ıdentityile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, SailPoint ıdentity'i Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. SailPoint ıdentity'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, SailPoint kimliğine artık erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, SailPoint kimliğine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SailPoint ıdentitynow çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SailPoint ıdentityartık **SP ve ıDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-sailpoint-identitynow-from-the-gallery"></a>Galeriden şimdi Saılpoint ıdentityekleniyor

Saılpoint ıdentity'in tümleştirmesini Azure AD 'ye şimdi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize şimdi Saılpoint ıdentityeklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Sailpoint ıdentityyazın** .
1. Sonuçlar panelinden **Sailpoint ıdentitynow** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-sailpoint-identitynow"></a>SailPoint ıdentityiçin Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu SailPoint ıdentityile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve SailPoint ıdentityiçindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu SailPoint ıdentityile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[SailPoint ıdentitynow SSO 'Yu yapılandırma](#configure-sailpoint-identitynow-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Sailpoint kimnow test kullanıcısı oluşturma](#create-sailpoint-identitynow-test-user)** -artık, kullanıcının Azure AD gösterimine bağlı olan sailpoint ıdentity'te B. Simon 'ın bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Sailpoint ıdentitynow** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT_NAME>.identitynow.com/sp`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT_NAME>.login.sailpoint.com/saml/SSO/alias/<TENANT_NAME>-sp`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANT_NAME>.identitynow.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Sailpoint ıdentitynow istemci destek ekibine](mailto:support@sailpoint.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **SailPoint Kimnow 'ı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, şimdi SailPoint Identity'e erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Sailpoint ıdentitynow**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sailpoint-identitynow-sso"></a>Saılpoint ıdentitynow SSO 'yu Yapılandır

**Sailpoint ıdentitynow** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den [saılpoint ıdentitysupport ekibine](mailto:suppor@sailpoint.com)uygun şekilde kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-sailpoint-identitynow-test-user"></a>SailPoint ıdentitynow test kullanıcısı oluştur

Bu bölümde, SailPoint ıdentity'te şimdi Britta Simon adlı bir Kullanıcı oluşturacaksınız. Sailpoint ıdentitynow platformunda kullanıcıları eklemek için, [Sailpoint ıdentitynow](mailto:support@sailpoint.com) ile birlikte çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz SailPoint ıdentitynow oturum açma URL 'sine yeniden yönlendirilir.  

1. SailPoint ıdentitynow oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız SailPoint ıdentity' de otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft Access panel ' i de kullanabilirsiniz. Erişim panelinde Saılpoint ıdentitynow kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız SailPoint ıdentity' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Saılpoint Kimnow 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).