---
title: Giriş bölgesi bulma ilkesini kullanarak Azure AD 'de oturum açma otomatik hızlandırmasını engelle
description: Federal IDPs 'Ler için domain_hint otomatik hızlandırmayı nasıl önleyeceğinizi öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375559"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Giriş bölgesi bulma ilkesiyle Kullanıcı oturumu sırasında bir federe ıDP 'ye otomatik hızlandırmayı devre dışı bırak

[Ana bölge bulma ilkesi](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) yöneticilere kullanıcıların kimliklerini nasıl ve nerede doğrulayacağını denetlemek için birden çok yol sunar. `domainHintPolicy`HRD ilkesinin bölümü, Federasyon kullanıcılarını her zaman Azure AD oturum açma sayfasını ziyaret ettiğinden ve etki alanı ipuçları nedeniyle federe IDP 'ye otomatik olarak hızlandırılmayan şekilde [Fido](../authentication/howto-authentication-passwordless-security-key.md)gibi bulut tarafından yönetilen kimlik bilgilerine geçirmeye yardımcı olmak için kullanılır.

Bu ilke, uygulamaların bir yöneticinin oturum açma işlemi sırasında etki alanı Ekle ipuçlarını denetleyemediği veya güncelleştiremediği durumlarda gereklidir.  Örneğin, kullanıcıyı `outlook.com/contoso.com` `&domain_hint=contoso.com` doğrudan etki alanı için Federal IDP 'ye otomatik olarak hızlandırmak üzere eklenen parametresi ile bir oturum açma sayfasına gönderir `contoso.com` . Federal ıDP 'ye gönderilen yönetilen kimlik bilgilerine sahip kullanıcılar, yönetilen kimlik bilgilerini kullanarak oturum açıp rastgele oturum açma deneyimleri olan güvenliği ve sinir bozucu kullanıcıları azaltır. Yöneticilerin yönetilen kimlik bilgilerini kullanıma almasını sağlayan Yöneticiler, kullanıcıların her zaman yönetilen kimlik bilgilerini kullanmasını sağlamak için [Bu ilkeyi de ayarlamanıza](#suggested-use-within-a-tenant) olanak sağlar.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy ayrıntıları

HRD ilkesinin DomainHintPolicy bölümü, yöneticinin belirli etki alanlarını ve uygulamaları etki alanı ipucu kullanımından geri almasına olanak tanıyan bir JSON nesnesidir.  Bu, Azure AD oturum açma sayfasına, `domain_hint` oturum açma isteğinde bir parametre yokmuş gibi davranmasını söyler.

### <a name="the-respect-and-ignore-policy-sections"></a>Dikkate alma ve yoksayma ilkesi bölümleri

|Section | Anlamı | Değerler |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Bu etki alanı ipucu istekte gönderilirse, bunu yoksayın. |Etki alanı adresleri dizisi (örneğin `contoso.com` ). De desteklenir `all_domains`|
|`RespectDomainHintForDomains`| İstekte bu etki alanı ipucu gönderilirse, `IgnoreDomainHintForApps` istekteki uygulamanın otomatik olarak hızlandırılmadığını belirten bile buna dikkat edin. Bu, ağınızdaki etki alanı ipuçlarının kullanımdan kaldırılması için kullanılır. bazı etki alanlarının hala hızlandırılmış olması gerektiğini belirtebilirsiniz. | Etki alanı adresleri dizisi (örneğin `contoso.com` ). De desteklenir `all_domains`|
|`IgnoreDomainHintForApps`| Bu uygulamadaki bir istek bir etki alanı ipucuyla birlikte geliyorsa, bunu yoksayın. | Uygulama kimlikleri dizisi (GUID 'Ler). De desteklenir `all_apps`|
|`RespectDomainHintForApps` |Bu uygulamadaki bir istek bir etki alanı ipucuyla birlikte geliyorsa, bu `IgnoreDomainHintForDomains` etki alanını da içerse de buna dikkat edin. Etki alanı ipuçları olmadan kes 'i bulduklarında bazı uygulamaların çalışmaya devam etmesini sağlamak için kullanılır. | Uygulama kimlikleri dizisi (GUID 'Ler). De desteklenir `all_apps`|

### <a name="policy-evaluation"></a>İlke değerlendirmesi

DomainHintPolicy Logic, bir etki alanı İpucu içeren her bir gelen istek üzerinde çalışır ve istekteki iki veri parçasına (etki alanı ipucunda etki alanı) ve istemci KIMLIĞINE (uygulama) göre hızlandırılır. Bir etki alanı veya uygulamanın kısa "öncelik" bölümünde, belirli bir etki alanı veya uygulama için bir etki alanı ipucunu "gözardı etme" yönergesinden önceliklidir.

1. Herhangi bir etki alanı ipucu ilkesi yokluğunda veya 4 bölümden hiçbiri belirtilen uygulamaya veya etki alanı ipucuna başvurmadıysanız, [HRD ilkesinin geri kalanı değerlendirilir](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Ya da her ikisi de, `RespectDomainHintForApps` `RespectDomainHintForDomains` istek içinde uygulama veya etki alanı ipucunu içeriyorsa, Kullanıcı, istenen şekilde Federasyon IDP 'ye otomatik olarak hızlandırılır.
1. Ya (ya da her ikisi) ya `IgnoreDomainHintsForApps` da `IgnoreDomainHintsForDomains` istekteki etki alanı ipucuya ya da varsa ve "dikkate al" bölümleri tarafından başvurulmamışsa, istek otomatik olarak hızlandırılır ve Kullanıcı bir Kullanıcı adı sağlamak IÇIN Azure AD oturum açma sayfasında kalır.

Kullanıcı oturum açma sayfasında bir Kullanıcı adı girdikten sonra, kendilerine kaydolduklarında, yönetilen kimlik bilgilerini kullanabilir.  Yönetilen bir kimlik bilgisi kullanmamalıdır veya hiç kaydolmadıysanız, her zamanki gibi kimlik bilgisi girişi için Federasyon ıDP 'ye alınacaktır.

## <a name="suggested-use-within-a-tenant"></a>Kiracı içinde önerilen kullanım

Federasyon etki alanlarının yöneticileri, HRD ilkesinin bu bölümünü dört aşamalı bir planda ayarlamanız gerekir. Bu planın hedefi, sonunda, bir Kiracıdaki tüm kullanıcıların etki alanı veya uygulamadan bağımsız olarak kendi yönetilen kimlik bilgilerini kullanma fırsatına sahip olması, kullanım üzerinde sabit bağımlılıklara sahip olan uygulamaları kaydetmektir `domain_hint` .  Bu plan, yöneticilerin bu uygulamaları bulmasına, yeni ilkeden muaf tuttuklarını ve kiracının geri kalanında değişikliği sunmaya devam etmesine yardımcı olur.

1. Bu değişikliği ilk olarak almak için bir etki alanı seçin.  Bu, test etki alanınız olacaktır, bu nedenle UX 'teki değişikliklere daha fazla yağtıcı olabilecek bir tane seçin (yani farklı bir oturum açma sayfası görüyor).  Bu, bu etki alanı adını kullanan tüm uygulamalardan tüm etki alanı ipuçlarını yoksayar. Bu ilkeyi kiracınızda [ayarlama](#configuring-policy-through-graph-explorer) -varsayılan HRD ilkeniz:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Sınama etki alanı kullanıcılarından geri bildirim toplayın. Bu değişikliğin sonucu veren uygulamalar için ayrıntıları toplayın-etki alanı ipucu kullanımı bağımlılığı vardır ve güncelleştirilmeleri gerekir. Şimdilik bunları `RespectDomainHintForApps` bölümüne ekleyin:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. İlke dağıtımını yeni etki alanlarına genişletmeye devam edin ve daha fazla geri bildirim toplamayı deneyin.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Tüm etki alanlarını doldurun ve hızlandırmaya devam etmeniz gereken bunları muaf tutma:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. adım tamamlandıktan sonra, ' de dışındaki tüm kullanıcılar, `guestHandlingDomain.com` etki alanı ipuçları bir Federasyon IDP 'ye otomatik hızlandırmasına neden olduğunda bile Azure AD oturum açma sayfasında oturum açabilir.  Bunun istisnası, oturum açma isteğinde bulunan uygulamanın muaf tutulanlardan biridir. bu uygulamalar için, tüm etki alanı ipuçları yine de kabul edilecektir.

## <a name="configuring-policy-through-graph-explorer"></a>Graph Explorer aracılığıyla ilke yapılandırma

Microsoft Graph kullanarak [HRD ilkesini](/graph/api/resources/homeRealmDiscoveryPolicy) her zamanki gibi ayarlayın.  

1. [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)'da Policy. ReadWrite. ApplicationConfiguration iznini verin.  
1. URL 'YI kullan `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Yeni ilkeyi bu URL 'ye GÖNDERIN veya `/policies/homerealmdiscoveryPolicies/{policyID}` var olan bir dosyanın üzerine yazıyorsanız ona yama yapın.

POST veya PATCH içeriği:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Graph kullanırken JSON bölümünün kaçış için eğik çizgi kullandığınızdan emin olun `Definition` .  

`isOrganizationDefault` true olmalı, ancak displayName ve Definition değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Passwordless güvenlik anahtarı oturum açmayı etkinleştir](../authentication/howto-authentication-passwordless-security-key.md)
* [Microsoft Authenticator uygulamayla passwordless oturum açmayı etkinleştirme](../authentication/howto-authentication-passwordless-phone.md)