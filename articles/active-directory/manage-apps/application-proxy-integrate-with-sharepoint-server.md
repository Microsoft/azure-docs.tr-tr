---
title: Azure AD Uygulama Ara Sunucusu SharePoint 'e uzaktan erişimi etkinleştirme | Microsoft Docs
description: Şirket içi SharePoint sunucusunu Azure AD Uygulama Ara Sunucusu ile tümleştirme hakkında temel bilgileri içerir.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f1351a2ebe6a23dc4d1e31f30f1c69ac862b21
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595444"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu ile SharePoint’e uzaktan erişimi etkinleştirme

Bu adım adım kılavuzda, şirket içi SharePoint grubunun Azure Active Directory (Azure AD) uygulama proxy 'Si ile nasıl tümleştirileceği açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Yapılandırmayı gerçekleştirmek için aşağıdaki kaynaklara ihtiyacınız vardır:
- SharePoint 2013 grubu veya daha yeni bir sürüm.
- Uygulama proxy 'Si içeren bir plana sahip bir Azure AD kiracısı. [Azure AD planları ve fiyatlandırma](https://azure.microsoft.com/pricing/details/active-directory/)hakkında daha fazla bilgi edinin.
- Azure AD kiracısında [özel, doğrulanmış bir etki alanı](../fundamentals/add-custom-domain.md) .
- Şirket içi Active Directory, kullanıcıların [Azure 'da oturum açmak için](../hybrid/plan-connect-user-signin.md)Azure AD Connect ile eşitlenir.
- Şirket etki alanındaki bir makineye yüklenen ve çalıştırılan bir uygulama proxy Bağlayıcısı.

SharePoint 'i uygulama proxy 'Si ile yapılandırmak için iki URL gerekir:
- Son kullanıcılar tarafından görülebilen ve Azure AD 'de belirlenen bir dış URL. Bu URL, özel bir etki alanı kullanabilir. [Azure AD uygulama ara sunucusu özel etki alanlarıyla çalışma](application-proxy-configure-custom-domain.md)hakkında daha fazla bilgi edinin.
- Yalnızca şirket etki alanı içinde bilinen ve doğrudan kullanılmayan bir iç URL.

> [!IMPORTANT]
> Bağlantıların doğru şekilde eşlendiğinden emin olmak için iç URL için aşağıdaki önerilere uyun:
> - HTTPS kullanın.
> - Özel bağlantı noktaları kullanmayın.
> - Şirket etki alanı adı sistemi 'nde (DNS), bir diğer ad (CName) değil, SharePoint WFE 'yi (veya yük dengeleyiciyi) göstermek için bir konak (A) oluşturun.

Bu makale aşağıdaki değerleri kullanır:
- İç URL: `https://sharepoint`
- Dış URL: `https://spsites-demo1984.msappproxy.net/`
- SharePoint Web uygulaması için uygulama havuzu hesabı: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1\. Adım: Azure AD 'de uygulama proxy 'Si kullanan bir uygulama yapılandırma

Bu adımda, Azure Active Directory kiracınızda uygulama proxy 'Si kullanan bir uygulama oluşturacaksınız. Dış URL 'yi ayarlarsınız ve ikisi de SharePoint 'te daha sonra kullanılan iç URL 'YI belirtirsiniz.

1. Aşağıdaki ayarlarla açıklandığı gibi uygulamayı oluşturun. Adım adım yönergeler için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Iç URL**: `https://sharepoint` gibi SharePoint 'te daha sonra ayarlanacak SharePoint iç URL 'si.
   * **Ön kimlik doğrulaması**: Azure Active Directory
   * **Üst bilgilerdeki URL 'Leri çevir**: Hayır
   * **Uygulama gövdesinde URL 'Leri çevir**: Hayır

   ![SharePoint 'i uygulama olarak Yayımla](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Uygulamanız yayımlandıktan sonra, çoklu oturum açma ayarlarını yapılandırmak için aşağıdaki adımları izleyin:

   1. Portaldaki uygulama sayfasında, **Çoklu oturum açma**' yı seçin.
   1. **Çoklu oturum açma modu**Için **Tümleşik Windows kimlik doğrulaması**' nı seçin.
   1. **Iç uygulama SPN 'sini** daha önce ayarladığınız değere ayarlayın. Bu örnekte, değer `HTTP/sharepoint`.
   1. **Temsilci atanan oturum kimliği**altında Active Directory orman yapılandırmanız için en uygun seçeneği belirleyin. Örneğin, ormanınızda tek bir Active Directory etki alanınız varsa Şirket **ıçı Sam hesap adı** ' nı (aşağıdaki ekran görüntüsünde gösterildiği gibi) seçin. Ancak kullanıcılarınız SharePoint ve uygulama proxy Bağlayıcısı sunucularıyla aynı etki alanında değilse **Şirket içi Kullanıcı asıl adı** ' nı (ekran görüntüsünde gösterilmez) seçin.

   ![SSO için tümleşik Windows kimlik doğrulamasını yapılandırma](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Uygulamanızı ayarlamayı bitirmeden, **Kullanıcılar ve gruplar** bölümüne gidin ve bu uygulamaya erişmek için kullanıcıları atayın. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2\. Adım: SharePoint Web uygulamasını yapılandırma

SharePoint Web uygulaması, Kerberos ile yapılandırılmalı ve Azure AD Uygulama Ara Sunucusu ile doğru şekilde çalışmak için uygun alternatif erişim eşlemeleriyle yapılandırılmalıdır. Olası iki seçenek vardır:

- Yeni bir Web uygulaması oluşturun ve yalnızca varsayılan bölgeyi kullanın. Bu, SharePoint ile en iyi deneyimi sunan tercih edilen seçenektir (örneğin, SharePoint tarafından oluşturulan e-posta uyarılarında bulunan bağlantılar her zaman varsayılan bölgeye işaret ediyor).
- Varsayılan olmayan bir bölgede Kerberos 'u yapılandırmak için mevcut bir Web uygulamasını genişletin.

> [!IMPORTANT]
> Kullanılan bölge ne olursa olsun, SharePoint Web uygulamasının uygulama havuzu hesabı, Kerberos 'un düzgün çalışması için bir etki alanı hesabı olmalıdır.

### <a name="provision-the-sharepoint-web-application"></a>SharePoint Web uygulamasını sağlama

- Yeni bir Web uygulaması oluşturur ve yalnızca varsayılan bölgeyi (tercih edilen seçenek) kullanıyorsanız:

    1. **SharePoint Yönetim Kabuğu** 'nu başlatın ve aşağıdaki betiği çalıştırın:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. **SharePoint Yönetim Merkezi** sitesini açın.
    1. **Sistem ayarları**altında, **Alternatif erişim eşlemelerini Yapılandır**' ı seçin. **Alternatif erişim eşleme koleksiyon** kutusu açılır.
    1. Ekranı yeni Web uygulamasıyla filtreleyin ve şuna benzer bir şey görmediğinizi onaylayın:

       ![Web uygulaması için alternatif erişim eşlemeleri](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Var olan bir Web uygulamasını yeni bir bölgeye genişletirseniz (varsayılan bölgeyi kullanamazsınız):

    1. SharePoint Yönetim Kabuğu 'nu başlatın ve aşağıdaki betiği çalıştırın:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. **SharePoint Yönetim Merkezi** sitesini açın.
    1. **Sistem ayarları**altında, **Alternatif erişim eşlemelerini Yapılandır**' ı seçin. **Alternatif erişim eşleme koleksiyon** kutusu açılır.
    1. Genişletilmiş Web uygulamasıyla ekranı filtreleyin ve şuna benzer bir şey görmediğinizi onaylayın:

        ![Genişletilmiş uygulamanın alternatif erişim eşlemeleri](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint Web uygulamasının bir etki alanı hesabı altında çalıştığından emin olun

SharePoint Web uygulamasının uygulama havuzunu çalıştıran hesabı belirlemek ve bir etki alanı hesabı olduğundan emin olmak için şu adımları izleyin:

1. **SharePoint Yönetim Merkezi** sitesini açın.
1. **Güvenlik** ' e gidin ve **hizmet hesaplarını yapılandır**' ı seçin.
1. **Web uygulaması havuzunu seçin-YourWebApplicationName**.

   ![Hizmet hesabı yapılandırma seçenekleri](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. **Bu bileşen için bir hesap seçin** ' in bir etki alanı hesabı döndürdüğünden emin olun ve bir sonraki adımda gerekli olacağı için onu hatırlayın.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Extranet bölgesinin IIS sitesi için bir HTTPS sertifikası yapılandırıldığından emin olun

Iç URL HTTPS protokolü (`https://SharePoint/`) kullandığından, Internet Information Services (IIS) sitesinde bir sertifikanın ayarlanması gerekir.

1. Windows PowerShell konsolunu açın.
1. Otomatik olarak imzalanan bir sertifika oluşturmak ve bunu bilgisayarın mağazama eklemek için aşağıdaki betiği çalıştırın:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Otomatik olarak imzalanan sertifikalar yalnızca test amacıyla uygundur. Üretim ortamlarında, bunun yerine bir sertifika yetkilisi tarafından verilen sertifikaları kullanmanızı önemle öneririz.

1. Internet Information Services Manager konsolunu açın.
1. Ağaç görünümünde sunucuyu genişletin, **siteler**' i genişletin, **SharePoint-AAD proxy** sitesini seçin ve **bağlamalar**' ı seçin.
1. **Https bağlamasını** seçin ve ardından **Düzenle**' yi seçin.
1. SSL sertifikası alanında **SharePoint** sertifikası ' nı seçin ve ardından **Tamam**' ı seçin.

Artık SharePoint sitesine Azure AD Uygulama Ara Sunucusu dışarıdan erişebilirsiniz.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>3\. Adım: Kerberos kısıtlı temsilcisini yapılandırma

Kullanıcılar, Azure AD proxy Bağlayıcısı aracılığıyla Kerberos kullanarak Azure AD 'de ve sonrasında SharePoint 'te kimlik doğrulaması yapacak. Bağlayıcının Azure AD kullanıcısı adına Kerberos belirteci almasına izin vermek için, Kerberos kısıtlı temsilcisini (KCD) protokol geçişi ile yapılandırmanız gerekir. KCD hakkında daha fazla bilgi edinmek için bkz. [Kerberos kısıtlı temsilciye genel bakış](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>SharePoint hizmet hesabı için SPN 'YI ayarlama

Bu makalede, iç URL `https://sharepoint`, bu nedenle hizmet asıl adı (SPN) `HTTP/sharepoint`. Bu değerleri ortamınıza karşılık gelen değerlerle değiştirmeniz gerekir.
SharePoint uygulama havuzu hesabı `Contoso\spapppool` için SPN `HTTP/sharepoint` kaydetmek için, etki alanının yöneticisi olarak bir komut isteminden aşağıdaki komutu çalıştırın:

`setspn -S HTTP/sharepoint Contoso\spapppool`

@No__t_0 komutu SPN 'YI eklemeden önce arar. SPN zaten varsa **Yinelenen BIR SPN değeri** hatası görürsünüz. Bu durumda, doğru uygulama havuzu hesabı altında ayarlanmamışsa mevcut SPN 'YI kaldırmayı göz önünde bulundurun. @No__t_0 komutunu-L seçeneğiyle çalıştırarak SPN 'nin başarıyla eklendiğini doğrulayabilirsiniz. Bu komut hakkında daha fazla bilgi edinmek için bkz. [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Bağlayıcının SharePoint uygulama havuzu hesabına eklenen SPN 'ye devredilmek için güvenilir olduğundan emin olun

KCD 'YI, Azure AD Uygulama Ara Sunucusu hizmetinin Kullanıcı kimliklerini SharePoint uygulama havuzu hesabına atayabilmesi için yapılandırın. Azure AD 'de kimlik doğrulamasından geçen kullanıcılarınız için Kerberos biletleri almak üzere uygulama proxy bağlayıcısını etkinleştirerek KCD 'yi yapılandırın. Ardından, bu sunucu bağlamı hedef uygulamaya geçirir (Bu durumda SharePoint).

KCD 'yi yapılandırmak için, her bağlayıcı makinesi için aşağıdaki adımları izleyin:

1. Etki alanı denetleyicisinde etki alanı yöneticisi olarak oturum açın ve ardından Active Directory Kullanıcıları ve Bilgisayarları ' nı açın.
1. Azure AD proxy bağlayıcısını çalıştıran bilgisayarı bulun. Bu örnekte, SharePoint sunucusudur.
1. Bilgisayara çift tıklayın ve ardından **temsili** sekmesini seçin.
1. Atama seçeneklerinin **yalnızca belirtilen hizmetlere temsilciliğini sağlamak üzere bu bilgisayara güvenecek**şekilde ayarlandığından emin olun. Ardından **herhangi bir kimlik doğrulama protokolünü kullan**' ı seçin.
1. **Ekle** düğmesini seçin, **Kullanıcılar veya bilgisayarlar**' ı seçin ve SharePoint uygulama havuzu hesabını bulun. Örneğin: `Contoso\spapppool`.
1. SPN 'Ler listesinde, daha önce hizmet hesabı için oluşturduğunuz birini seçin.
1. **Tamam** ' ı seçin ve sonra değişikliklerinizi kaydetmek için **Tamam** ' ı seçin.
  
   ![Temsili ayarları](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Artık dış URL 'YI kullanarak SharePoint 'te oturum açmaya ve Azure ile kimlik doğrulaması yapmaya hazırsınız.

## <a name="troubleshoot-sign-in-errors"></a>Oturum açma hatalarında sorun giderme

Sitede oturum açma çalışmıyorsa, bağlayıcı günlüklerinde sorun hakkında daha fazla bilgi edinebilirsiniz: bağlayıcıyı çalıştıran makineden, Olay Görüntüleyicisini açın,**Microsoft**  >   ** >  uygulamalar ve hizmetler günlüklerine gidin AadApplicationProxy**  > **Bağlayıcısı**ve **Yönetim** günlüğünü inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Uygulama Ara Sunucusu özel etki alanlarıyla çalışma](application-proxy-configure-custom-domain.md)
* [Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)
