---
title: 'Öğretici: Azure Active Directory Tümleştirmesi ile ITRP | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve ITRP arasında çoklu oturum açmayı yapılandırma öğreneceksiniz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656687"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Öğretici: ITRP ile Azure Active Directory Tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile ITRP tümleştirme öğreneceksiniz.
Bu tümleştirme aşağıdaki avantajları sağlar:

* Azure AD ITRP erişimi denetlemek için kullanabilirsiniz.
* Kullanıcılarınız için ITRP (çoklu oturum açma) ile Azure AD hesaplarına otomatik olarak oturum açmanız etkinleştirebilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilir: Azure portalı.

Azure AD SaaS uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory'de uygulamalar için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD Tümleştirmesi ile ITRP yapılandırmak için sahip olmanız gerekir:

* Azure AD aboneliğiniz. Bir Azure AD ortamını yoksa alabileceğiniz bir [ücretsiz bir hesap](https://azure.microsoft.com/free/).
* Tekli etkin oturum sahip bir ITRP aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, yapılandırma ve Azure AD çoklu oturum açma bir test ortamında test edin.

* ITRP SP tarafından başlatılan SSO'yu destekler.

## <a name="add-itrp-from-the-gallery"></a>Galeriden ITRP Ekle

Azure AD'de ITRP tümleştirmesini ayarlamak için yönetilen SaaS uygulamaları listenize Galeriden ITRP eklemeniz gerekir.

1. İçinde [Azure portalında](https://portal.azure.com), sol bölmede seçin **Azure Active Directory**:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. Git **kurumsal uygulamalar** > **tüm uygulamaları**:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için seçin **yeni uygulama** pencerenin üst kısmındaki:

    ![Yeni uygulama seçme](common/add-new-app.png)

4. Arama kutusuna **ITRP**. Seçin **ITRP** seçin ve arama sonuçlarını **Ekle**.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, yapılandırın ve Azure AD çoklu oturum açma ile ITRP Britta Simon adlı bir test kullanıcısı kullanarak test edin.
Çoklu oturum açmayı etkinleştirmek için ITRP içinde bir Azure AD kullanıcısı ile ilgili kullanıcı arasında bir ilişki yapmanız gerekir.

Yapılandırma ve Azure AD çoklu oturum açma ITRP ile test etmek için bu adımları tamamlamak gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on)**  kullanıcılarınız için özelliği etkinleştirmek için.
2. **[ITRP çoklu oturum açmayı yapılandırma](#configure-itrp-single-sign-on)**  uygulama tarafında.
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  Azure AD çoklu oturum açmayı test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  Azure AD çoklu oturum açma için kullanıcı etkinleştirmek için.
5. **[Bir ITRP test kullanıcısı oluşturma](#create-an-itrp-test-user)**  kullanıcı Azure AD gösterimini bağlı.
6. **[Çoklu oturum açmayı test](#test-single-sign-on)**  yapılandırma çalıştığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açma Azure portalındaki etkinleştirmeniz.

Azure AD çoklu oturum açma ile ITRP yapılandırmak için şu adımları uygulayın:

1. İçinde [Azure portalında](https://portal.azure.com/), ITRP uygulama tümleştirme sayfasında **çoklu oturum açma**:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. İçinde **tek bir oturum açma yönteminizi seçmeniz** Seç iletişim kutusunda **SAML/WS-Federasyon** modu, çoklu oturum açmayı etkinleştirmek için:

    ![Tek bir oturum açma yöntemi seçin](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlayın** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim kutusunda:

    ![Düzenle simgesi](common/edit-urls.png)

4. İçinde **temel SAML yapılandırma** iletişim kutusunda, aşağıdaki adımları uygulayın.

    ![Temel SAML yapılandırma iletişim kutusu](common/sp-identifier.png)

    1. İçinde **oturum açma URL'si** kutusuna, bu düzende bir URL girin:
    
       `https://<tenant-name>.itrp.com`

    1. İçinde **tanımlayıcı (varlık kimliği)** kutusuna, bu düzende bir URL girin:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek oturum açma URL'si ve tanımlayıcı kullanmanız gerekir. İlgili kişi [ITRP Destek ekibine](https://www.itrp.com/support) değerleri alabilirsiniz. Gösterilen desenleri de başvurabilirsiniz **temel SAML yapılandırma** Azure portalında iletişim kutusu.

5. İçinde **SAML imzalama sertifikası** bölümünden **Düzenle** açmak için simgeyi **SAML imzalama sertifikası** iletişim kutusunda:

    ![Düzenle simgesi](common/edit-certificate.png)

6. İçinde **SAML imzalama sertifikası** iletişim kutusu, kopyalama **parmak izi** değeri ve kaydedin:

    ![Parmak izi değerini kopyalayın](common/copy-thumbprint.png)

7. İçinde **ITRP kümesi** bölümünde, gereksinimlerinize göre uygun URL'ler kopyalayın:

    ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

    1. **Oturum açma URL'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL'si**.

### <a name="configure-itrp-single-sign-on"></a>ITRP çoklu oturum açmayı yapılandırın

1. Yeni bir web tarayıcısı penceresinde ITRP şirketinizin sitesi için bir yönetici olarak oturum açın

1. Pencerenin üst kısmında seçin **ayarları** simgesi:

    ![Ayarlar simgesi](./media/itrp-tutorial/ic775570.png "ayarlar simgesi")

1. Sol bölmede seçin **çoklu oturum açma**:

    ![Çoklu oturum açma seçin](./media/itrp-tutorial/ic775571.png "çoklu oturum açma seçin")

1. İçinde **çoklu oturum açma** yapılandırma bölümünde, aşağıdaki adımları uygulayın.

    ![Çoklu oturum açma bölümüne](./media/itrp-tutorial/ic775572.png "çoklu oturum açma bölümü")

    ![Çoklu oturum açma bölümüne](./media/itrp-tutorial/ic775573.png "çoklu oturum açma bölümü")

    1. **Etkin**’i seçin.

    1. İçinde **uzak oturum kapatma URL'si** kutusu, yapıştırma **oturum kapatma URL'si** Azure portaldan kopyaladığınız değeri.

    1. İçinde **SAML SSO URL** kutusu, yapıştırma **oturum açma URL'si** Azure portaldan kopyaladığınız değeri.

    1. İçinde **sertifika parmak izi** kutusu, yapıştırma **parmak izi** Azure portaldan kopyaladığınız sertifika değeri.

    1. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portalında Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portalında **Azure Active Directory** seçin sol bölmede **kullanıcılar**ve ardından **tüm kullanıcılar**:

    ![Tüm kullanıcıları seçin](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üst kısmındaki:

    ![Yeni bir kullanıcı seçin](common/new-user.png)

3. İçinde **kullanıcı** iletişim kutusunda, aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. İçinde **adı** kutusuna **BrittaSimon**.
  
    1. İçinde **kullanıcı adı** kutusuna **@ BrittaSimon\<yourcompanydomain >.\< Uzantı >** . (Örneğin, BrittaSimon@contoso.com.)

    1. Seçin **Göster parola**ve ardından içinde bir değer yazın **parola** kutusu.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Azure çoklu oturum açma kullanmak için ITRP erişim vererek Britta Simon tıklatmalarını sağlarsınız.

1. Azure portalında **kurumsal uygulamalar**seçin **tüm uygulamaları**ve ardından **ITRP**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde seçin **ITRP**.

    ![Uygulamaların listesi](common/all-applications.png)

3. Sol bölmede seçin **kullanıcılar ve gruplar**:

    ![Kullanıcıları ve grupları seçin](common/users-groups-blade.png)

4. Seçin **Kullanıcı Ekle**ve ardından **kullanıcılar ve gruplar** içinde **atama Ekle** iletişim kutusu.

    ![Kullanıcı Ekle seçeneğini belirleme](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **Britta Simon** kullanıcıların listesini ve ardından **seçin** pencerenin alt kısmındaki düğmesi.

6. SAML onaylaması rol değeri de beklediğiniz **rolü Seç** iletişim kutusunda, listeden bir kullanıcı için uygun rolü seçin. Tıklayın **seçin** pencerenin alt kısmındaki düğmesi.

7. İçinde **atama Ekle** iletişim kutusunda **atama**.

### <a name="create-an-itrp-test-user"></a>Bir ITRP test kullanıcısı oluşturma

ITRP için oturum açmak Azure AD kullanıcılarının sağlamak için ITRP eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:

1. ITRP kiracınızda oturum açın.

1. Pencerenin üst kısmında seçin **kayıtları** simgesi:

    ![Kayıt simgesini](./media/itrp-tutorial/ic775575.png "kayıtları simgesi")

1. Menüde **kişiler**:

    ![Kişileri seçin](./media/itrp-tutorial/ic775587.png "kişileri seçin")

1. Artı işaretini seçin ( **+** ) yeni bir kişiye eklemek için:

    ![Artı işaretini seçin](./media/itrp-tutorial/ic775576.png "artı işaretini seçin")

1. İçinde **Yeni Kişi Ekle** iletişim kutusunda, aşağıdaki adımları uygulayın.

    ![Windows yeni kişi Ekle iletişim kutusu](./media/itrp-tutorial/ic775577.png "Yeni Kişi Ekle iletişim kutusu")

    1. Geçerli bir Azure ad ve e-posta adresini girin, eklemek istediğiniz bir AD hesabı.

    1. **Kaydet**’i seçin.

> [!NOTE]
> Herhangi bir kullanıcı hesabı oluşturma aracı kullanabilir veya API Azure AD'ye kullanıcı hesapları sağlamak için ITRP tarafından sağlanan.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi Azure AD çoklu oturum açma yapılandırmanızı erişim panelini kullanarak test gerekir.

Erişim Paneli'nde ITRP kutucuğu seçtiğinizde, otomatik olarak SSO'yu ayarlama ITRP örneği için oturum açmanız. Erişim paneli hakkında daha fazla bilgi için bkz: [erişim ve kullanım uygulamaları uygulamalarım portalında](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
