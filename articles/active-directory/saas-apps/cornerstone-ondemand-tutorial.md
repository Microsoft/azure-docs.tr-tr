---
title: 'Öğretici: tek Sign-On köşeli çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve sağ kenar çoklu oturum açma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: ba6eb0a1b607fc05c4d0c660dd3d7016f81ef4b3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Öğretici: tek Sign-On köşeli oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, tek Sign-On Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Temel Stone tek Sign-On Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, köşeli sağ taş çoklu oturum açma erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla tek bir Sign-On için otomatik olarak oturum açmaya olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Köşeli çift taş tek Sign-On çoklu oturum açma (SSO) etkin abonelik.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Köşeli Sign-On tek başına, **SP** tarafından başlatılan SSO desteklenir.
* Temel taş tek Sign-On [Otomatik Kullanıcı sağlamayı](cornerstone-ondemand-provisioning-tutorial.md)destekler.


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Galeriden temel taş tek Sign-On ekleme

Temel bir Sign-On tek bir Azure AD ile tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize temel bir tek Sign-On eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **köşeli sağ taş çoklu oturum açma** yazın.
1. Sonuçlar panelinden **sağ Stone çoklu oturum açma** 'yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Tek Sign-On için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu, köşeli-kenar Sign-On Ile tek başına yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı ile birlikte sağ Stone çoklu oturum açma arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, köşeli çift yönlü çoklu oturum açma ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Tek **[Sign-On SSO 'Yu yapılandırma](#configure-cornerstone-single-sign-on-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. Kullanıcı Azure AD gösterimi ile bağlantılı tek bir Sign-On, en köşeli bir **[Sign-On test kullanıcısı oluşturun](#create-cornerstone-single-sign-on-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, temel **Stone çoklu oturum açma** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<PORTAL_NAME>.csod.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI, tanımlayıcı ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [tek bir Sign-On istemci destek ekibine](mailto:moreinfo@csod.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. En alt **pul ayarlama çoklu oturum açma** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma 'yı kullanarak, köşeli çift yönlü çoklu oturum açma erişimine izin vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **köşeli sağ taş çoklu oturum açma**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cornerstone-single-sign-on-sso"></a>Tek Sign-On SSO 'yu yapılandırma

Yan **taş çoklu oturum açma** tarafında çoklu oturum açma 'yı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' dan temel alınan url 'leri, [tek Sign-On destek ekibine](mailto:moreinfo@csod.com) ve lütfen iş ortağınızla iletişime geçerek göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Temel Stone oluşturma tek Sign-On test kullanıcısı

Bu bölümün amacı, alt kenar Stone çoklu oturum açma bölümünde B. Simon adlı bir Kullanıcı oluşturmaktır. Yan taş tek Sign-On, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](./cornerstone-ondemand-provisioning-tutorial.md) .

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz tek Sign-On oturum açma URL 'sine yönlendirecektir. 

* Tek başına Sign-On oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki köşeli Sign-On kutucuğunu tıklattığınızda, bu, köşeli çift yönlü Sign-On çoklu oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Tek başına bir köşeli Sign-On yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)