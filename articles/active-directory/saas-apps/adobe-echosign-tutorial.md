---
title: 'Öğretici: Adobe Sign ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Adobe Işareti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154032"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Öğretici: Adobe Sign ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Adobe Sign 'ı tümleştirmeyi öğreneceksiniz.
Adobe Sign 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Adobe Sign 'a erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak bir Adobe Sign (çoklu oturum açma) ile oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Adobe Sign ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çoklu oturum açma etkin aboneliğini Adobe Imzala

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Adobe Sign, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-adobe-sign-from-the-gallery"></a>Galeriden Adobe Sign ekleme

Adobe Sign 'ın Azure AD 'de tümleştirilmesini yapılandırmak için, galerisindeki Adobe Sign 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Adobe Sign eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Adobe Sign**yazın, sonuç panelinden **Adobe Sign** ' ı seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde Adobe Sign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına dayanarak Adobe Sign ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Adobe Sign içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Adobe Sign ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Adobe Sign Single Sign-on 'U yapılandırın](#configure-adobe-sign-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Adobe Sign 'da Britta Simon 'un bir karşılığı olacak şekilde **[Adobe Sign test kullanıcısı oluşturun](#create-adobe-sign-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Adobe Sign ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Adobe imza** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Adobe oturum etki alanı ve URL 'Leri çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.echosign.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Adobe Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Adobe Sign 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-adobe-sign-single-sign-on"></a>Adobe Sign çoklu oturum açmayı yapılandırma

1. Yapılandırmadan önce, Adobe [Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) başvurarak etki alanınızı Adobe Sign izin verilenler listesine ekleyin. Etki alanını ekleme:

    a. [Adobe Sign Client destek ekibi](https://helpx.adobe.com/in/contact/support.html) size rastgele oluşturulmuş bir belirteç gönderir. Etki alanınız için belirteç şu şekilde olacaktır: **Adobe-Sign-doğrulaması = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Doğrulama belirtecini bir DNS metin kaydında yayımlayın ve [Adobe Imza istemci desteği ekibine](https://helpx.adobe.com/in/contact/support.html)bildirin.
    
    > [!NOTE]
    > Bu, birkaç gün veya daha uzun sürebilir. DNS yayma gecikmelerinin, DNS 'de yayımlanan bir değerin saat veya daha uzun bir süre boyunca görülemeyebilir anlamına geldiğini unutmayın. BT yöneticiniz, bu belirteci bir DNS metin kaydında yayımlama hakkında bilgi sahibi olmalıdır.
    
    c. Destek bileti aracılığıyla [Adobe Sign Client destek ekibine](https://helpx.adobe.com/in/contact/support.html) bildirimde bulunduğunda, belirteç yayımlandıktan sonra etki alanını doğrular ve hesabınıza ekler.
    
    d. Genellikle, bir DNS kaydında belirtecin nasıl yayımlanacağı aşağıda verilmiştir:

    * Etki alanı hesabınızda oturum açın
    * DNS kaydını güncelleştirmek için sayfayı bulun. Bu sayfa DNS yönetimi, ad sunucusu yönetimi veya Gelişmiş ayarlar olarak adlandırılır.
    * Etki alanınız için TXT kayıtlarını bulun.
    * Adobe tarafından sağlanan tam belirteç değerine sahip bir TXT kaydı ekleyin.
    * Yaptığınız değişiklikleri kaydedin.

1. Farklı bir Web tarayıcısı penceresinde, Adobe Imza şirket sitenizde yönetici olarak oturum açın.

1. SAML menüsünde, **SAML ayarları** > **Hesap ayarları** ' nı seçin.
   
    ![Adobe Sign SAML ayarları sayfasının ekran görüntüsü](./media/adobe-echosign-tutorial/ic789520.png "Hesap")

1. **SAML ayarları** bölümünde aşağıdaki adımları uygulayın:
  
   ![SAML ayarlarının ekran görüntüsü](./media/adobe-echosign-tutorial/ic789521.png "SAML ayarları")
   
   ![SAML ayarlarının ekran görüntüsü](./media/adobe-echosign-tutorial/ic789522.png "SAML ayarları")

   a. **SAML modu**altında **SAML zorunlu**' yı seçin.
   
   b. **Yankı Imzası hesap yöneticilerinin yankı Işaret kimlik bilgilerini kullanarak oturum açmasına Izin ver '** i seçin.
   
   c. **Kullanıcı oluşturma**altında **SAML aracılığıyla kimliği doğrulanmış kullanıcıları otomatik olarak ekle**' yi seçin.

   d. Azure portal, **IDP VARLıK kimliği** metin kutusuna kopyaladığınız **Azure ad tanımlayıcısını**yapıştırın.
    
   e. **IDP oturum açma URL 'si** metin kutusuna kopyaladığınız Azure Portal, **oturum açma URL 'sini**yapıştırın.
   
   f. Azure portal, **IDP oturum kapatma URL 'si** metin kutusuna kopyaladığınız **Logout URL 'sini**yapıştırın.

   g. İndirilen **sertifika (base64)** Dosyanızı Not defteri 'nde açın. İçeriğini panonuza kopyalayın ve sonra **IDP sertifikası** metin kutusuna yapıştırın.

   h. **Değişiklikleri Kaydet**' i seçin.

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

Bu bölümde, Adobe Sign 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Adobe imzala**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, yazıp **Adobe Sign**' ı seçin.

    ![Uygulamalar listesindeki Adobe Sign bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign test kullanıcısı oluştur

Azure AD kullanıcılarının Adobe oturum açmasını etkinleştirmek için, bunların Adobe Sign 'a sağlanması gerekir. Bu, el ile gerçekleştirilen bir görevdir.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için Adobe Sign tarafından sunulan diğer herhangi bir Adobe Sign Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz. 

1. **Adobe imza** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Hesap**' ı seçin. Ardından, sol bölmede **kullanıcılar & gruplar** ' ı seçin > **Yeni bir kullanıcı oluşturun**.
   
    ![Hesap, kullanıcılar & grupları ve vurgulanmış yeni bir Kullanıcı oluşturma ile birlikte Adobe Sign şirket sitesinin ekran görüntüsü](./media/adobe-echosign-tutorial/ic789524.png "Hesap")
   
3. **Yeni Kullanıcı oluştur** bölümünde aşağıdaki adımları uygulayın:
   
    ![Yeni Kullanıcı oluştur bölümünün ekran görüntüsü](./media/adobe-echosign-tutorial/ic789525.png "Kullanıcı Oluştur")
   
    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure AD hesabının **e-posta adresini**, **adını**ve **soyadını** yazın.
   
    b. **Kullanıcı oluştur**' u seçin.

>[!NOTE]
>Azure Active Directory hesap sahibi, hesabı etkin hale gelmeden önce, hesabı onaylamaya yönelik bir bağlantı içeren bir e-posta alır. 

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Adobe Işareti kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Adobe Sign ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

