---
title: Uygulama proxy 'Si ile Azure Active Directory içinde Kerberos tabanlı çoklu oturum açma (SSO)
description: Azure Active Directory Uygulama Ara Sunucusu kullanarak çoklu oturum açma sağlamayı ele alır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a4fdd8d16854e76cdf20d27a6048694c01de8499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253892"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Uygulama proxy 'Si ile uygulamalarınıza çoklu oturum açma (SSO) için Kerberos kısıtlı temsili

Tümleşik Windows kimlik doğrulaması ile güvenliği sağlanan uygulama proxy 'Si aracılığıyla yayımlanan şirket içi uygulamalar için çoklu oturum açma sağlayabilirsiniz. Bu uygulamalar erişim için Kerberos bileti gerektirir. Uygulama proxy 'Si, bu uygulamaları desteklemek için Kerberos kısıtlanmış temsili (KCD) kullanır. 

Kullanıcıların kimliğine bürünmek için Active Directory ' de uygulama proxy bağlayıcıları izni vererek, tümleşik Windows kimlik doğrulaması (ıWA) kullanarak uygulamalarınızda çoklu oturum açmayı etkinleştirebilirsiniz. Bağlayıcılar adına belirteç göndermek ve almak için bu izni kullanır.

## <a name="how-single-sign-on-with-kcd-works"></a>KCD ile çoklu oturum açma nasıl işe yarar?
Bu diyagramda, bir Kullanıcı ıWA kullanan bir şirket içi uygulamaya erişmeyi denediğinde akış açıklanır.

![Microsoft AAD kimlik doğrulaması akış diyagramı](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. Kullanıcı, uygulama proxy 'si aracılığıyla şirket içi uygulamaya erişmek için URL 'YI girer.
2. Uygulama proxy 'Si, isteği Azure AD kimlik doğrulama hizmetleri 'ne ön kimlik doğrulamasından göre yeniden yönlendirir. Bu noktada, Azure AD çok faktörlü kimlik doğrulaması gibi geçerli kimlik doğrulama ve yetkilendirme ilkelerini uygular. Kullanıcı doğrulandıktan sonra, Azure AD bir belirteç oluşturur ve kullanıcıya gönderir.
3. Kullanıcı belirteci uygulama proxy 'sine geçirir.
4. Uygulama proxy 'Si belirteci doğrular ve bundan sonra Kullanıcı asıl adını (UPN) alır ve bağlayıcı, kimliği doğrulanmış bir güvenli kanal aracılığıyla UPN 'yi ve hizmet asıl adı 'nı (SPN) çeker.
5. Bağlayıcı, şirket içi AD ile Kerberos kısıtlı temsili (KCD) anlaşması gerçekleştirerek, kullanıcının uygulamaya bir Kerberos belirteci almasını sağlar.
6. Active Directory, uygulamanın Kerberos belirtecini bağlayıcıya gönderir.
7. Bağlayıcı, AD 'den aldığı Kerberos belirtecini kullanarak özgün isteği uygulama sunucusuna gönderir.
8. Uygulama, yanıtı bağlayıcıya gönderir ve ardından uygulama proxy 'Si hizmetine ve son olarak kullanıcıya döndürülür.

## <a name="prerequisites"></a>Önkoşullar
IWA uygulamaları için çoklu oturum açma ile çalışmaya başlamadan önce ortamınızın aşağıdaki ayarlar ve yapılandırmalara göre hazırlandığına emin olun:

* SharePoint Web Apps gibi uygulamalarınız tümleşik Windows kimlik doğrulaması kullanacak şekilde ayarlanmıştır. Daha fazla bilgi için bkz. [Kerberos kimlik doğrulaması desteğini etkinleştirme](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11))veya SharePoint için bkz. [SharePoint 2013 'de Kerberos kimlik doğrulaması planı](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Tüm uygulamalarınızın [hizmet sorumlusu adları](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)vardır.
* Bağlayıcıyı çalıştıran sunucu ve uygulamayı çalıştıran sunucu, etki alanına katılmış ve aynı etki alanının veya güvenen etki alanlarının bir parçasıdır. Etki alanına ekleme hakkında daha fazla bilgi için bkz. [bir bilgisayarı etki alanına ekleme](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11)).
* Bağlayıcıyı çalıştıran sunucu, kullanıcılar için TokenGroupsGlobalAndUniversal özniteliğini okuma erişimine sahiptir. Bu varsayılan ayar, ortamı güvenlik sağlamlaştırma tarafından etkilendi.

### <a name="configure-active-directory"></a>Active Directory'yi yapılandırma
Active Directory yapılandırması, uygulama proxy bağlayıcısının ve uygulama sunucusunun aynı etki alanında olup olmamasına bağlı olarak değişir.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Aynı etki alanında bağlayıcı ve uygulama sunucusu
1. Active Directory ' de **Araçlar**  >  **Kullanıcılar ve bilgisayarlar**' a gidin.
2. Bağlayıcıyı çalıştıran sunucuyu seçin.
3. Sağ tıklayıp **Özellikler**  >  **temsili**' ni seçin.
4. **Bu bilgisayara yalnızca belirtilen hizmetlere temsilci seçmek için güven**'i seçin. 
5. **Herhangi bir kimlik doğrulama protokolünü kullan**'ı seçin.
6. **Bu hesabın Temsilcili kimlik bilgileri Sunsunulabileceği hizmetler** ' in altında, uygulama sunucusunun SPN kimliği için değeri ekleyin. Bu, uygulama proxy bağlayıcısının AD içindeki kullanıcıları listede tanımlanan uygulamalarla taklit etmesine olanak sağlar.

   ![Bağlayıcı-SVR Özellikler penceresi ekran görüntüsü](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Farklı etki alanlarında bağlayıcı ve uygulama sunucusu
1. Etki alanlarında KCD ile çalışmaya yönelik önkoşulların listesi için bkz. [etki alanları arasında Kerberos kısıtlanmış temsili](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11)).
2. `principalsallowedtodelegateto`Uygulama proxy 'sinden (bağlayıcı) Kerberos kimlik doğrulaması temsilcisini etkinleştirmek için Web uygulamasının hizmet hesabının (bilgisayar veya adanmış etki alanı kullanıcı hesabı) özelliğini kullanın. Uygulama sunucusu, bağlamında çalışıyor `webserviceaccount` ve temsilci sunucu `connectorcomputeraccount` . Etki alanındaki bir etki alanı denetleyicisinde (Windows Server 2012 R2 veya üstünü çalıştıran) aşağıdaki komutları çalıştırın `webserviceaccount` . Her iki hesap için düz adlar (UPN olmayan) kullanın.

   `webserviceaccount`Bir bilgisayar hesabı ise şu komutları kullanın:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   `webserviceaccount`Bir kullanıcı hesabı ise şu komutları kullanın:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma 
1. Uygulamanızı [uygulama proxy 'si Ile yayımlama](application-proxy-add-on-premises-application.md)bölümünde açıklanan yönergelere göre yayımlayın. **Ön kimlik doğrulama yöntemi** olarak **Azure Active Directory** ' yi seçtiğinizden emin olun.
2. Uygulamanız kurumsal uygulamalar listesinde görüntülendikten sonra, seçin ve **Çoklu oturum açma**' ya tıklayın.
3. Çoklu oturum açma modunu **Tümleşik Windows kimlik doğrulaması** olarak ayarlayın.  
4. Uygulama sunucusunun **Iç uygulama SPN** 'sini girin. Bu örnekte, yayımlanmış uygulamamız için SPN, http/www. contoso. com ' dır. Bu SPN 'nin, bağlayıcının temsilci kimlik bilgileri sunabildiğinden hizmet listesinde olması gerekir. 
5. Bağlayıcıyı kullanıcılar adına kullanmak için **Temsilci Oturum Açma Kimliği**’ni seçin. Daha fazla bilgi için bkz. [farklı şirket içi ve bulut kimlikleriyle çalışma](#working-with-different-on-premises-and-cloud-identities)

   ![Gelişmiş uygulama yapılandırması](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Windows dışı uygulamalar için SSO

Azure AD, bulutta kullanıcının kimliğini doğruladığında Azure AD Uygulama Ara Sunucusu Kerberos temsili akışı başlar. İstek şirket içine ulaştığında Azure AD Uygulama Ara Sunucusu Bağlayıcısı, yerel Active Directory etkileşimde bulunarak Kullanıcı adına Kerberos bileti yayınlar. Bu işlem, Kerberos kısıtlanmış temsili (KCD) olarak adlandırılır. 

Sonraki aşamada, bu Kerberos bileti ile arka uç uygulamasına bir istek gönderilir. 

Bu tür isteklerde Kerberos biletinin nasıl gönderileceğini tanımlayan çeşitli mekanizmalar vardır. Windows olmayan çoğu sunucu, SPNEGO belirteci biçiminde almayı bekler. Bu mekanizma Azure AD Uygulama Ara Sunucusu desteklenir, ancak varsayılan olarak devre dışıdır. Bir bağlayıcı SPNEGO veya standart Kerberos belirteci için yapılandırılabilir, ancak her ikisine birden uygulanamaz.

SPNEGO için bir bağlayıcı makine yapılandırırsanız, o bağlayıcı grubundaki diğer tüm bağlayıcıların de SPNEGO ile yapılandırıldığından emin olun. Standart Kerberos belirtecinin beklediği uygulamalar, SPNEGO için yapılandırılmamış diğer bağlayıcılar üzerinden yönlendirilmelidir. Bazı Web uygulamaları, yapılandırmada herhangi bir değişiklik gerektirmeden her iki biçimi de kabul eder. 
 

SPNEGO 'i etkinleştirmek için:

1. Yönetici olarak çalışan bir komut istemi açın.
2. Komut isteminden, SPNEGO gereken bağlayıcı sunucularında aşağıdaki komutları çalıştırın.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Windows olmayan uygulamalar, etki alanı e-posta adresleri yerine genellikle Kullanıcı Kullanıcı adları veya SAM hesap adları. Bu durum uygulamalarınız için geçerliyse, bulut kimliklerinizi uygulama kimliklerinizde bağlamak için yetkilendirilmiş oturum açma kimliği alanını yapılandırmanız gerekir. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Farklı şirket içi ve bulut kimlikleriyle çalışma
Uygulama proxy 'Si, kullanıcıların bulutta ve şirket içinde tam olarak aynı kimliğe sahip olduğunu varsayar. Ancak, bazı ortamlarda şirket ilkeleri veya uygulama bağımlılıkları nedeniyle, kuruluşların oturum açma için alternatif kimlikler kullanması gerekebilir. Bu gibi durumlarda, yine de KCD 'yi çoklu oturum açma için kullanmaya devam edebilirsiniz. Her uygulama için, çoklu oturum açma gerçekleştirirken hangi kimliğin kullanılması gerektiğini belirtmek üzere, **yetkilendirilmiş bir oturum açma kimliği** yapılandırın.  

Bu özellik, farklı şirket içi ve bulut kimliklerine sahip olan birçok kuruluşun, kullanıcıların farklı Kullanıcı adları ve parolalar girmelerini gerektirmeden, buluttan şirket içi uygulamalara SSO sahip olmasını sağlar. Buna aşağıdaki kuruluşlar dahildir:

* Birden çok etki alanına ( joe@us.contoso.com , joe@eu.contoso.com ) ve bulutta tek bir etki alanına sahip ( joe@contoso.com ).
* Şirket içinde yönlendirilemeyen etki alanı adı ( joe@contoso.usa ) ve geçerli bir bulut.
* Etki alanı adlarını dahili olarak kullanma (ali)
* Şirket içinde ve bulutta farklı diğer adlar kullanın. Örneğin, joe-johns@contoso.com vs. joej@contoso.com  

Uygulama proxy 'Si ile, Kerberos anahtarını almak için kullanılacak kimliği seçebilirsiniz. Bu ayar uygulama başına olur. Bu seçeneklerden bazıları e-posta adresi biçimini kabul etmayan sistemler için uygundur, diğerleri alternatif oturum açma için tasarlanmıştır.

![Temsilci oturum kimliği parametresi ekran görüntüsü](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Temsil edilen oturum açma kimliği kullanılıyorsa, bu değer kuruluşunuzdaki tüm etki alanları veya ormanlar arasında benzersiz olmayabilir. İki farklı bağlayıcı grubunu kullanarak bu uygulamaları iki kez yayımlayarak bu sorundan kaçınabilirsiniz. Her uygulamanın farklı bir Kullanıcı hedef kitlesi olduğundan, bağlayıcılarını farklı bir etki alanına katabilirsiniz.

Oturum açma kimliği için şirket **ıçı Sam hesabı adı** kullanılırsa, bağlayıcıyı barındıran bilgisayarın, Kullanıcı hesabının bulunduğu etki alanına eklenmesi gerekir.

### <a name="configure-sso-for-different-identities"></a>Farklı kimlikler için SSO 'yu yapılandırma
1. Ana kimliğin e-posta adresi (posta) olması için Azure AD Connect ayarlarını yapılandırın. Bu, eşitleme ayarlarındaki **Kullanıcı asıl adı** alanını değiştirerek özelleştirme işleminin bir parçası olarak yapılır. Bu ayarlar ayrıca, kullanıcıların Office365, windows10 cihazlarda ve Azure AD 'yi kimlik deposu olarak kullanan diğer uygulamalarda oturum açmasını da tespit ediyor.  
   ![Kullanıcıları tanımlama ekran görüntüsü-Kullanıcı asıl adı açılan kutusu](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Değiştirmek istediğiniz uygulamanın uygulama yapılandırma ayarları ' nda, kullanılacak **atanmış oturum açma kimliğini** seçin:

   * Kullanıcı asıl adı (örneğin, joe@contoso.com )
   * Alternatif Kullanıcı asıl adı (örneğin, joed@contoso.local )
   * Kullanıcı asıl adının Kullanıcı adı parçası (örneğin, ali)
   * Alternatif Kullanıcı asıl adının Kullanıcı adı bölümü (örneğin, joed)
   * Şirket içi SAM hesap adı (etki alanı denetleyicisi yapılandırmasına bağlıdır)

### <a name="troubleshooting-sso-for-different-identities"></a>Farklı kimlikler için SSO sorunlarını giderme
SSO işleminde bir hata varsa, [sorun giderme](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)bölümünde açıklandığı gibi bağlayıcı makinesi olay günlüğünde görüntülenir.
Ancak, bazı durumlarda, bu uygulama diğer HTTP yanıtlarına yanıt verdiğinde, istek arka uç uygulamasına başarıyla gönderilir. Bu durumların giderilmesi, uygulama proxy 'Si oturumu olay günlüğündeki bağlayıcı makinesinde 24029 olay numarasını inceleyerek başlamalıdır. Yetkilendirme için kullanılan Kullanıcı kimliği, olay ayrıntıları içindeki "Kullanıcı" alanında görüntülenir. Oturum günlüğünü açmak için Olay Görüntüleyicisi Görünüm menüsünde **analitik ve hata ayıklama günlüklerini göster** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Kerberos kısıtlanmış temsilciyi kullanmak için bir uygulama proxy uygulaması yapılandırma](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Uygulama Ara Sunucusu ile ilgili sorunları giderme](application-proxy-troubleshoot.md)