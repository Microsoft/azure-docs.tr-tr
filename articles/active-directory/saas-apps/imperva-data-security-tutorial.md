---
title: 'Öğretici: bir çoklu oturum açma (SSO) Azure Active Directory, bu veri güvenliği ile tümleştirme | Microsoft Docs'
description: Azure Active Directory ve kusurda veri güvenliği arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 7e641af184351aa0d975738439c88724027751dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736580"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-imperva-data-security"></a>Öğretici: bir çoklu oturum açma (SSO) Azure Active Directory, bu veri güvenliği ile tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile ilgili olarak, kusurda veri güvenliğini tümleştirmeyi öğreneceksiniz. Azure AD ile bir yöva veri güvenliğini tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, bu da Kusurdaki veri güvenliğine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesapları ile ilgili olarak, daha fazla veri güvenliğine izin vermek üzere otomatik olarak oturum açabilmesi.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Oneva veri güvenliği çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Kusurda veri güvenliği **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-imperva-data-security-from-the-gallery"></a>Galeriden zva veri güvenliği ekleme

Azure AD 'de, eksik VA veri güvenliği tümleştirmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize bir de de eksik bir veri güvenliği eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna 1. **VA veri güvenliği** yazın.
1. Sonuçlar panelinden **kusuru veri güvenliği** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-imperva-data-security"></a>Azure AD SSO 'yu, kusurda veri güvenliği için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, BIR Azure AD SSO 'yu, bu da kusurdan fazla veri güvenliği ile yapılandırın SSO 'nun çalışması için, bir Azure AD kullanıcısı ve söz konusu Kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, kusuru veri güvenliği ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. , Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Inva Data SECURITY SSO 'Yu yapılandırın](#configure-imperva-data-security-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, eksik VA veri güvenliğine sahip B. Simon 'a sahip olmak için, eksik bir **[veri güvenliği test kullanıcısı oluşturun](#create-imperva-data-security-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **kusuru veri güvenliği** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir tanımlayıcı yazın:`application-name`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<IMPERVA_DNS_NAME>:8443`
    
    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<IMPERVA_DNS_NAME>:8443`
    
    d. **Oturum kapatma URL** 'si metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<IMPERVA_DNS_NAME>:8443`    

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, [kusurda veri güvenliği istemci destek ekibine](mailto:support@jsonar.imperva.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Bu **verileri ayarla güvenlik** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, bir Azure çoklu oturum açma özelliğini, bu da kusuru veri güvenliğine erişim vererek etkinleştirmek için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **kusuru veri güvenliği**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-imperva-data-security-sso"></a>Kusurda veri güvenliği SSO 'SU yapılandırma

Eksik **VA veri güvenliği** tarafında çoklu oturum açma 'yı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den daha fazla kopyalanmış URL 'leri, bu [verileri](mailto:support@jsonar.imperva.com)de (yani,) Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-imperva-data-security-test-user"></a>Kusurda veri güvenliği test kullanıcısı oluşturma

Bu bölümde, kusuru veri güvenliğine yönelik Britta Simon adlı bir Kullanıcı oluşturacaksınız. , Kusurda veri güvenliği platformunda kullanıcıları eklemek için, bu [veri güvenliği destek ekibi](mailto:support@jsonar.imperva.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal ' de bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız, en fazla bir de otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da Kusurdaki veri güvenliği kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Lava veri güvenliğine otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Ivva veri güvenliğini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).