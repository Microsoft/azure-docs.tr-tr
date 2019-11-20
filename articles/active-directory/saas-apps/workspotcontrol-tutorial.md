---
title: 'Öğretici: Azure Active Directory Tümleştirmesi Workspot denetimiyle | Microsoft Docs'
description: Azure Active Directory ve Workspot denetimi için çoklu oturum açmayı yapılandırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086676"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Öğretici: Azure Active Directory Tümleştirmesi Workspot denetimi ile

Bu öğreticide, Azure Active Directory (Azure AD) ile Workspot denetimi tümleştirme konusunda bilgi edinin. Workspot denetimi Azure AD ile tümleştirdiğinizde, şunları yapabilirsiniz:

* Workspot denetim erişimi denetlemek için Azure AD kullanın.
* Otomatik olarak Workspot denetime (çoklu oturum açma [SSO]), Azure AD hesaplarını kullanarak oturum açmalarını sağlar.
* Tek bir merkezi konumda hesaplarınızı yönetin: Azure portalı.

Azure AD SaaS uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure AD uygulamaları için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesi Workspot denetimi ile yapılandırmak için aşağıdakiler gerekir:

* Azure AD aboneliğiniz. Bir Azure AD ortamını yoksa alabileceğiniz bir [ücretsiz bir hesap](https://azure.microsoft.com/free/).

* Workspot denetim tek bir oturum üzerinde etkin olmayan abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, yapılandırma ve Azure AD çoklu oturum açma bir test ortamında test edin.

> [!Note]
> Workspot denetim SP tarafından başlatılan ve IDP tarafından başlatılan SSO'yu destekler.


## <a name="adding-workspot-control-from-the-gallery"></a>Galeriden Workspot denetimi ekleme

Azure AD'de Workspot denetim tümleştirmesini yapılandırmak için Workspot denetimi yönetilen SaaS uygulamaları listenize Galeriden eklemeniz gerekir.

**Galeriden Workspot denetim eklemek için aşağıdaki adımları izleyin:**

1. Sol bölmesinde [Azure portalında](https://portal.azure.com)seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar** seçip **tüm uygulamaları**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

3. Seçin **yeni uygulama** pencerenin üst kısmındaki.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Workspot denetimi**seçin **Workspot denetimi** sonuçlar paneli ve ardından **Ekle**.

     !["Galeriden Ekle" penceresi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, yapılandırma ve Azure AD çoklu oturum açma Workspot denetimiyle Britta Simon bir test kullanıcısı için test edin.
Tek iş için oturum açma için Workspot denetiminde bir Azure AD kullanıcısı ile ilgili kullanıcı arasında bir bağlantı oluşturmanız gerekir.

Yapılandırma ve Azure AD çoklu oturum açma Workspot denetimi ile test etmek için aşağıdaki görevleri tamamlamanız gerekir:

1. [Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on) kullanıcılarınızın özelliği kullanmak etkinleştirmek için.
2. [Workspot Denetim Çoklu oturum açmayı yapılandırma](#configure-workspot-control-single-sign-on) üzerinde uygulama tarafından çoklu oturum açma ayarları yapılandırmak için.
3. [Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user) Azure AD çoklu oturum açma Britta Simon için test etmek için.
4. [Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user) Britta Simon, Azure AD çoklu oturum açma kullanmak üzere etkinleştirmek için.
5. [Workspot denetim test kullanıcısı oluşturma](#create-a-workspot-control-test-user) Workspot denetiminde, kullanıcının Azure AD gösterimini bağlı bir karşılığı Britta simon'un kurmak için.
6. [Çoklu oturum açmayı test](#test-single-sign-on) yapılandırma çalıştığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açma Azure portalında etkinleştirin.

Azure AD çoklu oturum açma Workspot denetimi ile yapılandırmak için aşağıdaki adımları izleyin:

1. Üzerinde **Workspot denetimi** uygulama tümleştirme sayfasında [Azure portalında](https://portal.azure.com/)seçin **çoklu oturum açma**.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. İçinde **tek bir oturum açma yönteminizi seçmeniz** penceresinde **SAML** modu, çoklu oturum açmayı etkinleştirmek için.

    ![Çoklu oturum açma seçin yöntemi penceresi seçin](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** erişmek için (Kalem) simgesine **temel SAML yapılandırma**.

    !["Temel SAML yapılandırması" vurgulanmış simgesini Düzenle](common/edit-urls.png)

4. İçinde **temel SAML yapılandırma** bölümünde IDP tarafından başlatılan modunda uygulama yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

    ![Oturum açma bilgileri çoklu Workspot denetimi etki alanı ve URL'ler](common/idp-intiated.png)

    1. İçinde **tanımlayıcı** metin kutusunda, aşağıdaki desende bir URL girin:<br/>
    ***https://<<i></i>INSTANCENAME >-saml.workspot.com/saml/metadata***

    1. İçinde **yanıt URL'si** metin kutusunda, aşağıdaki desende bir URL girin:<br/>
    ***https://<<i></i>INSTANCENAME >-saml.workspot.com/saml/assertion***

5. SP tarafından başlatılan modunda uygulama yapılandırmak isteyip istemediğinizi seçin **ek URL'lerini ayarlayın**.

    ![Oturum açma bilgileri çoklu Workspot denetimi etki alanı ve URL'ler](common/metadata-upload-additional-signon.png)

    İçinde **oturum açma URL'si** metin kutusunda, aşağıdaki desende bir URL girin:<br/>
    ***https://<<i></i>INSTANCENAME >-saml.workspot.com/***

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerler gerçek tanımlayıcısıyla değiştirin, yanıt URL'si ve oturum açma URL'si. İlgili kişi [Workspot denetimi istemci Destek ekibine](mailto:support@workspot.com) bu değerleri almak için. Veya desenleri de başvurabilirsiniz **temel SAML yapılandırma** Azure portal'ın bölümü.

6. Üzerinde **ayarlanmış yukarı çoklu oturum açma SAML ile** sayfasında **SAML imzalama sertifikası** bölümünden **indirme** indirmek için **sertifika (Base64)** gereksinimlerinize kullanılabilir seçeneklerden. Dosyayı bilgisayarınıza kaydedin.

    ![Sertifika (Base64) indirme bağlantısı](common/certificatebase64.png)

7. İçinde **Workspot denetimini Ayarla** bölümünde, gereksinimlerinize uygun URL'leri kopyalayın:

    ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

    - **Oturum açma URL'si**

    - **Azure AD tanımlayıcısı**

    - **Oturum kapatma URL'si**

### <a name="configure-workspot-control-single-sign-on"></a>Workspot Denetim Çoklu oturum açmayı yapılandırın

1. Farklı bir web tarayıcı penceresinde Workspot denetlemek için güvenlik yöneticisi olarak oturum açın.

2. Sayfanın üst kısmındaki araç çubuğunda, seçin **Kurulum** ardından **SAML**.

    ![Kurulum seçenekleri](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. İçinde **güvenlik onaylama işlemi biçimlendirme dili Yapılandırması** penceresinde aşağıdaki adımları izleyin:
 
    ![Güvenlik onaylama işlemi biçimlendirme dili Yapılandırması penceresi](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. İçinde **varlık kimliği** kutusu, yapıştırma **Azure Ad tanımlayıcısı** Azure portaldan kopyaladığınız.

    1. İçinde **oturum açma hizmeti URL'si** kutusu, yapıştırma **oturum açma URL'si** Azure portaldan kopyaladığınız.

    1. İçinde **oturum kapatma hizmeti URL'si** kutusu, yapıştırma **oturum kapatma URL'si** Azure portaldan kopyaladığınız.

    1. Seçin **güncelleştirme dosyası** base-64 X.509 sertifikayı karşıya yüklemek için Azure portalından indirdiğiniz sertifika kodlanmış.

    1. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portalında bir test kullanıcısı oluşturun.

1. Azure portalının sol bölmesinde seçin **Azure Active Directory**, **kullanıcılar**, ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** pencerenin üst kısmındaki.

    !["Yeni kullanıcı" düğmesi](common/new-user.png)

3. Kullanıcı için Özellikler'de, aşağıdaki adımları izleyin:

    ![Kullanıcı Özellikleri penceresi](common/user-properties.png)

    1. İçinde **adı** alanına **BrittaSimon**.
  
    1. İçinde **kullanıcı adı** girin **brittasimon@* yourcompanydomain.extension***. Örneğin,  **BrittaSimon@contoso.<i> </i>com**.

    1. Seçin **Show parola** onay kutusu. Ardından görüntülenen değeri yazın **parola** kutusu.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, her Azure çoklu oturum açma kullanmak üzere etkinleştirmek için Workspot denetimine Britta Simon erişim verin.

1. Azure portalında **kurumsal uygulamalar**, **tüm uygulamaları**, ardından **Workspot denetim**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

2. Uygulamalar listesinden **Workspot denetim**.

    ![Uygulamalar listesinde Workspot denetimi bağlantı](common/all-applications.png)

3. Sol taraftaki menüden **kullanıcılar ve gruplar**.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. Seçin **Kullanıcı Ekle** düğmesi. Ardından **kullanıcılar ve gruplar** içinde **ataması ekleme** penceresi.

    !["Atama Ekle" penceresi](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** penceresinde **Britta Simon** gelen **kullanıcılar** listesi. Ardından **Seç**'e tıklayın.

6. SAML onaylaması içinde herhangi bir rolü değer bekliyorsanız, listeden bir kullanıcı için uygun rolü seçin **rolü Seç** penceresi. Ardından **seçin** altındaki.

7. İçinde **atama Ekle** penceresinde **atama**.

### <a name="create-a-workspot-control-test-user"></a>Workspot denetim test kullanıcısı oluşturma

Workspot denetimine oturum açmak Azure AD kullanıcılarının etkinleştirmek için bunlar Workspot denetimine sağlanması gerekir. Sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için şu adımları izleyin:**

1. Workspot denetimine bir güvenlik yöneticisi olarak oturum açın.

2. Sayfanın üst kısmındaki araç çubuğunda, seçin **kullanıcılar** ardından **Kullanıcı Ekle**.

    !["Kullanıcılar" seçenekleri](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. İçinde **yeni kullanıcı ekleme** penceresinde aşağıdaki adımları izleyin:

    !["Yeni kullanıcı ekleme" penceresi](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. İçinde **ad** kutusunda, bir kullanıcı adı gibi girin **Britta**.

    1. İçinde **Soyadı** metin kutusunda, son kullanıcı adını girin **Simon**.

    1. İçinde **e-posta** kutusuna, kullanıcının e-posta adresi gibi girin  **Brittasimon@contoso.<i> </i>com**.

    1. Uygun kullanıcı rolünden seçin **rol** aşağı açılan listesi.

    1. Uygun kullanıcı grubunu seçin **grubu** aşağı açılan listesi.

    1. Seçin **kullanıcı ekleme**.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, biz bizim Azure AD çoklu oturum açma yapılandırması yoluyla test *erişim paneli*.

Tıkladığınızda **Workspot denetimi** kutucuğuna erişim panelinde, otomatik olarak için SSO'yu ayarladığınız Workspot denetim oturum. Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Azure Active Directory'de uygulamalar için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
