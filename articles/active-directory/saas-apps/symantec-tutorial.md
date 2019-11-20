---
title: 'Öğretici: Symantec Web Security Service (WSS) ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Symantec Web Security Service (WSS) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159937"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Öğretici: Symantec Web Security Service (WSS) ile Azure Active Directory tümleştirme

Bu öğreticide, WSS Web güvenlik hizmeti (WSS) hesabınızı Azure Active Directory (Azure AD) hesabınızla tümleştirmeyi öğrenecek ve böylece WSS 'nin SAML kimlik doğrulaması kullanarak Azure AD 'de sağlanan bir son kullanıcının kimliğini doğrulayabilmesi ve kullanıcıyı zorlaması veya Grup düzeyi ilke kuralları.

Symantec Web Security Service 'i (WSS) Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

- WSS hesabınız tarafından Azure AD portalından kullanılan tüm son kullanıcıları ve grupları yönetin.

- Son kullanıcıların Azure AD kimlik bilgilerini kullanarak WSS 'de kendilerini kimlik doğrulamasına izin verin.

- WSS hesabınızda tanımlanan Kullanıcı ve Grup düzeyindeki ilke kurallarının uygulanmasını etkinleştirin.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Symantec Web Security Service (WSS) ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Symantec Web Security Service (WSS) çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Symantec Web Security Service (WSS), **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Galerideki Symantec Web Security Service (WSS) ekleme

Symantec Web Security hizmeti 'nin (WSS) Azure AD ile tümleştirilmesini yapılandırmak için, galerideki Symantec Web Security Service (WSS) öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Symantec Web Security hizmeti (WSS) eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Symantec Web Security Service (WSS)** yazın, sonuç panelinden **Symantec Web SECURITY Service (WSS)** öğesini seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açma 'yı, **Britta Simon**adlı bir test kullanıcısına göre Symantec Web Security SERVICE (WSS) ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Symantec Web Security Service (WSS) içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Symantec Web Security Service (WSS) ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **Symantec Web Security Service (WSS) çoklu oturum açmayı yapılandırın** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Symantec Web **[Security Service (WSS) test kullanıcısı oluşturun](#create-symantec-web-security-service-wss-test-user)** . Symantec Web Security SERVICE (WSS) ' de kullanıcının Azure AD gösterimine bağlı bir Britta Simon 'a sahip olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Symantec Web Security Service (WSS) ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/) **Symantec Web SECURITY Service (WSS)** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Symantec Web Security Service (WSS) etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir URL yazın: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. **Yanıt URL** 'si metin kutusuna bir URL yazın: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > [Symantec Web Security Service (WSS) istemci desteği ekibine](https://www.symantec.com/contact-us) başvurun f **tanımlayıcı** için değerler ve **yanıt URL 'si** bazı nedenlerle çalışmıyor. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Symantec Web Security Service (WSS) çoklu oturum açmayı yapılandırma

Symantec Web Security Service (WSS) tarafında çoklu oturum açmayı yapılandırmak için WSS çevrimiçi belgelerine bakın. İndirilen **Federasyon meta VERI XML** dosyası WSS portalına aktarılmalıdır. WSS portalındaki yapılandırmayla ilgili yardıma ihtiyacınız varsa [Symantec Web Security Service (WSS) destek ekibine](https://www.symantec.com/contact-us) başvurun.

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

Bu bölümde, Symantec Web Security Service (WSS) erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **Symantec Web Security hizmeti (WSS)** öğesini seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Symantec Web Security Service (WSS)** yazın ve seçin.

    ![Uygulamalar listesinde Symantec Web Security Service (WSS) bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) test kullanıcısı oluştur

Bu bölümde Symantec Web Security Service (WSS) ' de Britta Simon adlı bir Kullanıcı oluşturacaksınız. İlgili Son Kullanıcı adı, WSS portalında el ile oluşturulabilir veya Azure AD 'de sağlanan Kullanıcı/grupların, birkaç dakika sonra (yaklaşık 15 dakika) WSS portalı ile eşitlenmesi için bekleyebilirsiniz. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. Web sitelerine Gözatılacak olan Son Kullanıcı makinesinin genel IP adresi, Symantec Web Security Service (WSS) portalında da sağlanmalıdır.

> [!NOTE]
> Makinenizin genel IPAddress değerini almak için lütfen [buraya](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, artık WSS hesabınızı, SAML kimlik doğrulaması için Azure AD 'yi kullanacak şekilde yapılandırdığınıza göre çoklu oturum açma işlevlerini test edeceksiniz.

Web tarayıcınızı WSS 'ye proxy trafiğine yapılandırdıktan sonra, Web tarayıcınızı açıp bir siteye gözatmaya çalıştığınızda Azure oturum açma sayfasına yönlendirilirsiniz. Azure AD 'de (yani, BrittaSimon) ve ilişkili parolada sağlanan test son kullanıcısının kimlik bilgilerini girin. Kimliği doğrulandıktan sonra seçtiğiniz Web sitesine gözatabileceksiniz. Bu sitede belirli bir siteye gözatmasını engellemek için WSS tarafında bir ilke kuralı oluşturmanız gerekir, bu siteye Kullanıcı Brittasıas olarak gözatmaya çalıştığınızda WSS blok sayfasını görmeniz gerekir.

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

