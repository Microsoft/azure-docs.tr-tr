---
title: 'Öğretici: TOPdesk-public ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TOPdesk-public arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950416"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Öğretici: Topmasa-genel ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile TOPdesk-public tümleştirme hakkında bilgi edineceksiniz.
Azure AD ile TOPdesk-Public ' i tümleştirme aşağıdaki avantajları sağlar:

* Azure AD 'de Topmasa-herkese erişimi olan ' i denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak Topmasa-genel (çoklu oturum açma) için oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Prerequisites

Azure AD tümleştirmesini TOPdesk-public ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Topmasa-genel çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Topmasa-genel **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-topdesk---public-from-the-gallery"></a>Galeriden TOPdesk-public ekleme

Topmasa-genel ' i Azure AD ile tümleştirmeyi yapılandırmak için, Galeriden Topmasa-Public ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden TOPdesk-public eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Topmasa-public**yazın, sonuç panelinden **Topmasa-genel** ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Topmasa-genel](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre TopDesk-public ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile Topmasa-genel arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, TOPdesk-public ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Topmasa-genel çoklu oturum açmayı yapılandırın](#configure-topdesk---public-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Topmasa **[-genel test kullanıcısı oluşturma](#create-topdesk---public-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Topmasa-Genel bölümünde Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı TOPdesk-public ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Topmasa-genel** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4.  **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız**varsa, aşağıdaki adımları uygulayın:

    >[!NOTE]
    >**Hizmet sağlayıcı meta veri dosyasını** , Öğreticinin ilerleyen kısımlarında açıklanan, **Topmasa genel çoklu oturum açma** bölümünde bulabilirsiniz.

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.
    
    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    ,. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur.

    ![Topmasa-genel etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    TID. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.topdesk.net`

    a. **Tanımlayıcı URL** metin kutusunda, TopDesk yapılandırmasından alabileceğiniz TopDesk meta veri URL 'sini girin. Şu stili kullanmalıdır: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    vadeli. **Yanıt URL** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmazsa, bunları el ile girmeniz gerekir. Tanımlayıcı için yukarıda belirtilen kalıbı izleyin ve öğreticide daha sonra açıklanan **Topmasa genel çoklu oturum açma** bölümünden yanıt URL 'si değerini alın. **Oturum açma URL 'si** değeri gerçek değil, bu değeri gerçek oturum açma URL 'siyle güncelleştirmeniz gerekir. Değeri almak için [Topmasa-genel istemci desteği ekibine](https://help.topdesk.com/saas/enterprise/user/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **TOPdesk-public ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    ,. Oturum kapatma URL 'SI

### <a name="configure-topdesk---public-single-sign-on"></a>TOPdesk-genel çoklu oturum açmayı yapılandırma

1. **Topoloji-ortak** şirket sitenizde yönetici olarak oturum açın.

2. **TopDesk** menüsünde **Ayarlar**' a tıklayın.
   
    ![Ayarlar](./media/topdesk-public-tutorial/ic790598.png "ayarları")

3. **Oturum açma ayarları**' na tıklayın.
   
    ![Oturum açma ayarları](./media/topdesk-public-tutorial/ic790599.png "oturum açma ayarları")

4. **Oturum açma ayarları** menüsünü genişletin ve ardından **genel**' e tıklayın.
   
    ![Genel](./media/topdesk-public-tutorial/ic790600.png "genel")

5. **SAML oturum açma** Yapılandırması bölümünün **genel** bölümünde aşağıdaki adımları uygulayın:
   
    ![Teknik ayarlar](./media/topdesk-public-tutorial/ic790601.png "Teknik ayarları")
   
    a. Ortak meta veri dosyasını indirmek için **İndir** ' e tıklayın ve ardından bilgisayarınıza yerel olarak kaydedin.
   
    b. İndirilen meta veri dosyasını açın ve ardından **Assertionconsumerservice** düğümünü bulun.

    ![Assertionconsumerservice](./media/topdesk-public-tutorial/ic790619.png "assertionconsumerservice")
   
    ,. **Assertionconsumerservice** değerini kopyalayın, bu DEĞERI **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.      
   
6. Bir sertifika dosyası oluşturmak için aşağıdaki adımları gerçekleştirin:
    
    ![Sertifika](./media/topdesk-public-tutorial/ic790606.png "sertifikası")
    
    a. İndirilen meta veri dosyasını Azure portal açın.
    
    b. **Xsi: Type türünde** **Besleyicisi: Applicationservicetype**olan **RoleDescriptor** düğümünü genişletin.
    
    ,. **X509Certificate** düğümünün değerini kopyalayın.
    
    TID. Kopyalanmış **X509Certificate** değerini bilgisayarınıza yerel olarak bir dosyaya kaydedin.

7. **Genel** bölümünde, **Ekle**' ye tıklayın.
    
    ![SAML oturum açma](./media/topdesk-public-tutorial/ic790625.png "SAML oturum açma")

8. **SAML yapılandırma Yardımcısı** iletişim kutusunda, aşağıdaki adımları uygulayın:
    
    ![SAML yapılandırma Yardımcısı](./media/topdesk-public-tutorial/ic790608.png "SAML yapılandırma Yardımcısı")
    
    a. İndirilen meta veri dosyanızı Azure portal karşıya yüklemek için, **Federasyon meta verileri**altında, **Araştır**' a tıklayın.

    b. Sertifika dosyanızı karşıya yüklemek için, **sertifika (RSA)** altında, **Araştır**' a tıklayın.

    ,. TOPdesk destek ekibinden aldığınız logo dosyasını karşıya yüklemek için, **logo simgesi**altında, **Araştır**' a tıklayın.

    TID. **Kullanıcı adı öznitelik** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` yazın.

    a. **Görünen ad** metin kutusuna yapılandırmanız için bir ad yazın.

    vadeli. **Kaydet**'e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanına brittasimon@yourcompanydomain.extension yazın. Örneğin, BrittaSimon@contoso.com

    ,. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    TID. **Oluştur**'u tıklatın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Topmasa-herkese erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **topmasa-genel**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Topmasa-genel**' i seçin.

    ![Uygulamalar listesindeki Topmasa-genel bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-topdesk---public-test-user"></a>Topmasa-ortak test kullanıcısı oluşturma

Azure AD kullanıcılarının Topmasa-genel ' te oturum açmasını sağlamak için, bu kullanıcıların Topmasa-genel ' e sağlanması gerekir. Topmasa-genel durumunda, sağlama el ile gerçekleştirilen bir görevdir.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:

1. **Topoloji-ortak** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **TopDesk \> yeni \> destek dosyaları \> kişi**' ye tıklayın.
   
    ![Kişi](./media/topdesk-public-tutorial/ic790628.png "elemanı")

3. Yeni kişi iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Yeni kişi](./media/topdesk-public-tutorial/ic790629.png "Yeni kişi")
   
    a. Genel sekmesine tıklayın.

    b. **Soyadı** metin kutusunda, Simon gibi kullanıcının soyadı yazın
 
    ,. Hesap için bir **site** seçin.
 
    TID. **Kaydet**'e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, TOPdesk-public tarafından sunulan diğer tüm Topmasa-genel kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açmayı sına 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Topmasa-genel kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Topmasa-genel ' e otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
