---
title: Giriş bölgesi bulmayı kullanarak oturum açma otomatik hızlandırmasını yapılandırma
description: Otomatik hızlandırma ve etki alanı ipuçları dahil olmak üzere federasyon kullanıcıları için Azure Active Directory kimlik doğrulaması için giriş bölgesi bulma ilkesini nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af80979a4712f6d25d994835128f9d5d2205f42
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534733"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Giriş bölgesi bulma ilkesi kullanarak bir uygulama için Azure Active Directory oturum açma davranışı yapılandırma

Bu makalede, giriş bölgesi bulma (HRD) İlkesi kullanan Federasyon kullanıcıları için Azure Active Directory kimlik doğrulama davranışını yapılandırmaya bir giriş sunulmaktadır.  Kullanıcı adı giriş ekranını atlamak ve kullanıcıları otomatik olarak Federasyon oturum açma uç noktalarına iletmek için otomatik hızlandırmayı kullanma konusunu ele alır.  Microsoft, FIDO ve hinders işbirliği gibi daha güçlü kimlik doğrulama yöntemlerinin kullanımını engelleyebilen için otomatik hızlandırmayı daha uzun bir süre önce yapılandırmanızı önermez.

## <a name="home-realm-discovery"></a>Giriş Bölgesi Bulma

Giriş bölgesi bulma (HRD), Azure Active Directory (Azure AD), kullanıcının oturum açma zamanında kimlik doğrulaması yapması gereken kimlik sağlayıcısını ("IDP") belirlemesine izin veren bir işlemdir.  Bir Kullanıcı bir kaynağa erişmek için bir Azure AD kiracısında veya Azure AD ortak oturum açma sayfasına kaydolduğunda, bir Kullanıcı adı (UPN) YAZIRLAR. Azure AD, kullanıcının oturum açması gereken yeri saptamak için bunu kullanır.

Kullanıcı, kimlik doğrulaması için aşağıdaki kimlik sağlayıcılarından birine alınır:

- Kullanıcının ev kiracısı (kullanıcının erişmeye çalıştığı kaynakla aynı kiracı olabilir).

- Microsoft hesabı.  Kullanıcı, kimlik doğrulaması için bir tüketici hesabı kullanan kaynak kiracısında bir konudır.

- Active Directory Federasyon Hizmetleri (AD FS) (AD FS) gibi bir şirket içi kimlik sağlayıcısı.

- Azure AD kiracısı ile federe olan başka bir kimlik sağlayıcısı.

## <a name="auto-acceleration"></a>Otomatik hızlandırma

Bazı kuruluşlar Azure Active Directory kiracısındaki etki alanlarını, Kullanıcı kimlik doğrulaması için AD FS gibi başka bir IDP ile federasyona göre yapılandırır.  

Bir Kullanıcı bir uygulamada oturum açtığında, önce Azure AD oturum açma sayfasıyla birlikte sunulur. UPN 'leri yazdıktan sonra, bir Federasyon etki alanında yer alıyorsa, bu etki alanına hizmet veren IDP 'nin oturum açma sayfasına yönlendirilirsiniz. Belirli koşullarda, Yöneticiler belirli uygulamalarda oturum açtıklarında kullanıcıları oturum açma sayfasına yönlendirmek isteyebilir.

Sonuç olarak, kullanıcılar ilk Azure Active Directory sayfasını atlayabilir. Bu işlem, "oturum açma otomatik hızlandırma" olarak adlandırılır.

Kiracının oturum açma için başka bir IDP 'ye federe olduğu durumlarda, otomatik hızlandırma Kullanıcı oturum açmayı daha kolay hale getirir.  Bireysel uygulamalar için otomatik hızlandırma yapılandırabilirsiniz.

>[!NOTE]
>Bir uygulamayı otomatik hızlandırma için yapılandırırsanız, kullanıcılar yönetilen kimlik bilgilerini (FIDO gibi) kullanamaz ve Konuk kullanıcılar oturum açamaz. Kimlik doğrulaması için bir kullanıcıyı doğrudan federe IDP 'ye alırsanız, bunların Azure Active Directory oturum açma sayfasına geri dönmesi için bir yol yoktur. Konuk kullanıcılar, diğer kiracılara veya Microsoft hesabı gibi bir dış IDP 'ye yönlendirilmek zorunda kalabilir, giriş bölgesi bulma adımını attıkları için bu uygulamada oturum açamaz.  

Federe IDP için otomatik hızlandırmayı denetlemek için üç yol vardır:

- Bir uygulama için kimlik doğrulama isteklerinde bir [etki alanı ipucu](#domain-hints) kullanın.
- [Otomatik hızlandırmayı zorlamak](#home-realm-discovery-policy-for-auto-acceleration)Için bir giriş bölgesi bulma ilkesi yapılandırın.
- Belirli uygulamalardan veya belirli etki alanlarında [etki alanı ipuçlarını yoksayacak](prevent-domain-hints-with-home-realm-discovery.md) şekilde bir giriş bölgesi bulma ilkesi yapılandırın.

### <a name="domain-hints"></a>Etki alanı ipuçları

Etki alanı ipuçları, bir uygulamadan gelen kimlik doğrulama isteğine dahil olan yönergelerden yapılır. Bu kişiler, kullanıcıyı Federal IDP oturum açma sayfasında hızlandırmak için kullanılabilirler. Ya da çok kiracılı bir uygulama tarafından, kullanıcıyı kiracının kendi markalı Azure AD oturum açma sayfasına doğrudan hızlandırmak için kullanılabilirler.  

Örneğin, "largeapp.com" uygulaması, müşterilerinin "contoso.largeapp.com" özel URL 'sindeki uygulamaya erişmesini sağlayabilir. Uygulama, kimlik doğrulama isteğine contoso.com için bir etki alanı ipucu da içerebilir.

Etki alanı ipucu sözdizimi, kullanılan protokole bağlı olarak değişir ve genellikle uygulamada yapılandırılır.

**WS-Federation**: WHr = contoso. com sorgu dizesinde.

**SAML**: bir etki alanı ipucu veya bir sorgu dizesi WHr = contoso. com IÇEREN bir SAML kimlik doğrulama isteği.

**Açık kimlik Connect**: bir sorgu dizesi domain_hint = contoso. com.

Varsayılan olarak, Azure AD, aşağıdakilerin **her ikisi de** doğruysa, bir etki alanı Için yapılandırılan IDP 'de oturum açmayı yeniden yönlendirmeye çalışır:

- Bir etki alanı ipucu, uygulamadan gelen kimlik doğrulama isteğine dahildir **ve**
- Kiracı bu etki alanıyla birlikte verilir.

Etki alanı ipucu doğrulanmış bir Federasyon etki alanına başvurmazsa, yok sayılır.

Azure Active Directory tarafından desteklenen etki alanı ipuçlarını kullanarak otomatik hızlandırma hakkında daha fazla bilgi için, [Enterprise Mobility + Security bloguna](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)bakın.

>[!NOTE]
>Bir etki alanı ipucu bir kimlik doğrulama isteğine dahil edilip [olması halinde olması](#home-realm-discovery-policy-to-prevent-auto-acceleration)halinde, mevcut olması, HRD ilkesinde uygulama için ayarlanan otomatik hızlandırmayı geçersiz kılar.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Otomatik hızlandırma için ana bölge bulma ilkesi

Bazı uygulamalar, yaydıkları kimlik doğrulama isteğini yapılandırmak için bir yol sağlamaz. Bu durumlarda, otomatik hızlandırmayı denetlemek için etki alanı ipuçlarını kullanmak mümkün değildir. Aynı davranışa ulaşmak için otomatik hızlandırma, giriş bölgesi bulma ilkesi ile yapılandırılabilir.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Otomatik hızlandırmayı engellemek için giriş bölgesi bulma ilkesi

Bazı Microsoft ve SaaS uygulamaları domain_hints (örneğin, `https://outlook.com/contoso.com` eklenen bir oturum açma isteğiyle sonuçlanır `&domain_hint=contoso.com` ), Fido gibi yönetilen kimlik bilgilerinin dağıtımını kesintiye uğratan bir şekilde otomatik olarak dahildir.  Yönetilen kimlik bilgilerinin dağıtımı sırasında belirli uygulamalardan veya belirli etki alanlarında etki alanı ipuçlarını yoksaymak için [giriş bölgesi bulma ilkesini](/graph/api/resources/homeRealmDiscoveryPolicy) kullanabilirsiniz.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Eski uygulamalar için Federal kullanıcıların doğrudan ROPC kimlik doğrulamasını etkinleştir

Uygulamaların kimliğini doğrulamak için AAD kitaplıklarını ve etkileşimli oturum açmayı kullanması en iyi uygulamadır. Kitaplıklar, Federasyon Kullanıcı akışlarını üstlenir.  Bazen eski uygulamalar, özellikle ROPC 'yi kullananlar, Kullanıcı adı ve parolayı doğrudan Azure AD 'ye gönderir ve Federasyonu anlamak için yazılmaz. Bunlar, giriş bölgesi bulma gerçekleştirmez ve kullanıcının kimliğini doğrulamak için doğru Federal uç noktayla etkileşime girmez. ' I seçerseniz, Azure Active Directory doğrudan kimlik doğrulaması yapmak için ROPC iznini kullanarak Kullanıcı adı/parola kimlik bilgileri gönderen belirli eski uygulamaları etkinleştirmek üzere HRD Ilkesini kullanabilirsiniz. Parola karması eşitlemesi etkinleştirilmelidir.

> [!IMPORTANT]
> Yalnızca Parola karması eşitlemesi açıksa doğrudan kimlik doğrulamayı etkinleştirin ve şirket içi IDP 'niz tarafından uygulanan herhangi bir ilke olmadan bu uygulamanın kimliklerinin doğrulanmasının uygun olduğunu bilirsiniz. Parola karma eşitlemesini devre dışı bırakırsanız veya herhangi bir nedenden dolayı AD Connect ile dizin eşitlemeyi kapatırsanız, eski parola karmasını kullanarak doğrudan kimlik doğrulama olasılığa engel olmak için bu ilkeyi kaldırmanız gerekir.

## <a name="set-hrd-policy"></a>HRD ilkesini ayarla

Federasyon oturum açma otomatik hızlandırma veya doğrudan bulut tabanlı uygulamalar için bir uygulamada HRD ilkesi ayarlamanın üç adımı vardır:

1. Bir HRD ilkesi oluşturun.

2. İlkenin iliştirilebileceği hizmet sorumlusunu bulun.

3. İlkeyi hizmet sorumlusuna ekleyin.

İlkeler, yalnızca bir hizmet sorumlusu 'na eklendiğinde belirli bir uygulama için geçerli olur.

Hizmet sorumlusu üzerinde herhangi bir anda yalnızca bir HRD ilkesi etkin olabilir.  

HRD ilkesini oluşturmak ve yönetmek için Azure Active Directory PowerShell cmdlet 'lerini kullanabilirsiniz.

Örnek bir HRD ilke tanımı aşağıda verilmiştir:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

İlke türü "[Homerealmdiscoverypolicy](/graph/api/resources/homeRealmDiscoveryPolicy)" dir.

**Ivatetofederateddomain** isteğe bağlıdır. **Ivatetofederateddomain** false ise, ilkenin otomatik hızlandırma üzerinde hiçbir etkisi yoktur. **Ivatetofederateddomain** true ise ve kiracıda yalnızca bir doğrulanmış ve Federasyon etki alanı varsa, kullanıcılar oturum açmak Için Federal IDP 'ye doğrudan alınır. Doğru ise ve kiracıda birden fazla doğrulanmış etki alanı varsa, **Preferreddomain** belirtilmelidir.

**Preferreddomain** isteğe bağlıdır. **Preferreddomain** , hızlandıracak bir etki alanı göstermelidir. Kiracıda yalnızca bir Federasyon etki alanı varsa, bu atlanabilir.  Atlanırsa ve birden fazla doğrulanmış Federasyon etki alanı varsa, ilkenin hiçbir etkisi yoktur.

 **Preferreddomain** belirtilmişse, kiracının doğrulanmış, Federasyon etki alanıyla eşleşmesi gerekir. Uygulamanın tüm kullanıcılarının bu etki alanında oturum açabiliyor olması gerekir-Federasyon etki alanında oturum açabilen kullanıcılar yakalanacaktır ve oturum açma işleminin tamamlanamayacak.

**Allowcloudpasswordvalidation** isteğe bağlıdır. **Allowcloudpasswordvalidation** true ise, uygulamanın Kullanıcı adı/parola kimlik bilgilerini doğrudan Azure Active Directory belirteç uç noktasına sunarak federe bir kullanıcının kimlik doğrulamasına izin verilir. Bu yalnızca parola karması eşitleme etkinse geçerlidir.

Ayrıca, yukarıda gösterilmez iki kiracı düzeyi HRD seçeneği vardır:

- **Alternateıdlogin** isteğe bağlıdır.  Etkinleştirilirse bu, kullanıcıların Azure AD oturum açma sayfasında [kendı UPN 'si yerine e-posta adresleriyle oturum açmasına olanak tanır](../authentication/howto-authentication-use-email-signin.md) .  Alternatif kimlikler, bir Federasyon ıDP 'ye otomatik olarak hızlandırılmayan kullanıcıya bağımlıdır.

- **Domainhintpolicy** , [etki alanı ipuçlarının kullanıcıları federe etki alanlarına otomatik olarak hızlandırmasını *engelleyen*](prevent-domain-hints-with-home-realm-discovery.md)isteğe bağlı bir karmaşık nesnedir. Bu kiracı genelindeki bu ayar, etki alanı ipuçlarını gönderen uygulamaların, kullanıcıların bulut tarafından yönetilen kimlik bilgileriyle oturum açmasını önleyemediğinden emin olmak için kullanılır.

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ilkelerinin önceliği ve değerlendirmesi

HRD ilkeleri oluşturulup belirli kuruluşlara ve hizmet sorumlularına atanabilir. Bu, birden çok ilkenin belirli bir uygulamaya uygulanmasını olanaklı hale, bu nedenle Azure AD 'nin hangi bir önceliğe sahip olduğuna karar vermelidir. Bir dizi kural, hangi HRD ilkesinin (kaç tane uygulanmış) etkili olduğunu belirler:

- Kimlik doğrulama isteğinde bir etki alanı ipucu varsa, kiracı için HRD ilkesi (kiracı varsayılanı olarak ayarlanan ilke), [etki alanı ipuçlarının yoksayılıp sayılmayacağını](prevent-domain-hints-with-home-realm-discovery.md)görmek için denetlenir. Etki alanı ipuçlarına izin veriliyorsa, etki alanı ipucu tarafından belirtilen davranış kullanılır.

- Aksi takdirde, bir ilke hizmet sorumlusuna açıkça atanmışsa, zorlanır.

- Hiçbir etki alanı ipucu yoksa ve hizmet sorumlusuna açık bir ilke atanmamışsa, hizmet sorumlusunun ana kuruluşuna açıkça atanan bir ilke uygulanır.

- Bir etki alanı ipucu yoksa ve hizmet sorumlusuna veya kuruluşa bir ilke atanmamışsa, varsayılan HRD davranışı kullanılır.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Bir uygulamada HRD ilkesini ayarlama öğreticisi

Azure AD PowerShell cmdlet 'lerini aşağıdakiler de dahil olmak üzere birkaç senaryoya göre izlenecek şekilde kullanacağız:

- Tek bir Federasyon etki alanı olan Kiracıdaki bir uygulama için otomatik hızlandırma yapmak üzere HRD ilkesini ayarlama.

- Kiracı için doğrulanan birkaç etki alanından birine bir uygulama için otomatik hızlandırma yapmak üzere HRD ilkesini ayarlama.

- Eski bir uygulamanın bir federasyon kullanıcısına Azure Active Directory için doğrudan Kullanıcı adı/parola kimlik doğrulaması kurmasını sağlamak üzere HRD ilkesini ayarlama.

- Bir ilkenin yapılandırıldığı uygulamaları listeleme.

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki örneklerde, Azure AD 'de uygulama hizmeti sorumluları üzerinde ilkeler oluşturur, güncelleştirir, bağlar ve silebilirsiniz.

1. Başlamak için en son Azure AD PowerShell cmdlet Önizlemesi ' ni indirin.

2. Azure AD PowerShell cmdlet 'lerini indirdikten sonra, Yönetici hesabınızla Azure AD 'de oturum açmak için Connect komutunu çalıştırın:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Kuruluşunuzdaki tüm ilkeleri görmek için aşağıdaki komutu çalıştırın:

    ``` powershell
    Get-AzureADPolicy
    ```

Hiçbir şey döndürülmezse, kiracınızda hiçbir ilke oluşturulmamış demektir.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Örnek: bir uygulama için HRD ilkesi ayarlama

Bu örnekte, bir uygulamaya atandığı bir ilke oluşturursunuz:

- Kiracınızda tek bir etki alanı olduğunda bir uygulamada oturum açtıklarında kullanıcıları AD FS oturum açma ekranına otomatik olarak hızlandırır.
- Kullanıcıları AD FS oturum açma ekranına otomatik olarak hızlandırır kiracınızda birden fazla Federasyon etki alanı bulunur.
- , İlkenin atandığı uygulamalar için Federasyon kullanıcıları için doğrudan Azure Active Directory için etkileşimli olmayan Kullanıcı adı/parola oturum açma imkanı sağlar.

#### <a name="step-1-create-an-hrd-policy"></a>1. Adım: bir HRD ilkesi oluşturma

Aşağıdaki ilke, kiracınızda tek bir etki alanı olduğunda bir uygulamada oturum açtıklarında kullanıcıları AD FS oturum açma ekranına otomatik olarak hızlandırır.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aşağıdaki ilke, Kullanıcı AD FS oturum açma ekranına otomatik olarak hızlandırıyor. kiracınızda birden fazla Federasyon etki alanı var. Uygulamalar için kullanıcıların kimliğini doğrulayan birden fazla Federasyon etki alanınız varsa, otomatik olarak hızlandırmak için etki alanını belirtmeniz gerekir.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Belirli uygulamalar için Azure Active Directory doğrudan Federasyon kullanıcıları için Kullanıcı adı/parola kimlik doğrulamasını etkinleştirmek üzere bir ilke oluşturmak için aşağıdaki komutu çalıştırın:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Yeni ilkenizi görmek ve **ObjectID**'yi almak için aşağıdaki komutu çalıştırın:

``` powershell
Get-AzureADPolicy
```

Bunu oluşturduktan sonra HRD ilkesini uygulamak için, birden çok uygulama hizmeti sorumlularına atayabilirsiniz.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. Adım: ilkenin atanacağı hizmet sorumlusunu bulma

İlkeyi atamak istediğiniz hizmet sorumlularının **ObjectID** 'ye ihtiyacınız vardır. Hizmet sorumluları **ObjectID** 'yi bulmanın birkaç yolu vardır.

Portalı kullanabilir veya [Microsoft Graph](/graph/api/resources/serviceprincipal)sorgulayabilirsiniz. Ayrıca, tüm kuruluşunuzun hizmet sorumlularını görmek için [Graph Explorer aracına](https://developer.microsoft.com/graph/graph-explorer) gidebilir ve Azure AD hesabınızda oturum açabilirsiniz.

PowerShell 'i kullandığınız için, hizmet sorumlularını ve kimliklerini listelemek üzere aşağıdaki cmdlet 'i kullanabilirsiniz.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. Adım: ilkeyi hizmet sorumlusuna atama

Otomatik hızlandırmayı yapılandırmak istediğiniz uygulamanın hizmet sorumlusu **ObjectID** olduktan sonra aşağıdaki komutu çalıştırın. Bu komut, adım 1 ' de oluşturduğunuz HRD ilkesini adım 2 ' de bulduğunuz hizmet sorumlusu ile ilişkilendirir.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

İlkeyi eklemek istediğiniz her hizmet sorumlusu için bu komutu tekrarlayabilirsiniz.

Bir uygulamanın zaten bir HomeRealmDiscovery ilkesi atanmış olması durumunda ikinci bir tane ekleyemeyeceksiniz.  Bu durumda, ek parametreler eklemek için uygulamaya atanan giriş bölgesi bulma ilkesinin tanımını değiştirin.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4. Adım: HRD ilkenizin hangi uygulama hizmeti ilkelerine atandığını denetleyin

HRD ilkesinin hangi uygulamalarda yapılandırıldığını denetlemek için **Get-AzureADPolicyAppliedObject** cmdlet 'ini kullanın. İade etmek istediğiniz ilkenin **ObjectID** değerini geçirin.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>5. Adım: işiniz bitti!

Yeni ilkenin çalışıp çalışmadığını denetlemek için uygulamayı deneyin.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Örnek: HRD ilkesinin yapılandırıldığı uygulamaları listeleyin

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. Adım: kuruluşunuzda oluşturulan tüm ilkeleri listeleme

``` powershell
Get-AzureADPolicy
```

Atamalarını listelemek istediğiniz ilkenin **ObjectID** değerini unutmayın.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. Adım: ilkenin atandığı hizmet sorumlularını listeleyin  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Örnek: bir HRD ilkesini uygulamadan kaldırma

#### <a name="step-1-get-the-objectid"></a>1. Adım: ObjectID 'yi edinme

İlke **ObjectID** 'yi ve kaldırmak istediğiniz uygulama hizmeti sorumlusunu almak için önceki örneği kullanın.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. Adım: ilke atamasını uygulama hizmeti sorumlusundan kaldırma  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3. Adım: ilkenin atandığı hizmet sorumlularını listeleyerek kaldırma işlemini denetleyin

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD 'de kimlik doğrulamanın nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](../develop/authentication-vs-authorization.md).
- Kullanıcı çoklu oturum açma hakkında daha fazla bilgi için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](what-is-single-sign-on.md).
- Geliştiriciyle ilgili tüm içeriklere genel bakış için [Microsoft Identity platformu](../develop/v2-overview.md) ' nu ziyaret edin.