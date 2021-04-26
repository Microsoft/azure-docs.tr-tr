---
title: 'Öğretici: Iş noktası denetimiyle tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve çalışma noktası denetimi için çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 65e424c6275f9990a5d5a42a54bc4131300a45bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895044"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Öğretici: Iş noktası denetimiyle tümleştirme Azure Active Directory

Bu öğreticide, çalışma noktası denetimini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Iş noktası denetimini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Iş noktası denetimine kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcıların Azure AD hesaplarını kullanarak otomatik olarak Iş noktası denetiminde (çoklu oturum açma [SSO]) oturum açmasını sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure AD 'de uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Iş noktası denetimiyle yapılandırmak için aşağıdaki işlemleri yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

* Bir Iş noktası denetimi çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

> [!Note]
> Çalışma noktası denetimi SP tarafından başlatılan ve ıDP tarafından başlatılan SSO 'yu destekler.


## <a name="adding-workspot-control-from-the-gallery"></a>Galeriden çalışma noktası denetimi ekleme

Çalışma noktası denetiminin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize çalışma noktası denetimi eklemeniz gerekir.

**Galeriden çalışma noktası denetimi eklemek için şu adımları izleyin:**

1. [Azure Portal](https://portal.azure.com)sol bölmesinde **Azure Active Directory**' ı seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

3. Pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **çalışma noktası denetimi** girin, sonuçlar panelinden **çalışma noktası denetimi** ' ni seçin ve ardından **Ekle**' yi seçin.

     !["Galeriden Ekle" penceresi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, bir test kullanıcısı için, Britta Simon ile birlikte Iş noktası denetimiyle yapılandırıp test edersiniz.
Çoklu oturum açma için, çalışma noktası denetimindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD çoklu oturum açmayı Iş noktası denetimiyle yapılandırmak ve test etmek için aşağıdaki görevleri gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın özelliğini kullanmasını sağlamak için [Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on) .
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [Iş noktası denetimini çoklu oturum açmayı yapılandırın](#configure-workspot-control-single-sign-on) .
3. Britta Simon için Azure AD çoklu oturum açma sınamasını test etmek üzere [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
5. Kullanıcının Azure AD gösterimine bağlı olan, çalışma noktası denetiminde Britta Simon 'ın bir örneğini [oluşturmak için bir Iş noktası denetimi test kullanıcısı oluşturun](#create-a-workspot-control-test-user) .
6. Yapılandırmanın çalıştığını doğrulamak için [Çoklu oturum açmayı test](#test-single-sign-on) edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Iş noktası denetimiyle yapılandırmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) **çalışma noktası denetimi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seçin** penceresinde, çoklu oturum açmayı etkinleştirmek için **SAML** modu ' nu seçin.

    ![Çoklu oturum açma seçme yöntemi penceresi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırmasına** erişmek için **Düzenle** (kurşun kalem) simgesini seçin.

    !["Temel SAML yapılandırması" içinde vurgulanmış düzenleme simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı IDP tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    1. **Tanımlayıcı** metin kutusunda, aşağıdaki düzende bir URL girin:<br/>
    ***https://<<i></i> ınstancename>-SAML.workspot.com/SAML/Metadata***

    1. **Yanıt URL** 'si metin kutusuna aşağıdaki düzende bir URL girin:<br/>
    ***https://<<i></i> ınstancename>-SAML.workspot.com/SAML/assertion***

5. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla**' yı seçin.

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki düzende bir URL girin:<br/>
    ***https://<<i></i> ınstancename>-SAML.workspot.com/***

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile değiştirin. Bu değerleri almak için [Iş noktası denetimi istemci destek ekibine](mailto:support@workspot.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümündeki desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, gereksinimlerinize göre kullanılabilir seçeneklerden **sertifika (base64)** indirmek için **İndir** ' i seçin. Bu dosyayı bilgisayarınıza kaydedin.

    ![Sertifika (base64) indirme bağlantısı](common/certificatebase64.png)

7. **Çalışma noktası Ayarla denetim** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    - **Oturum açma URL 'SI**

    - **Azure AD tanımlayıcısı**

    - **Oturum kapatma URL 'SI**

### <a name="configure-workspot-control-single-sign-on"></a>Çalışma noktası denetimini çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, çalışma noktası denetiminde güvenlik yöneticisi olarak oturum açın.

2. Sayfanın üst kısmındaki araç çubuğunda, **Kurulum** ve ardından **SAML**' yi seçin.

    ![Kurulum seçenekleri](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. **Security assertion Markup Language yapılandırma** penceresinde aşağıdaki adımları izleyin:
 
    ![Security Assertion Markup Language yapılandırma penceresi](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. **VARLıK kimliği** kutusunda, Azure Portal kopyaladığınız **Azure ad tanımlayıcısını** yapıştırın.

    1. Oturum açma **hizmeti URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    1. **Oturum kapatma hizmeti URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL** 'sini yapıştırın.

    1. Azure portal indirdiğiniz temel 64 kodlu sertifikayı X. 509.440 sertifikasına yüklemek için **güncelleştirme dosyasını** seçin.

    1. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**, **Kullanıcılar** ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Pencerenin üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    !["Yeni Kullanıcı" düğmesi](common/new-user.png)

3. Kullanıcının özelliklerinde şu adımları izleyin:

    ![Kullanıcı Özellikleri penceresi](common/user-properties.png)

    1. **Ad** alanına **Brittasıon** yazın.
  
    1. **Kullanıcı adı** alanına, **brittasimon@* yourcompanydomain. Extension * * * yazın. Örneğin, girin **BrittaSimon@contoso . <i></i> com**.

    1. **Parolayı göster** onay kutusunu seçin. Ardından **parola** kutusunda görüntülenen değeri yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanmasını sağlamak için, çalışma noktası denetimine Britta Simon erişimi verirsiniz.

1. Azure portal **Kurumsal uygulamalar**, **tüm uygulamalar** ve ardından **çalışma noktası denetimi**' ni seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **çalışma noktası denetimi**' ni seçin.

    ![Uygulamalar listesindeki çalışma noktası denetim bağlantısı](common/all-applications.png)

3. Sol taraftaki menüden **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesini seçin. Sonra **atama Ekle** penceresinde **Kullanıcılar ve gruplar** ' ı seçin.

    !["Atama Ekle" penceresi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** penceresinde, **Kullanıcılar** listesinden **Britta Simon** ' u seçin. Ardından **Seç**'e tıklayın.

6. SAML onaylama 'da herhangi bir rol değeri bekleliyorsanız, **Rol Seç** penceresinde listeden Kullanıcı için uygun rolü seçin. Ardından en altta **Seç** ' e tıklayın.

7. **Atama Ekle** penceresinde **ata**' yı seçin.

### <a name="create-a-workspot-control-test-user"></a>Çalışma noktası denetimi test kullanıcısı oluşturma

Azure AD kullanıcılarının, çalışma noktası denetiminde oturum açmasını sağlamak için, Iş noktası denetimine sağlanması gerekir. Sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için şu adımları izleyin:**

1. Iş noktası denetiminde güvenlik yöneticisi olarak oturum açın.

2. Sayfanın üst kısmındaki araç çubuğunda **Kullanıcılar** ' ı ve sonra **Kullanıcı Ekle**' yi seçin.

    !["Kullanıcı" seçenekleri](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. **Yeni Kullanıcı Ekle** penceresinde aşağıdaki adımları izleyin:

    !["Yeni Kullanıcı Ekle" penceresi](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. **Ad** kutusuna bir kullanıcının adını ( **Britta** gibi) girin.

    1. **Soyadı** metin kutusuna kullanıcının soyadı ( **Simon** gibi) girin.

    1. **E-posta** kutusuna kullanıcının e-posta adresini girin, örneğin **Brittasimon@contoso . <i></i> com**.

    1. **Rol** açılan listesinden uygun Kullanıcı rolünü seçin.

    1. **Grup** açılan listesinden ilgili kullanıcı grubunu seçin.

    1. **Kullanıcı Ekle**' yi seçin.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, *erişim paneli* aracılığıyla Azure AD çoklu oturum açma yapılandırmanızı test ediyoruz.

Erişim paneli 'nde **çalışma noktası denetim** kutucuğuna TıKLADıĞıNıZDA, SSO 'Yu ayarladığınız Iş noktası denetiminde otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)