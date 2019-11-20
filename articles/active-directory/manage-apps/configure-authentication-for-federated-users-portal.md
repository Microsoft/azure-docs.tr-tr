---
title: Giriş bölgesi bulma ilkesini kullanarak oturum açma otomatik hızlandırmasını yapılandırma | Microsoft Docs
description: Otomatik hızlandırma ve etki alanı ipuçları dahil olmak üzere federasyon kullanıcıları için Azure Active Directory kimlik doğrulaması için giriş bölgesi bulma ilkesini nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8f51fcd69a7115879aad97bbf696833e87877b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477214"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Giriş bölgesi bulma ilkesi kullanarak bir uygulama için Azure Active Directory oturum açma davranışı yapılandırma

Bu makalede, Federasyon kullanıcıları için Azure Active Directory kimlik doğrulama davranışını yapılandırmaya bir giriş sunulmaktadır. Federasyon etki alanlarında bulunan kullanıcılar için otomatik hızlandırma ve kimlik doğrulama kısıtlamalarının yapılandırılmasını içerir.

## <a name="home-realm-discovery"></a>Giriş Bölgesi Bulma
Giriş bölgesi bulma (HRD), Azure Active Directory (Azure AD), kullanıcının oturum açma zamanında kimlik doğrulamasının nerede olduğunu belirlemesini sağlar.  Bir Kullanıcı bir kaynağa erişmek için bir Azure AD kiracısında veya Azure AD ortak oturum açma sayfasına kaydolduğunda, bir Kullanıcı adı (UPN) YAZIRLAR. Azure AD, kullanıcının oturum açması gereken yeri saptamak için bunu kullanır. 

Kullanıcının kimlik doğrulaması için aşağıdaki konumlardan birine alınması gerekebilir:

- Kullanıcının ev kiracısı (kullanıcının erişmeye çalıştığı kaynakla aynı kiracı olabilir). 

- Microsoft hesabı.  Kullanıcı, kaynak kiracısında bir konudır.

-  Active Directory Federasyon Hizmetleri (AD FS) (AD FS) gibi bir şirket içi kimlik sağlayıcısı.

- Azure AD kiracısı ile federe olan başka bir kimlik sağlayıcısı.

## <a name="auto-acceleration"></a>Otomatik hızlandırma 
Bazı kuruluşlar Azure Active Directory kiracısındaki etki alanlarını, Kullanıcı kimlik doğrulaması için AD FS gibi başka bir IDP ile federasyona göre yapılandırır.  

Bir Kullanıcı bir uygulamada oturum açtığında, önce Azure AD oturum açma sayfasıyla birlikte sunulur. UPN 'leri yazdıktan sonra, bir Federasyon etki alanında yer alıyorsa, bu etki alanına hizmet veren IDP 'nin oturum açma sayfasına yönlendirilirsiniz. Belirli koşullarda, Yöneticiler belirli uygulamalarda oturum açtıklarında kullanıcıları oturum açma sayfasına yönlendirmek isteyebilir. 

Sonuç olarak, kullanıcılar ilk Azure Active Directory sayfasını atlayabilir. Bu işlem, "oturum açma otomatik hızlandırma" olarak adlandırılır.

Kiracının oturum açma için başka bir IDP 'ye federe olduğu durumlarda, otomatik hızlandırma Kullanıcı oturum açmayı daha kolay hale getirir.  Bireysel uygulamalar için otomatik hızlandırma yapılandırabilirsiniz.

>[!NOTE]
>Bir uygulamayı otomatik hızlandırma için yapılandırırsanız, Konuk kullanıcılar oturum açabilirler. Kimlik doğrulaması için bir kullanıcıyı doğrudan federe IDP 'ye alırsanız, bunların Azure Active Directory oturum açma sayfasına geri dönmesi için bir yol yoktur. Konuk kullanıcılar, diğer kiracılara veya Microsoft hesabı gibi bir dış IDP 'ye yönlendirilmek zorunda kalabilir, giriş bölgesi bulma adımını attıkları için bu uygulamada oturum açamaz.  

Federe IDP için otomatik hızlandırmayı denetmanın iki yolu vardır:   

- Bir uygulama için kimlik doğrulama isteklerinde bir etki alanı ipucu kullanın. 
- Otomatik hızlandırmayı etkinleştirmek için bir giriş bölgesi bulma ilkesi yapılandırın.

### <a name="domain-hints"></a>Etki alanı ipuçları    
Etki alanı ipuçları, bir uygulamadan gelen kimlik doğrulama isteğine dahil olan yönergelerden yapılır. Bu kişiler, kullanıcıyı Federal IDP oturum açma sayfasında hızlandırmak için kullanılabilirler. Ya da çok kiracılı bir uygulama tarafından, kullanıcıyı kiracının kendi markalı Azure AD oturum açma sayfasına doğrudan hızlandırmak için kullanılabilirler.  

Örneğin, "largeapp.com" uygulaması, müşterilerinin "contoso.largeapp.com" özel URL 'sindeki uygulamaya erişmesini sağlayabilir. Uygulama, kimlik doğrulama isteğine contoso.com için bir etki alanı ipucu da içerebilir. 

Etki alanı ipucu sözdizimi, kullanılan protokole bağlı olarak değişir ve genellikle uygulamada yapılandırılır.

**WS-Federation**: WHr = contoso. com sorgu dizesinde.

**SAML**:  Bir etki alanı ipucu veya bir sorgu dizesi olan WHr = contoso. com içeren bir SAML kimlik doğrulama isteği.

**Açık kimlik bağlantısı**: Sorgu dizesi domain_hint = contoso. com. 

Bir etki alanı ipucu, uygulamadan gelen kimlik doğrulama isteğine dahil ise ve kiracı bu etki alanıyla federe ise, Azure AD bu etki alanı için yapılandırılmış IDP 'ye oturum açmayı yeniden yönlendirmeye çalışır. 

Etki alanı ipucu doğrulanmış bir Federasyon etki alanına başvurmazsa, yok sayılır ve normal giriş bölgesi bulma işlemi çağrılır.

Azure Active Directory tarafından desteklenen etki alanı ipuçlarını kullanarak otomatik hızlandırma hakkında daha fazla bilgi için, [Enterprise Mobility + Security bloguna](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)bakın.

>[!NOTE]
>Bir etki alanı ipucu bir kimlik doğrulama isteğine dahil ise, varlığı HRD ilkesinde uygulama için ayarlanan otomatik hızlandırmayı geçersiz kılar.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Otomatik hızlandırma için ana bölge bulma ilkesi
Bazı uygulamalar, yaydıkları kimlik doğrulama isteğini yapılandırmak için bir yol sağlamaz. Bu durumlarda, otomatik hızlandırmayı denetlemek için etki alanı ipuçlarını kullanmak mümkün değildir. Otomatik hızlandırma, ilke aracılığıyla aynı davranışa ulaşmak için yapılandırılabilir.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Eski uygulamalar için doğrudan kimlik doğrulamasını etkinleştir
Uygulamaların kimliğini doğrulamak için AAD kitaplıklarını ve etkileşimli oturum açmayı kullanması en iyi uygulamadır. Kitaplıklar, Federasyon Kullanıcı akışlarını üstlenir.  Bazen eski uygulamalar, Federasyonu anlamak için yazılmaz. Bunlar, giriş bölgesi bulma gerçekleştirmez ve kullanıcının kimliğini doğrulamak için doğru Federal uç noktayla etkileşime girmez. ' I seçerseniz, kimlik doğrulaması yapmak için Kullanıcı adı/parola kimlik bilgileri gönderen belirli eski uygulamaları etkinleştirmek için HRD Ilkesini kullanabilirsiniz Azure Active Directory. Parola karması eşitlemesi etkinleştirilmelidir. 

> [!IMPORTANT]
> Yalnızca Parola karması eşitlemesi açıksa doğrudan kimlik doğrulamayı etkinleştirin ve şirket içi IDP 'niz tarafından uygulanan herhangi bir ilke olmadan bu uygulamanın kimliklerinin doğrulanmasının uygun olduğunu bilirsiniz. Parola karma eşitlemesini devre dışı bırakırsanız veya herhangi bir nedenden dolayı AD Connect ile dizin eşitlemeyi kapatırsanız, eski parola karmasını kullanarak doğrudan kimlik doğrulama olasılığa engel olmak için bu ilkeyi kaldırmanız gerekir.

## <a name="set-hrd-policy"></a>HRD ilkesini ayarla
Federasyon oturum açma otomatik hızlandırma veya doğrudan bulut tabanlı uygulamalar için bir uygulamada HRD ilkesi ayarlamanın üç adımı vardır:

1. Bir HRD ilkesi oluşturun.

2. İlkenin iliştirilebileceği hizmet sorumlusunu bulun.

3. İlkeyi hizmet sorumlusuna ekleyin. 

İlkeler, yalnızca bir hizmet sorumlusu 'na eklendiğinde belirli bir uygulama için geçerli olur. 

Hizmet sorumlusu üzerinde herhangi bir anda yalnızca bir HRD ilkesi etkin olabilir.  

HRD İlkesi oluşturup yönetmek için Microsoft Azure Active Directory Graph API 'sini doğrudan veya Azure Active Directory PowerShell cmdlet 'lerini kullanabilirsiniz.

İlkeyi işleyen Graph API, MSDN 'deki [ilke üzerinde işlemler](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) makalesinde açıklanmaktadır.

Örnek bir HRD ilke tanımı aşağıda verilmiştir:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

İlke türü "HomeRealmDiscoveryPolicy" dir.

**Ivatetofederateddomain** isteğe bağlıdır. **Ivatetofederateddomain** false ise, ilkenin otomatik hızlandırma üzerinde hiçbir etkisi yoktur. **Ivatetofederateddomain** true ise ve kiracıda yalnızca bir doğrulanmış ve Federasyon etki alanı varsa, kullanıcılar oturum açmak Için Federal IDP 'ye doğrudan alınır. Doğru ise ve kiracıda birden fazla doğrulanmış etki alanı varsa, **Preferreddomain** belirtilmelidir.

**Preferreddomain** isteğe bağlıdır. **Preferreddomain** , hızlandıracak bir etki alanı göstermelidir. Kiracıda yalnızca bir Federasyon etki alanı varsa, bu atlanabilir.  Atlanırsa ve birden fazla doğrulanmış Federasyon etki alanı varsa, ilkenin hiçbir etkisi yoktur.

 **Preferreddomain** belirtilmişse, kiracının doğrulanmış, Federasyon etki alanıyla eşleşmesi gerekir. Uygulamanın tüm kullanıcılarının bu etki alanında oturum açabiliyor olması gerekir.

**Allowcloudpasswordvalidation** isteğe bağlıdır. **Allowcloudpasswordvalidation** true ise, uygulamanın Kullanıcı adı/parola kimlik bilgilerini doğrudan Azure Active Directory belirteç uç noktasına sunarak federe bir kullanıcının kimlik doğrulamasına izin verilir. Bu yalnızca parola karması eşitleme etkinse geçerlidir.

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ilkelerinin önceliği ve değerlendirmesi
HRD ilkeleri oluşturulup belirli kuruluşlara ve hizmet sorumlularına atanabilir. Bu, birden çok ilkenin belirli bir uygulamaya uygulanması mümkün olduğu anlamına gelir. Geçerli olan HRD ilkesi şu kurallara uyar:


- Kimlik doğrulama isteğinde bir etki alanı ipucu varsa, otomatik hızlandırma için herhangi bir HRD ilkesi yok sayılır. Etki alanı ipucu tarafından belirtilen davranış kullanılır.

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

1.  Başlamak için en son Azure AD PowerShell cmdlet Önizlemesi ' ni indirin. 

2.  Azure AD PowerShell cmdlet 'lerini indirdikten sonra, Yönetici hesabınızla Azure AD 'de oturum açmak için Connect komutunu çalıştırın:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Kuruluşunuzdaki tüm ilkeleri görmek için aşağıdaki komutu çalıştırın:

    ``` powershell
    Get-AzureADPolicy
    ```

Hiçbir şey döndürülmezse, kiracınızda hiçbir ilke oluşturulmamış demektir.

### <a name="example-set-hrd-policy-for-an-application"></a>Örnek: Bir uygulama için HRD ilkesini ayarlama 

Bu örnekte, bir uygulamaya atandığı bir ilke oluşturursunuz: 
- Kiracınızda tek bir etki alanı olduğunda bir uygulamada oturum açtıklarında kullanıcıları AD FS oturum açma ekranına otomatik olarak hızlandırır. 
- Kullanıcıları AD FS oturum açma ekranına otomatik olarak hızlandırır kiracınızda birden fazla Federasyon etki alanı bulunur.
- , İlkenin atandığı uygulamalar için Federasyon kullanıcıları için doğrudan Azure Active Directory için etkileşimli olmayan Kullanıcı adı/parola oturum açma imkanı sağlar.

#### <a name="step-1-create-an-hrd-policy"></a>1\. adım: Bir HRD ilkesi oluşturma

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

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2\. adım: İlkenin atanacağı hizmet sorumlusunu bulun  
İlkeyi atamak istediğiniz hizmet sorumlularının **ObjectID** 'ye ihtiyacınız vardır. Hizmet sorumluları **ObjectID** 'yi bulmanın birkaç yolu vardır.    

Portalı kullanabilir veya [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)sorgulayabilirsiniz. Ayrıca, tüm kuruluşunuzun hizmet sorumlularını görmek için [Graph Explorer aracına](https://developer.microsoft.com/graph/graph-explorer) gidebilir ve Azure AD hesabınızda oturum açabilirsiniz. 

PowerShell 'i kullandığınız için, hizmet sorumlularını ve kimliklerini listelemek üzere aşağıdaki cmdlet 'i kullanabilirsiniz.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3\. adım: İlkeyi hizmet sorumlusuna atama  
Otomatik hızlandırmayı yapılandırmak istediğiniz uygulamanın hizmet sorumlusu **ObjectID** olduktan sonra aşağıdaki komutu çalıştırın. Bu komut, adım 1 ' de oluşturduğunuz HRD ilkesini adım 2 ' de bulduğunuz hizmet sorumlusu ile ilişkilendirir.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

İlkeyi eklemek istediğiniz her hizmet sorumlusu için bu komutu tekrarlayabilirsiniz.

Bir uygulamanın zaten bir HomeRealmDiscovery ilkesi atanmış olması durumunda ikinci bir tane ekleyemeyeceksiniz.  Bu durumda, ek parametreler eklemek için uygulamaya atanan giriş bölgesi bulma ilkesinin tanımını değiştirin.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4\. Adım: HRD ilkenizin hangi uygulama hizmeti ilkelerine atandığını denetleyin
HRD ilkesinin hangi uygulamalarda yapılandırıldığını denetlemek için **Get-AzureADPolicyAppliedObject** cmdlet 'ini kullanın. İade etmek istediğiniz ilkenin **ObjectID** değerini geçirin.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5\. Adım: İşiniz bitti!
Yeni ilkenin çalışıp çalışmadığını denetlemek için uygulamayı deneyin.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Örnek: HRD ilkesinin yapılandırıldığı uygulamaları listeleyin

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1\. adım: Kuruluşunuzda oluşturulan tüm ilkeleri listeleme 

``` powershell
Get-AzureADPolicy
```

Atamalarını listelemek istediğiniz ilkenin **ObjectID** değerini unutmayın.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2\. adım: İlkenin atandığı hizmet sorumlularını listeleyin  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Örnek: Bir uygulama için HRD ilkesini kaldırma
#### <a name="step-1-get-the-objectid"></a>1\. adım: ObjectID 'yi al
İlke **ObjectID** 'yi ve kaldırmak istediğiniz uygulama hizmeti sorumlusunu almak için önceki örneği kullanın. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2\. adım: Uygulama hizmeti sorumlusundan ilke atamasını kaldırma  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3\. adım: İlkenin atandığı hizmet sorumlularını listeleyerek kaldırma işlemini denetleyin 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Sonraki adımlar
- Azure AD 'de kimlik doğrulamanın nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](../develop/authentication-scenarios.md).
- Kullanıcı çoklu oturum açma hakkında daha fazla bilgi için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](what-is-single-sign-on.md).
- Geliştirici ile ilgili tüm içeriklere genel bakış için [Active Directory geliştirici kılavuzunu](../develop/v1-overview.md) ziyaret edin.
