---
title: Uygulama proxy'si ile çoklu oturum açma | Microsoft Docs
description: Tek Azure AD uygulama ara sunucusu kullanarak oturum açmayı sağlamak nasıl etkinleştireceğinizi de açıklar.
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
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab378fe1e06de49df0fe6481a1aa475d426648dc
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032565"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos kısıtlanmış temsil için çoklu oturum açma uygulamalarınıza uygulama ara sunucusu ile

Şirket içi için çoklu oturum açma sağlayabilir yayımlanan uygulamaları aracılığıyla uygulama proxy'si tümleşik Windows kimlik doğrulaması ile güvenli hale getirilir. Bu uygulamalara erişim için bir Kerberos anahtarı gerektirir. Uygulama proxy'si, bu uygulamaları desteklemek için Kerberos Kısıtlı temsilci (KCD) kullanır. 

Kullanıcının kimliğine bürünmek için Active Directory Uygulama Proxy bağlayıcıları izni vererek tümleşik Windows kimlik doğrulaması (IWA) kullanarak uygulamalarınızı çoklu oturum açmayı etkinleştirebilirsiniz. Bağlayıcılar, göndermek ve kendileri adına belirteçlerini almak için bu izni kullanın.

## <a name="how-single-sign-on-with-kcd-works"></a>KCD works ile nasıl çoklu oturum açma
Bu diyagramda, bir Kullanıcı ıWA kullanan bir şirket içi uygulamaya erişmeyi denediğinde akış açıklanır.

![Microsoft AAD kimlik doğrulaması akışı diyagramı](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Kullanıcı, uygulama proxy 'si aracılığıyla şirket içi uygulamaya erişmek için URL 'YI girer.
2. Uygulama Ara sunucusu için Azure AD kimlik doğrulama hizmetleri preauthenticate için istek yönlendirir. Bu noktada, Azure AD, tüm geçerli kimlik doğrulama ve yetkilendirme ilkeleri, çok faktörlü kimlik doğrulaması gibi uygulanır. Kullanıcı doğrulanmış olması durumunda, Azure AD belirteç oluşturulur ve kullanıcıya gönderir.
3. Kullanıcı belirteci uygulama ara sunucusuna iletir.
4. Uygulama proxy 'Si belirteci doğrular ve bundan sonra Kullanıcı asıl adını (UPN) alır ve bağlayıcı, kimliği doğrulanmış bir güvenli kanal aracılığıyla UPN 'yi ve hizmet asıl adı 'nı (SPN) çeker.
5. Bağlayıcı, şirket içi AD ile Kerberos kısıtlı temsili (KCD) anlaşması gerçekleştirerek, kullanıcının uygulamaya bir Kerberos belirteci almasını sağlar.
6. Active Directory Uygulama bağlayıcısı için Kerberos belirteci gönderir.
7. Bağlayıcı, özgün istek AD'den alınan Kerberos belirteci kullanarak uygulama sunucusuna gönderir.
8. Uygulama için uygulama proxy'si hizmeti ardından döndürülen bağlayıcı yanıta gönderir ve son kullanıcı.

## <a name="prerequisites"></a>Önkoşullar
IWA uygulamalar için çoklu oturum açma ile çalışmaya başlamadan önce aşağıdaki ayarları ve yapılandırmaları ile ortamınızı hazır olduğundan emin olun:

* Uygulamalarınızı, SharePoint Web uygulamaları gibi tümleşik Windows kimlik doğrulaması kullanacak şekilde ayarlanmıştır. Daha fazla bilgi için [Kerberos kimlik doğrulaması için desteği etkinleştirme](https://technet.microsoft.com/library/dd759186.aspx), ya da SharePoint için bkz. [SharePoint 2013'te Kerberos kimlik doğrulaması için planlama](https://technet.microsoft.com/library/ee806870.aspx).
* Tüm uygulamalarınızın sahip [hizmet asıl adları](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Bağlayıcıyı çalıştıran sunucu ve uygulama çalıştıran sunucunun etki alanına katılan ve aynı etki alanında veya güvenilen etki alanlarını bir parçası olan. Etki alanına katılma hakkında daha fazla bilgi için bkz. [bilgisayarı bir etki alanına](https://technet.microsoft.com/library/dd807102.aspx).
* Bağlayıcısını çalıştıran sunucuda TokenGroupsGlobalAndUniversal öznitelik kullanıcılar için okuma erişimi var. Bu varsayılan ayarı ortam sağlamlaştırma güvenlik tarafından etkilenmiş.

### <a name="configure-active-directory"></a>Active Directory'yi yapılandırma
Active Directory yapılandırması, uygulama ara sunucusu bağlayıcısını ve uygulama sunucusu aynı etki alanında veya olmanıza bağlı olarak değişir.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Bağlayıcı ve uygulama sunucusu aynı etki alanında
1. Active Directory'de Git **Araçları** > **kullanıcıları ve Bilgisayarları**.
2. Bağlayıcısını çalıştıran sunucuyu seçin.
3. Sağ tıklayıp **özellikleri** > **temsilci**.
4. Seçin **bu bilgisayara yalnızca belirtilen hizmetlere temsilci seçmek için güven**. 
5. **Herhangi bir kimlik doğrulama protokolünü kullan**' ı seçin.
6. Altında **için bu hesabın temsilci seçilen kimlik bilgilerini sunacağı Hizmetler** uygulama sunucusunun SPN kimlik için bir değer ekleyin. Bu listede tanımlanan uygulamalarla AD'de kullanıcının kimliğine bürünmek uygulama ara sunucusu Bağlayıcısı sağlar.

   ![Bağlayıcı SVR Özellikler penceresi ekran görüntüsü](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Bağlayıcı ve uygulama sunucusu farklı etki alanlarında
1. KCD ile çalışma alanlarında önkoşullarının listesi için bkz. [etki alanlarında Kerberos kısıtlanmış temsil](https://technet.microsoft.com/library/hh831477.aspx).
2. Kullanım `principalsallowedtodelegateto` bağlayıcı sunucusu için temsilci seçmek uygulama proxy'sini etkinleştirmek için bağlayıcı sunucusu özelliği. Uygulama sunucusu olan `sharepointserviceaccount` ve temsilci sunucusu `connectormachineaccount`. Windows 2012 R2 için örnek olarak bu kodu kullanın:

```powershell
$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
```

`sharepointserviceaccount`SPS makine hesabı veya SPS uygulama havuzunun üzerinde çalıştığı bir hizmet hesabı olabilir.

## <a name="configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma 
1. Açıklanan yönergelere göre uygulamanızı yayımlayın [uygulama ara sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md). Seçtiğinizden emin olun **Azure Active Directory** olarak **ön kimlik doğrulama yöntemi**.
2. Uygulamanızı kurumsal uygulamalar listesinde göründükten sonra seçin ve **çoklu oturum açma**.
3. Çoklu oturum açma modu ayarlamak **tümleşik Windows kimlik doğrulaması**.  
4. Girin **iç uygulama SPN'si** uygulama sunucusunun. Bu örnekte, http/www.contoso.com yayımlanan uygulamamız için SPN'dir. Bu SPN, istediğiniz bağlayıcıyı temsilci seçilen kimlik bilgilerini sunacağı Hizmetler listesinde olması gerekiyor. 
5. Seçin **temsilci oturum açma kimliği** Bağlayıcısı kullanıcılarınız adına kullanın. Daha fazla bilgi için [farklı şirket içi ve bulut kimlikleri ile çalışma](#working-with-different-on-premises-and-cloud-identities)

   ![Gelişmiş uygulama yapılandırması](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Windows olmayan uygulamaları için SSO

Azure AD bulut kullanıcı kimliği doğruladığında Azure AD uygulama proxy'si Kerberos temsilci akışında başlatır. İstek, şirket içi geldikten sonra Azure AD uygulama ara sunucusu Bağlayıcısı'nı yerel Active Directory ile etkileşim kurarak kullanıcı adına bir Kerberos anahtarı verir. Bu işlem, Kerberos Kısıtlı temsilci (KCD) olarak adlandırılır. Sonraki aşamada, bu Kerberos anahtarı ile arka uç uygulaması için bir istek gönderilir. 

Bu tür istekleri göndermek nasıl tanımlayan birçok Protokolü vardır. Çoğu Windows olmayan sunucuları ile SPNEGO anlaşma beklenir. Bu protokol, Azure AD uygulama ara sunucusu desteklenir, ancak varsayılan olarak devre dışıdır. Bir sunucu SPNEGO veya standart KCD için yapılandırılmış, ancak ikisiyle olabilir.

Bağlayıcı makinesinde SPNEGO için yapılandırırsanız, diğer tüm bağlayıcılar, bağlayıcı grubunda SPNEGO ile yapılandırılmış olduğundan emin olun. Uygulama standart KCD SPNEGO için yapılandırılmamış olan diğer bağlayıcılar üzerinden yönlendirilmesi.
 

SPNEGO etkinleştirmek için:

1. Yönetici olarak çalıştırılan bir komut istemi açın.
2. Komut İstemi'nden SPNEGO gereken bağlayıcı sunucuları üzerinde aşağıdaki komutları çalıştırın.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Kerberos hakkında daha fazla bilgi için bkz: [tüm Kerberos Kısıtlı temsilci (KCD) hakkında bilmek istediğiniz](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Windows olmayan uygulamalar genellikle kullanıcı kullanıcı adını veya etki alanı yerine SAM hesabı adları e-posta adresleri. Bu durum, uygulamalarınız için geçerliyse, uygulama kimliklerinizi, bulut kimlikleri bağlamak için temsilci oturum açma kimlik alanı'nı yapılandırmanız gerekir. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Farklı şirket içi ve bulut kimlikleri ile çalışma
Uygulama proxy'si, kullanıcıların bulutta ve şirket içinde tam olarak aynı kimliğe sahip olduğunu varsayar. Ancak, bazı ortamlarda şirket ilkeleri veya uygulama bağımlılıkları nedeniyle, kuruluşların oturum açma için alternatif kimlikler kullanması gerekebilir. Bu gibi durumlarda, yine de KCD 'yi çoklu oturum açma için kullanmaya devam edebilirsiniz. Yapılandırma bir **oturum açma kimlik temsilcisi** her uygulamanın çoklu oturum açma yapılırken hangi kimlik kullanılması gerektiğini belirtin.  

Bu özellik, farklı şirket içi ve bulut kimlikleri SSO kullanıcıların farklı kullanıcı adları ve parolaları girmeye gerek kalmadan buluttan şirket içi uygulamalara sahip olan birçok kuruluş sağlar. Bu, kuruluşların içerir:

* Dahili olarak birden çok etki alanınız (joe@us.contoso.com, joe@eu.contoso.com) hem de bulutta tek bir etki alanı (joe@contoso.com).
* Dahili olarak yönlendirilemeyen etki alanı adına sahip (joe@contoso.usa) ve yasal bir bulutta.
* Etki alanı adları iç kullanmayın (ALi)
* Şirket içinde ve bulutta farklı diğer adlar kullanın. Örneğin, joe-johns@contoso.com vs. joej@contoso.com  

Uygulama proxy'si, Kerberos anahtarını almak için kullanılacak kimliği seçebilirsiniz. Bu ayar bir uygulamadır. Bu seçeneklerden bazısı e-posta adresi biçimi kabul sistemler için uygundur, diğer alternatif oturum açma için tasarlanmıştır.

![Temsilci oturum açma kimlik parametresi ekran görüntüsü](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Temsilci oturum açma kimliği kullanılırsa, değeri etki alanları veya kuruluşunuzdaki ormanlar arasında benzersiz olmayabilir. İki kez iki farklı bağlayıcı grupları kullanarak bu uygulamaları yayımlayarak, bu sorunu önleyebilirsiniz. Her uygulama farklı bir kullanıcı bir dinleyici olduğundan, kendi bağlayıcılar farklı bir etki alanına katılmasını sağlayabilirsiniz.

### <a name="configure-sso-for-different-identities"></a>SSO için farklı kimliklere yapılandırın
1. Asıl kimlik e-posta adresi (posta), bu nedenle, Azure AD Connect ayarlarını yapılandırın. Bu özelleştirme işleminin bir parçası değiştirerek yapılır **kullanıcı asıl adı** alanındaki eşitleme ayarları. Bu ayarlar, ayrıca kullanıcıların Office 365, Windows 10 cihazları nasıl oturum belirler ve bunların kimlik deposu olarak Azure AD'yi kullanan diğer uygulamalar.  
   ![Kullanıcıların ekran görüntüsü - kullanıcı asıl adı açılan tanımlama](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Değiştirmek istediğiniz uygulama için uygulama yapılandırma ayarlarında seçin **temsilci oturum açma kimliği** kullanılacak:

   * Kullanıcı asıl adı (örneğin, joe@contoso.com)
   * Diğer kullanıcı asıl adı (örneğin, joed@contoso.local)
   * Kullanıcı adı, kullanıcı asıl adı (örneğin, ALi) parçası
   * Kullanıcı adı bölümü diğer kullanıcı asıl adı (örneğin, joed)
   * Şirket içi SAM hesabı adı (etki alanı denetleyicisi yapılandırmasına bağlıdır)

### <a name="troubleshooting-sso-for-different-identities"></a>SSO için farklı kimliklere sorunlarını giderme
SSO işleminde bir hata varsa, bağlayıcı makine olay günlüğünde açıklandığı gibi göründüğü [sorun giderme](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Ancak, bu uygulama diğer çeşitli HTTP yanıtlarında yanıtlar karşın bazı durumlarda, istek başarıyla arka uç uygulaması için gönderilir. Bu gibi durumlarda sorun giderme bağlayıcı makinede uygulama ara sunucusu oturum olay günlüğündeki olay numarası 24029 inceleyerek başlamanız gerekir. Olay Ayrıntıları içinde "kullanıcı" alanında temsilci seçme için kullanılan kullanıcı kimliğini görünür. Oturum açma açmak için **Göster Analitik ve hata ayıklama günlüklerini** Olay Görüntüleyicisi'ni Görünüm menüsünde.

## <a name="next-steps"></a>Sonraki adımlar

* [Kerberos kısıtlanmış temsili kullanmak üzere bir uygulama proxy'si uygulaması yapılandırma](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Uygulama Ara sunucusu ile ilgili sorunları giderme](application-proxy-troubleshoot.md)


En yeni haberler ve güncelleştirmeler için [Uygulama Ara Sunucusu bloguna](https://blogs.technet.com/b/applicationproxyblog/) göz atın

