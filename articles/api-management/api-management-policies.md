---
title: Azure API Management ilkeleri | Microsoft Docs
description: Azure API Management'ta kullanılabilecek ilkeler hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219477"
---
# <a name="api-management-policies"></a>API Management ilkeleri
Bu bölüm aşağıdaki API Management ilkeleri için bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](api-management-howto-policies.md).  
  
 İlkeler, yayımcının API 'nin davranışını yapılandırma yoluyla değiştirmesine olanak tanıyan güçlü bir yetenektir. İlkeler, bir API 'nin isteği veya yanıtı üzerinde sırayla yürütülen deyimler koleksiyonudur. Popüler deyimler, XML 'den JSON 'a biçim dönüştürmeyi ve bir geliştiriciden gelen çağrıların miktarını kısıtlamak için çağrı hızı sınırını içerir. Birçok daha fazla ilke, kutudan daha fazla kullanılabilir.  
  
 İlke ifadeleri herhangi bir API Management ilkesinde, ilke aksini belirtmedikçe, öznitelik değerleri ya da metin değerleri olarak kullanılabilir. [Akışı denetle](api-management-advanced-policies.md#choose) ve [Değişken ayarla](api-management-advanced-policies.md#set-variable) gibi bazı ilkeler ilke ifadelerini temel alır. Daha fazla bilgi için bkz. [Gelişmiş ilkeler](api-management-advanced-policies.md#AdvancedPolicies) ve [İlke ifadeleri](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a>Elerindeki  
  
-   [Erişimi kısıtlama ilkeleri](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Http üstbilgisini denetle](api-management-access-restriction-policies.md#CheckHTTPHeader) -http üstbilgisinin varlığını ve/veya değerini uygular.  
    -   [Abonelik ile çağrı hızını sınırla](api-management-access-restriction-policies.md#LimitCallRate) -çağrı hızını her abonelik IÇIN sınırlayarak API kullanım artışlarını engeller.  
    -   [Anahtara göre çağrı hızını sınırla](api-management-access-restriction-policies.md#LimitCallRateByKey) -çağrı hızını anahtar TEMELINDE sınırlayarak API kullanım artışlarını engeller.  
    -   Belirli IP adreslerinden ve/veya adres aralıklarından çağrı [IP 'leri filtrelerini kısıtla](api-management-access-restriction-policies.md#RestrictCallerIPs) (izin verir/reddeder).  
    -   [Kullanım kotasını aboneliğe göre ayarla](api-management-access-restriction-policies.md#SetUsageQuota) -yenilenebilir bir veya yaşam süresi araması hacmi ve/veya bant genişliği kotasını abonelik başına temelinde zorlamanıza olanak sağlar.  
    -   [Anahtar temelinde kullanım kotasını ayarlama](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -bir yenilenebilir veya yaşam süresi çağrı hacmi ve/veya bant genişliği kotasını anahtar başına temelinde zorlamanıza olanak sağlar.  
    -   [JWT 'Yi doğrula](api-management-access-restriction-policies.md#ValidateJWT) -BELIRTILEN bir http üst bilgisinden veya belirtilen sorgu parametresinden AYıKLANAN bir JWT 'ın varlığını ve geçerliliğini zorlar.  
-   [Gelişmiş ilkeler](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Denetim akışı](api-management-advanced-policies.md#choose) -Koşullu ifadeler değerlendirmesi temelinde ilke deyimlerini koşullu olarak uygular.  
    -   [İlet isteği](api-management-advanced-policies.md#ForwardRequest) -isteği arka uç hizmetine iletir.
    -   [Eşzamanlılık sınırı](api-management-advanced-policies.md#LimitConcurrency) -geçen ilkelerin aynı anda belirtilen sayıda istekten daha fazla tarafından yürütülmesini önler.
    -   [Olay Hub 'ında günlüğe kaydet](api-management-advanced-policies.md#log-to-eventhub) -belirtilen biçimdeki Iletileri bir günlükçü varlığı tarafından tanımlanan bir ileti hedefine gönderir.
    -   [Sahte yanıt](api-management-advanced-policies.md#mock-response) -işlem hattı yürütmesini iptal eder ve doğrudan çağırana bir yanıt döndürür.
    -   [Yeniden dene](api-management-advanced-policies.md#Retry) -koşul karşılanana kadar, ekteki ilke deyimlerinin yürütülmesini yeniden dener. Yürütme belirtilen zaman aralıklarıyla ve belirtilen yeniden deneme sayısına kadar yinelenir.  
    -   [Dönüş yanıtı](api-management-advanced-policies.md#ReturnResponse) -işlem hattı yürütmesini iptal eder ve belirtilen yanıtı doğrudan çağırana döndürür.  
    -   [Tek yönlü gönderme isteği](api-management-advanced-policies.md#SendOneWayRequest) -bir yanıtı beklemeden belirtilen URL 'ye bir istek gönderir.  
    -   [Istek gönder](api-management-advanced-policies.md#SendRequest) -belirtilen URL 'ye bir istek gönderir.
    -   [Http proxy 'Yi ayarlama](api-management-advanced-policies.md#SetHttpProxy) -iletilen ISTEKLERI bir http proxy 'si aracılığıyla yönlendirmenize olanak tanır.
    -   [Set değişkeni](api-management-advanced-policies.md#set-variable) -daha sonra erişim için adlandırılmış bağlam değişkeninde bir değeri kalıcı hale getirin.  
    -   [Set Request Yöntemi](api-management-advanced-policies.md#SetRequestMethod) -bir Istek için http yöntemini değiştirmenize izin verir.  
    -   [Durum kodu ayarla](api-management-advanced-policies.md#SetStatus) -http durum kodunu belirtilen değere dönüştürür.  
    -   [Trace](api-management-advanced-policies.md#Trace) - [API denetçisi](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) çıktısına, Application Insights Telemetriler ve tanılama günlüklerine özel izlemeler ekler.  
    -   [Bekleme](api-management-advanced-policies.md#Wait) -devam etmeden önce, kapalı [gönderme isteği](api-management-advanced-policies.md#SendRequest), [önbellekten değer alma](api-management-caching-policies.md#GetFromCacheByKey)veya [Denetim akışı](api-management-advanced-policies.md#choose) ilkelerinin tamamlanmasını bekler.  
-   [Kimlik doğrulama ilkeleri](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   Temel kimlik doğrulaması kullanarak arka uç hizmeti ile temel kimlik doğrulaması [Ile kimlik](api-management-authentication-policies.md#Basic) doğrulaması yapın.  
    -   [İstemci sertifikası Ile kimlik doğrulama](api-management-authentication-policies.md#ClientCertificate) -istemci sertifikaları kullanarak arka uç hizmetiyle kimlik doğrulaması yapın.  
    -   Yönetilen [kimlik Ile kimlik doğrulama](api-management-authentication-policies.md#ManagedIdentity) - [yönetilen kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)kullanarak arka uç hizmetiyle kimlik doğrulaması yapın.  
-   [Önbelleğe alma ilkeleri](api-management-caching-policies.md#CachingPolicies)  
    -   [Önbellekten al](api-management-caching-policies.md#GetFromCache) -önbellek araması yapın ve kullanılabilir olduğunda geçerli bir önbelleğe alınmış yanıt döndürür.  
    -   [Önbellekte depola](api-management-caching-policies.md#StoreToCache) -belirtilen önbellek denetimi yapılandırmasına göre yanıtı önbelleğe alır.  
    -   [Önbellekten değer al](api-management-caching-policies.md#GetFromCacheByKey) -önbelleğe alınmış bir öğeyi anahtara göre alır.  
    -   [Önbellekte depolama değeri](api-management-caching-policies.md#StoreToCacheByKey) -bir öğeyi bir anahtarla önbelleğe saklayın.  
    -   [Önbellekten değer kaldır](api-management-caching-policies.md#RemoveCacheByKey) -önbellekteki bir öğeyi anahtarla kaldırın.  
-   [Çapraz etki alanı ilkeleri](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Etki alanları arası çağrılara Izin ver](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -API 'Yi Adobe Flash ve Microsoft Silverlight tarayıcı tabanlı istemcilerden erişilebilir hale getirir.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -tarayıcı tabanlı istemcilerden etki alanları arası çağrılara izin vermek için bir işleme veya bir API 'ye çıkış noktaları arası kaynak PAYLAŞıMı (CORS) desteği ekler.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -JavaScript tarayıcı tabanlı istemcilerden etki alanları arası çağrılara izin vermek için, doldurma (JSONP) desteğiyle birlikte bir Işleme veya API 'yi ekler.  
-   [Dönüştürme ilkeleri](api-management-transformation-policies.md#TransformationPolicies)  
    -   [JSON 'ı XML 'e Dönüştür](api-management-transformation-policies.md#ConvertJSONtoXML) -JSON 'dan XML 'e istek veya Yanıt gövdesini dönüştürür.  
    -   [XML 'YI JSON 'A Dönüştür](api-management-transformation-policies.md#ConvertXMLtoJSON) -istek veya Yanıt gövdesini XML 'den JSON 'a dönüştürür.  
    -   [Gövdede dize bul ve Değiştir](api-management-transformation-policies.md#Findandreplacestringinbody) -bir istek veya Yanıt alt dizesini bulur ve farklı bir alt dizeyle değiştirir.  
    -   Yanıt gövdesindeki içerik-yazma (maske) bağlantılarında [bulunan URL 'leri](api-management-transformation-policies.md#MaskURLSContent) , ağ geçidi aracılığıyla eşdeğer bağlantıya işaret eden şekilde maskelerle.  
    -   [Arka uç hizmetini ayarlama](api-management-transformation-policies.md#SetBackendService) -gelen istek için arka uç hizmetini değiştirir.  
    -   [Gövde ayarla](api-management-transformation-policies.md#SetBody) -ileti gövdesini gelen ve giden istekler için ayarlar.  
    -   [Http üstbilgisini ayarla](api-management-transformation-policies.md#SetHTTPheader) -mevcut bir yanıt ve/veya istek üstbilgisine bir değer atar veya yeni bir yanıt ve/veya istek üst bilgisi ekler.  
    -   [Sorgu dizesi parametresini ayarla](api-management-transformation-policies.md#SetQueryStringParameter) -ekler, değerini değiştirir veya istek sorgu dizesi parametresini siler.  
    -   [Yeniden yazma URL 'si](api-management-transformation-policies.md#RewriteURL) -BIR istek URL 'sini ortak formdan Web hizmeti tarafından beklenen biçime dönüştürür.  
    -   [XSLT kullanarak XML dönüştürme](api-management-transformation-policies.md#XSLTransform) -istek veya yanıt gövdesinde XML 'e bir XSL dönüştürmesi uygular.  



## <a name="next-steps"></a>Sonraki adımlar
İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API Management ilkeler](api-management-howto-policies.md)
+ [API dönüştürme](transform-api.md)
+ [İlke örnekleri](policy-samples.md)   
