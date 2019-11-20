---
title: 'Öğretici: AnswerHub ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve AnswerHub arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152998"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Öğretici: AnswerHub ile tümleştirme Azure Active Directory

Bu öğreticide, AnswerHub 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
AnswerHub Azure AD ile tümleştirmek şu avantajları sağlar:

* Azure AD 'yi, AnswerHub 'e kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla (çoklu oturum açma) AnswerHub 'de otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı merkezi bir konumdan yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini AnswerHub ile yapılandırmak için aşağıdakiler gerekir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık denemeyi](https://azure.microsoft.com/pricing/free-trial/)başlatabilirsiniz.
* Çoklu oturum açma özelliği etkin olan bir AnswerHub aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* AnswerHub, SP tarafından başlatılan SSO 'yu destekler.

## <a name="add-answerhub-from-the-gallery"></a>Galeriden AnswerHub ekleme

AnswerHub 'in Azure AD 'ye tümleştirmesini ayarlamak için galerideki AnswerHub 'yi yönetilen SaaS uygulamalarınıza eklemeniz gerekir.

**Galeriden AnswerHub eklemek için:**

1. [Azure Portal](https://portal.azure.com)sol bölmedeki **Azure Active Directory**' ı seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **AnswerHub**girin. Sonuçlar listesinde **AnswerHub** ' ı seçin ve ardından **Ekle**' yi seçin.

     ![Sonuç listesinde AnswerHub](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı ayarlama ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısını kullanarak AnswerHub ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, AnswerHub içinde bir Azure AD kullanıcısı ile buna karşılık gelen kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD çoklu oturum açmayı AnswerHub ile yapılandırmak ve test etmek için şu görevleri gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın özelliğini kullanmasını sağlamak için [Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on) .
2. Uygulama tarafında çoklu oturum açma ayarlarını ayarlamak için [AnswerHub çoklu oturum açmayı yapılandırın](#configure-answerhub-single-sign-on) .
3. Britta Simon adlı [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
5. Azure AD test kullanıcısına bağlı olan ve öğesine karşılık gelen bir AnswerHub test kullanıcısı oluşturun.
6. Yapılandırmanın çalıştığını doğrulamak için [Çoklu oturum açmayı test](#test-single-sign-on) edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma 'yı ayarlarsınız.

**Azure AD çoklu oturum açmayı AnswerHub ile yapılandırmak için:**

1. [Azure Portal](https://portal.azure.com/), **AnswerHub** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma düğmesi](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma yöntemi seçme iletişim kutusu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** iletişim kutusunu açmak için Düzenle simgesini seçin.

    ![SAML sayfasıyla çoklu oturum açmayı ayarlama](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın:

    ![Temel SAML yapılandırma bölümü](common/sp-identifier.png)

    a. **Oturum açma URL 'si** kutusunda, bu düzene sahıp bir URL girin: `https://<company>.answerhub.com`

    b. **Tanımlayıcı (VARLıK kimliği)** kutusunda, bu düzene sahıp bir URL girin: `https://<company>.answerhub.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Değerleri almak için [AnswerHub destek ekibine](mailto:success@answerhub.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **indirme** bağlantısını, gereksinimlerinize göre seçin ve sertifikayı bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **AnswerHub ayarla** bölümünde, gereksinimlerinize göre uygun URL 'Yi veya URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

   Şu URL 'Leri kopyalayabilirsiniz:
    - Oturum açma URL 'SI

    - Azure AD tanımlayıcısı

    - Oturum kapatma URL 'SI

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub çoklu oturum açmayı yapılandırma

Bu bölümde, AnswerHub için çoklu oturum açma ayarlarsınız.  

**AnswerHub çoklu oturum açmayı yapılandırmak için:**

1. Farklı bir Web tarayıcısı penceresinde, AnswerHub şirket sitenizde yönetici olarak oturum açın.

    > [!NOTE]
    > AnswerHub yapılandırması için yardıma ihtiyacınız varsa, [AnswerHub destek ekibine](mailto:success@answerhub.com.)başvurun.

2. **Yönetim**bölümüne gidin.

3. **Kullanıcı ve gruplar** sekmesindeki sol bölmede, **sosyal ayarlar** bölümünde **SAML kurulumu**' nu seçin.

4. **IDP yapılandırması** sekmesinde, şu adımları izleyin:

    ![Kullanıcılar & gruplar sekmesi](./media/answerhub-tutorial/ic785172.png "SAML kurulumu")  
  
    a. **IDP oturum açma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.
  
    b. **IDP oturum kapatma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL** 'sini yapıştırın.

    c. **IDP ad tanımlayıcı biçimi** kutusunda, Azure Portal **Kullanıcı öznitelikleri** bölümünde seçili **tanımlayıcı** değeri girin.
  
    d. **Anahtarlar ve Sertifikalar ' ı**seçin.

5. **Anahtarlar ve sertifikalar** bölümünde, şu adımları izleyin:

    ![Anahtarlar ve sertifikalar bölümü](./media/answerhub-tutorial/ic785173.png "Anahtarlar ve sertifikalar")  

    a. Not defteri 'nde Azure portal indirdiğiniz base64 kodlu sertifikayı açın, içeriğini kopyalayın ve ardından içeriği **IDP ortak anahtarı (x509 biçimi)** kutusuna yapıştırın.
  
    b. **Kaydet**’i seçin.

6. **IDP yapılandırması** sekmesinde yeniden **Kaydet** ' i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

**Bir Azure AD test kullanıcısı oluşturmak için:**

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    ![Azure Active Directory, kullanıcılar, tüm kullanıcılar ' ı seçin](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özelliklerinde, bu adımları izleyin.

    ![Kullanıcı özellikleri](common/user-properties.png)

    a. **Ad** kutusuna **Brittasıon**yazın.
  
    b. **Kullanıcı adı** kutusuna **brittasıon\@< yourcompanydomain. Extension >** girin.  
    Örneğin, BrittaSimon@contoso.com.

    c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.

    d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Kullanıcı AnswerHub 'ye erişim izni vererek Azure AD çoklu oturum açma 'yı kullanmak için Kullanıcı Britta Simon 'u ayarlarsınız.

**Azure AD test kullanıcısını atamak için:**

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **AnswerHub**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **AnswerHub**' yi seçin.

    ![Uygulamalar listesi](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcıları ve grupları seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama bölmesi Ekle](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda **Kullanıcılar** listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.

6. SAML assertion 'da bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. 

7. Ekranın alt kısmındaki **Seç** düğmesini seçin.

8. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-an-answerhub-test-user"></a>AnswerHub test kullanıcısı oluşturma

Azure AD kullanıcılarının AnswerHub 'de oturum açmasını sağlamak için bunları AnswerHub 'ye eklemeniz gerekir. AnswerHub ' de, bu görev el ile yapılır.

**Bir kullanıcı hesabı ayarlamak için:**

1. **AnswerHub** şirket sitenizde yönetici olarak oturum açın.

2. **Yönetim**bölümüne gidin.

3. **Kullanıcılar & grupları** sekmesini seçin.

4. Sol bölmedeki **Kullanıcıları Yönet** bölümünde, **Kullanıcı oluştur veya içeri aktar**' ı seçin ve ardından **Kullanıcılar & grupları**' nı seçin.

   ![Kullanıcılar & gruplar sekmesi](./media/answerhub-tutorial/ic785175.png "Kullanıcılar & grupları")

5. Uygun kutulara, eklemek istediğiniz geçerli bir Azure AD hesabının **e-posta adresini**, **Kullanıcı adını**ve **parolasını** girin ve ardından **Kaydet**' i seçin.

> [!NOTE]
> Azure AD Kullanıcı hesaplarını ayarlamak için AnswerHub tarafından sunulan başka bir kullanıcı hesabı oluşturma aracını veya API 'YI kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde AnswerHub kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız AnswerHub için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirmek için öğreticiler](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

