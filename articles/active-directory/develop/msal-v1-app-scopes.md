---
title: Bir v 1.0 uygulaması için kapsamlar (Microsoft kimlik doğrulama kitaplığı) | Mavisi
description: Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir v 1.0 uygulaması için kapsamlar hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532345"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>V 1.0 belirteçlerini kabul eden bir Web API 'SI için kapsamlar

OAuth2 izinleri, bir Azure AD for Developers (v 1.0) Web API (kaynak) uygulamasının istemci uygulamalarına sunduğu izin kapsamlardır. Bu izin kapsamları, izin sırasında istemci uygulamalarına verilebilir. `oauth2Permissions` [Azure Active Directory uygulama bildirimi başvurusunda](reference-app-manifest.md#manifest-reference)hakkında bölümüne bakın.

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının belirli OAuth2 izinlerine erişim istemek için kapsamlar
Bir v 1.0 uygulamasının belirli kapsamları için belirteçler almak istiyorsanız (örneğin, https:\//Graph.Windows.net), istenen bir kaynak tanımlayıcısını istenen bir OAuth2 izniyle birleştirerek kapsamlar oluşturmanız gerekir Bu kaynak için.

Örneğin, Kullanıcı adına, uygulama KIMLIĞI URI 'sinin `ResourceId`bulunduğu bir v 1.0 Web API 'sine erişmek için:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Azure AD Graph API 'si (https:\//Graph.Windows.net/) kullanarak msal.net Azure Active Directory ile okumak ve yazmak isterseniz, aşağıdaki gibi kapsamların bir listesini oluşturursunuz:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Azure Resource Manager API 'sine (https:\//Management.Core.Windows.net/) karşılık gelen kapsamı yazmak isterseniz, aşağıdaki kapsamı istemeniz gerekir (iki eğik çizgi).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Azure Resource Manager API 'SI, hedef kitle talebinde (AUD) bir eğik çizgi beklediği için iki eğik çizgi kullanmanız gerekir ve ardından API adını kapsamdan ayırmak için bir eğik çizgi vardır.

Azure AD tarafından kullanılan mantık şunlardır:

- Bir v 1.0 erişim belirtecine sahip ADAL (v 1.0) uç noktası (tek olası), AUD = kaynak
- MSAL (Microsoft Identity platform (v 2.0) uç noktası) için, v 2.0 belirteçleri, AUD = kaynağı kabul eden bir kaynak için erişim belirteci soran bir. AppID
- MSAL (v 2.0 uç noktası) için, bir v 1.0 erişim belirtecini kabul eden bir kaynak için erişim belirteci isteyen (Yukarıdaki durum), Azure AD, son eğik çizgiden önce her şeyi alarak ve bunu kaynak tanımlayıcısı olarak kullanarak istenen kapsamdaki hedef kitleyi ayrıştırır. Bu nedenle, https\/:/Database.Windows.net "https:\//Database.Windows.net/" kitlesini beklediğinde, "https:\//Database.Windows.net//.default" kapsamını istemeniz gerekir. Ayrıca bkz. GitHub [sorunu #747: Kaynak URL 'sinin sondaki eğik çizgi gözardı edilir ve bu da SQL kimlik](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)doğrulama hatasına neden olur.

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının tüm izinlerine erişim istemek için kapsamlar
Bir v 1.0 uygulamasının tüm statik kapsamları için bir belirteç almak istiyorsanız, API 'nin uygulama KIMLIĞI URI 'sine ". default" ekleyin:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>İstemci kimlik bilgisi akışı/Daemon uygulaması için istek yapılacak kapsamlar
İstemci kimlik bilgileri akışı durumunda geçirilecek kapsam de olur `/.default`. Bu, Azure AD 'ye ("yöneticinin uygulamanın kaydında sahip olduğu tüm uygulama düzeyindeki izinleri) söyler.
