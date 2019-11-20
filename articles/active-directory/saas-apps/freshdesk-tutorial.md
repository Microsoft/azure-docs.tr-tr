---
title: 'Öğretici: en güncel çalışma ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve en güncel çalışma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: add43f081ca6059651725cd6f817d4cce127b74b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73155766"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Öğretici: en güncel çalışma ile Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile en güncel şekilde tümleştirmeyi öğreneceksiniz.
En güncel çalışma masası 'nı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, en yenisi olan bir masa 'ya erişimi olan denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak (çoklu oturum açma) oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini en güncel çalışma ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Tek oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* En güncel çalışma **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-freshdesk-from-the-gallery"></a>Galeriden en güncel çalışma ekleme

En güncel çalışma alanının Azure AD ile tümleştirilmesini yapılandırmak için Galeriden, yönetilen SaaS uygulamaları listenize en güncel çalışma sayfasına eklemeniz gerekir.

**Galeriden en güncel çalışma eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, en sonra, uygulamayı eklemek için Ekle ' ye tıklayın, sonuç panelinden **yeniden Seç '** i **seçin ve ardından** **Ekle** düğmesine tıklayın.

     ![Sonuç listesinde en güncel çalışma](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak en güncel şekilde yapılandırır ve test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı yapılandırma ve test etmek için, aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, isteğe bağlı olarak **[Çoklu oturum açmayı yapılandırın](#configure-freshdesk-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı için Azure AD gösterimine bağlı olan, en güncel bir masa 'daki Britta Simon 'a sahip olmak için, yalnızca bir **[kayıt testi kullanıcısı oluşturun](#create-freshdesk-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı en güncel şekilde yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **en sonra, bir uygulama tümleştirmesi** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![En basit çalışma alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenant-name>.freshdesk.com` veya başka bir değer olan herhangi bir değer varsa.

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenant-name>.freshdesk.com` veya başka bir değer olan herhangi bir diğer değer için önerilir.

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, en güncel olan [istemci desteği ekibine](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Bu uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde buna bir örnek gösterilmektedir. **Benzersiz kullanıcı tanımlayıcısının** varsayılan değeri **User. UserPrincipalName** ' dir, ancak bu, varsayılan olarak, kullanıcının e-posta adresiyle eşleştirilmesini bekler. Bu şekilde, listeden **User. Mail** özniteliğini kullanabilir veya kuruluşunuzun yapılandırmasına göre uygun öznitelik değerini kullanabilirsiniz. 

    ![image](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin:
    
    | Adı | Kaynak özniteliği |
    | ---------------| --------------- |
    | Benzersiz kullanıcı tanımlayıcısı | Kullanıcı. Mail |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

7. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Komut istemi** 'ni açın ve aşağıdaki komutları çalıştırın:

    a. Komut istemine `certutil.exe -dump FreshDesk.cer` değeri girin.

    > [!NOTE]
    > Burada, Azure portal indirdiğiniz sertifika **. cer** öğesini burada bulabilirsiniz.

    b. **CERT Hash (SHA256)** değerini kopyalayın ve Not defteri 'ne yapıştırın. 

9. Tek başına **Ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-freshdesk-single-sign-on"></a>İsteğe bağlı olarak çoklu oturum açmayı Yapılandır

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak iki adımda şirket sitenizde oturum açın.

2. **Ayarlar simgesini** seçin ve **güvenlik** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Çoklu Oturum Açma](./media/freshdesk-tutorial/IC776770.png "Çoklu Oturum Açma")
  
    a. **Çoklu oturum açma (SSO)** için **Açık**' ı seçin.

    b. **SAML SSO**'yu seçin.

    c. **SAML oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Oturum kapatma URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    e. **Güvenlik sertifikası parmak izi** metin kutusunda, daha önce edindiğiniz **CERT Hash (SHA256)** değerini yapıştırın.
  
    f. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak en güncel çalışma alanına erişim izni vererek Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' **ı seçin ve**ardından yeniden Seç ' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, öğesini yazın ve ardından yeniden **Seç ' i seçin**.

    ![Uygulamalar listesindeki en güncel çalışma bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-freshdesk-test-user"></a>Yalnızca bir test kullanıcısı oluştur

Azure AD kullanıcılarının, en güncel şekilde oturum açmasını sağlamak için, bu kullanıcıların, tek yapmanız gerekir.  
Bu durumda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bu **kiracınızda** oturum açın.

2. Üstteki menüden **yönetici**' ye tıklayın.

    ![Yöneticileri](./media/freshdesk-tutorial/IC776772.png "Yöneticileri")

3. **Genel ayarlar** sekmesinde **aracılar**' a tıklayın.
  
    ![Aracısını](./media/freshdesk-tutorial/IC776773.png "Aracılar")

4. **Yeni Aracı**' e tıklayın.

    ![Yeni Aracı](./media/freshdesk-tutorial/IC776774.png "Yeni Aracı")

5. Aracı bilgileri iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Aracı bilgileri](./media/freshdesk-tutorial/IC776775.png "Aracı bilgileri")

    a. **E-posta** metin kutusuna, sağlamak ISTEDIĞINIZ Azure AD HESABıNıN Azure ad e-posta adresini yazın.

    b. **Tam ad** metin kutusuna sağlamak ISTEDIĞINIZ Azure AD hesabının adını yazın.

    c. **Title** metin kutusuna sağlamak ISTEDIĞINIZ Azure AD hesabının başlığını yazın.

    d. **Kaydet** düğmesine tıklayın.

    >[!NOTE]
    >Azure AD hesap tutucusu, hesabı etkinleştirilmeden önce onaylanacak bir bağlantı içeren bir e-posta alır.
    >
    >[!NOTE]
    >Başka bir kullanıcı hesabı oluşturma aracını veya en güncel kullanıcı hesaplarını, en silebilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde, bir daha sonra da, SSO 'yu ayarladığınız otomatik olarak bir şekilde oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

