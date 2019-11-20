---
title: 'Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve F5 arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, F5 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. F5 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de F5 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla F5 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* F5 çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* F5 **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* F5 SSO üç farklı şekilde yapılandırılabilir.

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Galeriden F5 ekleme

F5 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize F5 ' i eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **F5** yazın.
1. Sonuçlar panelinden **F5** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu F5 ile yapılandırın ve test edin. SSO 'nun çalışması için, F5 'teki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu F5 ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[F5-SSO](#configure-f5-sso)** -' i yapılandırın.
    1. F5 'te kullanıcının Azure AD gösterimine bağlı olan B. Simon 'un bir karşılığı olacak şekilde **[F5 test kullanıcısı oluşturun](#create-f5-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **F5** uygulaması tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<YourCustomFQDN>.f5.com/`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<YourCustomFQDN>.f5.com/`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [F5 istemci destek ekibine](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **F5 ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, F5 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **F5**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-f5-sso"></a>F5 SSO 'yu Yapılandır

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve F5 (Gelişmiş Kerberos) Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Meta veri sertifikasını, kurulum işleminin ilerleyen kısımlarında kullanılacak F5 (Gelişmiş Kerberos) içine aktarmanız gerekir. **> trafik sertifika yönetimi > > SSL sertifika listesi**' ne > gidin. Sağ köşedeki **Içeri aktarma** seçeneğine tıklayın.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure01.png)
 
1. SAML ıDP 'yi ayarlamak için **> federasyon > SAML hizmeti sağlayıcısı > meta verilerden > oluştur**' a gidin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Görevden karşıya yüklenen sertifikayı belirtin 3

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure07.png)

 1. SAML SP 'yi ayarlamak için **> federasyon > SAML hizmeti federasyonu > yerel SP hizmetleri > oluştur**' a gidin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure08.png)
 
1. **OK (Tamam)** düğmesine tıklayın.

1. SP yapılandırması ' nı seçin ve **IDP bağlayıcılarını çöz/Kaldır ' a**tıklayın.

     ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. **Yeni satır ekle** ' ye tıklayın ve önceki adımda oluşturulan **dış IDP bağlayıcısını** seçin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Kerberos SSO 'yu yapılandırmak için **> > çoklu oturum açma 'Ya erişin**

    >[!Note]
    >Oluşturulacak ve belirtilecektir Kerberos temsili hesabı gerekir. KCD bölümüne başvurun (değişken başvuruları için eki Inceleyin)

    • Kullanıcı adı kaynak `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Kullanıcı bölgesi kaynak `session.logon.last.domain`

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure11.png)

1. Erişim profilini yapılandırmak için, erişim profili **> > profil/ılkelere erişin (oturum ilkeleri başına)** .

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure16.png)
 
    * Session. Logon. son. usernameUPN Expr {[mcget {Session. SAML. Last. Identity}]}

    * Session. ad. lastactualdomain metın superdemo. canlı

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {Session. Logon. Last. usernameUPN})

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure19.png)

    * Session. Logon. Last. UserName expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {Session. Logon. Last. UserName}
    * mcget {Session. Logon. Last. Password}

1. Yeni düğüm eklemek için, **düğüm listesi > + ' > yerel trafik > düğümlere**gidin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Yeni bir havuz oluşturmak için, **oluştur > havuz listesi > yerel trafik > havuzları**' na gidin.

     ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure22.png)

 1. Yeni bir sanal sunucu oluşturmak için, sanal **sunucular > sanal sunucu listesi > + > yerel trafiğe**gidin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure23.png)

1. Önceki adımda oluşturulan erişim profilini belirtin.

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos temsilcisini ayarlama 

>[!Note]
>Daha fazla ayrıntı için [buraya](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) bakın

* **1. Adım: bir temsili hesabı oluşturma**

    * Örnek
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **2. Adım: SPN 'YI ayarlama (APM temsili hesabında)**

    *  Örnek
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **3. Adım: SPN temsili (App Service hesabı için)**

    * F5 temsili hesabı için uygun temsilciyi ayarlayın.
    * Aşağıdaki örnekte, APM temsili hesabı FRP-app1. superdemo. Live uygulaması için KCD için Yapılandırılıyor.

        ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure25.png)

1. Yukarıdaki başvuru belgesinde bahsedilen ayrıntıları [Bu](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) şekilde belirtin

1. Ek-SAML – F5 BIG-IP değişken eşlemeleri aşağıda gösterilmiştir:

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (Gelişmiş Kerberos) yapılandırması](./media/advance-kerbf5-tutorial/configure27.png) 

1. Varsayılan SAML özniteliklerinin tam listesi aşağıda verilmiştir. Bu, aşağıdaki dize kullanılarak temsil edilir.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6. Session. SAML. Last. AssertionId 'si | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssueInstant  | `<ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6. Session. SAML. Last. Audience | `https://kerbapp.superdemo.live` |
| eb46b6b6. Session. SAML. Last. authNContextClassRef | urn: oassıs: adlar: TC: SAML: 2.0: AC: sınıflar: parola |
| eb46b6b6. Session. SAML. Last. Authnınstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. Identity | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. InResponseTo | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. Nameıdvalue | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. Nameıdformat | urn: oassıs: adlar: TC: SAML: 1.1: NameID-Format: Emapostaadı |
| eb46b6b6. Session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. Responseıd | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. Responseıssueınstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. Responseıssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. Result | 1 |
| eb46b6b6. Session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. Session. SAML. Last. SessionIndex | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. statusValue | urn: oassıs: adlar: TC: SAML: 2.0: durum: başarılı |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. subjectConfirmMethod | urn: oassıs: adlar: TC: SAML: 2.0: cm: taşıyıcı |
| eb46b6b6. Session. SAML. Last. validityNotBefore | `<ID>` |
| eb46b6b6. Session. SAML. Last. validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>F5 test kullanıcısı oluştur

Bu bölümde, F5 'te B. Simon adlı bir Kullanıcı oluşturacaksınız. F5 platformunda kullanıcıları eklemek için [F5 istemci destek ekibi](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde F5 kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız F5 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile F5 'i deneyin](https://aad.portal.azure.com/)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](kerbf5-tutorial.md)

