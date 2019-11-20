---
title: 'Öğretici: Azure Active Directory Tümleştirmesi ile AirWatch | Microsoft Docs'
description: Azure Active Directory ve AirWatch arasında çoklu oturum açmayı yapılandırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227684"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Öğretici: AirWatch Azure Active Directory ile tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile AirWatch tümleştirme öğreneceksiniz. AirWatch Azure AD ile tümleştirdiğinizde, şunları yapabilirsiniz:

* AirWatch erişimi, Azure AD'de denetler.
* Otomatik olarak AirWatch için kendi Azure AD hesapları ile oturum açmış olmasını sağlayın.
* Bir merkezi konumda - Azure portalı hesaplarınızı yönetin.

Azure AD SaaS uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [uygulama erişimi ve Azure Active Directory ile çoklu oturum açma nedir](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gerekir:

* Azure AD aboneliğiniz. Bir aboneliğiniz yoksa, bir aylık ücretsiz deneme alabilirsiniz [burada](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch çoklu oturum açma (SSO) abonelik etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, yapılandırma ve Azure AD SSO bir test ortamında test edin. AirWatch destekler **SP** SSO başlattı.

## <a name="adding-airwatch-from-the-gallery"></a>Galeriden AirWatch ekleme

Azure AD'de AirWatch tümleştirmesini yapılandırmak için AirWatch Galeriden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde seçin **Azure Active Directory** hizmeti.
1. Gidin **kurumsal uygulamalar** seçip **tüm uygulamaları**.
1. Yeni bir uygulama eklemek için seçin **yeni uygulama**.
1. İçinde **Galeriden Ekle** bölümüne şunu yazın **AirWatch** arama kutusuna.
1. Seçin **AirWatch** gelen sonuçlar panelinde ve uygulama ekleyin. Uygulama, kiracınıza eklendiği sırada birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Yapılandırma ve Azure AD SSO kullanarak adlı bir test kullanıcı AirWatch ile test etme **B.Simon**. Çalışmak SSO için AirWatch içinde bir Azure AD kullanıcısı ile ilgili kullanıcı arasında bir bağlantı ilişki oluşturmanız gerekir.

Yapılandırma ve Azure AD SSO ile AirWatch sınamak için aşağıdaki yapı taşlarını tamamlayın:

1. **[Azure AD SSO'yu yapılandırma](#configure-azure-ad-sso)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. **[AirWatch SSO'yu yapılandırarak](#configure-airwatch-sso)**  - uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[AirWatch test kullanıcısı oluşturma](#create-airwatch-test-user)**  - kullanıcı Azure AD gösterimini bağlı AirWatch Britta simon'un bir karşılığı vardır.
5. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
6. **[Test SSO](#test-sso)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO'yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek üzere aşağıdaki adımları izleyin.

1. İçinde [Azure portalında](https://portal.azure.com/), **AirWatch** uygulama tümleştirme sayfası, bulma **Yönet** bölümünde ve seçin **çoklu oturum açma**.
1. Üzerinde **tek bir oturum açma yönteminizi seçmeniz** sayfasında **SAML**.
1. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlayın** sayfasında, düzenleme/kalem simgesine tıklayıp **temel SAML yapılandırma** ayarlarını düzenlemek için.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. Üzerinde **temel SAML yapılandırma** sayfasında, aşağıdaki alanlar için değerleri girin:

    1. İçinde **oturum açma URL'si** metin kutusuna şu biçimi kullanarak bir URL yazın: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. İçinde **tanımlayıcı (varlık kimliği)** metin kutusunda, değer olarak girin: `AirWatch`

    > [!NOTE]
    > Bu değer, gerçek değil. Bu değer, gerçek oturum açma URL'si ile güncelleştirin. İlgili kişi [AirWatch istemci Destek ekibine](https://www.air-watch.com/company/contact-us/) bu değeri alınamıyor. Gösterilen desenleri de başvurabilirsiniz **temel SAML yapılandırma** bölümünde Azure portalında.

1. AirWatch uygulama belirli bir biçimde SAML onaylamalarını bekler. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu öznitelikleri değerlerini yönetebilirsiniz **kullanıcı öznitelikleri** uygulama tümleştirme sayfasında bölümü. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için düğmeyi **kullanıcı öznitelikleri** iletişim.

    ![image](common/edit-attribute.png)

1. İçinde **kullanıcı taleplerini** bölümünde **kullanıcı öznitelikleri** iletişim kutusunda kullanarak talep Düzenle **düzenleme simgesi** veya talep kullanarak **Ekle yeni talep**SAML belirteci özniteliği yukarıdaki görüntüde gösterildiği gibi yapılandırın ve aşağıdaki adımları gerçekleştirin:

    | Ad |  Kaynak özniteliği|
    |---------------|----------------|
    | KULLANICI KİMLİĞİ | User.userPrincipalName |
    | | |

    a. Tıklayın **Ekle yeni talep** açmak için **yönetmek, kullanıcı talepleri** iletişim.

    b. İçinde **adı** metin kutusuna, bu satır için gösterilen öznitelik adı yazın.

    c. Bırakın **Namespace** boş.

    d. Kaynağı olarak **özniteliği**.

    e. Gelen **kaynak özniteliği** listesinde, ilgili satır için gösterilen öznitelik değeri yazın.

    f. Tıklayın **Tamam**

    g. **Kaydet**’e tıklayın.

1. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **SAML imzalama sertifikası** bölümünde, bulma **Federasyon meta verileri XML** seçip **indirin** meta veri XML indirin ve bilgisayarınıza kaydedin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. Üzerinde **AirWatch kümesi** bölümünde, ihtiyacınıza göre uygun URL'lerini kopyalayın.

   ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>AirWatch SSO yapılandırma

1. Farklı bir web tarayıcı penceresinde AirWatch şirketinizin sitesi için bir yönetici olarak oturum açın.

1. Ayarlar sayfasında. Seçin **Ayarları > Kurumsal tümleştirme > Dizin Hizmetleri**.

   ![Ayarları](./media/airwatch-tutorial/ic791921.png "ayarları")

1. Tıklayın **kullanıcı** sekmesinde **temel DN** metin etki alanı adınızı yazın ve ardından **Kaydet**.

   ![Kullanıcı](./media/airwatch-tutorial/ic791922.png "kullanıcı")

1. Tıklayın **sunucu** sekmesi.

   ![Sunucu](./media/airwatch-tutorial/ic791923.png "sunucusu")

1. Aşağıdaki adımları gerçekleştirin **LDAP** bölümü:

    ![Karşıya yükleme](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Olarak **dizin türü**seçin **hiçbiri**.

    b. Seçin **SAML kimlik doğrulaması için kullanmak**.

1. Üzerinde **SAML 2.0** bölümünde indirilen sertifikayı karşıya yüklemek için tıklayın **karşıya**.

    ![Karşıya yükleme](./media/airwatch-tutorial/ic791932.png "karşıya yükleme")

1. İçinde **istek** bölümünde, aşağıdaki adımları gerçekleştirin:

    ![İstek](./media/airwatch-tutorial/ic791925.png "iste")  

    a. Olarak **isteği bağlama türü**seçin **POST**.

    b. Azure portalında, üzerinde **çoklu oturum açma sırasında AirWatch yapılandırma** iletişim sayfası, kopyalama **oturum açma URL'si** değeri ve ardından yapıştırın **kimlik sağlayıcısı tek işareti bulunan URL'si** metin kutusu.

    c. Olarak **Nameıd biçimi**seçin **e-posta adresi**.

    d. Olarak **kimlik doğrulama isteği güvenliği**seçin **hiçbiri**.

    e. **Kaydet**’e tıklayın.

1. Tıklayın **kullanıcı** yeniden sekmesi.

    ![Kullanıcı](./media/airwatch-tutorial/ic791926.png "kullanıcı")

1. İçinde **özniteliği** bölümünde, aşağıdaki adımları gerçekleştirin:

    ![Öznitelik](./media/airwatch-tutorial/ic791927.png "özniteliği")

    a. İçinde **nesne tanımlayıcısı** metin kutusuna `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. İçinde **kullanıcıadı** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. İçinde **görünen ad** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. İçinde **ad** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. İçinde **Soyadı** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. İçinde **e-posta** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, bir test kullanıcısı B.Simon adlı Azure portalında oluşturacaksınız.

1. Azure Portalı'ndaki sol bölmeden seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. İçinde **kullanıcı** özellikleri, aşağıdaki adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. İçinde **kullanıcı adı** alanına username@companydomain.extension. Örneğin: `B.Simon@contoso.com`.
   1. Seçin **Show parola** onay kutusunu işaretleyin ve ardından görüntülenen değeri yazın **parola** kutusu.
   1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Azure çoklu oturum açma kullanmak için AirWatch erişim vererek B.Simon tıklatmalarını sağlarsınız.

1. Azure portalında **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.
1. Uygulamalar listesinde **AirWatch**.
1. Uygulamanın genel bakış sayfasında bulma **Yönet** seçin ve bölüm **kullanıcılar ve gruplar**.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. Seçin **Kullanıcı Ekle**, ardından **kullanıcılar ve gruplar** içinde **atama Ekle** iletişim.

    ![Kullanıcı ekleme bağlantısı](common/add-assign-user.png)

1. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **B.Simon** kullanıcılar listesinden ardından **seçin** ekranın alt kısmındaki düğmesi.
1. SAML onaylama işlemi herhangi bir rolü değer de beklediğiniz varsa **rolü Seç** iletişim kutusunda, listeden bir kullanıcı için uygun rolü seçin ve ardından **seçin** ekranın alt kısmındaki düğmesi.
1. İçinde **atama Ekle** iletişim kutusunda, tıklayın **atama** düğmesi.

### <a name="create-airwatch-test-user"></a>AirWatch test kullanıcısı oluşturma

Azure AD kullanıcıları için AirWatch oturum açmak etkinleştirmek için bunlar içinde AirWatch için sağlanması gerekir. AirWatch söz konusu olduğunda, sağlama bir el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları gerçekleştirin:**

1. Oturum açın, **AirWatch** şirketinizin sitesi yöneticisi olarak.

2. Sol taraftaki gezinti bölmesinde **hesapları**ve ardından **kullanıcılar**.
  
   ![Kullanıcılar](./media/airwatch-tutorial/ic791929.png "kullanıcılar")

3. İçinde **kullanıcılar** menüsünde tıklatın **liste görünümü**ve ardından **Ekle > Kullanıcı Ekle**.
  
   ![Kullanıcı ekleme](./media/airwatch-tutorial/ic791930.png "kullanıcı ekleme")

4. Üzerinde **Ekle / Düzenle kullanıcı** iletişim kutusunda, aşağıdaki adımları gerçekleştirin:

   ![Kullanıcı ekleme](./media/airwatch-tutorial/ic791931.png "kullanıcı ekleme")

   a. Tür **kullanıcıadı**, **parola**, **parolayı onaylayın**, **ad**, **Soyadı**,  **E-posta adresi** istediğiniz ilgili metin kutularına zbilgisayarlar geçerli bir Azure Active Directory hesabı.

   b. **Kaydet**’e tıklayın.

> [!NOTE]
> Herhangi diğer AirWatch kullanıcı hesabı oluşturma araçları kullanabilir veya API'leri için AAD kullanıcı hesapları sağlamak AirWatch tarafından sağlanan.

### <a name="test-sso"></a>Test SSO

Erişim Paneli'nde AirWatch kutucuğu seçtiğinizde, otomatik olarak SSO'yu ayarlama AirWatch için oturum açmanız. Erişim paneli hakkında daha fazla bilgi için bkz: [erişim Paneli'ne giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
