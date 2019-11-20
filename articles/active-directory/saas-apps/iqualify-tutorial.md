---
title: 'Öğretici: Initelemek LMS ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ınitelemek LMS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944984"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Öğretici: Initelemek LMS ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile ınitelikli LMS 'yi tümleştirmeyi öğreneceksiniz.
Gerekli LMS 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, ıniteleme LMS 'ye erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla LMS 'yi (çoklu oturum açma) bulmak için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınız bir merkezi konumda - Azure portalında yönetebilir.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
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

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **gereklı LMS**yazın, sonuç panelinden **initelendir** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![sonuçlar listesinde LMS 'yi nitele](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına dayanarak ıNITELEME LMS ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin, LMS 'ye uygun bir şekilde oluşturulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için gerekli LMS 'yi kullanarak, aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırmayı](#configure-azure-ad-single-sign-on)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[LMS 'Yi tek tek oturum açma yapılandırma](#configure-iqualify-lms-single-sign-on)** .
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
5. Kullanıcının Azure AD gösterimine bağlı olan ınitelemek LMS 'de Britta Simon 'ın bir karşılığı olacak şekilde, **[LMS 'ye uygun LMS test kullanıcısı oluşturun](#create-iqualify-lms-test-user)** .
6. **[Çoklu oturum açmayı test](#test-single-sign-on)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı ınitelemek LMS ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), LMS Uygulama tümleştirmesini **nitele** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![LMS etki alanı ve URL 'Ler çoklu oturum açma bilgilerini nitele](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    | |
    |--|--|
    | Üretim ortamı:`https://<yourorg>.iqualify.com/`|
    | Test ortamı:`https://<yourorg>.iqualify.io`|

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:
    | |
    |--|--|
    | Üretim ortamı:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Test ortamı:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![LMS etki alanı ve URL 'Ler çoklu oturum açma bilgilerini nitele](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    | |
    |--|--|
    | Üretim ortamı:`https://<yourorg>.iqualify.com/login` |
    | Test ortamı:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [LMS istemci desteği ekibine](https://www.iqualify.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. Initelendirme LMS uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

7. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin:

    | Ad | Kaynak özniteliği|
    | --- | --- |
    | email | User. UserPrincipalName |
    | first_name | Kullanıcı. |
    | last_name | User. soyadı |
    | person_id | "özniteliklerinizi" |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. Tıklayın **Tamam**

    g. **Kaydet**’e tıklayın.

    > [!Note]
    > **Person_id** özniteliği **isteğe bağlıdır**

8. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

9. **NITELIKLI LMS 'Yi ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL'si:

    b. Azure AD Tanımlayıcısı

    c. Oturum Kapatma URL'si

### <a name="configure-iqualify-lms-single-sign-on"></a>Initelendir LMS çoklu oturum açmayı yapılandırma

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

    e. **KAYDET**'e tıklayın.

    f. **Güncelleştir**' e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Britta Simon adlı Azure portalında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üstünde.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özellikleri, aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, ıuse LMS 'ye erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı SEÇIN, sonra da **LMS**'yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **LMS**'yi seçin.

    ![Uygulamalar listesinde ıniteleme LMS bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **Britta Simon** 'a tıklayın kullanıcı listesinde **seçin** ekranın alt kısmındaki düğmesi.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-iqualify-lms-test-user"></a>Initelemek LMS test kullanıcısı oluştur

Bu bölümde, ınitelemek LMS 'de Britta Simon adlı bir Kullanıcı oluşturulur. ınitelemek LMS, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı ınitelemek LMS 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Initeleme LMS kutucuğuna tıkladığınızda, gerekli LMS uygulamanızın oturum açma sayfasını almalısınız. 

   ![oturum açma sayfası](./media/iqualify-tutorial/login.png) 

**Azure AD düğmesini kullanarak oturum aç** ' a tıklayın ve ıconnitelendirme uygulamanıza otomatik olarak oturum açmanız gerekir.

Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)