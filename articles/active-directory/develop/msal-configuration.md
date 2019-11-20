---
title: Android Microsoft kimlik doğrulama kitaplığı (MSAL) yapılandırma dosyasını anlayın
titleSuffix: Microsoft identity platform
description: Azure Active Directory bir uygulamanın yapılandırmasını temsil eden Android Microsoft kimlik doğrulama kitaplığı (MSAL) yapılandırma dosyasına genel bakış.
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: c06bc1ebf3b87f622029e9e875fe478eae7e6a30
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803162"
---
# <a name="android-microsoft-authentication-library-msal-configuration-file"></a>Android Microsoft kimlik doğrulama kitaplığı (MSAL) yapılandırma dosyası

MSAL, varsayılan yetkili, hangi yetkililerin kullanılacağı gibi şeyler için genel istemci uygulamanızın davranışını tanımlamak üzere özelleştirdiğiniz [varsayılan bir CONFIGURATION JSON dosyasıyla](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) birlikte gelir.

Bu makale yapılandırma dosyasında çeşitli ayarları anlamanıza ve MSAL tabanlı uygulamanızda kullanılacak yapılandırma dosyasını nasıl belirtdiğinize yardımcı olur.

## <a name="configuration-settings"></a>Yapılandırma ayarları

### <a name="general-settings"></a>Genel ayarlar

| Özellik | Veri Türü | Gereklidir | Notlar |
|-----------|------------|-------------|-------|
| `client_id` | Dize | Yes | [Uygulama kayıt sayfasından](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) UYGULAMANıZıN istemci kimliği |
| `redirect_uri`   | Dize | Yes | [Uygulama kayıt sayfasından](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) uygulamanızın yeniden yönlendirme URI 'si |
| `authorities` | \<yetkilisi > listeleyin | Hayır | Uygulamanızın ihtiyaç duyacağı yetkililer listesi |
| `authorization_user_agent` | AuthorizationAgent (enum) | Hayır | Olası değerler: `DEFAULT`, `BROWSER`, `WEBVIEW` |
| `http` | HttpConfiguration | Hayır | `HttpUrlConnection` `connect_timeout` ve `read_timeout` yapılandırın |
| `logging` | LoggingConfiguration | Hayır | Günlüğe kaydetme ayrıntı düzeyini belirtir. İsteğe bağlı yapılandırmalara şunlar dahildir: bir Boole değeri alan `pii_enabled`ve `ERROR`, `WARNING`, `INFO`veya `VERBOSE`alan `log_level`. |

### <a name="client_id"></a>client_id

Uygulamanızı kaydettirdiğiniz sırada oluşturulan istemci KIMLIĞI veya uygulama KIMLIĞI.

### <a name="redirect_uri"></a>isteğinde

Uygulamanızı kaydettirdiğiniz sırada kaydettiğiniz yeniden yönlendirme URI 'SI. Yeniden yönlendirme URI 'SI bir aracı uygulamasına ise, aracı uygulamanız için doğru yeniden yönlendirme URI 'SI biçimini kullandığınızdan emin olmak için [ortak istemci uygulamaları Için yeniden YÖNLENDIRME URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) 'sine bakın.

### <a name="authorities"></a>Kaynakça

Sizin tarafınızdan bilinen ve güvenilir olan yetkililer listesi. Burada listelenen yetkililere ek olarak, MSAL Microsoft 'un bilinen bulutların ve yetkililerinin bir listesini almak için Microsoft 'u da sorgular. Bu Kaynakça listesinde, yetkilinin türünü ve `"audience"`gibi ek isteğe bağlı parametreleri belirtin ve uygulamanızın kayıt defteri temelinde uygulamanızın hedef kitlesi ile hizalanması gerekir. Aşağıda, kaynakçanın örnek bir listesi verilmiştir:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD yetkilisi & kitlesini Microsoft Identity platform uç noktalarına eşleyin

| Tür | Hedef Kitle | Kiracı Kimliği | Authority_Url | Sonuç uç noktası | Notlar |
|------|------------|------------|----------------|----------------------|---------|
| AAD | Azureadandpersonmicrosoftaccount | | | https://login.microsoftonline.com/common | `common`, hesabın nerede olduğu, kiracı diğer adıdır. Örneğin, belirli bir Azure Active Directory kiracı veya Microsoft hesabı sistemi. |
| AAD | AzureADMyOrg | contoso.com | | https://login.microsoftonline.com/contoso.com | Yalnızca contoso.com içinde bulunan hesaplar bir belirteç alabilir. Doğrulanmış etki alanı veya kiracı GUID 'SI, kiracı KIMLIĞI olarak kullanılabilir. |
| AAD | AzureADMultipleOrgs | | | https://login.microsoftonline.com/organizations | Bu uç noktayla yalnızca Azure Active Directory hesapları kullanılabilir. Microsoft hesapları, kuruluşların üyesi olabilir. Bir kuruluştaki kaynak için Microsoft hesabı kullanarak bir belirteç almak için, belirteci istediğiniz kuruluş kiracısını belirtin. |
| AAD | Personmicrosoftaccount | | | https://login.microsoftonline.com/consumers | Bu uç noktayı yalnızca Microsoft hesapları kullanabilir. |
| B2C | | | Elde edilen uç noktaya bakın | https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/ | Yalnızca contoso.onmicrosoft.com kiracısında bulunan hesaplar bir belirteç alabilir. Bu örnekte B2C ilkesi, yetkili URL yolunun bir parçasıdır. |

> [!NOTE]
> MSAL içinde yetkili doğrulaması etkinleştirilemiyor ve devre dışı bırakılamaz.
> Yetkililer, yapılandırma yoluyla belirtilen veya Microsoft tarafından meta veriler aracılığıyla bilinen geliştiriciler olarak sizin için bilinmektedir.
> MSAL, bilinmeyen bir yetkiliye belirteç isteği alırsa, `MsalClientException` `UnknownAuthority` sonuçları yazın.

#### <a name="authority-properties"></a>Yetkili özellikleri

| Özellik | Veri türü  | Gereklidir | Notlar |
|-----------|-------------|-----------|--------|
| `type` | Dize | Yes | Uygulama hedeflerinizin kitlesini veya hesap türünü yansıtır. Olası değerler: `AAD`, `B2C` |
| `audience` | Nesne | Hayır | Yalnızca Type =`AAD`olduğunda geçerlidir. Uygulamanızın hedeflediği kimliği belirtir. Uygulama kaydınızdan değeri kullanın |
| `authority_url` | Dize | Yes | Yalnızca Type =`B2C`olduğunda gereklidir. Uygulamanızın kullanması gereken yetkili URL 'sini veya ilkeyi belirtir  |
| `default` | boole | Yes | Bir veya daha fazla sertifika belirtildiğinde tek bir `"default":true` gereklidir. |

#### <a name="audience-properties"></a>Hedef kitle özellikleri

| Özellik | Veri Türü  | Gereklidir | Notlar |
|-----------|-------------|------------|-------|
| `type` | Dize | Yes | Uygulamanızın hedeflemek istediği izleyiciyi belirtir. Olası değerler: `AzureADandPersonalMicrosoftAccount`, `PersonalMicrosoftAccount`, `AzureADMultipleOrgs`, `AzureADMyOrg` |
| `tenant_id` | Dize | Yes | Yalnızca `"type":"AzureADMyOrg"`olduğunda gereklidir. Diğer `type` değerleri için isteğe bağlıdır. Bu, `contoso.com`gibi bir kiracı etki alanı ya da `72f988bf-86f1-41af-91ab-2d7cd011db46`gibi bir kiracı KIMLIĞI olabilir |

### <a name="authorization_user_agent"></a>authorization_user_agent

Bir hesapta oturum açarken veya bir kaynağa erişimi yetkilendirirken, ekli bir Web görünümü veya cihazda varsayılan tarayıcı kullanılıp kullanılmayacağını belirtir.

Olası değerler:
- `DEFAULT`: sistem tarayıcısını tercih eder. Cihazda bir tarayıcı yoksa, katıştırılmış Web görünümünü kullanır.
- `WEBVIEW`: Katıştırılmış Web görünümünü kullanın.
- `BROWSER`: cihazdaki varsayılan tarayıcıyı kullanır.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Birden çok ulusal bulutu destekleyen istemciler için `true`belirtin. Daha sonra Microsoft Identity platformu yetkilendirme ve belirteç kullanım sırasında doğru Ulusal buluta otomatik olarak yönlendirilir. `AuthenticationResult`ilişkili yetkiyi inceleyerek, oturum açmış olan hesabın Ulusal bulutunu belirleyebilirsiniz. `AuthenticationResult`, belirteç istediğiniz kaynağın buluta özgü Ulusal uç nokta adresini sağlamayacağını unutmayın.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Microsoft Identity broker ile uyumlu bir aracı yeniden yönlendirme URI 'SI kullanıp kullanmayacağınızı belirten bir Boole değeri. Aracı uygulamanızda kullanmak istemiyorsanız `false` olarak ayarlayın.

Hedef kitle `"MicrosoftPersonalAccount"`olarak ayarlanmış AAD yetkilisini kullanıyorsanız, aracı kullanılmaz.

### <a name="http"></a>http

HTTP zaman aşımları için genel ayarları yapılandırın, örneğin:

| Özellik | Veri türü | Gereklidir | Notlar |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Hayır | Milisaniye cinsinden süre |
| `read_timeout` | int | Hayır | Milisaniye cinsinden süre |

### <a name="logging"></a>Açmak

Günlüğe kaydetme için aşağıdaki genel ayarlar verilmiştir:

| Özellik | Veri Türü  | Gereklidir | Notlar |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boole | Hayır | Kişisel verilerin yayanıp bildirilmeyeceğini belirtir |
| `log_level`   | boole | Hayır | Çıktının kaydedileceği günlük iletileri |
| `logcat_enabled` | boole | Hayır | Günlüğe kaydetme arabirimine ek olarak günlük Cat 'e çıkış yapılıp yapılmayacağını belirtir |

### <a name="account_mode"></a>account_mode

Uygulamanızda aynı anda kaç tane hesap kullanılabileceğini belirtir. Olası değerler şunlardır:

- `MULTIPLE` (varsayılan)
- `SINGLE`

Bu ayar ile eşleşmeyen bir hesap modunu kullanarak bir `PublicClientApplication` oluşturmak özel durum oluşmasına neden olur.

Tek ve birden çok hesap arasındaki farklar hakkında daha fazla bilgi için bkz. [tek ve birden çok hesap uygulamaları](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

MSAL ile uyumlu tarayıcıların izin verilenler listesi. Bu tarayıcılar, yeniden yönlendirmeleri özel amaçlar halinde doğru şekilde işler. Bu listeye ekleyebilirsiniz. Varsayılan yapılandırma aşağıda gösterilen varsayılan yapılandırmada verilmiştir.
``
## <a name="the-default-msal-configuration-file"></a>Varsayılan MSAL yapılandırma dosyası

MSAL ile birlikte gelen varsayılan MSAL yapılandırması aşağıda gösterilmiştir. [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)'da en son sürümü görebilirsiniz.

Bu yapılandırma, sağladığınız değerlere göre takıma alınmıştır. Sağladığınız değerler varsayılan değerleri geçersiz kılar.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Örnek temel yapılandırma

Aşağıdaki örnek, istemci KIMLIĞINI, yeniden yönlendirme URI 'sini, bir aracı yeniden yönlendirmenin kaydedilip kaydedilmediğini ve bir yetkililer listesini belirten temel bir yapılandırmayı gösterir.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Yapılandırma dosyası kullanma

1. Bir yapılandırma dosyası oluşturun. Özel yapılandırma dosyanızı `res/raw/auth_config.json`oluşturmanız önerilir. Ancak istediğiniz yere koyabilirsiniz.
2. `PublicClientApplication`oluştururken MSAL yapılandırmanıza nereden bakacağınızı söyleyin. Örnek:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
