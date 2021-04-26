---
title: 'Öğretici: Learnon ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Öğrennon arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 297395fbd0a7fd3fc8959eed5c0aeabd7b8e5751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458714"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Öğretici: Learnon ile tümleştirme Azure Active Directory

Bu öğreticide, Öğrennon 'u Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile Learnon tümleştirme, aşağıdaki avantajları sağlar:

* Azure AD 'de Öğrennon 'a erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Öğrenirme (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Öğreniron ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Çoklu oturum açma etkin aboneliğini öğrenin

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.


* **IDP** tarafından başlatılan SSO 'yu öğrenir

* **Tam zamanında** Kullanıcı sağlamayı destekliyor


## <a name="adding-learnupon-from-the-gallery"></a>Galeriden Öğreniri ekleme

Öğrennon 'un Azure AD 'de tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize öğrenimi eklemeniz gerekir.

**Galeriden Öğreniron eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **öğrennon** yazın, sonuç panelinden **learnon** ' u seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuçlar listesinde Öğrenirme](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre learnon Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Öğrennon içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Öğrennon ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Learnon çoklu oturum açma 'Yı yapılandırın](#configure-learnupon-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Öğrennon 'da Britta Simon 'a sahip olmak için **[test kullanıcısı oluşturun](#create-learnupon-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Öğrennon ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), uygulama tümleştirmesi **öğrenimi** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Etki alanı ve URL 'Lerde çoklu oturum açma bilgileri Öğrenirme](common/idp-reply.png)

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Değeri almak için [istemci desteği ekibinin Öğreniri](https://www.learnupon.com/features/support/) ile iletişime geçin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **parmak izini** bulun; bu, öğrennon SAML ayarlarınıza eklenecektir.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **Öğreniron 'ı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-learnupon-single-sign-on"></a>Tek Sign-On Learnon yapılandırma

1. Başka bir tarayıcı örneği açın ve bir yönetici hesabıyla Öğreniron durumunda oturum açın.

1. **Ayarlar** sekmesine tıklayın.

    ![Ekran görüntüsü ayarlar sekmesini gösterir.](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. SAML ayarlarını yapılandırmak için **Tekli oturum açma-SAML**' ye ve ardından **Genel ayarlar** ' a tıklayın.
   
    ![Ekran görüntüsü, genel ayarlar seçiliyken seçili çoklu oturum açma SAML 'Yi gösterir.](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. **Genel ayarlar** bölümünde aşağıdaki adımları uygulayın:
   
    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz genel ayarlar bölümünü gösterir.](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. **Etkin**'i seçin.

    b. **Sürüm** olarak **2,0**' i seçin.

    c. Koşulları **Hayır** olarak **Atla** ' yı seçin.

    d. **SAML belirteci Post param Name** metin kutusuna, doğrulanacak ve kimlik doğrulaması yapılacak SAML onaylama, örneğin **SAMLResponse** gıbı, yukarıda belirtilen saml tüketicisi URL 'sine istek Post parametresinin adını yazın.

    e. **Ad tanımlayıcı biçimi** metin kutusuna, SAML onaylayıcın Kullanıcı tanımlayıcısı (e-posta adresi) nerede olduğunu belirten değeri yazın `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. **Sağlayıcı konumunu tanımla** metin kutusuna, Azure Portal oturum açma ekranından karşıya yüklenen simgeye tıkladıklarında kullanıcıların nereye gönderileceğini belirten değeri yazın.
  
    örneğin: **Oturumu Kapat URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    h. **Parmak Izlerini Yönet**' e tıklayın ve ardından indirilen sertifikanızın parmak izi ' ni karşıya yükleyin.

1. **Kullanıcı ayarları**' na tıklayın ve ardından aşağıdaki adımları gerçekleştirin:

     ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı ayarları bölümünü gösterir.](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. **Ad tanımlayıcı biçimi** metin kutusuna, SAML onaylayıcın Kullanıcı adı ' nda nerede olduğunu belirten değeri yazın; örneğin: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. **Son ad tanımlayıcı biçimi** metin kutusuna, SAML onaylayıcın Kullanıcı soyadı ' nda nerede olduğunu bildiren değeri yazın. Örneğin: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Öğrennon 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'ı etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **öğrennsonra**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Learnon**' u seçin.

    ![Uygulamalar listesinde Learnon bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-learnupon-test-user"></a>Test kullanıcısı oluşturma

Bu bölümde, Learnon 'da Britta Simon adlı bir Kullanıcı oluşturulmuştur. Öğrenirnon, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekliyor. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Öğrennon 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur. Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [Destek ekibine](https://www.learnupon.com/features/support/)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Öğrennon kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Öğrennon 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)