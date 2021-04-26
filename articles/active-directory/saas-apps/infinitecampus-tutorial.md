---
title: 'Öğretici: sonsuz kampüs ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve sonsuz kampüs arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460251"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Öğretici: sonsuz kampüs ile tümleştirme Azure Active Directory

Bu öğreticide, sınırsız Kampıyı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Sonsuz kampüs 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Sınırsız kampüs erişimine erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla sonsuz kampüs (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini sonsuz kampüs ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Sonsuz kampüs çoklu oturum açma etkin abonelik
* En azından, yapılandırmayı gerçekleştirmek için bir Azure Active Directory yöneticisi olmanız ve "öğrenci bilgi sistemi (SIS)" için bir kampüs ürün güvenlik rolüne sahip olmanız gerekir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Sonsuz kampüs, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-infinite-campus-from-the-gallery"></a>Galeriden sonsuz kampüs ekleme

Sınırsız kampüs 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize sonsuz kampüs eklemeniz gerekir.

**Galeriden sonsuz kampüs eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **sonsuz kampüs** yazın, sonuç panelinden **sonsuz kampüs** seçin, sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde sonsuz kampüs](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre sonsuz kampüs ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve sonsuz kampüs içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı sonsuz kampüs ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Sonsuz kampüs çoklu oturum açmayı yapılandırma](#configure-infinite-campus-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı sonsuz Kampınon ile bir Britta Simon 'ın bir karşılığı olacak şekilde **[sonsuz kampüs test kullanıcısı oluşturun](#create-infinite-campus-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı sonsuz kampüs ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **sonsuz kampüs** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. Temel SAML yapılandırması bölümünde aşağıdaki adımları gerçekleştirin (etki alanının barındırma modeliyle değişebileceğine, ancak **tam etkı alanı** değerinin sonsuz kampüs yüklemenizin ile eşleşmesi gerekir):

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Sonsuz kampüs etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Sonsuz kampüs tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, sınırsız Kampüye güvenlik yöneticisi olarak oturum açın.

2. Menünün sol tarafında **sistem yönetimi**' ne tıklayın.

    ![Yönetici](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. **Kullanıcı güvenliği**  >  **SAML yönetimi**  >  **SSO hizmeti sağlayıcısı yapılandırması**' na gidin.

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. **SSO hizmeti sağlayıcısı yapılandırma** sayfasında, aşağıdaki adımları uygulayın:

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. **SAML çoklu oturum açmayı etkinleştir '** i seçin.

    b. **Isteğe bağlı öznitelik adını** **ad** içerecek şekilde düzenleyin

    c. **Kimlik sağlayıcısı (ıDP) sunucu verilerini almak için bir seçenek belirleyin** bölümünde, **meta veri URL 'si**' ni seçin, kutuya Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'Si** değerini yapıştırın ve ardından **Eşitle**' ye tıklayın.

    d. Değerleri **Eşitle** ' ye tıkladıktan sonra, **SSO hizmeti sağlayıcısı yapılandırma** sayfasında otomatik doldurulmuş olarak al ' ı tıklatın. Bu değerler, yukarıdaki 4. adımda görülen değerlerle eşleşecek şekilde doğrulanabilir.

    e. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com.

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

> [!NOTE]
> Tüm Azure kullanıcılarınızın sınırsız kampüs için çoklu oturum açma erişimine sahip olmasını ve erişimi denetlemek için sınırsız kampüs iç izinleri sistemine sahip olmasını istiyorsanız, uygulamanın **Kullanıcı Ataması gereken** özelliğini Hayır olarak ayarlayabilir ve aşağıdaki adımları atlayabilirsiniz.

Bu bölümde, sonsuz kampüs 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra **sonsuz kampüs**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **sonsuz kampüs**' ı seçin.

    ![Uygulamalar listesindeki sonsuz kampüs bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-infinite-campus-test-user"></a>Sonsuz kampüs test kullanıcısı oluştur

Sonsuz kampüs, demografik ortalanmış bir mimariye sahiptir. Sınırsız kampüs platformunda kullanıcıları eklemek için lütfen [sonsuz kampüs destek ekibine](mailto:sales@infinitecampus.com) başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde sonsuz kampüs kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız sonsuz kampüs için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)