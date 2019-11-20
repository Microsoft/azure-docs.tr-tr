---
title: MSAL. js ve ADAL. js arasındaki farklar
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL. js) için Microsoft kimlik doğrulama kitaplığı ve JavaScript (ADAL. js) için Azure AD kimlik doğrulama kitaplığı ve hangisinin kullanılacağını seçme arasındaki farklar hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d831f9f1cf8dbf565d569f63ee6215fac80949d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803178"
---
# <a name="differences-between-msal-js-and-adal-js"></a>MSAL JS ve ADAL JS arasındaki farklar

JavaScript (MSAL. js) için Microsoft kimlik doğrulama kitaplığı ve JavaScript için Azure AD kimlik doğrulama kitaplığı (ADAL. js), Azure AD varlıklarının kimliğini doğrulamak ve Azure AD 'de belirteçleri istemek için kullanılır. Bu aşamada, çoğu geliştirici, ADAL kullanarak belirteçleri isteyerek Azure AD kimliklerinin (iş ve okul hesapları) kimliğini doğrulamak üzere geliştiriciler için Azure AD (v 1.0) ile çalıştık. Artık, MSAL. js ' yi kullanarak Microsoft Identity platform (v 2.0) aracılığıyla daha geniş bir Microsoft kimliği kümesinin (Azure AD kimlikleri ve Microsoft hesapları ve sosyal ve yerel hesaplar Azure AD B2C aracılığıyla) kimlik doğrulaması yapabilirsiniz.

Bu makalede JavaScript (MSAL. js) için Microsoft kimlik doğrulama kitaplığı ve JavaScript (ADAL. js) için Azure AD kimlik doğrulama kitaplığı arasından seçim yapabileceğiniz ve iki kitaplığı karşılaştıran açıklanır.

## <a name="choosing-between-adaljs-and-msaljs"></a>ADAL. js ve MSAL. js arasında seçim yapma

Çoğu durumda, Microsoft kimlik doğrulama kitaplıklarının en son nesli olan Microsoft Identity platform ve MSAL. js ' yi kullanmak istersiniz. MSAL. js kullanarak, Azure AD (iş ve okul hesapları), Microsoft (kişisel) hesapları (MSA) veya Azure AD B2C ile uygulamanızda oturum açan kullanıcılar için belirteçler elde edersiniz.

Zaten v 1.0 uç noktası (ve ADAL. js) hakkında bilginiz varsa, [v 2.0 uç noktası ile Ilgili ne kadar farklı olduğunu](active-directory-v2-compare.md)okumak isteyebilirsiniz.

Ancak, uygulamanızın daha önceki [Active Directory Federasyon Hizmetleri (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)sürümleriyle oturum açması GEREKIYORSA, adal. js ' yi yine de kullanmanız gerekir.

## <a name="key-differences-in-authentication-with-msaljs"></a>MSAL. js ile kimlik doğrulamasında önemli farklılıklar

### <a name="core-api"></a>Çekirdek API 'SI

* ADAL. js, bir yetkili URL 'SI aracılığıyla uygulamanızın yetkilendirme sunucusuna veya kimlik sağlayıcısına bağlantısının bir örneğinin temsili olarak [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) kullanır. Aksine, MSAL. js API 'SI, Kullanıcı Aracısı istemci uygulaması (istemci kodunun bir Web tarayıcısı gibi bir Kullanıcı aracısında yürütüldüğü bir genel istemci uygulaması formu) etrafında tasarlanmıştır. Bu, bir uygulamanın yetkilendirme sunucusu ile kimlik doğrulama bağlamı örneğini temsil etmesi için `UserAgentApplication` sınıfını sağlar. Daha ayrıntılı bilgi için bkz. [msal. js kullanarak başlatma](msal-js-initializing-client-applications.md).

* ADAL. js ' de, belirteçleri alma yöntemleri `AuthenticationContext` ' da ayarlanan tek bir yetkili ile ilişkilendirilir. MSAL. js ' de, belirteç alma istekleri `UserAgentApplication` ' da ayarlandıklardan farklı yetki değerleri alabilir. Bu, MSAL. js ' nin aynı uygulamadaki birden fazla kiracı ve Kullanıcı hesabı için belirteçleri ayrı olarak almasına ve önbelleğe almasına olanak tanır.

* Kullanıcılardan, ADAL. js ' de `acquireToken` olarak adlandırılmasını istemeden belirteçleri alma ve yenileme yöntemi. MSAL. js ' de, bu işlevselliğe daha açıklayıcı olması için bu yöntemin adı `acquireTokenSilent` ' dır.

### <a name="authority-value-common"></a>Yetkili değeri `common`

V 1.0 'da `https://login.microsoftonline.com/common` yetkilisini kullanmak, kullanıcıların herhangi bir Azure AD hesabıyla oturum açmalarına olanak tanır (herhangi bir kuruluş için).

V 2.0 'da, `https://login.microsoftonline.com/common` yetkilisini kullanarak, kullanıcıların herhangi bir Azure AD kuruluş hesabıyla veya bir Microsoft kişisel hesabıyla (MSA) oturum açmalarına izin vermeyecektir. Oturum açmayı yalnızca Azure AD hesaplarında kısıtlamak için (ADAL. js ile aynı davranış), `https://login.microsoftonline.com/organizations` kullanmanız gerekir. Ayrıntılar için [msal. js kullanarak başlatma](msal-js-initializing-client-applications.md)içindeki `authority` yapılandırma seçeneğine bakın.

### <a name="scopes-for-acquiring-tokens"></a>Belirteçleri almak için kapsamlar
* Belirteçleri almak için kimlik doğrulama isteklerinde kaynak parametresi yerine kapsam

    v 2.0 protokolü isteklerde kaynak yerine kapsamları kullanır. Diğer bir deyişle, uygulamanızın MS Graph gibi bir kaynak için izinleri olan belirteçleri istemesi gerektiğinde, kitaplık yöntemlerine geçirilen değerlerin farkı aşağıdaki gibidir:

    v 1.0: kaynak = https\://graph.microsoft.com

    v 2.0: kapsam = https\://graph.microsoft.com/User.Read

    Şu biçimdeki API URI 'sini kullanarak tüm kaynak API 'leri için kapsam isteyebilirsiniz: Appıduri/Scope örneğin: https: \//mytenant. onmicrosoft. com/uygulamaı/API. Read

    Yalnızca MS Graph API için, `user.read` kapsam değeri https: \//Graph. Microsoft. com/user. Read ile eşlenir ve birbirlerinin yerine kullanılabilir.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Artımlı izin için dinamik kapsamlar.

    V 1.0 kullanarak uygulama oluştururken, kullanıcının oturum açma sırasında izin vermesini sağlamak için uygulamanın gerektirdiği tüm izin (statik kapsamlar) kümesini kaydetmeniz gerekir. V 2.0 'da, izinleri istediğiniz zaman istemek için kapsam parametresini kullanabilirsiniz. Bunlara dinamik kapsamlar denir. Bu, kullanıcının kapsamlar için artımlı onay sağlamasına izin verir. Bu nedenle, yalnızca kullanıcının uygulamanızda oturum açmasını istiyor ve herhangi bir erişime ihtiyacınız yoksa, bunu yapabilirsiniz. Daha sonra kullanıcının takvimini okuma olanağına ihtiyacınız varsa, bu durumda acquireToken yöntemlerinde takvim kapsamını isteyebilir ve kullanıcının onay almasını sağlayabilirsiniz. Örnek:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* V 1.0 API 'Leri için kapsamlar

    MSAL. js kullanarak V 1.0 API 'Leri için belirteçler alırken API 'de kayıtlı tüm statik kapsamları, API 'nin uygulama KIMLIĞI URI 'sine kapsam olarak `.default` ekleyerek isteyebilirsiniz. Örnek:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için [v 1.0 ve v 2.0 karşılaştırması](active-directory-v2-compare.md)bölümüne bakın.
