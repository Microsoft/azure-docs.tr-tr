---
title: MSAL.js ve ADAL.js arasındaki farklar | Mavisi
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL.js) için Microsoft kimlik doğrulama kitaplığı ve JavaScript için Azure AD kimlik doğrulama kitaplığı (ADAL.js) ve hangisinin kullanılacağını seçme arasındaki farklar hakkında bilgi edinin.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a56685c830fc7aa717add3e826c68c04449e378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580855"
---
# <a name="differences-between-msaljs-and-adaljs"></a>MSAL.js ve ADAL.js arasındaki farklar

JavaScript için Microsoft kimlik doğrulama kitaplığı (MSAL.js) ve JavaScript (ADAL.js) için Azure AD kimlik doğrulama kitaplığı, Azure AD varlıklarının kimliğini doğrulamak ve Azure AD 'de belirteçleri istemek için kullanılır. Bu aşamada, çoğu geliştirici, ADAL kullanarak belirteçleri isteyerek Azure AD kimliklerinin (iş ve okul hesapları) kimliğini doğrulamak üzere geliştiriciler için Azure AD (v 1.0) ile çalıştık. Artık MSAL.js kullanarak Microsoft Identity platformu aracılığıyla daha geniş bir Microsoft kimlikleri kümesinin (Azure AD kimlikleri ve Microsoft hesapları ve sosyal ve yerel hesaplar Azure AD B2C aracılığıyla) kimlik doğrulaması yapabilirsiniz.

Bu makalede JavaScript (MSAL.js) için Microsoft kimlik doğrulama kitaplığı ve JavaScript (ADAL.js) için Azure AD kimlik doğrulama kitaplığı arasından seçim yapabileceğiniz ve iki kitaplığı karşılaştıran açıklanır.

## <a name="choosing-between-adaljs-and-msaljs"></a>ADAL.js ve MSAL.js arasında seçim yapma

Çoğu durumda Microsoft kimlik platformunu ve MSAL.js kullanarak en son nesil Microsoft kimlik doğrulama kitaplıkları. MSAL.js kullanarak, uygulamanızda Azure AD (iş ve okul hesapları), Microsoft (kişisel) hesapları (MSA) veya Azure AD B2C ile oturum açan kullanıcılar için belirteçler elde edersiniz.

Zaten v 1.0 uç noktası (ve ADAL.js) hakkında bilginiz varsa, [v 2.0 uç noktası ile ilgili farklı olanları](../azuread-dev/azure-ad-endpoint-comparison.md)okumak isteyebilirsiniz.

Ancak, uygulamanızın daha önceki [Active Directory Federasyon Hizmetleri (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)sürümleriyle oturum açması gerekiyorsa ADAL.js kullanmanız gerekir.

## <a name="key-differences-in-authentication-with-msaljs"></a>MSAL.js ile kimlik doğrulamasında önemli farklılıklar

### <a name="core-api"></a>Çekirdek API 'SI

* ADAL.js, bir yetkili URL 'SI aracılığıyla uygulamanızın yetkilendirme sunucusuna veya kimlik sağlayıcısına bağlantısının bir örneğinin temsili olarak [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) kullanır. Aksine, MSAL.js API 'SI Kullanıcı Aracısı istemci uygulaması (istemci kodunun web tarayıcısı gibi bir Kullanıcı aracısında yürütüldüğü bir genel istemci uygulaması formu) etrafında tasarlanmıştır. `UserAgentApplication`Uygulama, yetkilendirme sunucusu ile uygulamanın kimlik doğrulama bağlamının bir örneğini temsil etmek için sınıfını sağlar. Daha ayrıntılı bilgi için bkz. [MSAL.jskullanarak başlatma ](msal-js-initializing-client-applications.md).

* ADAL.js, belirteçleri elde etmek için yöntemler, içinde ayarlanan tek bir yetkili ile ilişkilendirilir `AuthenticationContext` . MSAL.js, belirteç alma istekleri, içinde ayarlandıklardan farklı yetkili değerleri alabilir `UserAgentApplication` . Bu, MSAL.js aynı uygulamadaki birden fazla kiracı ve Kullanıcı hesabı için belirteçleri ayrı olarak almasına ve önbelleğe almasına olanak tanır.

* Kullanıcıların ADAL.js olarak adlandırılmasını istemeden belirteçleri elde etmek ve yenilemek için yöntemi `acquireToken` . MSAL.js, bu yöntemin `acquireTokenSilent` Bu işlevselliğe daha açıklayıcı olması için adı verilir.

### <a name="authority-value-common"></a>Yetkili değeri `common`

V 1.0 'da, yetkiyi kullanmak `https://login.microsoftonline.com/common` kullanıcıların herhangi bir Azure AD hesabıyla oturum açmalarına olanak tanır (herhangi bir kuruluş için).

V 2.0 sürümünde, yetkiyi kullanarak `https://login.microsoftonline.com/common` , kullanıcıların herhangi bir Azure AD kuruluş hesabıyla veya bir Microsoft kişisel hesabıyla (MSA) oturum açmalarına olanak tanır. Oturum açma yöntemini yalnızca Azure AD hesaplarında (ADAL.js ile aynı davranış) kısıtlamak için kullanın `https://login.microsoftonline.com/organizations` . Ayrıntılar için `authority` [MSAL.jskullanarak başlatma ](msal-js-initializing-client-applications.md)içindeki yapılandırma seçeneğine bakın.

### <a name="scopes-for-acquiring-tokens"></a>Belirteçleri almak için kapsamlar
* Belirteçleri almak için kimlik doğrulama isteklerinde kaynak parametresi yerine kapsam

    v 2.0 protokolü isteklerde kaynak yerine kapsamları kullanır. Diğer bir deyişle, uygulamanızın MS Graph gibi bir kaynak için izinleri olan belirteçleri istemesi gerektiğinde, kitaplık yöntemlerine geçirilen değerlerin farkı aşağıdaki gibidir:

    v 1.0: kaynak = https \: //Graph.Microsoft.com

    v 2.0: kapsam = https \: //Graph.Microsoft.com/user.Read

    Şu biçimdeki API URI 'sini kullanarak tüm kaynak API 'leri için kapsamlar isteyebilirsiniz: appidURI/Scope örneğin: https: \/ /mytenant.onmicrosoft.com/myApi/api.Read

    Yalnızca MS Graph API için bir kapsam değeri `user.read` https:/Graph.Microsoft.com/user.Read ile eşlenir \/ ve birbirlerinin yerine kullanılabilir.

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

    MSAL.js kullanarak V 1.0 API 'Leri için belirteçler alırken, API `.default` 'Nin uygulama KIMLIĞI URI 'sine kapsam olarak ekleyerek API 'de kayıtlı tüm statik kapsamları isteyebilirsiniz. Örnek:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için [v 1.0 ve v 2.0 karşılaştırması](../azuread-dev/azure-ad-endpoint-comparison.md)bölümüne bakın.
