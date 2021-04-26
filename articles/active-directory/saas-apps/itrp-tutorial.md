---
title: 'Öğretici: IRP ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ile IRP arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
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
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459688"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Öğretici: IRP ile tümleştirme Azure Active Directory

Bu öğreticide, IRP 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bu tümleştirme bu avantajları sağlar:

* Azure AD 'yi, IRP 'ye kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla IRP 'de (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini IRP ile yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma özelliği etkinleştirilmiş bir IRP aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* IRP, SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-itrp-from-the-gallery"></a>Galeriden IRP ekleyin

IRP 'yi Azure AD 'ye tümleştirmeyi ayarlamak için Galeriden yönetilen SaaS uygulamaları listenize IRP eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin:

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **ITRP** yazın. Arama sonuçlarında **IRP** ' yi seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısı kullanarak ITRP ile Azure AD çoklu oturum açmayı yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ve IRP içindeki ilgili Kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açma 'yı IRP ile yapılandırmak ve test etmek için şu adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. Uygulama tarafında **[IRP çoklu oturum açmayı yapılandırın](#configure-itrp-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[BIR IRP test kullanıcısı oluşturun](#create-an-itrp-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açma 'yı IRP ile yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), IRP uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Ekran görüntüsü, Düzenle simgesi seçili olan SAML ile tekli Sign-On ayarlama sayfasını gösterir.](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın.

    ![Temel SAML yapılandırması iletişim kutusu](common/sp-identifier.png)

    1. **Oturum açma URL 'si** kutusuna, bu modele bir URL girin:
    
       `https://<tenant-name>.itrp.com`

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, bu modele bir URL girin:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek oturum açma URL 'sini ve tanımlayıcıyı kullanmanız gerekir. Değerleri almak için [IRP destek ekibine](https://www.4me.com/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** iletişim kutusunda gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Ekran görüntüsünde, düzenleme simgesi seçili olan SAML Imzalama sertifikası sayfası gösterilir.](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** iletişim kutusunda, **parmak izi** değerini kopyalayın ve kaydedin:

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **IRP ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-itrp-single-sign-on"></a>IRP çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcısı penceresinde, IRP şirket sitenizde yönetici olarak oturum açın.

1. Pencerenin üst kısmında, **Ayarlar** simgesini seçin:

    ![Ayarlar simgesi](./media/itrp-tutorial/ic775570.png "Ayarlar simgesi")

1. Sol bölmede **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](./media/itrp-tutorial/ic775571.png "Tek Sign-On seçin")

1. **Çoklu oturum açma** yapılandırması bölümünde aşağıdaki adımları uygulayın.

    ![Ekran görüntüsü, seçili etkin olan tek Sign-On bölümünü gösterir.](./media/itrp-tutorial/ic775572.png "Tek Sign-On bölümü")

    ![Ekran görüntüsü, bu adımda açıklanan bilgileri ekleyebileceğiniz tek Sign-On bölümünü gösterir.](./media/itrp-tutorial/ic775573.png "Tek Sign-On bölümü")

    1. **Etkin**'i seçin.

    1. **Uzaktan oturum kapatma URL 'si** kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    1. **SAML SSO URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. **Sertifika parmak izi** kutusunda, Azure Portal kopyaladığınız sertifikanın **parmak izi** değerini yapıştırın.

    1. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory** ' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin:

    ![Tüm kullanıcılar'ı seçin](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin:

    ![Yeni Kullanıcı Seç](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. **Ad** kutusuna **Brittasıon** yazın.
  
    1. **Kullanıcı adı** kutusuna **BrittaSimon@ girin \<yourcompanydomain> . \<extension>** (Örneğin, BrittaSimon@contoso.com .)

    1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda değer ' i yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak ITRP 'ye erişim izni vererek Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **IRP**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **IRP**' yi seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Pencerenin alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-an-itrp-test-user"></a>IRP test kullanıcısı oluşturma

Azure AD kullanıcılarının IRP 'de oturum açmasını sağlamak için, onları IRP 'ye eklemeniz gerekir. Bunları el ile eklemeniz gerekir.

Bir kullanıcı hesabı oluşturmak için şu adımları uygulayın:

1. IRP kiracınızda oturum açın.

1. Pencerenin üst kısmında **kayıtlar** simgesini seçin:

    ![Kayıtlar simgesi](./media/itrp-tutorial/ic775575.png "Kayıtlar simgesi")

1. Menüsünde **kişiler**' i seçin:

    ![Kişi Seç](./media/itrp-tutorial/ic775587.png "Kişi Seç")

1. **+** Yeni bir kişi eklemek için artı işaretini () seçin:

    ![Artı işaretini seçerek](./media/itrp-tutorial/ic775576.png "Artı işaretini seçerek")

1. **Yeni kişi ekle** iletişim kutusunda aşağıdaki adımları uygulayın.

    ![Yeni kişi Ekle iletişim kutusu](./media/itrp-tutorial/ic775577.png "Yeni kişi Ekle iletişim kutusu")

    1. Eklemek istediğiniz geçerli bir Azure AD hesabının adını ve e-posta adresini girin.

    1. **Kaydet**’i seçin.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için IRP tarafından sunulan herhangi bir kullanıcı hesabı oluşturma aracını veya API 'YI kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde IRP kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız IRP örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [Apps Portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)