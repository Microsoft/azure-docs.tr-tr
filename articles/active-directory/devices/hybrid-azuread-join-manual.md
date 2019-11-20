---
title: Hibrit Azure Active Directory'ye katılmış cihazları elle yapılandır | Microsoft Docs
description: Karma Azure Active Directory katılmış cihazları el ile yapılandırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed28b4bb8ec61455168f50058c8cdcaf9f50717d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882844"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Öğretici: Hibrit Azure Active Directory'ye katılmış cihazları elle yapılandırma

Azure Active Directory (Azure AD) ' de cihaz yönetimiyle, Kullanıcıların kaynaklarınıza güvenlik ve uyumluluk için standartlarınızı karşılayan cihazlarınızla erişmesini sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory cihaz yönetimine giriş](overview.md).

> [!TIP]
> Azure AD Connect kullanmak sizin için bir seçenek ise, [yönetilen](hybrid-azuread-join-managed-domains.md) veya [Federasyon](hybrid-azuread-join-federated-domains.md) etki alanları için ilgili öğreticilere bakın. Azure AD Connect kullanarak, karma Azure AD JOIN 'in yapılandırmasını önemli ölçüde kolaylaştırabilirsiniz.

Şirket içi Active Directory ortamınız varsa ve etki alanınıza katılmış cihazları Azure AD'ye katmak istiyorsanız hibrit Azure AD'ye katılmış cihazları yapılandırarak bunu gerçekleştirebilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Karma Azure AD katılımı el ile yapılandırın
> * Hizmet bağlantı noktası yapılandırma
> * Taleplerin verilmesini ayarlama
> * Windows alt düzey cihazlarını etkinleştirme
> * Katılmış cihazları doğrulama
> * Uygulamanızda sorun giderme

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide hakkında bilgi sahibi olduğunuz varsayılmaktadır:

* [Azure Active Directory'de cihaz yönetimine giriş](../device-management-introduction.md)
* [Hibrit Azure Active Directory JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
* [Cihazlarınızın hibrit Azure AD katılımını denetleme](hybrid-azuread-join-control.md)

Kuruluşunuzda karma Azure AD 'ye katılmış cihazları etkinleştirmeye başlamadan önce şunları yaptığınızdan emin olun:

* Azure AD Connect güncel bir sürümünü çalıştırıyorsunuz.
* Azure AD Connect, hibrit Azure AD 'nin Azure AD 'ye katılmış olmasını istediğiniz cihazların bilgisayar nesnelerini eşitlemiştir. Bilgisayar nesneleri belirli kuruluş birimlerine (OU) aitse, bu OU 'Ların de Azure AD Connect eşitleme için yapılandırılması gerekir.

Azure AD Connect:

* Şirket içi Active Directory örneğindeki bilgisayar hesabı ve Azure AD 'deki cihaz nesnesi arasındaki ilişkilendirmeyi korur.
* Iş için Windows Hello gibi cihazla ilgili diğer özellikleri de sunar.

Bilgisayarların Azure AD 'ye kaydı için kuruluşunuzun ağındaki bilgisayarlardan aşağıdaki URL 'Lerin erişilebilir olduğundan emin olun:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* Kuruluşunuzun STS 'si (federe etki alanları için), kullanıcının yerel intranet ayarlarına dahil edilmelidir

Kuruluşunuz sorunsuz SSO kullanmayı planlıyorsa, aşağıdaki URL 'nin kuruluşunuzdaki bilgisayarlardan erişilebilir olması gerekir. Ayrıca, kullanıcının yerel intranet bölgesine de eklenmelidir.

* `https://autologon.microsoftazuread-sso.com`

Ayrıca kullanıcının intranet bölgesinde aşağıdaki ayarların etkinleştirilebilmesi gereklidir: "Betikle durum çubuğu güncelleştirmelerine izin ver".

Kuruluşunuz şirket içi Active Directory ile yönetilen (Federe olmayan) kurulumu kullanıyorsa ve Azure AD ile federasyona eklemek için Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanmıyorsa, Windows 10 ' da karma Azure AD birleştirme, etkin olan bilgisayar nesnelerine bağımlıdır Azure AD ile eşitlenecek dizin. Karma Azure AD 'ye katılması gereken bilgisayar nesnelerini içeren tüm OU 'Ların Azure AD Connect eşitleme yapılandırmasında eşitleme için etkinleştirildiğinden emin olun.

Sürüm 1703 veya önceki sürümlerde Windows 10 cihazlarında, kuruluşunuz giden bir ara sunucu üzerinden internet erişimi gerektiriyorsa, Windows 10 bilgisayarlarının Azure AD 'ye kaydolmasının etkinleştirilmesi için Web proxy otomatik bulma (WPAD) uygulamanız gerekir.

Windows 10 1803 ile başlayarak, Federasyon etki alanındaki bir cihaz tarafından AD FS aracılığıyla bir karma Azure AD katılımı denemesi başarısız olur ve Azure AD Connect bilgisayar/cihaz nesnelerini Azure AD 'ye eşitlemek üzere yapılandırılmışsa, cihaz, eşitlenen bilgisayarı/cihazı kullanarak karma Azure AD JOIN 'i tamamlamaya çalışır.

Cihazın sistem hesabı altında yukarıdaki Microsoft kaynaklarına erişip erişemediğinizi doğrulamak için, [test cihazı kayıt bağlantı](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) betiği ' ni kullanabilirsiniz.

## <a name="verify-configuration-steps"></a>Yapılandırma adımlarını doğrulama

Hibrit Azure AD'ye katılmış cihazları çeşitli türlerdeki Windows cihazı platformları için yapılandırabilirsiniz. Bu konu, tüm tipik yapılandırma senaryoları için gereken adımları içerir.  

Senaryonuz için gereken adımlara ilişkin genel bir bakış elde etmek için aşağıdaki tabloyu kullanın:  

| Adımlar | Windows geçerli ve parola karması eşitleme | Windows geçerli ve federasyon | Windows alt düzey |
| :--- | :---: | :---: | :---: |
| Hizmet bağlantı noktasını yapılandırma | ![İşaretli][1] | ![İşaretli][1] | ![İşaretli][1] |
| Taleplerin verilmesini ayarlama |     | ![İşaretli][1] | ![İşaretli][1] |
| Windows 10 olmayan cihazları etkinleştirme |       |        | ![İşaretli][1] |
| Katılmış cihazları doğrulama | ![İşaretli][1] | ![İşaretli][1] | [Denetlemez][1] |

## <a name="configure-a-service-connection-point"></a>Hizmet bağlantı noktası yapılandırma

Cihazlarınız Azure AD kiracı bilgilerini bulmaya yönelik kayıt sırasında bir hizmet bağlantı noktası (SCP) nesnesi kullanır. Şirket içi Active Directory Örneğinizde, karma Azure AD 'ye katılmış cihazların SCP nesnesi, bilgisayarın ormanının yapılandırma adlandırma bağlamı bölümünde bulunmalıdır. Orman başına yalnızca bir yapılandırma adlandırma bağlamı bulunur. Çok ormanlı bir Active Directory yapılandırmasında, hizmet bağlantı noktası, etki alanına katılmış bilgisayarlar içeren tüm ormanlarda bulunmalıdır.

Ormanınızın yapılandırma adlandırma bağlamını almak için [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) cmdlet öğesini kullanabilirsiniz.  

Active Directory etki alanı adı *fabrikam.com* olan bir orman için yapılandırma adlandırma bağlamı:

`CN=Configuration,DC=fabrikam,DC=com`

Ormanınızda, etki alanına katılmış cihazların otomatik kaydına ilişkin SCP nesnesi şu konumdadır:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Azure AD Connect nasıl dağıttığınıza bağlı olarak, SCP nesnesi zaten yapılandırılmış olabilir.
Aşağıdaki Windows PowerShell betiğini kullanarak nesnenin varlığını doğrulayabilirsiniz ve bulma değerlerini alabilirsiniz:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

**$SCP. Anahtar sözcük** çıkışları, Azure AD kiracı bilgilerini gösterir. Bir örneği aşağıda verilmiştir:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Hizmet bağlantı noktası mevcut değilse Azure AD Connect sunucunuzda `Initialize-ADSyncDomainJoinedComputerSync` cmdlet öğesini çalıştırarak oluşturabilirsiniz. Bu cmdlet 'i çalıştırmak için Kuruluş Yöneticisi kimlik bilgileri gereklidir.  

cmdlet:

* Azure AD Connect bağlandığı Active Directory ormanında hizmet bağlantı noktasını oluşturur.
* `AdConnectorAccount` parametresini belirtmenizi gerektirir. Bu hesap, Azure AD Connect Active Directory bağlayıcı hesabı olarak yapılandırılır.


Aşağıdaki betikte, cmdlet kullanımına ilişkin bir örnek gösterilmektedir. Bu betikte, `$aadAdminCred = Get-Credential` bir kullanıcı adı yazmanızı gerektirir. Kullanıcı asıl adı (UPN) biçiminde kullanıcı adı sağlamanız gerekir (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

`Initialize-ADSyncDomainJoinedComputerSync` cmdlet:

* Active Directory PowerShell modülünü ve Azure Active Directory Domain Services (Azure AD DS) araçlarını kullanır. Bu araçlar, bir etki alanı denetleyicisinde çalışan Active Directory Web hizmetlerini kullanır. Active Directory Web Hizmetleri Windows Server 2008 R2 ve sonraki sürümleri çalıştıran etki alanı denetleyicilerinde desteklenir.
* Yalnızca MSOnline PowerShell modülü sürüm 1.1.166.0 tarafından desteklenir. Bu modülü indirmek için [Bu bağlantıyı](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/)kullanın.
* AD DS Araçları yüklü değilse, `Initialize-ADSyncDomainJoinedComputerSync` başarısız olur. AD DS araçlarını, **özellikler** > **uzak sunucu yönetim araçları** > **rol yönetim araçları**altında Sunucu Yöneticisi aracılığıyla yükleyebilirsiniz.

Windows Server 2008 veya önceki sürümlerini çalıştıran etki alanı denetleyicileri için, hizmet bağlantı noktasını oluşturmak üzere aşağıdaki betiği kullanın. Çok ormanlı bir yapılandırmada, bilgisayarların mevcut olduğu her ormanda hizmet bağlantı noktasını oluşturmak için aşağıdaki betiği kullanın.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

Önceki betikte, `$verifiedDomain = "contoso.com"` yer tutucudur. Azure AD 'de doğrulanmış etki alanı adlarından biriyle değiştirin. Kullanabilmeniz için etki alanına sahip olmanız gerekir.

Doğrulanmış etki alanı adları hakkında daha fazla bilgi için, [Azure Active Directory için özel etki alanı adı ekleme](../active-directory-domains-add-azure-portal.md)bölümüne bakın.

Doğrulanmış şirket etki alanlarınızın bir listesini edinmek için [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) cmdlet öğesini kullanabilirsiniz.

![Şirket etki alanlarının listesi](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Taleplerin verilmesini ayarlama

Federasyon Azure AD yapılandırmasında, cihazlar Azure AD kimlik doğrulaması için bir Microsoft iş ortağından AD FS veya şirket içi Federasyon Hizmeti 'ni kullanır. Cihazlar, Azure Active Directory Device Registration Service (Azure DRS) kullanarak kayıt için erişim belirteci almak üzere kimlik doğrulaması yapar.

Windows geçerli cihazlar, tümleşik Windows kimlik doğrulamasını şirket içi Federasyon Hizmeti tarafından barındırılan etkin bir WS-Trust uç noktasına (1,3 veya 2005 sürüm) kullanarak kimlik doğrular.

AD FS kullanırken, aşağıdaki WS-Trust uç noktalarını etkinleştirmeniz gerekir
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Hem **ADFS/Service/Trust/2005/windowstransport** ya da **ADFS/Services/Trust/13/windowstransport** , yalnızca intranet 'e yönelik uç noktalar olarak etkinleştirilmelidir ve Web uygulaması ara sunucusu aracılığıyla extranet 'e yönelik uç noktalar olarak gösterilmemelidir. WS-Trust Windows uç noktalarını devre dışı bırakma hakkında daha fazla bilgi için, bkz. [proxy 'de WS-Trust Windows uç noktalarını devre dışı bırakma](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). **Hizmet** > **uç noktaları**altında AD FS Yönetim Konsolu aracılığıyla hangi uç noktaların etkinleştirildiğini görebilirsiniz.

> [!NOTE]
>Şirket içi Federasyon hizmetiniz olarak AD FS yoksa, WS-Trust 1,3 veya 2005 uç noktalarını desteklediklerinden ve bunların meta veri değişim dosyası (MEX) üzerinden yayımlandıklarından emin olmak için satıcınızdan yönergeleri izleyin.

Cihaz kaydının tamamlanabilmesi için, Azure DRS 'nin aldığı belirteçte aşağıdaki talepler bulunmalıdır. Azure DRS, bu bilgilerden bazıları için Azure AD 'de bir cihaz nesnesi oluşturur. Azure AD Connect, yeni oluşturulan cihaz nesnesini Şirket içindeki bilgisayar hesabıyla ilişkilendirmek için bu bilgileri kullanır.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Birden fazla doğrulanmış etki alanı adınız varsa bilgisayarlar için aşağıdaki talebi sağlamanız gerekir:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Zaten bir ımutableıd talebi veriyorsanız (örneğin, alternatif oturum açma KIMLIĞI), bilgisayarlar için karşılık gelen bir talep sağlamanız gerekir:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Aşağıdaki bölümlerde, şu konular hakkında bilgiler edinebilirsiniz:

* Her talebin sahip olması gereken değerler.
* Bir tanım AD FS nasıl görünür?.

Tanım, değerlerin mevcut olup olmadığını veya bunları oluşturmanızın gerekip gerekmediğini doğrulamanıza yardımcı olur.

> [!NOTE]
> Şirket içi federasyon sunucunuz için AD FS kullanmıyorsanız bu talepleri vermek üzere uygun yapılandırmayı oluşturmak için satıcınızın talimatlarını uygulayın.

### <a name="issue-account-type-claim"></a>Hesap türü talep verme

`http://schemas.microsoft.com/ws/2012/01/accounttype` talebi, cihazı etki alanına katılmış bir bilgisayar olarak tanımlayan bir **DJ**değeri içermelidir. AD FS'de, aşağıdaki gibi görünen bir verme aktarım kuralı ekleyebilirsiniz:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Şirket içi bilgisayar hesabının objectGUID değerini verme

`http://schemas.microsoft.com/identity/claims/onpremobjectguid` talebi, şirket içi bilgisayar hesabının **Objectguıd** değerini içermelidir. AD FS'de, aşağıdaki gibi görünen bir verme aktarım kuralı ekleyebilirsiniz:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Şirket içi bilgisayar hesabının objectSID değerini verme

`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` talebi, şirket içi bilgisayar hesabının **objectSID** değerini içermelidir. AD FS'de, aşağıdaki gibi görünen bir verme aktarım kuralı ekleyebilirsiniz:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Azure AD 'de birden çok doğrulanmış etki alanı adı olduğunda bilgisayar için ıssuerıd sorun

`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` talebi, belirteci veren şirket içi Federasyon Hizmeti (AD FS veya iş ortağı) ile bağlanan doğrulanmış etki alanı adlarından herhangi birinin Tekdüzen Kaynak tanımlayıcısını (URI) içermelidir. AD FS ' de, yukarıdaki gibi, belirli bir sıraya göre aşağıdaki gibi görünen verme dönüştürme kuralları ekleyebilirsiniz. Kullanıcılara kuralı açıkça vermek için bir kuralın gerekli olduğunu unutmayın. Aşağıdaki kurallarda, Kullanıcı ve bilgisayar kimlik doğrulamasını tanımlayan bir ilk kural eklenir.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

Önceki talep içinde `<verified-domain-name>` yer tutucudur. Azure AD 'de doğrulanmış etki alanı adlarından biriyle değiştirin. Örneğin, `Value = "http://contoso.com/adfs/services/trust/"`kullanın.

Doğrulanmış etki alanı adları hakkında daha fazla bilgi için, [Azure Active Directory için özel etki alanı adı ekleme](../active-directory-domains-add-azure-portal.md)bölümüne bakın.  

Doğrulanmış şirket etki alanlarınızın bir listesini edinmek için [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet öğesini kullanabilirsiniz.

![Şirket etki alanlarının listesi](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Kullanıcılar için (örneğin, diğer bir oturum açma KIMLIĞI ayarlandığında) bilgisayar için ImmutableID verme

`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` talebi bilgisayarlar için geçerli bir değer içermelidir. AD FS'de aşağıda şekilde verme aktarım kuralı oluşturabilirsiniz:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>AD FS verme aktarım kuralları oluşturmak üzere yardımcı betik

Aşağıdaki betik, daha önce açıklanan verme dönüştürme kurallarının oluşturulmasına yardımcı olur.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Açıklamalar

* Bu betik, kuralları mevcut kurallara ekler. Kural kümesi iki kez eklenebildiğinden, betiği iki kez çalıştırmayın. Betiği yeniden çalıştırmadan önce bu talepler için hiçbir karşılık gelen kural olmadığından emin olun (karşılık gelen koşullar bölümünde).
* Birden çok doğrulanmış etki alanı adı (Azure AD portalında veya **Get-MsolDomain** cmdlet 'i aracılığıyla gösterildiği gibi) varsa, betikteki **$multipleVerifiedDomainNames** değerini **$true**olarak ayarlayın. Ayrıca, Azure AD Connect veya başka yollarla oluşturulmuş olabilecek mevcut **ıssuerıd** talebini kaldırdığınızdan emin olun. Bu kural için bir örnek aşağıda verilmiştir:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Kullanıcı hesapları için zaten bir **ImmutableID** talebi verdiyseniz betikteki **$immutableIDAlreadyIssuedforUsers** değerini **$true** olarak ayarlayın.

## <a name="enable-windows-down-level-devices"></a>Windows alt düzey cihazlarını etkinleştirme

Bazı etki alanına katılmış cihazlar Windows alt düzey cihazlarıysa şunları gerçekleştirmeniz gerekir:

* Kullanıcıların cihazları kaydedebilmesini sağlamak için Azure AD'de bir ilke ayarlayın.
* Şirket içi Federasyon hizmetinizi, cihaz kaydı için tümleşik Windows kimlik doğrulamasını (ıWA) destekleyecek talepler verecek şekilde yapılandırın.
* Cihazın kimliğini doğrularken sertifika istemlerinin önüne geçmek için Azure AD cihaz kimlik doğrulaması uç noktasını yerel intranet bölgelerine ekleyin.
* Windows alt düzey cihazlarını denetleme.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Kullanıcıların cihaz kaydetmesini sağlamak için Azure AD 'de bir ilke ayarlama

Windows alt düzey cihazlarını kaydetmek için kullanıcıların Azure AD 'ye cihaz kaydetmesine izin ver ayarının etkinleştirildiğinden emin olun. Azure portal bu ayarı, > **Kullanıcılar ve gruplar** > **cihaz ayarları** **Azure Active Directory** altında bulabilirsiniz.

Aşağıdaki ilke **tümüne**ayarlanmış olmalıdır: **KULLANıCıLAR cihazlarını Azure AD 'ye kaydedebilir**.

![Kullanıcıların cihaz kaydetmesini sağlayan tümü düğmesi](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Şirket içi Federasyon hizmetini yapılandırma

Şirket içi Federasyon Hizmetiniz, aşağıdaki kodlanmış değere sahip bir resource_params parametresi tutan Azure AD bağlı olan tarafa bir kimlik doğrulama isteği aldığında, **AuthenticationMethod** ve **wiaormultiauthn** taleplerini vermeyi desteklemelidir:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Böyle bir istek geldiğinde, şirket içi Federasyon Hizmeti 'nin tümleşik Windows kimlik doğrulaması kullanarak kullanıcının kimlik doğrulaması gerekir. Kimlik doğrulaması başarılı olduğunda, Federasyon hizmetinin aşağıdaki iki talebi vermesi gerekir:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

AD FS, kimlik doğrulama yönteminden geçen bir verme dönüşüm kuralı eklemeniz gerekir. Bu kuralı eklemek için:

1. AD FS Yönetim konsolunda, **bağlı olan taraf güvenleri** > **AD FS** > **güven ilişkileri** ' ne gidin.
1. Microsoft Office 365 Identity Platform bağlı olan taraf güven nesnesine sağ tıklayın ve ardından **Talep Kurallarını Düzenle** seçeneğini belirleyin.
1. **Verme Aktarım Kuralları** sekmesinde, **Kural Ekle** seçeneğini belirleyin.
1. **Talep kuralı** şablon listesinde, **Talepleri Özel Kural Kullanarak Gönder** seçeneğini belirleyin.
1. **İleri**’yi seçin.
1. **Talep kuralı adı** kutusuna **AUTH yöntemi talep kuralı**' nı girin.
1. **Talep kuralı** kutusuna aşağıdaki kuralı girin:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Federasyon sunucunuzda aşağıdaki PowerShell komutunu girin. **\<RPObjectName\>** değerini, Azure AD bağlı olan taraf güveni Nesnenizin bağlı olan taraf nesne adıyla değiştirin. Bu nesne genellikle **Microsoft Office 365 Identity Platform** olarak adlandırılır.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Azure AD cihaz kimlik doğrulama uç noktasını yerel intranet bölgelerine ekleme

Kayıtlı cihazların kullanıcıları Azure AD 'de kimlik doğrulaması yaparken sertifika istemlerine engel olmak için, Internet Explorer 'daki yerel intranet bölgesine aşağıdaki URL 'yi eklemek üzere etki alanına katılmış cihazlarınıza bir ilke gönderebilirsiniz:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Windows alt düzey cihazlarını denetleme

Windows alt düzey cihazlarını kaydetmek için İndirme Merkezi’nden bir Windows Installer paketi (.msi) indirip yüklemeniz gerekir. Daha fazla bilgi için bkz. [Windows alt düzey cihazlarda karma Azure AD JOIN 'In denetim doğrulaması](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices)bölümü.

## <a name="verify-joined-devices"></a>Katılmış cihazları doğrulama

[Azure Active Directory PowerShell modülündeki](/powershell/azure/install-msonlinev1?view=azureadps-2.0) [Get-msoldevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet 'ini kullanarak kuruluşunuzda başarıyla katılmış cihazları kontrol edebilirsiniz.

Bu cmdlet öğesinin çıktısı, Azure AD ile kaydedilmiş ve katılmış cihazları gösterir. Tüm cihazları almak için **-All** parametresini kullanın ve ardından **Devicetrusttype** özelliğini kullanarak bunları filtreleyin. Etki alanına katılmış cihazlarda **etki alanına katılmış**bir değer vardır.

## <a name="troubleshoot-your-implementation"></a>Uygulamanızda sorun giderme

Etki alanına katılmış Windows cihazlarına yönelik karma Azure AD katılımı tamamlanırken sorun yaşıyorsanız, bkz.:

* [Windows geçerli cihazları için Hibrit Azure AD'ye katılım sorunlarını giderme](troubleshoot-hybrid-join-windows-current.md)
* [Windows alt düzey cihazları için Hibrit Azure AD'ye katılım sorunlarını giderme](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory'de cihaz yönetimine giriş](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
