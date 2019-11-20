---
title: 'Öğretici: MoveIt aktarımı ile Azure Active Directory tümleştirme-Azure AD tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve MoveIt aktarma-Azure AD tümleştirmesi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161325"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Öğretici: MoveIt aktarımı ile Azure Active Directory tümleştirme-Azure AD tümleştirmesi

Bu öğreticide, MOVEit aktarım-Azure AD tümleştirmesini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
MoveIt aktarımını tümleştirme-Azure AD tümleştirmesi, Azure AD ile aşağıdaki avantajları sağlar:

* Azure AD 'de MoveIt aktarma-Azure AD tümleştirmesi erişimine sahip olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla MoveIt aktarma-Azure AD tümleştirmesi (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini MoveIt transfer-Azure AD tümleştirmesi ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* MoveIt aktarımı-Azure AD tümleştirmesi çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* MoveIt aktarımı-Azure AD tümleştirmesi, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MoveIt aktarımı ekleme-Galeriden Azure AD tümleştirmesi

MOVEit transfer-Azure AD tümleştirmesini Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden MoveIt transfer-Azure AD tümleştirmesini, Galeri 'den yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden MoveIt aktarımı-Azure AD tümleştirmesi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **MoveIt transfer-Azure AD tümleştirmesi**yazın, **moveit transfer-sonuç panelinden Azure AD tümleştirmesi** ' ni seçin ve ardından uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![MoveIt aktarımı-Azure AD tümleştirmesi sonuçlar listesinde](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, MoveIt aktarmalı adlı bir test kullanıcısına göre Azure AD tümleştirmesiyle yapılandırıp test **edersiniz.**
Çoklu oturum açma için, Azure AD kullanıcısı ve MoveIt aktarımı 'ndaki ilgili Kullanıcı arasındaki bağlantı ilişkisi-Azure AD tümleştirmesi 'nin kurulması gerekir.

Azure AD çoklu oturum açma 'yı MoveIt aktarma-Azure AD tümleştirmesi ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[MoveIt aktarımını Yapılandırma-Azure AD tümleştirmesi çoklu oturum açma](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[MoveIt transfer-Azure AD tümleştirme test kullanıcısına](#create-moveit-transfer---azure-ad-integration-test-user)** , MoveIt aktarma-Azure AD tümleştirmesinde kullanıcının Azure AD gösterimine bağlı bir Britta Simon 'un bir karşılığı olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı MoveIt aktarma-Azure AD tümleştirmesi ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **moveit transfer-Azure AD tümleştirme** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız**varsa, aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değeri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur:

    ![MoveIt aktarımı-Azure AD tümleştirme etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://contoso.com`

    > [!NOTE]
    > **Oturum açma URL 'si** değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Bu değeri almak için [MoveIt transfer-Azure AD tümleştirme istemci destek](https://community.ipswitch.com/s/support) ekibine başvurun. Service Provider meta verileri **dosyasını** , daha sonra öğreticideki **MoveIt aktarımını Yapılandırma-Azure AD tümleştirmesi çoklu oturum açma** bölümünde açıklanan **hizmet sağlayıcı meta veri URL** 'sinden indirebilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **MoveIt aktarımını ayarla-Azure AD tümleştirmesi** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MoveIt aktarımını Yapılandırma-Azure AD tümleştirmesi çoklu oturum açma

1. MoveIt aktarım kiracınızda yönetici olarak oturum açın.

2. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.

    ![Uygulama tarafındaki ayarlar bölümü](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. **Güvenlik ilkeleri-> Kullanıcı kimlik doğrulaması**altında bulunan **tek** oturum açma bağlantısı ' na tıklayın.

    ![Uygulama tarafında güvenlik Ilkeleri](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Meta veri belgesini indirmek için meta veri URL 'SI bağlantısına tıklayın.

    ![Hizmet sağlayıcı meta veri URL 'SI](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * **Temel SAML yapılandırması** bölümünde **EntityId** 'nin **tanımlayıcıyla** eşleştiğini doğrulayın.
   * **Assertionconsumerservice** location URL 'Si, **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** 'siyle eşleşiyor.
    
     ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Yeni bir federal kimlik sağlayıcısı eklemek için **kimlik sağlayıcı ekle** düğmesine tıklayın.

    ![Kimlik sağlayıcısı ekle](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Azure portal ' den indirdiğiniz meta veri dosyasını seçmek için **araştır...** ' a tıklayın ve ardından indirilen dosyayı karşıya yüklemek Için **kimlik sağlayıcısı ekle** ' ye tıklayın.

    ![SAML kimlik sağlayıcısı](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. **Federal kimlik sağlayıcısı ayarlarını Düzenle...** sayfasında, **etkin** olarak "**Evet**" seçeneğini belirleyin ve **Kaydet**' e tıklayın.

    ![Federal Kimlik sağlayıcısı ayarları](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. **Federal kimlik sağlayıcısı Kullanıcı ayarlarını Düzenle** sayfasında, aşağıdaki işlemleri gerçekleştirin:
    
    ![Federal Kimlik sağlayıcısı ayarlarını Düzenle](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. **SAML NameID** öğesini **oturum açma adı**olarak seçin.
    
    b. **Tam ad** olarak **diğer** ' i seçin ve **öznitelik adı** metin kutusuna değeri koyun: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. **E-posta** olarak **diğer** ' i seçin ve **öznitelik adı** metin kutusuna şu değeri koyun: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. **Oturum açma sırasında hesabı otomatik oluştur**olarak **Evet** ' i seçin.
    
    e. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, MoveIt aktarma-Azure AD tümleştirmesi ' ne erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **MOVEıT aktarımı-Azure AD tümleştirmesi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **moveit aktarımı-Azure AD tümleştirmesi**' ni seçin.

    ![Uygulama listesindeki MoveIt aktarımı-Azure AD tümleştirme bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MoveIt aktarımı oluşturma-Azure AD tümleştirme test kullanıcısı

Bu bölümün amacı, MoveIt aktarma-Azure AD tümleştirmesi ' nde Britta Simon adlı bir Kullanıcı oluşturmaktır. MoveIt aktarımı-Azure AD tümleştirmesi, etkinleştirdiğiniz tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Taşınabilir BT aktarımına erişme denemesi sırasında yeni bir Kullanıcı oluşturulur-Azure AD tümleştirmesi henüz yoksa.

>[!NOTE]
>Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [moveit transfer-Azure AD tümleştirme istemci desteği ekibine](https://community.ipswitch.com/s/support)başvurmanız gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Access panel 'deki MoveIt transfer-Azure AD tümleştirme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız MoveIt aktarımı-Azure AD tümleştirmesinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

