---
title: 'Öğretici: SAP Qualtrics ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve SAP Qualtrics arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 2031864ea57a2f061c69219a2382429ee035804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652549"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Öğretici: SAP Qualtrics ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide SAP Qualtrics Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. SAP Qualtrıcs 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP Qualtrics erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP Qualtrics ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) için etkinleştirilen bir SAP Qualtrics aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SAP Qualtrics **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.
* SAP Qualtrics **tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="add-sap-qualtrics-from-the-gallery"></a>Galeriden SAP Qualtrics ekleme

SAP Qualtrics tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerisinden SAP Qualtrıcs 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP Qualtrics** yazın.
1. Sonuçlardan **SAP Qualtrics** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>SAP Qualtrics için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test KULLANıCıSı kullanarak SAP Qualtrics Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP Qualtrics içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu SAP Qualtrics ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [SAP Qualtrics SSO 'Yu yapılandırın](#configure-sap-qualtrics-sso) .
    1. [BIR SAP Qualtrics test kullanıcısı oluşturun](#create-sap-qualtrics-test-user) ve bu, kullanıcının Azure AD gösterimine bağlı olarak SAP Qualtrics 'de B. Simon 'a sahip olacak.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP Qualtrics** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, uygulamayı **IDP** başlatıldı modunda yapılandırmak istiyorsanız, aşağıdaki alanlar için değerleri girin:
    
    a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:

    `https://< DATACENTER >.qualtrics.com`
   
    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. **Geçiş durumu** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. **Ek URL 'Ler ayarla**' yı seçin ve uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı, yanıt URL 'SI ve geçiş durumuyla güncelleştirin. Bu değerleri almak için [Qualtrics istemci destek ekibine](https://www.qualtrics.com/support/)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde Kopyala simgesini seçerek **App Federasyon meta verileri URL 'sini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu işaretleyin ve parolayı aşağı yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, SAP Qualtrics erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SAP Qualtrics**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics SSO 'yu yapılandırma

SAP Qualtrics tarafında çoklu oturum açmayı yapılandırmak için, Azure portal kopyalanmış **uygulama Federasyon meta verileri URL 'Sini** [SAP Qualtrics destek ekibine](https://www.qualtrics.com/support/)gönderin. Destek ekibi, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlar.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics test kullanıcısı oluşturma

SAP Qualtrics, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Yapmanız gereken başka bir eylem yoktur. Bir Kullanıcı SAP Qualtrics 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SAP Qualtrics oturum açma URL 'sine yeniden yönlendirilir.  

* SAP Qualtrics oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'YU ayarladığınız SAP Qualtrics ' de otomatik olarak oturum açmış olmanız gerekir.

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki SAP Qualtrics kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız SAP Qualtrics ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SAP Qualtrics yapılandırmasını yaptıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).