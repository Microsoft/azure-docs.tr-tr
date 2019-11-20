---
title: 'Öğretici: Mitel Connect ile Azure Active Directory Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Mitel Connect arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160542"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Öğretici: Mitel Micyüksek Connect ile Azure Active Directory tümleştirme

Bu öğreticide, Mitel Micyüksek Connect 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Micyüksek Connect 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, kurumsal kimlik bilgilerini kullanarak Micyüksek Connect uygulamalarına erişimi olan denetim yapabilirsiniz.
* Hesabınızdaki kullanıcıların Azure AD hesaplarıyla (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.


Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, Micyüksek Connect ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği

  Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Bir Mitel Micyüksek Connect hesabı

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide Azure AD çoklu oturum açma 'yı (SSO) yapılandırıp test edeceksiniz.

* Mitel Connect, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-mitel-connect-from-the-gallery"></a>Galeriden Mitel Connect ekleme

Mitel Connect 'in Azure AD 'ye tümleştirilmesini yapılandırmak için galerideki Mitel Connect 'i Azure portal yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Mitel Connect eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure portalda](https://portal.azure.com)** sol taraftaki gezinti panelinden **Azure Active Directory**’ye tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a ve ardından **tüm uygulamalar**' a tıklayın.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. **Yeni uygulama**' ya tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama alanına **Mitel Connect** yazın, sonuçlar panelinden **Mitel Connect** ' e tıklayın ve ardından **Ekle**' ye tıklayın.

     ![Sonuç listesinde Mitel bağlantısı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına göre Micyüksek Connect Ile Azure AD çoklu oturum açmayı yapılandırıp test edeceksiniz. Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili kullanıcının, Micı Connect 'te bir bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, Micyüksek Connect ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak ve uygulama tarafında SSO ayarlarını yapılandırmak için **[Azure AD Ile SSO Için Micyüksek Connect 'ı yapılandırın](#configure-micloud-connect-for-sso-with-azure-ad)** .
2. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
3. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
4. Kullanıcının Azure AD gösterimine bağlı olan, Mictta SIG Connect hesabınızda Britta Simon 'ın bir karşılığı olacak şekilde **[bir Mitel Micyüksek Connect test kullanıcısı oluşturun](#create-a-mitel-micloud-connect-test-user)** .
5. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Azure AD ile SSO için Micyüksek Connect 'i yapılandırma

Bu bölümde, Azure portal Micyüksek Connect için Azure AD çoklu oturum açma 'yı etkinleştireceksiniz ve Azure AD 'yi kullanarak, Micyüksek Connect hesabınızı SSO 'ya izin verecek şekilde yapılandıracaksınız.

Azure AD için aynı SSO ile Micyüksek Connect 'i yapılandırmak için Azure portal ve Mitel hesabı portalını yan yana açmak en kolay yoldur. Azure portal bazı bilgileri Mitel Hesap portalına ve bir kısmını Mitel hesap portalından Azure portal kopyalamanız gerekir.


1. [Azure Portal](https://portal.azure.com/)yapılandırma sayfasını açmak için aşağıdakileri yapın:

    a. **Mitel Connect** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' ya tıklayın.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

    b. **Çoklu oturum açma yöntemi seç** Iletişim kutusunda **SAML**' ye tıklayın.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)
    
    SAML tabanlı oturum açma sayfası görüntülenir.

2. Mitel hesap portalında yapılandırma iletişim kutusunu açmak için aşağıdakileri yapın:

    a. **Telefon sistemi** menüsünde, **eklenti özellikleri**' ne tıklayın.

    b. **Çoklu oturum açma**'Nın sağında **Etkinleştir** veya **Ayarlar**' a tıklayın.
    
    Çoklu oturum açma ayarlarını bağla iletişim kutusu görüntülenir.
    
3. **Çoklu oturum açmayı etkinleştir** onay kutusunu seçin.
    ![resim](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Azure portal, **temel SAML yapılandırması** bölümündeki **Düzenle** simgesine tıklayın.
    ![resim](common/edit-urls.png)

    Temel SAML yapılandırması iletişim kutusu görüntülenir.

5.  Mitel hesap portalındaki **Mitel tanımlayıcı (VARLıK kimliği)** alanından URL 'yi kopyalayın ve Azure Portal ' de **tanımlayıcı (varlık kimliği)** alanına yapıştırın.

6. URL 'YI, Mitel hesap portalındaki **yanıt URL 'si (onaylama Işlemi tüketici hizmeti URL 'si)** alanından kopyalayın ve Azure Portal, **yanıt URL 'Si (onaylama tüketici hizmeti URL 'si)** alanına yapıştırın.  
   ![resim](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. **Oturum açma URL 'si** metin kutusunda, aşağıdaki URL 'lerden birini yazın:

    * **https://portal.shoretelsky.com** -Mitel hesabı portalını varsayılan Mitel uygulamanız olarak kullanmak için
    * **https://teamwork.shoretel.com** -takım Işini varsayılan Mitel uygulamanız olarak kullanmak için

    **Note**: varsayılan Mitel uygulaması, bir Kullanıcı erişim panelinde Mitel Connect kutucuğuna tıkladığında erişilen uygulamadır. Bu Ayrıca, Azure AD 'den bir test kurulumu gerçekleştirirken erişilen uygulamadır.

8. Azure portal **temel SAML yapılandırması** Iletişim kutusunda **Kaydet** ' e tıklayın.

9. Azure portal SAML **tabanlı oturum açma** sayfasındaki SAML **imzalama sertifikası** bölümünde, **imzalama sertifikasını** indirmek ve bilgisayarınıza kaydetmek Için **sertifika (base64)** yanındaki **İndir** ' e tıklayın.
    ![resim](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Imza sertifikası dosyasını bir metin düzenleyicisinde açın, dosyadaki tüm verileri kopyalayın ve ardından Mitel hesabı portalındaki **Imza sertifikası** alanından verileri yapıştırın. 
    ![resim](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Azure portal **SAML tabanlı oturum açma** sayfasının **Setup Mitel Connect** bölümünde aşağıdakileri yapın:

    a. **Oturum açma URL** 'si alanından URL 'yi kopyalayın ve Mitel hesabı portalındaki **oturum açma URL 'si** alanına yapıştırın.

    b. **Azure AD tanımlayıcı** alanından URL 'yi kopyalayın ve Mitel hesabı PORTALıNDAKI **varlık kimliği** alanına yapıştırın.
    ![resim](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Mitel hesap portalındaki **Çoklu oturum açma ayarlarını bağla** Iletişim kutusunda **Kaydet** ' e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory**' e, **Kullanıcılar**' a ve ardından **tüm kullanıcılar**' a tıklayın.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' ya tıklayın.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**yazın.
  
    b. **Kullanıcı adı** alanına brittasıon @\<yourcompanydomain\>yazın.\<uzantısı\>.  
Örneğin, BrittaSimon@contoso.com.

    c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Mitel Connect 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' a ve ardından **tüm uygulamalar**' a tıklayın.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Mitel Connect**' e tıklayın.

    ![Uygulamalar listesindeki Mitel Connect bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' a tıklayın.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' ye tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' a tıklayın.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda **Kullanıcılar** listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmında **Seç** ' e tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmında **Seç** ' e tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' ya tıklayın.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel Micyüksek Connect test kullanıcısı oluşturma

Bu bölümde, Mictta Simon adlı bir kullanıcıyı, Micyüksek bağlantı hesabınızda oluşturursunuz. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

Mitel Hesap portalına Kullanıcı ekleme hakkında daha fazla bilgi için Mitel Bilgi Bankası 'nda [Kullanıcı ekleme](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) makalesine bakın.

Aşağıdaki ayrıntılarla, Micyüksek Connect hesabınızda bir kullanıcı oluşturun:

  * **Ad:** Britta Simon

* **Iş e-posta adresi:** `brittasimon@<yourcompanydomain>.<extension>`   
(Örnek: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Kullanıcı adı:** `brittasimon@<yourcompanydomain>.<extension>`  
(Örnek: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); kullanıcının Kullanıcı adı genellikle kullanıcının iş e-posta adresiyle aynıdır)

**Note:** Kullanıcının Micyüksek Connect Kullanıcı adı, kullanıcının Azure 'daki e-posta adresiyle aynı olmalıdır.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edeceksiniz.

Erişim panelinde Mitel Connect kutucuğuna tıkladığınızda, **URL oturum açma** alanında varsayılan olarak yapılandırdığınız Micyüksek Connect uygulamasında oturum açmak için otomatik olarak yeniden yönlendirilmelisiniz. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
