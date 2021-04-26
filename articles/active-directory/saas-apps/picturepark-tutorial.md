---
title: 'Öğretici: Picturepark ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Picturepark arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 8a00cf11edfea2e732a18a392d465525b38ea45f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520872"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Öğretici: Picturepark ile Azure Active Directory tümleştirme

Bu öğreticide, Picturepark 'nın Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Picturepark 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Picturepark alanına erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Picturepark (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Picturepark ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Picturepark çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Picturepark, **SP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-picturepark-from-the-gallery"></a>Galeriden Picturepark ekleme

Picturepark 'nın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Picturepark ' i eklemeniz gerekir.

**Galeriden Picturepark eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Picturepark** yazın, sonuç panelinden **Picturepark** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde Picturepark](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre Picturepark ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Picturepark alanındaki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Picturepark ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurmanız gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Picturepark çoklu oturum açmayı yapılandırın](#configure-picturepark-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Picturepark ortamında Britta Simon 'un bir karşılığı olacak şekilde **[Picturepark testi kullanıcısı oluşturun](#create-picturepark-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Picturepark ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Picturepark** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Picturepark alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.picturepark.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Picturepark istemci destek ekibine](https://picturepark.com/company/picturepark-customer-support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **Picturepark ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın. **Oturum açma URL 'si** için değeri aşağıdaki Düzenle kullanın:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ , Azure AD aboneliğinin kiracı kimliğidir.

    ![Yapılandırma URL 'Lerini Kopyala](./media/picturepark-tutorial/configurls.png)

    a. Azure AD tanımlayıcısı

    b. Oturum kapatma URL 'SI

### <a name="configure-picturepark-single-sign-on"></a>Picturepark tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Picturepark şirketi sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda **Yönetimsel Araçlar**' a ve ardından **Yönetim Konsolu**' na tıklayın.
   
    ![Yönetim Konsolu](./media/picturepark-tutorial/ic795062.png "Yönetim Konsolu")

3. **Kimlik doğrulaması**' na ve ardından **kimlik sağlayıcıları**' na tıklayın.
   
    ![Kimlik Doğrulaması](./media/picturepark-tutorial/ic795063.png "Kimlik Doğrulaması")

4. **Kimlik sağlayıcısı yapılandırma** bölümünde aşağıdaki adımları uygulayın:
   
    ![Kimlik sağlayıcısı yapılandırması](./media/picturepark-tutorial/ic795064.png "Kimlik sağlayıcısı yapılandırması")
   
    a. **Ekle**'ye tıklayın.
  
    b. Yapılandırmanız için bir ad yazın.
   
    c. **Varsayılan olarak ayarla**' yı seçin.
   
    d. **Veren URI** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
   
    e. **Güvenilen veren** parmak izi metin kutusunda, **SAML imzalama sertifikası** bölümünden kopyaladığınız **parmak izine** ait değeri yapıştırın. 

5. **Joindefaultusersgroup** öğesine tıklayın.

6. **Talep** metin kutusunda **emapostaadresi** özniteliğini ayarlamak Için, yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ve **Kaydet**' e tıklayın.

      ![Yapılandırma](./media/picturepark-tutorial/ic795065.png "Yapılandırma")

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

Bu bölümde, Picturepark alanına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Picturepark**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Picturepark**' ı seçin.

    ![Uygulamalar listesindeki Picturepark bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-picturepark-test-user"></a>Picturepark testi kullanıcısı oluşturma

Azure AD kullanıcılarının, Picturepark ortamında oturum açmasını sağlamak için, bu onların Picturepark halinde sağlanması gerekir. Picturepark durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Picturepark** kiracınızda oturum açın.

1. Üstteki araç çubuğunda **Yönetimsel Araçlar**' a ve ardından **Kullanıcılar**' a tıklayın.
   
    ![Kullanıcılar](./media/picturepark-tutorial/ic795067.png "Kullanıcılar")

1. **Kullanıcılara genel bakış** sekmesinde **Yeni**' ye tıklayın.
   
    ![Kullanıcı yönetimi](./media/picturepark-tutorial/ic795068.png "Kullanıcı yönetimi")

1. **Kullanıcı oluştur** iletişim kutusunda, sağlamak istediğiniz geçerli bir Azure Active Directory Kullanıcı için aşağıdaki adımları gerçekleştirin:
   
    ![Create User](./media/picturepark-tutorial/ic795069.png "Kullanıcı Oluştur")
   
    a. **E-posta adresi** metin kutusuna kullanıcının **e-posta adresini** yazın `BrittaSimon@contoso.com` .  
   
    b. **Parola** ve **Parolayı Onayla** metin kutularına brittasıon **parolasını** yazın. 
   
    c. **Ilk ad** metin kutusuna **, kullanıcının** **ilk adını** yazın. 
   
    d. **Soyadı** metin kutusuna kullanıcı **Simon**' un **soyadını** yazın.
   
    e. **Şirket** metin kutusuna kullanıcının **şirket adını** yazın. 
   
    f. **Ülke** metin kutusunda kullanıcının **ülkesini/bölgesini** seçin.
  
    örneğin: **Zip** metin kutusuna şehrin **posta kodunu** yazın.
   
    h. **Şehir** metin kutusuna kullanıcının **şehir adını** yazın.

    i. Bir **dil** seçin.
   
    j. **Oluştur**’a tıklayın.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için, Picturepark tarafından sunulan diğer bir Picturepark Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.
>

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Picturepark kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız Picturepark 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)