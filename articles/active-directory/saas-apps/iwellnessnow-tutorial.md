---
title: 'Öğretici: ıwellneskar ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve ıwellneskar arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 4143d44591d92111d4d4a6cbba3e9059f201a29c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459620"
---
# <a name="tutorial-integrate-iwellnessnow-with-azure-active-directory"></a>Öğretici: ıwellneskar Azure Active Directory tümleştirin

Bu öğreticide, ıwellneskar 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Iwellneskar 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ıwellneskar 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ıwellneskar için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* ıwellneskar çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ıwellneskar **SP ve ıDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-iwellnessnow-from-the-gallery"></a>Galeriden ıwellneskar ekleme

Iwellneskar 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize ıwellneskar eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ıwellneskar** yazın.
1. Sonuçlar panelinden **ıwellneskar** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak ıwellneskar Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ıwellneskar içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu ıwellneskar ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[ıwellneskar SSO 'Yu yapılandırın](#configure-iwellnessnow-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Kullanıcının Azure AD gösterimine bağlı olan ıwellneskar 'da B. Simon 'ın bir karşılığı olacak şekilde **[ıwellneskar test kullanıcısı oluşturun](#create-iwellnessnow-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **ıwellneskar** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa ve **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur.

    ![Ekran görüntüsü, yanıt U R L girebilir ve Kaydet ' i seçerek temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak alamazsanız, değerleri gereksinimlerinize göre el ile girin.

1. **Hizmet sağlayıcı meta veri dosyanız** yoksa ve **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![ıwellneskar etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`http://<CustomerName>.iwellnessnow.com`

    b. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CustomerName>.iwellnessnow.com/ssologin`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CustomerName>.iwellnessnow.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [ıwellneskar istemci destek ekibine](mailto:info@iwellnessnow.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **meta veri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Iwellneskar ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-iwellnessnow-sso"></a>Iwellneskar SSO 'yu yapılandırma

**Iwellneskar** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [ıwellneskar destek ekibine](mailto:info@iwellnessnow.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

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

Bu bölümde, ıwellneskar 'a erişim vererek B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **ıwellneskar**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-iwellnessnow-test-user"></a>Iwellneskar test kullanıcısı oluştur

Bu bölümde, ıwellneskar içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Iwellneskar platformundaki kullanıcıları eklemek için [ıwellneskar destek ekibi](mailto:info@iwellnessnow.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ıwellneskar kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ıwellneskar üzerinden otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)