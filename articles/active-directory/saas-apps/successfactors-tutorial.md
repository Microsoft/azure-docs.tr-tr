---
title: 'Öğretici: Başarıyla Azure Active Directory çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve başarılı faktörleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38d40a2f72e73dde0f99ebbc9701e02c8d03738b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Öğretici: Başarıyla Azure Active Directory çoklu oturum açma (SSO) Tümleştirmesi

Bu öğreticide, başarılı faktörleri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Başarılı faktörleri Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de başarılı faktörlere erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla başarılı bir şekilde oturum açmaya yönelik otomatik olarak oturum açmaya olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik başarıyla etkinleştirildi.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Başarılı etken **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-successfactors-from-the-gallery"></a>Galeriden başarılı faktör ekleme

Başarılı faktörlerin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize başarılı bir faktör eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **başarılı faktörleri** yazın.
1. Sonuçlar panelinden **başarılı etmenler** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Başarılı faktörlere yönelik Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu başarılı faktörlerle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında başarılı bir şekilde bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu başarılı faktörlerle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[başarılı BIR SSO 'Yu yapılandırın](#configure-successfactors-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan başarılı faktörlerdeki B. Simon 'a sahip olmak için **[başarılı bir test kullanıcısı oluşturun](#create-successfactors-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **başarılı etken** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. İçinde **tanımlayıcı** metin kutusuna bir URL şu biçimi kullanarak:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [başarılı olan istemci desteği ekibine](https://www.successfactors.com/content/ssf-site/en/support.html) başvurun.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Başarılı etmenleri ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
    1. **Ad** alanına `B.Simon` girin.  
    1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
    1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
    1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, başarılı etkenlere erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **başarılı etmenler**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-successfactors-sso"></a>Başarılı etken SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak başarılı bir şekilde **Yönetim Portalı** 'nda oturum açın.

2. **Uygulama güvenliğini** ve yerel olarak **Çoklu oturum açma özelliğini**ziyaret edin.

3. Herhangi bir değeri **sıfırlama belirtecine** koyun ve SAML SSO 'yu etkinleştirmek Için **belirteci kaydet** ' e tıklayın.

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma][11]

    > [!NOTE]
    > Bu değer, açık/kapalı anahtar olarak kullanılır. Herhangi bir değer kaydedilirse, SAML SSO 'SU açık olur. Boş bir değer kaydedilirse, SAML SSO KAPALıDıR.

4. ' In altında yerel ekran görüntüsü ve aşağıdaki eylemleri gerçekleştirir:

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma][12]
  
    a. **SAML V2 SSO** radyo düğmesini seçin
  
    b. **SAML asserting parti adını**(ÖRNEĞIN, SAML veren + şirket adı) ayarlayın.

    c. **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. **Zorunlu Imza ıste** **onay** seçimini yapın.

    e. **ETKIN** **SAML bayrağını etkinleştir**' i seçin.

    f. **Oturum açma Isteği imzası olarak Hayır (SF tarafından oluşturulan/SP/RP)** seçeneğini belirleyin.

    g. **SAML profili**olarak **tarayıcı/gönderi profili** ' ni seçin.

    h. **Sertifikayı zorla geçerli süre**olarak **Hayır** ' ı seçin.

    i. İndirilen sertifika dosyasının içeriğini Azure portal kopyalayın ve ardından bunu **SAML sertifikası** metin kutusuna yapıştırın.

    > [!NOTE] 
    > Sertifika içeriğinin başlangıç sertifikası ve bitiş sertifikası etiketleri olmalıdır.

5. SAML V2 ' ye gidin ve aşağıdaki adımları gerçekleştirin:

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma][13]

    a. **SP tarafından başlatılan genel oturum kapatma desteği**olarak **Evet** ' i seçin.

    b. **Global oturum kapatma hizmeti URL 'si (LogoutRequest Destination)** metin kutusunda, Azure Portal formunu kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    c. **Hayır** , **SP ıste tüm NameID öğelerini şifrelemelidir**.

    d. **NameID biçimi**olarak **belirtilmemiş** öğesini seçin.

    e. **SP tarafından başlatılan oturum açmayı etkinleştir (authbir)** olarak **Evet** ' i seçin.

    f. **Gönderme Isteği şirket genelinde veren** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

6. Oturum açma kullanıcı adları büyük/küçük harfe duyarsız hale getirmek istiyorsanız bu adımları gerçekleştirin.

    ![Çoklu oturum açmayı yapılandırın][29]

    a. **Şirket ayarlarını**ziyaret edin (alt kısma yakın).

    b. **Büyük/küçük harfe duyarlı olmayan kullanıcı adını etkinleştir**yakınında onay kutusunu seçin.

    c. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Bunu etkinleştirmeye çalışırsanız, sistem yinelenen bir SAML oturum açma adı oluşturup oluşturmadığını denetler. Örneğin, müşteri için Kullanıcı1 ve Kullanıcı1 Kullanıcı adları varsa. Büyük/küçük harf duyarlılığı bu yinelemeleri yapar. Sistem size bir hata iletisi verir ve özelliği etkinleştirmez. Müşterinin, farklı bir şekilde yazılması için kullanıcı adlarıyla birini değiştirmesi gerekir.

### <a name="create-successfactors-test-user"></a>Başarılı etken testi Kullanıcı Oluştur

Azure AD kullanıcılarının başarılı bir faktörde oturum açmasını sağlamak için, bunların başarılı bir şekilde sağlanması gerekir. Başarılı etken durumunda, sağlama el ile gerçekleştirilen bir görevdir.

Kullanıcıları başarılı bir şekilde oluşturulan kullanıcılara almak için, [başarılı olan destek ekibine](https://www.successfactors.com/content/ssf-site/en/support.html)başvurmanız gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde başarılı bir kutucuğa tıkladığınızda, SSO 'yu ayarladığınız başarılı faktörlerin otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile başarılı faktörleri deneyin](https://aad.portal.azure.com)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
