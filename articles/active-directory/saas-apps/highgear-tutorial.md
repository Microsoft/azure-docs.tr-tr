---
title: 'Öğretici: Highdişli ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve üst kısımdaki çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159043"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Öğretici: Highdişli ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğrenebilirsiniz.
Highdişli 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Highdişli 'a erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Highdişli ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Kurumsal veya sınırsız lisansa sahip bir Highdişli sistem

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırmayı ve test yapmayı öğrenebilirsiniz.

* Highdişli **, SP ve IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-highgear-from-the-gallery"></a>Galeriden üst sınır ekleme

Highdişli 'ın Azure AD ile tümleştirilmesini yapılandırmak için galerideki yönetilen SaaS uygulamaları listenize üst kısımdaki bir üst sınır eklemeniz gerekir.

**Galeriden üst sınır eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **highdişli**yazın, sonuç panelinden **highdişli** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde Highdişli](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına göre highdişli SISTEMINIZLE Azure AD çoklu oturum açmayı yapılandırma ve test etme hakkında bilgi edinebilirsiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin, üst düzey sisteminizin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Highdişli sisteminizle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Üst düzey uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Highdişli çoklu oturum açmayı yapılandırın](#configure-highgear-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan bir Lüylede bir köken Simon 'ın bir karşılığı olacak şekilde **[highdişli test kullanıcısı oluşturun](#create-highgear-test-user)** . 
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı nasıl etkinleştirebileceğinizi öğrenebilirsiniz.

Azure AD çoklu oturum açma 'yı Highdişli sisteminizle yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **highdişli** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Highdişli etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, Highdişli sisteminizdeki çoklu oturum açma ayarları sayfasında bulunan **HIZMET sağlayıcısı varlık kimliği** alanının değerini yapıştırın.

    ![Hizmet sağlayıcısı varlık KIMLIĞI alanı](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Çoklu oturum açma ayarları sayfasına erişmek için Highdişli sisteminizde oturum açmanız gerekir. Oturum açtıktan sonra, farenizi üst kısımdaki Yönetim sekmesinin üzerine taşıyın ve çoklu oturum açma ayarları menü öğesine tıklayın.
    
    ![Çoklu oturum açma ayarları menü öğesi](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. **Yanıt URL 'si** metin kutusunda, Highdişli sisteminizdeki çoklu oturum açma ayarları sayfasından **onaylama TÜKETICI hizmeti (ACS) URL** 'sinin değerini yapıştırın.

    ![Onaylama tüketici hizmeti (ACS) URL 'SI alanı](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

     ![Highdişli etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

     **Oturum açma URL 'si** metin kutusunda, Highdişli sisteminizdeki çoklu oturum açma ayarları sayfasında bulunan **HIZMET sağlayıcısı varlık kimliği** alanının değerini yapıştırın. (Bu varlık KIMLIĞI, SP tarafından başlatılan oturum açma işlemi için kullanılacak olan Highdişli sisteminin temel URL 'sidir.)

    ![Hizmet sağlayıcısı varlık KIMLIĞI alanı](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Highdişli sisteminizdeki **Çoklu oturum açma ayarları** sayfasından gerçek tanımlayıcı, yanıt URL 'Si ve oturum açma URL 'si ile güncelleştirin. Yardıma ihtiyacınız varsa lütfen [Highdişli destek ekibine](mailto:support@highgear.com)başvurun.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifikayı** indirmek için **İndir** ' e tıklayın ve bilgisayarınıza kaydedin. Çoklu oturum açma yapılandırmasının sonraki adımında ihtiyacınız olacak.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Highdişli ayarla** bölümünde, aşağıdaki URL 'lerin konumunu aklınızda edin.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI. Bu değere aşağıdaki #2, aşağıdaki üst düzey üst **oturumu Yapılandır** altında yer alan gerekecektir.

    b. Azure AD tanımlayıcısı. Bu değere aşağıdaki #3, aşağıdaki üst düzey üst **oturumu Yapılandır** altında yer alan gerekecektir.

    c. Oturum kapatma URL 'SI. Bu değere aşağıdaki #4, aşağıdaki üst düzey üst **oturumu Yapılandır** altında yer alan gerekecektir.

### <a name="configure-highgear-single-sign-on"></a>Highdişli çoklu oturum açmayı yapılandırma

Çoklu oturum açma için üst düzey yapılandırmak üzere lütfen Highdişli sisteminizde oturum açın. Oturum açtıktan sonra, farenizi üst kısımdaki Yönetim sekmesinin üzerine taşıyın ve çoklu oturum açma ayarları menü öğesine tıklayın.

![Çoklu oturum açma ayarları menü öğesi](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. **Kimlik sağlayıcısı adı**' nda, oturum açma sayfasında, highdişli 'ın çoklu oturum açma düğmesinde görünecek kısa bir açıklama yazın. Örneğin: Azure AD

2. Üst kısımdaki **Çoklu oturum açma (SSO) URL 'si** alanında, Azure 'Da **highdişli ayarlama** bölümünde bulunan **oturum açma URL 'si** alanından değeri yapıştırın.

3. Üst kısımdaki **kimlik sağlayıcısı VARLıK kimliği** alanında, Azure 'Da **highdişli ayarlama** bölümünde yer alan **Azure AD tanımlayıcı** alanından değeri yapıştırın.

4. Üst kısımdaki **Çoklu oturum kapatma (SLO) URL 'si** alanında, Azure 'Da **highdişli ayarlama** bölümünde yer alan **oturum kapatma URL 'si** alanından değeri yapıştırın.

5. Azure 'daki **SAML Imzalama sertifikası** bölümünde indirdiğiniz sertifikayı açmak Için Not defteri ' ni kullanın. **Sertifika (base64)** biçimini indirmiş olmanız gerekir. Sertifikanın içeriğini Not defteri ' nden kopyalayın ve üst kısımdaki **kimlik sağlayıcısı sertifikası** alanına yapıştırın.

6. Highdişli sertifikanızı istemek için [Highdişli destek ekibine](mailto:support@highgear.com) e-posta gönderin. **Highdişli sertifikası** ve üst **sertifika parolası** alanlarını doldurun için onlardan aldığınız yönergeleri izleyin.

7. Highdişli çoklu oturum açma yapılandırmanızı kaydetmek için **Kaydet** düğmesine tıklayın.

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

Bu bölümde, üst düzey 'e erişim izni vererek Britta Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **highdişli**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Highdişli**' ı seçin.

    ![Uygulamalar listesindeki Highdişli bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-highgear-test-user"></a>Highdişli test kullanıcısı oluştur

Çoklu oturum açma yapılandırmanızı test etmek üzere bir Highdişli test kullanıcısı oluşturmak için lütfen Highdişli sisteminizde oturum açın.

1. **Yeni kişi oluştur** düğmesine tıklayın.

    ![Yeni kişi oluştur düğmesi](media/highgear-tutorial/create-new-contact-button.png)

    Oluşturmak istediğiniz kişi türünü seçmenize olanak sağlayan bir menü görünür.

2. Bir üst öğe Kullanıcı oluşturmak için **bireysel** menü öğesine tıklayın.

    Yeni Kullanıcı için bilgileri yazabileceğiniz şekilde sağ tarafta bir bölme açılır.  
    ![Yeni kişi formu](media/highgear-tutorial/new-contact-form.png)

3. **Ad** alanına kişi için bir ad yazın. Örneğin: Britta Simon

4. **Diğer seçenekler** menüsüne tıklayın ve **hesap bilgisi** menü öğesini seçin.

    ![Hesap bilgisi menü öğesine tıklanın](media/highgear-tutorial/account-info-menu-item.png)

5. **Oturum açabilme** alanını Evet olarak ayarlayın.

    **Çoklu oturum açmayı etkinleştir** alanı otomatik olarak Evet olarak ayarlanır.

6. **Çoklu oturum açma kullanıcı kimliği** alanında, kullanıcının kimliğini yazın. Örneğin, BrittaSimon@contoso.com

    Hesap bilgileri bölümü artık şuna benzer görünmelidir:  
    ![Tamamlanan hesap bilgisi bölümü](media/highgear-tutorial/finished-account-info-section.png)

7. Kişiyi kaydetmek için bölmenin alt kısmındaki **Kaydet** düğmesine tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Highdişli kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız üst kısımdaki otomatik olarak oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

