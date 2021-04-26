---
title: 'Öğretici: üç cülü ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve üçüncü olarak çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514673"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Öğretici: üçüncülü ile tümleştirme Azure Active Directory

Bu öğreticide, üç cülü Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, Thdlight öğesine kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla, üç cülü (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini üç cülü ile yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir üçüncülü abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* On Dlight, SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-thirdlight-from-the-gallery"></a>Galeriden üç cülü ekleyin

Üç cülü 'ın Azure AD 'ye tümleştirmesini ayarlamak için, Galeriden, yönetilen SaaS uygulamaları listenize üç cülü eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna, **üç Dlight** girin. Arama sonuçlarında **Ondlight** ' i seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısını kullanarak üçüncü olarak Azure AD çoklu oturum açmayı yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ve ondlight içindeki ilgili Kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açma 'yı üçüncü hafif ile yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında on **[Dlight çoklu oturum açmayı yapılandırın](#configure-thirdlight-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı olan **[bir üçüncü açık test kullanıcısı oluşturun](#create-a-thirdlight-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açma 'yı üçe Dlight ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), üçüncü açık uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları izleyin.

    ![Temel SAML yapılandırması iletişim kutusu](common/sp-identifier.png)

    1. **Oturum açma URL 'si** kutusuna, bu modele bir URL girin:
    
          `https://<subdomain>.thirdlight.com/`

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, bu modele bir URL girin:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Bu değerler yer tutuculardır. Gerçek oturum açma URL 'sini ve tanımlayıcıyı kullanmanız gerekir. Değerleri almak için [üç cülü destek ekibine](https://www.thirdlight.com/support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** iletişim kutusunda gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**' nün yanındaki **İndir** bağlantısını seçin ve dosyayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. Üçüncü olarak **Ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-thirdlight-single-sign-on"></a>Üçüncü açık çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcısı penceresinde, diğer şirket sitenizde yönetici olarak oturum açın.

1. **Yapılandırma**  >  **sistem yönetimi**  >  **SAML2** şuraya gidin:

    ![Sistem Yönetimi](./media/thirdlight-tutorial/ic805843.png "Sistem Yönetimi")

1. SAML2 yapılandırma bölümünde aşağıdaki adımları uygulayın.
  
    ![SAML2 yapılandırma bölümü](./media/thirdlight-tutorial/ic805844.png "SAML2 yapılandırma bölümü")

    1. **SAML2 çoklu oturum açmayı etkinleştir '** i seçin.

    1. **IDP meta verileri Için kaynak** altında, **XML 'Den IDP meta verilerini yükle**' yi seçin.

    1. Önceki bölümde Azure portal indirdiğiniz meta veri dosyasını açın. Dosyanın içeriğini kopyalayın ve **IDP Metadata XML** kutusuna yapıştırın.

    1. **SAML2 ayarlarını kaydet**' i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory** ' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin:

    ![Tüm kullanıcılar'ı seçin](common/users.png)

2. Pencerenin üst kısmındaki **Yeni Kullanıcı** ' yı seçin:

    ![Yeni Kullanıcı Seç](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. **Ad** kutusuna **Brittasıon** yazın.
  
    1. **Kullanıcı adı** kutusuna **BrittaSimon@ girin \<yourcompanydomain> . \<extension>** (Örneğin, BrittaSimon@contoso.com .)

    1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda değer ' i yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, tek Dlight 'e erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve sonra da **üçüncü** olarak ' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **üç cülü**' ı seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-thirdlight-test-user"></a>Üçüncü bir test kullanıcısı oluşturma

Azure AD kullanıcılarının, her ikisi de 1. adımda oturum açmasını sağlamak için bunları üç cülü 'a eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:

1. Diğer şirket sitenizde yönetici olarak oturum açın.

1. **Kullanıcılar** sekmesine gidin.

1. **Kullanıcılar ve gruplar ' ı** seçin.

1. **Yeni Kullanıcı Ekle**' yi seçin.

1. Sağlamak istediğiniz geçerli bir Azure AD hesabının kullanıcı adını, adını veya açıklamasını ve e-posta adresini girin. Bir önayar veya yeni üye grubunu seçin.

1. **Oluştur**’u seçin.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için herhangi bir kullanıcı hesabı oluşturma aracını veya diğer bir deyişle, üçüncü olarak sunulan API 'leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde 3. bir kutucuğu seçtiğinizde, SSO 'yu ayarladığınız üç cülü örneğine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [Apps Portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)