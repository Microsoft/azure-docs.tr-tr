---
title: Hatalar ve özel durumlar (MSAL Android) | Mavisi
titleSuffix: Microsoft identity platform
description: MSAL Android uygulamalarında hataları ve özel durumları, koşullu erişimi ve talep sorunlarını nasıl işleyeceğinizi öğrenin.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761395"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Android için MSAL 'te özel durumları ve hataları işleme

Microsoft kimlik doğrulama kitaplığı 'ndaki (MSAL) özel durumlar, uygulama geliştiricilerinin uygulamayla ilgili sorunları gidermelerine yardımcı olmak için tasarlanmıştır. Özel durum iletileri yerelleştirilmemiş.

Özel durumları ve hataları işlerken, özel durum türünü ve hata kodunu kullanarak özel durumlar arasında ayrım yapabilirsiniz.  Hata kodlarının bir listesi için bkz. [kimlik doğrulama ve yetkilendirme hata kodları](reference-aadsts-error-codes.md).

Oturum açma deneyimi sırasında, yarışmalar, koşullu erişim (MFA, cihaz yönetimi, konum tabanlı kısıtlamalar), belirteç verme ve teminat ve Kullanıcı özellikleri hakkında hatalarla karşılaşabilirsiniz.


|Hata sınıfı | Neden/hata dizesi| Nasıl idare edilecek |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Erişim belirtecini kullanmak için kullanılan yenileme belirteci geçersiz, zaman aşımına uğradı veya iptal edildi. Bu özel durum, bir parola değişikliği olabilir. </li><li>`NO_TOKENS_FOUND`: Erişim belirteci yok ve erişim belirtecini kullanmak için yenileme belirteci bulunamadı.</li> <li>Adım gerekli<ul><li>Çok faktörlü kimlik doğrulaması</li><li>Eksik talepler</li></ul></li><li>Koşullu erişim tarafından engellendi (örneğin, [kimlik doğrulama Aracısı](./msal-android-single-sign-on.md) yüklemesi gerekir)</li><li>`NO_ACCOUNT_FOUND`: Sessiz kimlik doğrulaması önbelleğinde kullanılabilir hesap yok.</li></ul> |`acquireToken()`Kullanıcıdan Kullanıcı adı ve parola girmesini ve çok faktörlü kimlik doğrulaması gerçekleştirmesine izin vermek için çağrısı yapın.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: Kullanıcı veya sunucu tüm kapsamları kabul etmedi. İstenen kapsam desteklenmiyorsa, tanınmazsa veya belirli bir hesap için desteklenmiyorsa sunucu bir kapsamı reddedebilir. </li></ul>| Geliştirici verilen kapsamlarla kimlik doğrulamaya devam edilip edilmeyeceğini veya kimlik doğrulama işlemini sonlandırmaya karar vermelidir. Belirteç alma isteğini yalnızca verilen kapsamlar için yeniden gönderme seçeneği ve geçirerek ve çağırarak izin verilen izinler sağlar `silentParametersForGrantedScopes` `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: Bu istekte gerekli bir parametre eksik, geçersiz bir parametre içeriyor, birden fazla parametre içeriyor ya da başka bir şekilde hatalı biçimlendirilmiş. </li><li>`SERVICE_NOT_AVAILABLE`: Hizmetin kapanmakta olması nedeniyle 500/503/506 hata kodunu temsil eder. </li><li>`UNAUTHORIZED_REQUEST`: İstemcinin bir yetkilendirme kodu isteme yetkisi yok.</li><li>`ACCESS_DENIED`: Kaynak sahibi veya yetkilendirme sunucusu isteği reddetti.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` doğrulama başarısız oldu</li><li>`UNKNOWN_ERROR`: Sunucuya istek başarısız oldu, ancak bir hata ve `error_description` hizmetten geri döndü.</li><ul>| Bu özel durum sınıfı, hizmetle iletişim kurarken oluşan hataları temsil eder, yetkilendirme veya belirteç uç noktalarından olabilir. MSAL, hata ve sunucu yanıtından error_description okur. Genellikle, bu hatalar kodda veya uygulama kayıt portalı 'nda uygulama yapılandırmalarının düzeltilmesi ile çözümlenir. Nadiren bir hizmet kesintisi bu uyarıyı tetikleyebilir ve yalnızca hizmetin kurtarılmasını beklemeden azaltılabilir.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Birden fazla önbellek girdisi bulunur ve SDK, önbellekten doğru erişimi veya yenileme belirtecini tanımlayamıyor. Bu özel durum genellikle, belirteçleri depolamak için SDK 'daki bir hatayı gösterir ya da yetki sessiz istekte sağlanmadı ve birden çok eşleşen belirteç bulundu. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Cihazda etkin bir ağ yok. </li><li>`JSON_PARSE_FAILURE`: SDK JSON biçimini ayrıştıramadı.</li><li>`IO_ERROR`: `IOException` gerçekleşti, bir cihaz veya ağ hatası olabilir. </li><li>`MALFORMED_URL`: URL hatalı biçimlendirilmiş. Bunun nedeni, kimlik doğrulama isteği, yetkilisi veya yeniden yönlendirme URI 'SI oluşturulurken oluşmuş olabilir. </li><li>`UNSUPPORTED_ENCODING`: Kodlama cihaz tarafından desteklenmiyor. </li><li>`NO_SUCH_ALGORITHM`: [Pkce](https://tools.ietf.org/html/rfc7636) sınaması oluşturmak için kullanılan algoritma desteklenmez. </li><li>`INVALID_JWT`: `JWT` sunucu tarafından döndürülen geçerli değil veya boş ya da hatalı biçimlendirilmiş. </li><li>`STATE_MISMATCH`: Yetkilendirme yanıtından gelen durum, yetkilendirme isteğindeki durumla eşleşmedi. Yetkilendirme istekleri için SDK, yeniden yönlendirmenin döndürdüğü durumu ve istekte gönderilen durumu doğrular. </li><li>`UNSUPPORTED_URL`: Desteklenmeyen URL, ADFS yetkilendirme doğrulaması gerçekleştiremez. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: Yetkili, yetkili doğrulaması için desteklenmez. SDK B2C yetkililerini destekler, ancak B2C yetkilendirme doğrulamasını desteklemez. Yalnızca iyi bilinen ana bilgisayar desteklenecektir. </li><li>`CHROME_NOT_INSTALLED`: Cihazda Chrome yüklü değil. SDK, kullanılabiliyorsa, yetkilendirme istekleri için Chrome özel sekmesini kullanır ve Chrome tarayıcısına geri döner. </li><li>`USER_MISMATCH`: Belirteç alma isteğinde verilen Kullanıcı, sunucudan döndürülen kullanıcı ile eşleşmiyor.</li></ul>|Bu özel durum sınıfı, kitaplıkta yerel olan genel hataları temsil eder. Bu özel durumlar, istek düzeltilerek işlenebilir.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: Kullanıcı etkileşimli akışı başlattı ve belirteçleri geri almadan önce isteği iptal etmiş olurlar. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: İçin yetkili belirtilmelidir `acquireTokenSilent` .</li></ul>|Bu hatalar, geliştirici düzeltme bağımsız değişkenleri tarafından azaltılabilir ve etkileşimli kimlik doğrulama, tamamlama geri çağırma, kapsamlar ve geçerli KIMLIĞE sahip bir hesap sağlanmış olabilir.|


## <a name="catching-errors"></a>Yakalama hataları

Aşağıdaki kod parçacığında, sessiz çağrılar durumunda hata yakalama örneği gösterilmektedir `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Android IÇIN msal 'de günlüğe kaydetme](msal-logging-android.md)hakkında daha fazla bilgi edinin.