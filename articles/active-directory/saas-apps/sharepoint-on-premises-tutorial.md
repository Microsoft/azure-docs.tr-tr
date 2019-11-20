---
title: 'Öğretici: SharePoint şirket içi ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Şirket içi Azure Active Directory ve SharePoint arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164354"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Öğretici: Şirket içi SharePoint ile Azure Active Directory tümleştirme

Bu öğreticide, SharePoint şirket içi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
SharePoint şirket içi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Şirket içi SharePoint 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SharePoint şirket içi (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınız bir merkezi konumda - Azure portalında yönetebilir.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini şirket içi SharePoint ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* SharePoint şirket içi çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SharePoint şirket içi, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Galeriden SharePoint şirket içi ekleme

SharePoint 'in şirket içi tümleştirmesini Azure AD ile yapılandırmak için, Galeriden SharePoint Şirket içinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden SharePoint şirket içi eklemek için aşağıdaki adımları uygulayın:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

    > [!NOTE]   
    > Öğe kullanılabilir olmaması gerekiyorsa, sol gezinti bölmesinin en üstündeki sabit **tüm hizmetler** bağlantısı aracılığıyla da açılabilirler. Aşağıdaki genel bakışta, **Azure Active Directory** bağlantısı **kimlik** bölümünde bulunur veya filtre metin kutusu kullanılarak aranabilir.

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SharePoint şirket içi**yazın, sonuç panelinden **SharePoint şirket içi** ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuç listesinde SharePoint şirket içi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına dayanarak şirket içi SharePoint ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve SharePoint şirket içi ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve şirket içi SharePoint ile test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırmayı](#configure-azure-ad-single-sign-on)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. **[SharePoint şirket Içi çoklu oturum açmayı yapılandırma](#configure-sharepoint-on-premises-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. Azure AD 'de çoklu oturum açma için yeni bir güvenlik grubunu etkinleştirmek üzere **[Azure Portal Azure AD güvenlik grubu oluşturun](#create-an-azure-ad-security-group-in-the-azure-portal)** .
5. **[SharePoint şirket Içi güvenlik grubuna erişim Izni verme](#grant-access-to-sharepoint-on-premises-security-group)** -belirli bir grup IÇIN Azure AD 'ye erişim izni verin.
6. Kimlik doğrulaması için Azure AD 'ye belirli bir grubu atamak üzere **[Azure Portal Azure AD güvenlik grubunu atayın](#assign-the-azure-ad-security-group-in-the-azure-portal)** .
7. **[Çoklu oturum açmayı test](#test-single-sign-on)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Şirket içi SharePoint ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **SharePoint şirket içi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![SharePoint şirket içi etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:sharepoint:federation`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [SharePoint şirket Içi istemci desteği ekibine](https://support.office.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

    > [!Note]
    > Yapılandırma için PowerShell betiğinden daha sonra kullanmanız gerektiğinde, sertifika dosyasını indirdiğiniz dosya yolunu lütfen unutmayın.

6. SharePoint Şirket **Içi ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın. **Çoklu oturum açma hizmeti URL 'si**için aşağıdaki düzenin bir değerini kullanın:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ , Azure AD aboneliğinin kiracı kimliğidir.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL'si:

    b. Azure AD Tanımlayıcısı

    c. Oturum Kapatma URL'si

    > [!NOTE]
    > SharePoint şirket Içi uygulaması SAML 1,1 belirtecini kullanır, bu nedenle Azure AD, SharePoint sunucusundan WS Bestalebi bekliyor ve kimlik doğrulamasından sonra SAML 1,1 ' i yayınlar. simgesinde.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>SharePoint şirket içi çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, SharePoint şirket içi şirket sitenizde yönetici olarak oturum açın.

2. **SharePoint Server 2016 ' de yeni bir güvenilen kimlik sağlayıcısı yapılandırma**

    SharePoint Server 2016 sunucusunda oturum açın ve SharePoint 2016 Yönetim Kabuğu 'nu açın. $Realm değerlerini (SharePoint şirket içi etki alanı ve URL 'lerden tanımlayıcı değer Azure portal), $wsfedurl (çoklu oturum açma hizmeti URL 'SI) ve $filepath (sertifika dosyasını indirdiğiniz dosya yolu) Azure portal ve çalıştırmak için girin Yeni bir güvenilen kimlik sağlayıcısını yapılandırmak için aşağıdaki komutlar.

    > [!TIP]
    > PowerShell 'i kullanmaya yeni başladıysanız veya PowerShell 'in nasıl çalıştığı hakkında daha fazla bilgi edinmek istiyorsanız bkz. [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ardından, uygulamanız için güvenilir kimlik sağlayıcısını etkinleştirmek üzere aşağıdaki adımları izleyin:

    a. Merkezi Yönetim 'de **Web uygulamasını Yönet** ' e gidin ve Azure AD ile güvenli hale getirmek istediğiniz Web uygulamasını seçin.

    b. Şeritte, **kimlik doğrulama sağlayıcıları** ' na tıklayın ve kullanmak istediğiniz bölgeyi seçin.

    c. **Güvenilen kimlik sağlayıcısı** ' nı seçin ve ardından *azuread*adlı yeni kaydettiğiniz sağlayıcıyı tanımla ' yı seçin.

    d. Oturum açma sayfası URL 'SI ayarında, **özel oturum açma sayfası** ' nı seçin ve "/_trust/" değerini sağlayın.

    e. **Tamam**'ı tıklatın.

    ![Kimlik doğrulama sağlayıcınızı yapılandırma](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Bazı dış kullanıcılar bu çoklu oturum açma tümleştirmesini kullanamaz, bu da UPN 'lerine benzer `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`bir şekilde karıştırılmış değere sahip olur. Yakında, Kullanıcı türüne bağlı olarak UPN 'nin nasıl işleneceği konusunda müşterilerin uygulama yapılandırmasına izin vereceğiz. Tüm konuk kullanıcılarınızın, kuruluşun çalışanları kadar SSO 'yu sorunsuz bir şekilde kullanabilmesi gerekir.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Britta Simon adlı Azure portalında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üstünde.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özellikleri, aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alan türü`brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**'a tıklayın.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal bir Azure AD güvenlik grubu oluşturun

1. **Tüm grupları > Azure Active Directory**' ye tıklayın.

    ![Azure AD güvenlik grubu oluşturma](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. **Yeni Grup**' a tıklayın:

    ![Azure AD güvenlik grubu oluşturma](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. **Grup türü**, **Grup adı**, **Grup açıklaması**, **üyelik türü**' nde Fill yazın. Üyeler ' i seçmek için oka tıklayın ve ardından gruba eklemek istediğiniz üyeyi arayın veya tıklayın. Seçili üyeleri eklemek için **Seç** ' e tıklayın, ardından **Oluştur**' a tıklayın.

    ![Azure AD güvenlik grubu oluşturma](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Şirket içi SharePoint 'e Azure Active Directory güvenlik grupları atamak için, şirket içi SharePoint grubunda [Azurecp](https://yvand.github.io/AzureCP/) 'yi YÜKLEYIP yapılandırmak veya SharePoint için alternatif bir özel talep sağlayıcısı geliştirmek ve yapılandırmak gerekir.  AzureCP kullanmıyorsanız, kendi özel talep sağlayıcınızı oluşturmak için belgenin sonundaki daha fazla bilgi bölümüne bakın.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>SharePoint şirket içi güvenlik grubuna erişim izni verme

**Uygulama kaydında güvenlik gruplarını ve Izinleri yapılandırma**

1. Azure portal **Azure Active Directory**' i seçin ve **uygulama kayıtları**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Arama kutusuna **SharePoint şirket içi**yazın ve seçin.

    ![Sonuç listesinde SharePoint şirket içi](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. **Bildirim**' e tıklayın.

    ![Bildirim seçeneği](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Şu şekilde `groupMembershipClaims` değiştirin`groupMembershipClaims`: .`SecurityGroup` `NULL` Sonra Kaydet ' e tıklayın.

    ![Bildirimi Düzenle](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. **Ayarlar**' a ve ardından **gerekli izinler**' e tıklayın.

    ![Gerekli izinler](./media/sharepoint-on-premises-tutorial/settings.png)

6. **Ekle** ' ye tıklayın ve ardından **bir API seçin**.

    ![API Erişimi](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Hem **Windows Azure Active Directory** hem de **Microsoft Graph API 'si**ekleyin, ancak tek seferde yalnızca bir tane seçmek mümkündür.

    ![API seçimi](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Windows Azure Active Directory seçin, dizin verilerini oku ' nı işaretleyin ve Seç ' e tıklayın. Geri dönüp Microsoft Graph ekleyin ve dizin verilerini okuma ' yı seçin.  Seç ' e tıklayın ve bitti ' ye tıklayın.

    ![Erişimi Etkinleştir](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Şimdi, gerekli ayarlar altında izin **ver** ' e ve ardından izin vermek Için Evet ' e tıklayın.

    ![İzin Ver](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > İzinlerin başarılı bir şekilde verilip verilmediğini öğrenmek için bildirimleri inceleyin.  Aksi takdirde, AzureCP düzgün çalışmaz ve SharePoint şirket içi Azure Active Directory güvenlik grupları yapılandırmak mümkün olmayacaktır.

10. AzureCP 'yi SharePoint şirket içi grubunda veya alternatif bir özel talep sağlayıcı çözümünde yapılandırın.  Bu örnekte, AzureCP kullanıyoruz.

    > [!NOTE]
    > AzureCP 'nin bir Microsoft ürünü değil veya Microsoft teknik desteği tarafından desteklendiğine lütfen emin olun. Şirket içi SharePoint grubuna göre AzureCP 'yi indirin, yükleyin ve yapılandırın https://yvand.github.io/AzureCP/ 

11. Şirket **Içi SharePoint 'te Azure Active Directory güvenlik grubuna erişim Izni verin** :-gruplara SharePoint şirket içi içindeki uygulamaya erişim verilmelidir.  Web uygulamasına erişim izinlerini ayarlamak için aşağıdaki adımları kullanın.

12. Merkezi Yönetim 'de uygulama yönetimi ' ne tıklayın, Web uygulamalarını yönetin, sonra şeridi etkinleştirmek için Web uygulamasını seçin ve Kullanıcı Ilkesi ' ne tıklayın.

    ![Yönetim Merkezi](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Web uygulaması Ilkesi altında Kullanıcı Ekle ' ye ve ardından bölgeyi seçin, Ileri ' ye tıklayın.  Adres defterine tıklayın.

    ![Web uygulaması ilkesi](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Sonra, Azure Active Directory güvenlik grubunu arayıp ekleyin ve Tamam 'a tıklayın.

    ![Güvenlik grubu ekleniyor](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Izinleri seçin ve ardından son ' a tıklayın.

    ![Güvenlik grubu ekleniyor](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Bkz. Web uygulaması Ilkesi altında, Azure Active Directory grubu eklenir.  Grup talebi, Kullanıcı adı için Azure Active Directory güvenlik grubu nesne KIMLIĞINI gösterir.

    ![Güvenlik grubu ekleniyor](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. SharePoint site koleksiyonuna gidin ve grubu buraya ekleyin. Site Ayarları ' na ve ardından Site izinleri ' ne ve Izin ver ' e tıklayın.  Grup rolü talebini arayın, izin düzeyini atayın ve paylaşma ' ya tıklayın.

    ![Güvenlik grubu ekleniyor](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Birden çok Web uygulaması için bir güvenilen kimlik sağlayıcısı yapılandırma

Yapılandırma tek bir Web uygulaması için kullanılabilir, ancak birden fazla Web uygulaması için aynı güvenilir kimlik sağlayıcısını kullanmak istiyorsanız ek yapılandırma gerekir. Örneğin, URL 'yi `https://portal.contoso.local` kullanmak için bir Web uygulaması genişlettik ve şimdi de `https://sales.contoso.local` kullanıcıların kimliğini doğrulamak istiyorsunuz. Bunu yapmak için, kimlik sağlayıcısını WReply parametresini kabul etmek ve Azure AD 'de uygulama kaydını güncelleştirmek ve bir yanıt URL 'SI eklemek için güncelleştirmeniz gerekir.

1. Azure portal Azure AD dizinini açın. **Uygulama kayıtları**' a ve ardından **tüm uygulamaları görüntüle**' ye tıklayın. Daha önce oluşturduğunuz uygulamaya (SharePoint SAML tümleştirmesi) tıklayın.

2. **Ayarlar**' a tıklayın.

3. Ayarlar dikey penceresinde **yanıt URL 'leri**' ne tıklayın.

4. URL 'ye `/_trust/default.aspx` eklenmiş ek Web uygulamasının URL 'sini ( `https://sales.contoso.local/_trust/default.aspx`gibi) ekleyin ve **Kaydet**' e tıklayın.

5. SharePoint sunucusunda, **sharepoint 2016 Yönetim Kabuğu** 'nu açın ve daha önce kullandığınız güvenilen kimlik belirteci verenin adını kullanarak aşağıdaki komutları yürütün.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Merkezi Yönetim 'de Web uygulamasına gidin ve mevcut güvenilen kimlik sağlayıcısını etkinleştirin. Oturum açma sayfası URL 'sini de özel bir oturum açma sayfası `/_trust/`olarak yapılandırmayı unutmayın.

7. Merkezi Yönetim bölümünde Web uygulamasına tıklayın ve **Kullanıcı ilkesi**' ni seçin. Bu makalede daha önce gösterildiği gibi, uygun izinlere sahip bir kullanıcı ekleyin.

### <a name="fixing-people-picker"></a>Kişi Seçicisi düzeltiliyor

Kullanıcılar artık Azure AD 'deki kimlikleri kullanarak SharePoint 2016 'de oturum açabilirler, ancak kullanıcı deneyimine yönelik geliştirme için hala fırsatlar vardır. Örneğin, bir kullanıcının aranması, kişiler seçicisindeki birden çok arama sonucu sunar. Talep eşlemesinde oluşturulan 3 talep türünün her biri için bir arama sonucu vardır. Kişiler seçiciyi kullanarak bir kullanıcı seçmek için, Kullanıcı adını tam olarak yazmanız ve **ad** talep sonucunu seçmeniz gerekir.

![Talep arama sonuçları](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Aradığınız değerlerde bir doğrulama yoktur; bu, yanlış bir talep türü veya Kullanıcı adına, **Soyadı** talebi gibi atamak için yanlış talep türünü seçme ile sonuçlanabilir. Bu, kullanıcıların kaynaklara başarıyla erişmesini engelleyebilir.

Bu senaryoya yardımcı olması için, SharePoint 2016 için özel bir talep sağlayıcı sağlayan [Azurecp](https://yvand.github.io/AzureCP/) adlı açık kaynaklı bir çözüm vardır. Kullanıcıların hangi kullanıcılara girip doğrulama gerçekleştireceğini çözümlemek için Azure AD grafiğini kullanacaktır. [Azurecp](https://yvand.github.io/AzureCP/)'de daha fazla bilgi edinin.

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure portal Azure AD güvenlik grubunu atama

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Şirket içi SharePoint**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **SharePoint şirket içi**' i yazın ve seçin.

    ![Uygulamalar listesinde SharePoint şirket içi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' ye tıklayın.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. Kullanmak istediğiniz güvenlik grubunu arayın, ardından üyeleri seçin bölümüne eklemek için gruba tıklayın. **Seç**' e ve ardından **ata**' ya tıklayın.

    ![Güvenlik grubu ara](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Grubun Azure portal kurumsal uygulamaya başarıyla atandığını uyarılmak için menü çubuğundaki bildirimleri kontrol edin.

### <a name="create-sharepoint-on-premises-test-user"></a>SharePoint şirket içi test kullanıcısı oluşturma

Bu bölümde, SharePoint şirket içi 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. SharePoint şirket içi platformunda kullanıcıları eklemek için [SharePoint şirket içi destek ekibi](https://support.office.com/) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde SharePoint şirket içi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız şirket içi SharePoint 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
