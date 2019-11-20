---
title: 'Öğretici: oto görev çalışma alanıyla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve oto çalışma alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a68fd74449ea34438348cbc4c9d09c06d46d71
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157801"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Öğretici: oto görev çalışma alanıyla Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile, oto görev çalışma alanını tümleştirmeyi öğreneceksiniz.
Azure AD ile diğer görev çalışma alanını tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, oto görev çalışma alanına erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik görev çalışma alanına (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, oto görev çalışma alanıyla birlikte yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Oto görevi çalışma alanı çoklu oturum açma etkin aboneliği
* Bir oto görev çalışma alanına çoklu oturum açma etkin abonelik
* Çalışma alanında yönetici veya süper yönetici olmanız gerekir.
* Azure AD 'de bir yönetici hesabınız olmalıdır.
* Bu özelliği kullanacak olan kullanıcıların çalışma alanı ve Azure AD içinde hesaplara sahip olması ve her ikisi için e-posta adreslerinin eşleşmesi gerekir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Oto görevi çalışma alanı **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-autotask-workplace-from-the-gallery"></a>Galeriden oto görev çalışma alanı ekleme

Oto görev çalışma alanının Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize yeniden görev çalışma alanı eklemeniz gerekir.

**Galeriden, görev çalışma alanı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, tekrar **görev çalışma alanı**yazın, sonuç panelinden yeniden **görev çalışma alanı** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde, oto görevi çalışma alanı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre, oto görev çalışma alanıyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve diğer görev çalışma alanındaki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için, aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. , Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[oto görev çalışma alanı çoklu oturum açma 'Yı yapılandırın](#configure-autotask-workplace-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimi ile bağlantılı olan oto görev çalışma alanında Britta Simon 'a sahip olmak için, **[oto görev çalışma alanı test kullanıcısı oluşturun](#create-autotask-workplace-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, oto görev çalışma alanıyla yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **oto görevi çalışma alanı** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Oto görevi çalışma alanı etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Oto görevi çalışma alanı etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, [oto görev çalışma alanı istemci desteği ekibine](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. Alt **görev çalışma alanını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-autotask-workplace-single-sign-on"></a>Oto görev çalışma alanını çoklu oturum açma yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici kimlik bilgilerini kullanarak çalışma alanı çevrimiçi olarak oturum açın.

    > [!Note]
    > IDP 'yi yapılandırırken bir alt etki alanının belirtilmesi gerekir. Doğru alt etki alanını onaylamak için çalışma alanına çevrimiçi oturum açın. Oturum açtıktan sonra URL 'deki alt etki alanına dikkat edin. Alt etki alanı, "https://" ve ". awp.autotask.net/" arasındaki kısmıdır ve ABD, AB, CA veya au olmalıdır.

2. **Yapılandırma** > **Çoklu oturum açma** ' ya gidin ve aşağıdaki adımları gerçekleştirin:

    ![Oto görevi çoklu oturum açma yapılandırması](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. **XML meta veri dosyası** seçeneğini belirleyin ve ardından Azure Portal Indirilen **Federasyon meta veri XML** dosyasını karşıya yükleyin.

    b. **SSO 'Yu etkinleştir**' e tıklayın.

    ![Oto görevi çoklu oturum açma onaylama yapılandırması](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. **Bu bilgilerin doğru olduğunu ve bu IDP 'ye güveneceğim** onay kutusunu seçin.

    d. **Onayla**' ya tıklayın.

> [!Note]
> Yeniden görev çalışma alanını yapılandırmaya yönelik yardıma ihtiyacınız varsa, lütfen çalışma alanı hesabınızla ilgili yardım almak için [Bu sayfaya](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) bakın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, oto görev çalışma alanına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından yeniden **görev çalışma alanı**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **oto görevi çalışma alanı**' nı seçin.

    ![Uygulamalar listesinde, oto görevi çalışma alanı bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-autotask-workplace-test-user"></a>Oto görevi çalışma alanı test kullanıcısı oluştur

Bu bölümde, oto görevi çalışma alanında Britta Simon adlı bir Kullanıcı oluşturacaksınız. Lütfen, kullanıcıları yeniden görev çalışma alanı platformuna eklemek için, [oto görev çalışma alanı destek ekibi](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) ile çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde otomatik görev çalışma alanı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız otomatik görev çalışma alanında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
