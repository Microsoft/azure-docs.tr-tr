---
title: 'Öğretici: ınitelemek LMS ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve ınitelemek LMS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bdd68a4f0dafd3b336bf5a3fb34399d725c9ff5c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626121"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Öğretici: ınitelemek LMS ile Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile ınitelikli LMS 'yi tümleştirmeyi öğreneceksiniz.
Gerekli LMS 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, ıniteleme LMS 'ye erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla LMS 'yi (çoklu oturum açma) bulmak için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini ınitelemek LMS ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* ınitelendir LMS çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* ıniteleme LMS **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* ınitelemek LMS **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-iqualify-lms-from-the-gallery"></a>Galeriden ınitelemek LMS ekleme

Azure AD 'ye ıniteleme LMS 'nin tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize ıniteleme LMS eklemeniz gerekir.

**Galeriden ınitelemek LMS eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **gereklı LMS** yazın, sonuç panelinden **initelendir** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![sonuçlar listesinde LMS 'yi nitele](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına dayanarak ıNITELEME LMS ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin, LMS 'ye uygun bir şekilde oluşturulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için gerekli LMS 'yi kullanarak, aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[LMS 'Yi tek tek oturum açma yapılandırması yapılandırma](#configure-iqualify-lms-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan ınitelemek LMS 'de Britta Simon 'ın bir karşılığı olacak şekilde, **[LMS 'ye uygun LMS test kullanıcısı oluşturun](#create-iqualify-lms-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı ınitelemek LMS ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), LMS Uygulama tümleştirmesini **nitele** sayfasında, **Çoklu oturum açma**' yı seçin.

   ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

   ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

   ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)
   
   1. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

      * Üretim ortamı: `https://<yourorg>.iqualify.com/`
      * Test ortamı: `https://<yourorg>.iqualify.io`

   2. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

      * Üretim ortamı: `https://<yourorg>.iqualify.com/auth/saml2/callback` 
      * Test ortamı: `https://<yourorg>.iqualify.io/auth/saml2/callback`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

   ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

   **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

      * Üretim ortamı: `https://<yourorg>.iqualify.com/login`
      * Test ortamı: `https://<yourorg>.iqualify.io/login`

   > [!NOTE]
   > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [LMS istemci desteği ekibine](https://www.iqualify.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. Initelendirme LMS uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

7. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği|
    | --- | --- |
    | e-posta | User. UserPrincipalName |
    | first_name | Kullanıcı. |
    | last_name | User. soyadı |
    | person_id | "özniteliklerinizi" |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![Ekran görüntüsü, yeni talep ekleme seçeneğiyle Kullanıcı taleplerini gösterir.](common/new-save-attribute.png)

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı taleplerini Yönet iletişim kutusunu gösterir.](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

    > [!Note]
    > **Person_id** özniteliği **isteğe bağlıdır**

8. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

9. **NITELIKLI LMS 'Yi ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-iqualify-lms-single-sign-on"></a>Initeleme LMS tek Sign-On yapılandırma

1. Yeni bir tarayıcı penceresi açın ve ardından, bir yönetici olarak ınitelendirme ortamınızda oturum açın.

1. Oturum açtıktan sonra sağ üstteki Avatar ' e tıklayın ve ardından **Hesap ayarları** ' na tıklayın.

    ![Hesap ayarları](./media/iqualify-tutorial/setting1.png)

1. Hesap ayarları alanında, sol taraftaki şerit menüsüne tıklayın ve **tümleştirmelere** tıklayın

    ![TÜMLEŞTIRMELER](./media/iqualify-tutorial/setting2.png)

1. TÜMLEŞTIRMELER altında **SAML** simgesine tıklayın.

    ![SAML simgesi](./media/iqualify-tutorial/setting3.png)

1. **SAML kimlik doğrulama ayarları** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![SAML kimlik doğrulama ayarları](./media/iqualify-tutorial/setting4.png)

    a. **SAML çoklu oturum açma hizmeti URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinden kopyalanmış **oturum açma URL 'si** değerini yapıştırın.

    b. **SAML oturum kapatma URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinden kopyalanmış **oturum kapatma URL 'si** değerini yapıştırın.

    c. İndirilen sertifika dosyasını Not defteri 'nde açın, içeriği kopyalayın ve sonra **genel sertifika** kutusuna yapıştırın.

    d. **Oturum aç DÜĞMESI etiketi** oturum aç sayfasında görüntülenecek düğmenin adını girin.

    e. **Kaydet**' e tıklayın.

    f. **Güncelleştir**' e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, ıuse LMS 'ye erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı SEÇIN, sonra da **LMS**'yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **LMS**'yi seçin.

    ![Uygulamalar listesinde ıniteleme LMS bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-iqualify-lms-test-user"></a>Initelemek LMS test kullanıcısı oluştur

Bu bölümde, ınitelemek LMS 'de Britta Simon adlı bir Kullanıcı oluşturulur. ınitelemek LMS, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı ınitelemek LMS 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Initeleme LMS kutucuğuna tıkladığınızda, gerekli LMS uygulamanızın oturum açma sayfasını almalısınız. 

   ![oturum açma sayfası](./media/iqualify-tutorial/login.png) 

**Azure AD düğmesini kullanarak oturum aç** ' a tıklayın ve ıconnitelendirme uygulamanıza otomatik olarak oturum açmanız gerekir.

Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md). 

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)