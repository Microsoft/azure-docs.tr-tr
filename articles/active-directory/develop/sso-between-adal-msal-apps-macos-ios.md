---
title: ADAL & MSAL uygulamaları (iOS/macOS) arasındaki SSO | Mavisi
titleSuffix: Microsoft identity platform
description: ADAL ve MSAL uygulamaları arasında SSO paylaşma hakkında bilgi edinin
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584256"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Nasıl yapılır: macOS ve iOS 'ta ADAL ve MSAL uygulamaları arasında SSO

İOS için Microsoft kimlik doğrulama kitaplığı (MSAL), uygulamalar arasında [adal hedefi-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ile SSO durumunu paylaşabilir. Uygulamalarınızı kendi hızınızda MSAL geçirebilirsiniz, böylece kullanıcılarınızın, ADAL ve MSAL tabanlı uygulamalar karışımından bile, platformlar arası SSO 'dan yine de faydalanabilir.

MSAL SDK kullanarak uygulamalar arasında SSO ayarlama kılavuzunu arıyorsanız, bkz. [birden çok uygulama arasında sessız SSO](single-sign-on-macos-ios.md#silent-sso-between-apps). Bu makale, ADAL ve MSAL arasında SSO 'ya odaklanır.

SSO uygulayan ayrıntılar, kullanmakta olduğunuz ADAL sürümüne bağlıdır.

## <a name="adal-27x"></a>ADAL 2.7. x

Bu bölüm MSAL ve ADAL 2.7. x arasındaki SSO farklarını anlatmaktadır

### <a name="cache-format"></a>Önbellek biçimi

ADAL 2.7. x, MSAL önbellek biçimini okuyabilir. ADAL 2.7. x sürümü ile uygulamalar arası SSO için özel bir şey yapmanız gerekmez. Ancak, bu iki kitaplığı destekledikleri hesap tanımlayıcılarının farklarından haberdar olun.

### <a name="account-identifier-differences"></a>Hesap tanımlayıcı farklılıkları

MSAL ve ADAL farklı hesap tanımlayıcıları kullanır. ADAL birincil hesap tanımlayıcısı olarak UPN kullanır. MSAL, bir nesne KIMLIĞI ve AAD hesapları için kiracı KIMLIĞI ve `sub` diğer hesap türleri için bir talep temel alan, görüntülenebilen olmayan bir hesap tanımlayıcısı kullanır.

`MSALAccount`Msal sonucunda bir nesne aldığınızda, özelliğinde bir hesap tanımlayıcısı bulunur `identifier` . Uygulama, sonraki sessiz istekler için bu tanımlayıcıyı kullanmalıdır.

Buna ek olarak `identifier` , `MSALAccount` nesnesi adında görüntülenebilen bir tanımlayıcı içerir `username` . Bu, `userId` adal içine çeviren. `username` benzersiz bir tanımlayıcı olarak değerlendirilmez ve her zaman değişebilir, bu nedenle yalnızca ADAL ile geriye dönük uyumluluk senaryolarında kullanılmalıdır. MSAL, veya kullanan önbellek sorgularını `username` destekler `identifier` , burada, sorgulama `identifier` önerilir.

Aşağıdaki tablo, ADAL ve MSAL arasındaki hesap tanımlayıcı farklarını özetler:

| Hesap tanımlayıcısı                | MSAL                                                         | ADAL 2.7. x      | Eski ADAL (ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| görüntülenebilen tanımlayıcı            | `username`                                                   | `userId`        | `userId`                       |
| benzersiz, görüntülenebilen olmayan tanımlayıcı | `identifier`                                                 | `homeAccountId` | Yok                            |
| Bilinen hesap kimliği yok               | İçindeki API aracılığıyla tüm hesapları sorgula `allAccounts:``MSALPublicClientApplication` | Yok             | Yok                            |

Bu, bu `MSALAccount` tanımlayıcıları sağlayan arabirimdir:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>MSAL 'den ADAL 'ye SSO

Bir MSAL uygulamanız ve bir ADAL uygulamanız varsa ve Kullanıcı ilk olarak MSAL tabanlı uygulamada oturum açarsa, `username` `MSALAccount` nesneyi nesnesinden kaydederek ve bunu olarak adal tabanlı UYGULAMANıZA geçirerek adal uygulamasında SSO 'yu alabilirsiniz `userId` . ADAL daha sonra API ile sessizce hesap bilgilerini bulabilir `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` .

### <a name="sso-from-adal-to-msal"></a>ADAL 'den MSAL 'ye SSO

Bir MSAL uygulamanız ve bir ADAL uygulamanız varsa ve Kullanıcı ilk olarak ADAL tabanlı uygulamada oturum açarsa, MSAL 'de hesap aramaları için ADAL Kullanıcı tanımlayıcılarını kullanabilirsiniz. Bu, ADAL 'ten MSAL 'e geçiş yaparken de geçerlidir.

#### <a name="adals-homeaccountid"></a>ADAL 'ın Homeaccountıd

ADAL 2.7. x, `homeAccountId` `ADUserInformation` Bu özellik aracılığıyla sonuç içindeki nesnesindeki öğesini döndürür:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` ADAL 'ın ' de MSAL ile eşdeğerdir `identifier` . API ile hesap aramalarıyla ilgili MSAL için kullanmak üzere bu tanımlayıcıyı kaydedebilirsiniz `accountForIdentifier:error:` .

#### <a name="adals-userid"></a>ADAL 's `userId`

`homeAccountId`Kullanılabilir değilse veya yalnızca görüntülenebilen bir tanımlayıcıya sahipseniz, `userId` msal 'de hesabı aramak için Adal ' ı kullanabilirsiniz.

MSAL ' de, önce veya ile bir hesap `username` bulun `identifier` . `identifier`, Varsa sorgulamak için her zaman kullanın ve yalnızca `username` geri dönüş olarak kullanın. Hesap bulunursa, çağrılarında hesabı kullanın `acquireTokenSilent` .

Amaç-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

SWIFT

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL desteklenen hesap arama API 'Leri:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

Bu bölüm MSAL ve ADAL 2. x-2.6.6 arasındaki SSO farklarını ele alır.

Eski ADAL sürümleri, MSAL önbellek biçimini yerel olarak desteklemez. Ancak, ADAL 'den MSAL 'e kesintisiz geçiş sağlamak için MSAL, Kullanıcı kimlik bilgilerini yeniden sormadan eski ADAL önbellek biçimini okuyabilir.

, `homeAccountId` Eskı adal sürümlerinde kullanılamadığından, aşağıdakileri kullanarak hesapları aramanız gerekir `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Örnek:

Amaç-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

SWIFT

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternatif olarak, diğer tüm hesapları okuyabilir ve bu da ADAL 'den hesap bilgilerini okuyacaksınız:

Amaç-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

SWIFT

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin
