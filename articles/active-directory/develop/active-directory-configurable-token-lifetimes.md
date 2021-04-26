---
title: Yapılandırılabilir belirteç yaşam süreleri
titleSuffix: Microsoft identity platform
description: Microsoft Identity platform tarafından verilen erişim, SAML ve KIMLIK belirteçleri için yaşam sürelerini ayarlamayı öğrenin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363980"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Microsoft Identity platformunda yapılandırılabilir belirteç yaşam süreleri (Önizleme)

Microsoft Identity platform tarafından verilen erişim, KIMLIK veya SAML belirtecinin ömrünü belirtebilirsiniz. Kuruluşunuzdaki tüm uygulamalar, çok kiracılı (çok kuruluşlu) bir uygulama veya belirli bir hizmet sorumlusu için belirteç ömrünü ayarlayabilirsiniz. Ancak, şu anda [yönetilen kimlik hizmeti sorumluları](../managed-identities-azure-resources/overview.md)için belirteç yaşam sürelerini yapılandırmayı desteklemiyoruz.

Azure AD 'de bir ilke nesnesi, tek tek uygulamalarda veya bir kuruluştaki tüm uygulamalarda zorlanan bir kurallar kümesini temsil eder. Her ilke türünün, atandıkları nesnelere uygulanan bir özellikler kümesi ile benzersiz bir yapısı vardır.

Bir ilkeyi kuruluşunuz için varsayılan ilke olarak belirleyebilirsiniz. İlke, daha yüksek önceliğe sahip bir ilke tarafından geçersiz kılınmadığı sürece, kuruluştaki herhangi bir uygulamaya uygulanır. Ayrıca, belirli uygulamalara bir ilke atayabilirsiniz. Öncelik sırası, ilke türüne göre farklılık gösterir.

Örnekler için, [belirteç yaşam sürelerinin nasıl yapılandırılacağı hakkında örnekleri](configure-token-lifetimes.md)okuyun.

> [!NOTE]
> Yapılandırılabilir belirteç ömür ilkesi yalnızca SharePoint Online ve OneDrive Iş kaynaklarına erişen mobil ve Masaüstü istemcilerine uygulanır ve Web tarayıcı oturumları için geçerli değildir.
> SharePoint Online ve OneDrive Iş için Web tarayıcısı oturumlarının ömrünü yönetmek üzere [koşullu erişim oturumu ömrü](../conditional-access/howto-conditional-access-session-lifetime.md) özelliğini kullanın. Boş oturum zaman aşımlarını yapılandırma hakkında daha fazla bilgi edinmek için [SharePoint Online bloguna](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) bakın.

## <a name="license-requirements"></a>Lisans gereksinimleri

Bu özelliğin kullanılması için Azure AD Premium P1 lisansı gerekir. Gereksinimleriniz için doğru lisans bulmak için bkz. [ücretsiz ve Premium sürümlerin genel kullanıma sunulan özelliklerini karşılaştırma](https://azure.microsoft.com/pricing/details/active-directory/).

[Microsoft 365 iş lisanslarına](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) sahip müşterilerin koşullu erişim özelliklerine de erişimi vardır.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Erişim, SAML ve KIMLIK belirteçleri için belirteç ömür ilkeleri

Erişim belirteçleri, SAML belirteçleri ve KIMLIK belirteçleri için belirteç ömür ilkeleri ayarlayabilirsiniz.

### <a name="access-tokens"></a>Erişim belirteçleri

İstemciler korumalı bir kaynağa erişmek için erişim belirteçlerini kullanır. Erişim belirteci, yalnızca belirli bir Kullanıcı, istemci ve kaynak birleşimi için kullanılabilir. Erişim belirteçleri iptal edilemez ve süresi sona ermeden geçerli olur. Erişim belirteci elde eden kötü niyetli bir aktör bunu yaşam süresinin kapsamı için kullanabilir. Erişim belirtecinin kullanım süresini ayarlamak, sistem performansını artırma ve kullanıcının hesabı devre dışı bırakıldıktan sonra istemcinin erişimi koruduğunu geçen süreyi artırma arasında bir denge. Gelişmiş sistem performansı, bir istemcinin yeni bir erişim belirteci almak için ihtiyaç duymasının kaç kez daha fazla azaltılmasıyla elde edilir.  Varsayılan değer, belirteci isteyen istemci uygulamasına bağlı olarak değişir. Örneğin, CAE duyarlı oturumlara anlaşacak sürekli erişim değerlendirmesi (CAE) özellikli istemciler uzun süreli bir belirteç ömrü (28 saate kadar) görür. Belirtecin süresi dolduktan sonra, istemcinin yenileme belirtecini (genellikle sessizce) yeni bir yenileme belirteci ve erişim belirteci almak için kullanması gerekir.

### <a name="saml-tokens"></a>SAML belirteçleri

SAML belirteçleri, Web tabanlı birçok SaaS uygulaması tarafından kullanılır ve Azure Active Directory SAML2 protokol uç noktası kullanılarak elde edilir. Bunlar, WS-Federation kullanan uygulamalar tarafından da kullanılır. Belirtecin varsayılan yaşam süresi 1 saattir. Bir uygulamanın perspektifinden, belirtecin geçerlilik süresi belirteçteki öğenin NotOnOrAfter değeri ile belirtilir `<conditions …>` . Belirtecin geçerlilik süresi sona erdikten sonra, istemcinin yeni bir kimlik doğrulama isteği başlatması gerekir. Bu, genellikle çoklu oturum açma (SSO) oturum belirtecinin bir sonucu olarak etkileşimli oturum açmadan memnun olur.

NotOnOrAfter değeri, `AccessTokenLifetime` içindeki parametresi kullanılarak değiştirilebilir `TokenLifetimePolicy` . Varsa, ilkede yapılandırılan yaşam süresine ayarlanır ve beş dakikalık bir saat eğriltme faktörü olur.

Öğesinde belirtilen konu onayı NotOnOrAfter, `<SubjectConfirmationData>` belirteç ömür yapılandırmasından etkilenmez. 

### <a name="id-tokens"></a>Kimlik belirteçleri

KIMLIK belirteçleri Web sitelerine ve yerel istemcilere geçirilir. KIMLIK belirteçleri bir kullanıcıyla ilgili profil bilgilerini içerir. KIMLIK belirteci, belirli bir Kullanıcı ve istemci birleşimine bağlanır. KIMLIK belirteçleri, süresi doluncaya kadar geçerli kabul edilir. Genellikle, bir Web uygulaması, kullanıcının uygulamadaki oturum ömrünü Kullanıcı için verilen KIMLIK belirtecinin ömrü boyunca eşleştirir. Web uygulamasının uygulama oturumunun ne sıklıkta dolacağını ve kullanıcının Microsoft Identity platformu ile yeniden kimlik doğrulaması gerektirdiğini (sessizce veya etkileşimli olarak) denetlemek için bir KIMLIK belirtecinin ömrünü ayarlayabilirsiniz.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Belirteçleri ve oturum belirteçlerini yenileme için belirteç ömür ilkeleri

Yenileme belirteçleri ve oturum belirteçleri için belirteç ömür ilkeleri ayarlayamazsınız.

> [!IMPORTANT]
> 30 Ocak 2021 itibariyle yenileme ve oturum belirteci yaşam sürelerini yapılandıramazsınız. Azure Active Directory var olan ilkelerde artık yenileme ve oturum belirteci yapılandırması yok.  Mevcut belirteçlerin süre dolduktan sonra verilen yeni belirteçler [varsayılan yapılandırma](#configurable-token-lifetime-properties)olarak ayarlanır. Yenileme ve oturum belirteci yapılandırması kullanımdan kaldırıldıktan sonra erişimi, SAML ve KIMLIK belirteci yaşam sürelerini yapılandırmaya devam edebilirsiniz.
>
> Mevcut belirtecin ömrü değiştirilmeyecektir. Süreleri dolduktan sonra, varsayılan değere göre yeni bir belirteç verilir.
>
> Bir kullanıcının yeniden oturum açması istenmeden önce geçen süreyi tanımlamaya devam etmeniz gerekiyorsa, koşullu erişimde oturum açma sıklığını yapılandırın. Koşullu erişim hakkında daha fazla bilgi edinmek için [koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](../conditional-access/howto-conditional-access-session-lifetime.md)makalesini okuyun.

## <a name="configurable-token-lifetime-properties"></a>Yapılandırılabilir belirteç ömrü özellikleri
Belirteç ömür ilkesi, belirteç ömrü kurallarını içeren bir ilke nesnesi türüdür. Bu ilke, bu kaynak için ne kadar erişim, SAML ve KIMLIK belirteçleri geçerli kabul edileceğini denetler. Belirteç ömür ilkeleri yenileme ve oturum belirteçleri için ayarlanamaz. İlke ayarlanmamışsa, sistem varsayılan yaşam süresi değerini uygular.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Erişim, KIMLIK ve SAML2 belirteci ömür ilkesi özellikleri

Erişim belirteci yaşam süresi özelliğinin azaltılması, bir erişim belirtecinin veya kötü amaçlı aktör tarafından uzun bir süre için kullanılan KIMLIK belirtecinin riskini azaltır. (Bu belirteçler iptal edilemez.) Bu konuda, belirteçlerin daha sık değiştirilmeleri gerektiğinden, performansın olumsuz bir şekilde etkilenmesi önemlidir.

Bir örnek için bkz. [Web oturumu için Ilke oluşturma](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

Access, ID ve SAML2 Token yapılandırması aşağıdaki özelliklerden ve sırasıyla ayarlanan değerleri etkiler:

- **Özellik**: erişim belirteci ömrü
- **İlke özelliği dizesi**: accesstokenlifetime
- **Etkiler**: erişim BELIRTEÇLERI, kimlik BELIRTEÇLERI, SAML2 belirteçleri
- **Varsayılan**:
    - Erişim belirteçleri: belirteci isteyen istemci uygulamasına bağlı olarak değişir. Örneğin, CAE duyarlı oturumlara anlaşacak sürekli erişim değerlendirmesi (CAE) özellikli istemciler uzun süreli bir belirteç ömrü (28 saate kadar) görür.
    - KIMLIK belirteçleri, SAML2 belirteçleri: 1 saat
- **En az**: 10 dakika
- **Maksimum**: 1 gün

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Yenileme ve oturum belirteci ömür ilkesi özellikleri

Yenileme ve oturum belirteci yapılandırması aşağıdaki özelliklerden ve sırasıyla ayarlanan değerleriyle etkilenir. 30 Ocak 2021 tarihinde yenileme ve oturum belirteci yapılandırması kullanımdan kaldırıldıktan sonra, Azure AD yalnızca aşağıda açıklanan varsayılan değerleri kabul eder. Oturum açma sıklığını yönetmek için [koşullu erişimi](../conditional-access/howto-conditional-access-session-lifetime.md) kullanmamaya karar verirseniz yenileme ve oturum belirteçleriniz bu tarih için varsayılan yapılandırmaya ayarlanır ve artık yaşam sürelerini değiştiremeyeceksiniz.  

|Özellik   |İlke Özellik dizesi    |Ekranlarını |Varsayılan |
|----------|-----------|------------|------------|
|Yenileme belirteci en fazla etkin olmayan süre |Maxınactivetime  |Belirteçleri Yenile |90 gün  |
|Single-Factor yenileme belirtecinin en fazla yaşı  |Maxagesinglefaktör  |Belirteçleri Yenile (tüm kullanıcılar için)  |İptal edilene kadar  |
|Multi-Factor Refresh belirtecinin en fazla yaşı  |MaxAgeMultiFactor  |Belirteçleri Yenile (tüm kullanıcılar için) |İptal edilene kadar  |
|Single-Factor oturum belirtecinin maksimum yaşı  |Maxagesessionsinglefaktör |Oturum belirteçleri (kalıcı ve kalıcı olmayan)  |İptal edilene kadar |
|Multi-Factor Session belirtecinin en fazla yaşı  |MaxAgeSessionMultiFactor  |Oturum belirteçleri (kalıcı ve kalıcı olmayan)  |İptal edilene kadar |

Kullanımdan kaldırılacak ilkeleri bulmak için PowerShell kullanabilirsiniz.  Kuruluşunuzda oluşturulan tüm ilkeleri görmek veya belirli bir ilkeye bağlı olan uygulamaları ve hizmet sorumlularını bulmak için [PowerShell cmdlet 'lerini](configure-token-lifetimes.md#get-started) kullanın.

## <a name="policy-evaluation-and-prioritization"></a>İlke değerlendirmesi ve önceliği belirleme
Belirli bir uygulamaya, kuruluşunuza ve hizmet sorumlularına bir belirteç ömür İlkesi oluşturup bu ilke atayabilirsiniz. Belirli bir uygulama için birden çok ilke uygulanabilir. Geçerli olan belirteç ömrü ilkesi şu kurallara uyar:

* Bir ilke hizmet sorumlusuna açıkça atanmışsa, zorlanır.
* Hizmet sorumlusuna açıkça bir ilke atanmamışsa, hizmet sorumlusunun ana kuruluşa açıkça atanan bir ilke zorlanır.
* Hizmet sorumlusuna veya kuruluşa açıkça bir ilke atanmamışsa, uygulamaya atanan ilke zorlanır.
* Hizmet sorumlusuna, kuruluşa veya uygulama nesnesine bir ilke atanmamışsa, varsayılan değerler zorlanır. ( [Yapılandırılabilir belirteç ömrü özelliklerindeki](#configurable-token-lifetime-properties)tabloya bakın.)

Uygulama nesneleri ve hizmet sorumlusu nesneleri arasındaki ilişki hakkında daha fazla bilgi için, bkz. [Azure Active Directory uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

Belirtecin geçerliliği, belirtecin kullanıldığı sırada değerlendirilir. Erişildiği uygulama üzerinde en yüksek önceliğe sahip ilke devreye girer.

Burada kullanılan tüm zaman dilimlerini C# [TimeSpan](/dotnet/api/system.timespan) nesnesine göre biçimlendirilir-D. hh: mm: ss.  Bu nedenle 80 gün ve 30 dakika olabilir `80.00:30:00` .  0 olduğunda önde gelen D bırakılabilir, bu nedenle 90 dakika olur `00:90:00` .  

## <a name="cmdlet-reference"></a>Cmdlet başvurusu

[Grafik önizleme modülüne yönelik Azure Active Directory PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)içindeki cmdlet 'ler şunlardır.

### <a name="manage-policies"></a>İlkeleri yönetme

İlkeleri yönetmek için aşağıdaki cmdlet 'leri kullanabilirsiniz.

| Cmdlet | Açıklama | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Yeni bir ilke oluşturur. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tüm Azure AD ilkelerini veya belirli bir ilkeyi alır. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Bir ilkeyle bağlantılı tüm uygulamaları ve hizmet sorumlularını alır. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Var olan bir ilkeyi güncelleştirir. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Belirtilen ilkeyi siler. |

### <a name="application-policies"></a>Uygulama ilkeleri
Uygulama ilkeleri için aşağıdaki cmdlet 'leri kullanabilirsiniz.</br></br>

| Cmdlet | Açıklama | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Belirtilen ilkeyi bir uygulamaya bağlar. |
| [Get-Azureadakural Ilkesi](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Bir uygulamaya atanan ilkeyi alır. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Bir uygulamadan bir ilkeyi kaldırır. |

### <a name="service-principal-policies"></a>Hizmet sorumlusu ilkeleri
Hizmet sorumlusu ilkeleri için aşağıdaki cmdlet 'leri kullanabilirsiniz.

| Cmdlet | Açıklama | 
| --- | --- |
| [Add-Azureadservicesprincipalpolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Belirtilen ilkeyi bir hizmet sorumlusu ile bağlantılandırır. |
| [Get-Azureadservicesprincipalpolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Belirtilen hizmet sorumlusuna bağlı herhangi bir ilkeyi alır.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | İlkeyi belirtilen hizmet sorumlusu 'ndan kaldırır.|

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için, [belirteç yaşam sürelerinin nasıl yapılandırılacağı hakkında örnekleri](configure-token-lifetimes.md)okuyun.
