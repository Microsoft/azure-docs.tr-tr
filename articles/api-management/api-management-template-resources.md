---
title: Azure API Management şablonu kaynakları | Microsoft Docs
description: Azure API Management 'de geliştirici portalı şablonlarında kullanılabilecek kaynak türleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176501"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management şablonu kaynakları
Azure API Management, geliştirici portalı şablonlarında kullanılmak üzere aşağıdaki kaynak türlerini sağlar.  
  
-   [Dize kaynakları](#strings)  
  
-   [Glif kaynakları](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a>Dize kaynakları  
 API Management, Geliştirici Portalında kullanılmak üzere kapsamlı bir dize kaynakları kümesi sağlar. Bu kaynaklar API Management tarafından desteklenen tüm dillerde yerelleştirilir. Varsayılan şablon kümesi, bu kaynakları sayfa üstbilgileri, Etiketler ve Geliştirici Portalında görüntülenen sabit dizeler için kullanır. Şablonlarınızın bir dize kaynağını kullanmak için aşağıdaki örnekte gösterildiği gibi, kaynak dize önekini ve ardından dize adını belirtin.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Aşağıdaki örnek, ürün listesi şablonundan verilmiştir ve sayfanın üst kısmındaki **ürünleri** görüntüler.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Aşağıdaki yerelleştirme seçenekleri desteklenir:

| Yerel ayar    | Dil               |
|-----------|------------------------|
| "tr"      | Ingilizce              |
| 'ye      | "Čeština"              |
| seçimini      | Deutsch              |
| es      | "Espabir ol"              |
| kesir      | Français             |
| Hu      | Magyar               |
| içerdiği      | Italiano             |
| "ja-JP"   | "日本語"                |
| dili      | "한국어"                |
| nl      | Nederlands           |
| pl      | Polski               |
| "pt-br"   | "Português (BRASIL)"   |
| "PT-PT"   | "Português (Portekiz)" |
| ru      | "Русский"              |
| v      | Svenska              |
| tr      | Türkçe               |
| "zh-Hans" | "中文(简体)"           |
| "zh-Hant" | "中文(繁體)"           |

 Geliştirici portalı şablonlarınızda kullanılabilecek dize kaynakları için aşağıdaki tablolara bakın. Tablo adını bu tablodaki dize kaynaklarının ön eki olarak kullanın.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [Appayrıntılar dizeleri](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [Ortak kaynaklar](#CommonResources)  
  
-   [Ortak dizeler](#CommonStrings)  
  
-   [Belgelerle](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [Issuesstrings](#IssuesStrings)  
  
-   [Notfounddizeleri](#NotFoundStrings)  
  
-   [Productayrıntılar dizeleri](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [Signınresources](#SigninResources)  
  
-   [Signınsthalkalar](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Adı|Metin|  
|----------|----------|  
|Pagetitleapı 'Leri|API'ler|  
  
###  <a name="AppDetailsStrings"></a>Appayrıntılar dizeleri  
  
|Adı|Metin|  
|----------|----------|  
|Webapplicationsdetaillistle|Uygulama önizlemesi|  
|Webapplicationsgereksinimsheader|Gereksinimler|  
|WebApplicationsScreenshotAlt|Yakala|  
|WebApplicationsScreenshotsHeader|Ekran görüntüleri|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Adı|Metin|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Uygulamayı kaldırmak istediğinizden emin misiniz?|  
|WebDevelopersAppNotPublished|Yayımlanmadı|  
|WebDevelopersAppNotSubmitted|Gönderilmedi|  
|WebDevelopersAppTableCategoryHeader|Kategori|  
|WebDevelopersAppTableNameHeader|Adı|  
|WebDevelopersAppTableStateHeader|Eyalet|  
|WebDevelopersEditLink|Düzenle|  
|WebDevelopersRegisterAppLink|Uygulamayı Kaydet|  
|WebDevelopersRemoveLink|Kaldır|  
|WebDevelopersSubmitLink|Gönder|  
|WebDevelopersYourApplicationsHeader|Uygulamalarınız|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Adı|Metin|  
|----------|----------|  
|WebApplicationsHeader|Uygulamalar|  
  
###  <a name="CommonResources"></a>Ortak kaynaklar  
  
|Adı|Metin|  
|----------|----------|  
|Noıtemstodisplay|Sonuç bulunamadı.|  
|GeneralExceptionMessage|Bir şey doğru değil. Geçici bir hata veya hata olabilir. Lütfen yeniden deneyin.|  
|GeneralJsonExceptionMessage|Bir şey doğru değil. Geçici bir hata veya hata olabilir. Lütfen sayfayı yeniden yükleyin ve tekrar deneyin.|  
|ConfirmationMessageUnsavedChanges|Kaydedilmemiş değişiklikler var. İptal etmek ve değişiklikleri atmak istediğinizden emin misiniz?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Istek gövdesi çok büyük.|  
  
###  <a name="CommonStrings"></a>Ortak dizeler  
  
|Adı|Metin|  
|----------|----------|  
|ButtonLabelCancel|İptal|  
|ButtonLabelSave|Kaydet|  
|GeneralExceptionMessage|Bir şey doğru değil. Geçici bir hata veya hata olabilir. Lütfen yeniden deneyin.|  
|Noıtemstodisplay|Görüntülenecek öğe yok.|  
|PagerButtonLabelFirst|İlk|  
|PagerButtonLabelLast|soyadına|  
|PagerButtonLabelNext|Sonraki|  
|PagerButtonLabelPrevious|Önceki|  
|Pagerlabelpagenofa|{1} sayfa {0}|  
|PasswordTooShort|Parola çok kısa|  
|EmailAsPassword|E-postanızı parolanız olarak kullanmayın|  
|PasswordSameAsUserName|Parolanız Kullanıcı adınızı içeremez|  
|Passwordtwokaraktersınıfları|Farklı karakter sınıfları kullanın|  
|Passwordtoomanytekrarları|Çok fazla sayıda tekrarlanıyor|  
|PasswordSequenceFound|Parolanız diziler içeriyor|  
|PagerLabelPageSize|Sayfa boyutu|  
|Curcontainer Labelyükleniyor|Yükleniyor...|  
|TablePlaceholderNothingToDisplay|Seçili dönem ve kapsam için veri yok|  
|ButtonLabelClose|Uyumluluk tekliflerini|  
  
###  <a name="Documentation"></a>Belgelerle  
  
|Adı|Metin|  
|----------|----------|  
|Webbelgetationınvalidheadererrormessage|Geçersiz üst bilgi '{0}'|  
|Webbelgetationınvalidrequesterrormessage|Geçersiz Istek URL 'SI|  
|TextboxLabelAccessToken|Erişim belirteci *|  
|DropdownOptionPrimaryKeyFormat|Birincil-{0}|  
|DropdownOptionSecondaryKeyFormat|İkincil-{0}|  
|Webbelgetationsubscriptionkeytext|Abonelik anahtarınız|  
|Webbelgetationtemplatesaddheaders|Gerekli HTTP üstbilgilerini ekleyin|  
|Webbelgetationtemplates Basicauthsample|Temel yetkilendirme örneği|  
|Webbelgetationtemplates Curlforbasicauth|Temel yetkilendirme kullanımı için:--Kullanıcı {username}: {Password}|  
|Webbelgetationtemplates Curlvaluesforpath|Yol parametreleri ({...} olarak gösterilir), abonelik anahtarınız ve sorgu parametreleri için değerler belirtin|  
|WebDocumentationTemplatesDeveloperKey|Abonelik anahtarınızı belirtin|  
|Webbelgetationtemplates Javaapache|Bu örnek, HTTP bileşenlerinden Apache HTTP istemcisini kullanır (http://hc.apache.org/httpcomponents-client-ga/)|  
|Webbelgetationtemplates Optionalparams|İsteğe bağlı parametrelerin değerlerini gerektiği gibi belirtin|  
|Webbelgetationtemplatesphppackage|Bu örnek, HTTP_Request2 paketini kullanır. (daha fazla bilgi için: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Yol parametreleri ({...} olarak gösterilir) ve gerekirse istek gövdesi değerlerini belirtin|  
|Webbelgetationtemplates Requestbody|İstek gövdesini belirtin|  
|Webbelgetationtemplates Requiredparams|Aşağıdaki gerekli parametreler için değerleri belirtin|  
|Webbelgetationtemplates Valuesforpath|Yol parametreleri ({...} olarak gösterilir) için değerler belirtin|  
|OAuth2AuthorizationEndpointDescription|Yetkilendirme uç noktası, kaynak sahibiyle etkileşim kurmak ve yetkilendirme izni almak için kullanılır.|  
|OAuth2AuthorizationEndpointName|Yetkilendirme uç noktası|  
|OAuth2TokenEndpointDescription|Belirteç uç noktası, istemci tarafından, yetkilendirme verme veya yenileme belirtecini sunarak bir erişim belirteci almak için kullanılır.|  
|OAuth2TokenEndpointName|Belirteç uç noktası|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> istemci, kaynak sahibinin Kullanıcı aracısını yetkilendirme uç noktasına yönlendirerek akışı başlatır.  İstemci, istemci tanımlayıcısını, istenen kapsamı, yerel durumu ve erişim izni verildiğinde (veya reddedildikten) yetkilendirme sunucusunun Kullanıcı aracısını geri göndereceği bir yeniden yönlendirme URI 'sini içerir.     </p\> < p\> yetkilendirme sunucusu, kaynak sahibinin kimliğini doğrular (Kullanıcı Aracısı aracılığıyla) ve kaynak sahibinin istemcinin erişim isteğine izin verip vermeip reddetmediğini belirler.     </p\> < p\> kaynak sahibinin erişim verdiğini varsayarak, yetkilendirme sunucusu daha önce sağlanmış olan yeniden yönlendirme URI 'sini kullanarak Kullanıcı aracısını istemciye geri yönlendirir (istekte veya istemci kaydı sırasında).  Yeniden yönlendirme URI 'SI, bir yetkilendirme kodu ve istemci tarafından daha önce sunulan herhangi bir yerel durum içerir.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Kullanıcı erişim isteğini reddettiğinde istek geçersiz olursa istemci, yeniden yönlendirmeye eklenen aşağıdaki parametreler kullanılarak bilgilendirilir: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Yetkilendirme isteği|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> istemci uygulamanın, OAuth işlemini başlatmak için kullanıcıyı yetkilendirme uç noktasına aktarması gerekir.          Yetkilendirme uç noktasında Kullanıcı kimliğini doğrular ve ardından uygulamaya erişim izni verir veya reddeder.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> kaynak sahibinin erişim verdiğini varsayarak, yetkilendirme sunucusu daha önce sağlanmış olan yeniden yönlendirme URI 'sini kullanarak Kullanıcı aracısını istemciye geri yönlendirir (istekte veya istemci kaydı sırasında).  Yeniden yönlendirme URI 'SI, bir yetkilendirme kodu ve istemci tarafından daha önce sunulan herhangi bir yerel durum içerir. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> istemci, önceki adımda alınan yetkilendirme kodunu ekleyerek yetkilendirme sunucusu 'nun belirteç uç noktasından bir erişim belirteci ister.  İstek yapıldığında, istemci yetkilendirme sunucusuyla kimliğini doğrular.  İstemci, doğrulama için yetkilendirme kodunu elde etmek üzere kullanılan yeniden yönlendirme URI 'sini içerir. </p\> < p\> yetkilendirme sunucusu istemcinin kimliğini doğrular, yetkilendirme kodunu doğrular ve alınan yeniden yönlendirme URI 'sinin, adımda istemciyi yeniden yönlendirmek için kullanılan URI ile eşleştiğinden emin olmanızı sağlar.  Geçerliyse, yetkilendirme sunucusu bir erişim belirteci ve isteğe bağlı olarak bir yenileme belirteci ile geri yanıt verir. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> istek istemci kimlik doğrulaması başarısız olduysa veya geçersizse, yetkilendirme sunucusu HTTP 400 (Hatalı Istek) durum kodu ile yanıt verir (aksi belirtilmedikçe) ve aşağıdaki parametreleri yanıtla birlikte içerir. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> istemci, "application/x-www-form-urlencoded" biçimini kullanarak, HTTP isteği varlık gövdesinde UTF-8 karakter kodlamasıyla bir istek yapar. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> yetkilendirme sunucusu, bir erişim belirteci ve isteğe bağlı yenileme belirteci yayınlar ve HTTP yanıtının varlık gövdesine 200 (Tamam) durum kodu ile aşağıdaki parametreleri ekleyerek yanıtı oluşturur. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> istemci yetkilendirme sunucusuyla kimliğini doğrular ve belirteç uç noktasından bir erişim belirteci ister. </p\> < p\> yetkilendirme sunucusu istemcinin kimliğini doğrular ve geçerliyse, bir erişim belirteci yayınlar. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> istemci kimlik doğrulaması başarısız olursa veya geçersizse, yetkilendirme sunucusu HTTP 400 (Hatalı Istek) durum kodu ile yanıt verir (aksi belirtilmedikçe) ve aşağıdaki parametreleri yanıtla birlikte bulundurur. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> istemci, "application/x-www-form-urlencoded" biçimini kullanarak, HTTP isteği varlık gövdesinde UTF-8 karakter kodlamasıyla bir istek yapar. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> erişim belirteci isteği geçerli ve yetkilendirirse, yetkilendirme sunucusu bir erişim belirteci ve isteğe bağlı yenileme belirteci yayınlar ve HTTP yanıtının varlık gövdesine 200 ile aşağıdaki parametreleri ekleyerek yanıtı oluşturur (Tamam ) durum kodu. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> istemci, kaynak sahibinin Kullanıcı aracısını yetkilendirme uç noktasına yönlendirerek akışı başlatır.  İstemci, istemci tanımlayıcısını, istenen kapsamı, yerel durumu ve erişim izni verildiğinde (veya reddedildikten) yetkilendirme sunucusunun Kullanıcı aracısını geri göndereceği bir yeniden yönlendirme URI 'sini içerir. </p\> < p\> yetkilendirme sunucusu kaynak sahibinin kimliğini doğrular (Kullanıcı Aracısı aracılığıyla) ve kaynak sahibinin istemci erişim isteği verip vermediği veya reddetmediğini belirler. </p\> < p\> kaynak sahibinin erişim verdiğini varsayarak, yetkilendirme sunucusu daha önce sağlanmış olan yeniden yönlendirme URI 'sini kullanarak Kullanıcı aracısını istemciye geri yönlendirir.  Yeniden yönlendirme URI 'SI, URI parçasındaki erişim belirtecini içerir. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> kaynak sahibi erişim isteğini reddederse veya istek eksik ya da geçersiz bir yeniden yönlendirme URI 'SI dışında nedenlerle başarısız olursa, yetkilendirme sunucusu istemciye, aşağıdaki parametreleri TH 'nin parça bileşenine ekleyerek bildirir "application/x-www-form-urlencoded" biçimini kullanarak URI 'yi yeniden yönlendirme. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> istemci uygulamanın, OAuth işlemini başlatmak için kullanıcıyı yetkilendirme uç noktasına aktarması gerekir.      Yetkilendirme uç noktasında Kullanıcı kimliğini doğrular ve ardından uygulamaya erişim izni verir veya reddeder. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> kaynak sahibi erişim isteğini veriyorsa, yetkilendirme sunucusu bir erişim belirteci yayınlar ve "application/x-www-for" kullanarak yeniden yönlendirme URI 'sinin parça bileşenine aşağıdaki parametreleri ekleyerek istemciye gönderir. a-urlencoded "biçimi. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Yetkilendirme kodu akışı, kimlik bilgilerinin gizliliğini (örneğin, PHP, Java, Python, Ruby, ASP.NET vb. kullanarak uygulanan Web sunucusu uygulamaları) korumak özellikli istemciler için iyileştirilmiştir.|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Yetkilendirme kodu verme|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|İstemci kimlik bilgileri akışı, istemcisinin (uygulamanızın) denetimi altındaki korumalı kaynaklara erişim istediği durumlarda uygundur. İstemci, kaynak sahibi olarak kabul edilir, bu yüzden son kullanıcı etkileşimi gerekli değildir.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|İstemci kimlik bilgileri verme|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Örtük akış, belirli bir yeniden yönlendirme URI 'SI çalıştırmak için bilinen kimlik bilgilerinin gizliliğini korumak zorunda olmayan istemciler için iyileştirilmiştir. Bu istemciler genellikle JavaScript gibi bir komut dosyası dili kullanılarak bir tarayıcıda uygulanır.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Örtük izin|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Kaynak sahibi parola kimlik bilgileri akışı, kaynak sahibinin istemci ile (uygulamanızın), cihaz işletim sistemi veya yüksek ayrıcalıklı bir uygulama gibi bir güven ilişkisine sahip olduğu durumlarda uygundur. Bu akış, kaynak sahibinin kimlik bilgilerini (genellikle etkileşimli bir form kullanarak Kullanıcı adı ve parola) elde eden istemciler için uygundur.|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Kaynak sahibi parola kimlik bilgileri verme|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> kaynak sahibi istemciye Kullanıcı adı ve parolasını sağlar. </p\> < p\> istemci, kaynak sahibinden alınan kimlik bilgilerini ekleyerek yetkilendirme sunucusu 'nun belirteç uç noktasından bir erişim belirteci ister.  İstek yapıldığında, istemci yetkilendirme sunucusuyla kimliğini doğrular. </p\> < p\> yetkilendirme sunucusu istemcinin kimliğini doğrular ve kaynak sahibi kimlik bilgilerini doğrular ve geçerliyse, bir erişim belirteci yayınlar. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> istemci kimlik doğrulaması başarısız olursa veya geçersizse, yetkilendirme sunucusu HTTP 400 (Hatalı Istek) durum kodu ile yanıt verir (aksi belirtilmedikçe) ve aşağıdaki parametreleri yanıtla birlikte bulundurur. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> istemci, "application/x-www-form-urlencoded" biçimini kullanarak, HTTP isteği varlık gövdesinde UTF-8 karakter kodlamasıyla bir istek yapar. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> erişim belirteci isteği geçerli ve yetkilendirirse, yetkilendirme sunucusu bir erişim belirteci ve isteğe bağlı yenileme belirteci yayınlar ve bir 200 (O ile HTTP yanıtının varlık gövdesine aşağıdaki parametreleri ekleyerek yanıtı oluşturur). K) durum kodu. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Erişim belirteci isteği|  
|OAuth2Step_AuthorizationRequest_Name|Yetkilendirme isteği|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Gerekli. Yetkilendirme sunucusu tarafından verilen erişim belirteci.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Gerekli. Yetkilendirme sunucusu tarafından verilen erişim belirteci.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Gerekli. Yetkilendirme sunucusu tarafından verilen erişim belirteci.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Gerekli. Yetkilendirme sunucusu tarafından verilen erişim belirteci.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Gerekli. İstemci tanımlayıcısı.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|İstemci, yetkilendirme sunucusunda kimlik doğrulaması yapmıyorsa GEREKLIDIR.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Gerekli. İstemci tanımlayıcısı.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Gerekli. Yetkilendirme sunucusu tarafından oluşturulan yetkilendirme kodu.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Gerekli. Yetkilendirme sunucusundan alınan yetkilendirme kodu.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Seçim. Daha fazla bilgi sağlayan insan tarafından okunabilen ASCII metni.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Seçim. Daha fazla bilgi sağlayan insan tarafından okunabilen ASCII metni.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Seçim. Daha fazla bilgi sağlayan insan tarafından okunabilen ASCII metni.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Seçim. Daha fazla bilgi sağlayan insan tarafından okunabilen ASCII metni.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Seçim. Daha fazla bilgi sağlayan insan tarafından okunabilen ASCII metni.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Seçim. Hatayla ilgili bilgiler içeren, insan tarafından okunabilen bir Web sayfasını tanımlayan bir URI.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Seçim. Hatayla ilgili bilgiler içeren, insan tarafından okunabilen bir Web sayfasını tanımlayan bir URI.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Seçim. Hatayla ilgili bilgiler içeren, insan tarafından okunabilen bir Web sayfasını tanımlayan bir URI.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Seçim. Hatayla ilgili bilgiler içeren, insan tarafından okunabilen bir Web sayfasını tanımlayan bir URI.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Seçim. Hatayla ilgili bilgiler içeren, insan tarafından okunabilen bir Web sayfasını tanımlayan bir URI.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Gerekli. Aşağıdakilerden tek bir ASCII hata kodu: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Gerekli. Aşağıdakilerden tek bir ASCII hata kodu: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Gerekli. Aşağıdakilerden tek bir ASCII hata kodu: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Gerekli. Aşağıdakilerden tek bir ASCII hata kodu: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Gerekli. Aşağıdakilerden tek bir ASCII hata kodu: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Önerilen. Erişim belirtecinin saniye cinsinden yaşam süresi.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Önerilen. Erişim belirtecinin saniye cinsinden yaşam süresi.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Önerilen. Erişim belirtecinin saniye cinsinden yaşam süresi.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Önerilen. Erişim belirtecinin saniye cinsinden yaşam süresi.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Gerekli. Değer "authorization_code" olarak ayarlanmalıdır.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Gerekli. Değer "client_credentials" olarak ayarlanmalıdır.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Gerekli. Değer "Password" olarak ayarlanmalıdır.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Gerekli. Kaynak sahibi parolası.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Seçim. Yeniden yönlendirme uç noktası URI 'si mutlak bir URI olmalıdır.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|Yetkilendirme isteğine "redirect_uri" parametresi dahil edilse ve değerleri özdeş OLMALıDıR.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Seçim. Yeniden yönlendirme uç noktası URI 'si mutlak bir URI olmalıdır.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Seçim. Yeni erişim belirteçleri elde etmek için kullanılabilecek yenileme belirteci.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Seçim. Yeni erişim belirteçleri elde etmek için kullanılabilecek yenileme belirteci.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Seçim. Yeni erişim belirteçleri elde etmek için kullanılabilecek yenileme belirteci.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Gerekli. Değer "Code" olarak ayarlanmalıdır.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Gerekli. Değer "token" olarak ayarlanmalıdır.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Seçim. Erişim isteğinin kapsamı.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|İstemci tarafından istenen kapsam ile aynıysa Isteğe bağlı; Aksi takdirde, gereklı.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Seçim. Erişim isteğinin kapsamı.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|İstemci tarafından istenen kapsam ile aynıysa Isteğe bağlı; Aksi takdirde, gereklı.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Seçim. Erişim isteğinin kapsamı.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|İstemci tarafından istenen kapsam ile aynıysa Isteğe bağlı; Aksi takdirde, gereklı.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Seçim. Erişim isteğinin kapsamı.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|İstemci tarafından istenen kapsam ile aynıysa Isteğe bağlı; Aksi takdirde, gereklı.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|İstemci yetkilendirme isteğinde "durum" parametresi mevcutsa GEREKLIDIR.  İstemciden alınan tam değer.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Önerilen. İstek ve geri çağırma arasındaki durumu korumak için istemci tarafından kullanılan donuk bir değer.  Yetkilendirme sunucusu, Kullanıcı aracısını istemciye yeniden yönlendirirken bu değeri içerir.  Bu parametre, siteler arası istek sahteciliği önlemek için kullanılmalıdır.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|İstemci yetkilendirme isteğinde "durum" parametresi mevcutsa GEREKLIDIR.  İstemciden alınan tam değer.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|İstemci yetkilendirme isteğinde "durum" parametresi mevcutsa GEREKLIDIR.  İstemciden alınan tam değer.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Önerilen. İstek ve geri çağırma arasındaki durumu korumak için istemci tarafından kullanılan donuk bir değer.  Yetkilendirme sunucusu, Kullanıcı aracısını istemciye yeniden yönlendirirken bu değeri içerir.  Bu parametre, siteler arası istek sahteciliği önlemek için kullanılmalıdır.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|İstemci yetkilendirme isteğinde "durum" parametresi mevcutsa GEREKLIDIR.  İstemciden alınan tam değer.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Gerekli. Verilen belirtecin türü.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Gerekli. Verilen belirtecin türü.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Gerekli. Verilen belirtecin türü.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Gerekli. Verilen belirtecin türü.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Gerekli. Kaynak sahibinin Kullanıcı adı.|  
|OAuth2UnsupportedTokenType|'{0}' belirteç türü desteklenmiyor.|  
|OAuth2InvalidState|Yetkilendirme sunucusundan geçersiz yanıt|  
|OAuth2GrantType_AuthorizationCode|yetkilendirme kodu|  
|OAuth2GrantType_Implicit|İndirgen|  
|OAuth2GrantType_ClientCredentials|İstemci kimlik bilgileri|  
|OAuth2GrantType_ResourceOwnerPassword|Kaynak sahibi parolası|  
|WebDocumentation302Code|302 bulundu|  
|WebDocumentation400Code|400 (hatalı istek)|  
|OAuth2SendingMethod_AuthHeader|Yetkilendirme üst bilgisi|  
|OAuth2SendingMethod_QueryParam|Sorgu parametresi|  
|OAuth2AuthorizationServerGeneralException|{0} aracılığıyla erişim yetkilendirirken bir hata oluştu|  
|OAuth2AuthorizationServerCommunicationException|Yetkilendirme sunucusuna HTTP bağlantısı kurulamadı veya beklenmedik şekilde kapatıldı.|  
|WebDocumentationOAuth2GeneralErrorMessage|Beklenmeyen bir hata oluştu.|  
|AuthorizationServerCommunicationException|Yetkilendirme sunucusu iletişim özel durumu oluştu. Lütfen yöneticiye başvurun.|  
|TextblockSubscriptionKeyHeaderDescription|Bu API 'ye erişim sağlayan abonelik anahtarı. < A href = '/Developer '\>profili </a\>içinde bulunur.|  
|TextblockOAuthHeaderDescription|< İ\>{0}</i\>tarafından alınan OAuth 2,0 erişim belirteci. Desteklenen izin türleri: < ı\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|API 'ye gönderilen gövdenin medya türü.|  
|Errormessageapinotaccesrilabilen|Çağırmayı denediğiniz API Şu anda erişilebilir değil. Lütfen href = "/sorunlar"\></a\>< API yayımcısına başvurun.|  
|Errormessageapimeınali|Çağırmayı denediğiniz API, yanıt almak için normalden daha uzun sürüyor. Lütfen href = "/sorunlar"\></a\>< API yayımcısına başvurun.|  
|Badrequestparameterbekleniyordu|"'{0}' parametresi bekleniyor"|  
|TooltipTextDoubleClickToSelectAll|Tümünü seçmek için çift tıklayın.|  
|TooltipTextHideRevealSecret|Göster/gizle|  
|ButtonLinkOpenConsole|Deneyin|  
|SectionHeadingRequestBody|İstek gövdesi|  
|SectionHeadingRequestParameters|İstek parametreleri|  
|SectionHeadingRequestUrl 'Si|İstek URL'si|  
|SectionHeadingResponse|Yanıt|  
|SectionHeadingRequestHeaders|İstek üst bilgileri|  
|Formlabelsubtextopyapısal|Seçim|  
|SectionHeadingCodeSamples|Kod örnekleri|  
|Textblockopenıdconnectheaderdescription|OpenID Connect ID belirteci < i\>{0}</i\>tarafından alındı. Desteklenen izin türleri: < ı\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Adı|Metin|  
|----------|----------|  
|LinkLabelBack|Geri|  
|LinkLabelHomePage|Giriş sayfası|  
|LinkLabelSendUsEmail|Bize e-posta gönderin|  
|Pagetitlehatası|Ne yazık ki istenen sayfaya hizmet verirken bir sorun oluştu|  
|TextblockPotentialCauseIntermittentIssue|Bu, zaten geçmiş bir aralıklı veri erişim sorunu olabilir.|  
|TextblockPotentialCauseOldLink|Tıkladığınız bağlantı eski olabilir ve artık doğru konuma işaret etmez.|  
|TextblockPotentialCauseTechnicalProblem|Bizim uçta teknik bir sorun olabilir.|  
|TextblockPotentialSolutionRefresh|Sayfayı yenilemeyi deneyin.|  
|TextblockPotentialSolutionStartOver|{0}baştan başlayın.|  
|TextblockPotentialSolutionTryAgain|{0} gidin ve yaptığınız eylemi yeniden deneyin.|  
|TextReportProblem|Neyin yanlış olduğunu açıklayan {0} ve mümkün olan en kısa sürede bakacağız.|  
|TitlePotentialCause|Olası neden|  
|TitlePotentialSolution|Yalnızca geçici bir sorun olabilir, deneyebileceğiniz birkaç şey|  
  
###  <a name="IssuesStrings"></a>Issuesstrings  
  
|Adı|Metin|  
|----------|----------|  
|Webıssuesındextitle|Sorunlar|  
|WebIssuesNoActiveSubscriptions|Etkin aboneliğiniz yok. Bir sorunu bildirmek için bir ürüne abone olmanız gerekir.|  
|Webıssuesnotsignın|Oturumunuz açık değil. Lütfen bir sorunu raporlamak veya yorum göndermek için {0}.|  
|Webıssuesreportısuebutton|Sorun bildir|  
|Webıssuessignın|oturum aç|  
|Webıssuesstatusreportedby|Durum: {1} &#124; tarafından bildirilen {0}|  
  
###  <a name="NotFoundStrings"></a>Notfounddizeleri  
  
|Adı|Metin|  
|----------|----------|  
|LinkLabelHomePage|Giriş sayfası|  
|LinkLabelSendUsEmail|bize e-posta gönderin|  
|PageTitleNotFound|Üzgünüz, aradığınız sayfayı bulamıyoruz|  
|TextblockPotentialCauseMisspelledUrl|Yazdığınız URL 'YI yanlış yazmış olabilirsiniz.|  
|TextblockPotentialCauseOldLink|Tıkladığınız bağlantı eski olabilir ve artık doğru konuma işaret etmez.|  
|TextblockPotentialSolutionRetype|URL 'YI yeniden yazmayı deneyin.|  
|TextblockPotentialSolutionStartOver|{0}baştan başlayın.|  
|TextReportProblem|Neyin yanlış olduğunu açıklayan {0} ve mümkün olan en kısa sürede bakacağız.|  
|TitlePotentialCause|Olası neden|  
|TitlePotentialSolution|Olası çözüm|  
  
###  <a name="ProductDetailsStrings"></a>Productayrıntılar dizeleri  
  
|Adı|Metin|  
|----------|----------|  
|WebProductsAgreement|{0} ürüne abone olunarak `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`kabul ediyorum.|  
|WebProductsLegalTermsLink|Kullanım Koşulları|  
|WebProductsSubscribeButton|Abone olun|  
|WebProductsUsageLimitsHeader|Kullanım sınırları|  
|Webproductsyouarenotabone|Bu ürüne abone oldunuz.|  
|WebProductsYouRequestedSubscription|Bu ürüne abonelik istediniz.|  
|ErrorYouNeedToAgreeWithLegalTerms|Devam edebilmeniz için kullanım koşullarını kabul etmeniz gerekir.|  
|ButtonLabelAddSubscription|Abonelik ekleme|  
|LinkLabelChangeSubscriptionName|Değişebilir|  
|Buttonlabelonayla|Onayla|  
|TextblockMultipleSubscriptionsCount|Bu ürüne {0} aboneliğiniz var:|  
|TextblockSingleSubscriptionsCount|Bu ürüne {0} aboneliğiniz var:|  
|TextblockSingleApisCount|Bu ürün {0} API içerir:|  
|TextblockMultipleApisCount|Bu ürün {0} API 'Leri içerir:|  
|TextblockHeaderSubscribe|Ürüne abone ol|  
|TextblockSubscriptionDescription|Yeni bir abonelik şu şekilde oluşturulur:|  
|Textblocksubscriptionlimitulaşıldı|Abonelik sınırına ulaşıldı.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Adı|Metin|  
|----------|----------|  
|PageTitleProducts|Ürünler|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Adı|Metin|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Oturum açma, şu anda Yöneticiler tarafından devre dışı bırakıldı.|  
|TextboxExternalIdentitiesSigninInvitation|Alternatif olarak, ile oturum açın|  
|TextboxExternalIdentitiesSigninInvitationPrimary|İle oturum açın:|  
  
###  <a name="SigninResources"></a>Signınresources  
  
|Adı|Metin|  
|----------|----------|  
|PrincipalNotFound|Asıl bulunamadı veya imza geçersiz|  
|ErrorSsoAuthenticationFailed|SSO kimlik doğrulaması başarısız oldu|  
|ErrorSsoAuthenticationFailedDetailed|Geçersiz belirteç belirtildi veya imza doğrulanamıyor.|  
|Errorssotokengeçersiz|SSO belirteci geçersiz|  
|Validationerrorbelirtimi Icemaılalreadyexists|'{0}' e-postası zaten kayıtlı|  
|Validationerrorbelirtimi Icemailgeçersiz|'{0}' e-postası geçersiz|  
|Validationerrorpasswordgeçersizdir|Parola geçersiz. Lütfen hataları düzeltip yeniden deneyin.|  
|PropertyTooShort|{0} çok kısa|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Geçersiz e-posta adresi.|  
|ValidationMessageNewPasswordConfirmationRequired|Yeni Parolayı Onayla|  
|ValidationErrorPasswordConfirmationRequired|Parolanın boş olduğunu onaylayın|  
|WebAuthenticationEmailChangeNotice|Değişiklik onay e-postası {0}. Yeni e-posta adresinizi doğrulamak için lütfen içindeki yönergeleri izleyin. E-posta, sonraki birkaç dakika içinde gelen kutunuza ulaşmazsa lütfen gereksiz e-posta klasörünüzü kontrol edin.|  
|WebAuthenticationEmailChangeNoticeHeader|E-posta değişim isteğiniz başarıyla işlendi|  
|WebAuthenticationEmailChangeNoticeTitle|E-posta değişikliği istendi|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-posta zaten var. İstek geri döndürüldü|  
|ValidationErrorEmailAlreadyExists|E-posta zaten var|  
|ValidationErrorEmailInvalid|Geçersiz e-posta adresi|  
|TextboxLabelEmail|E-posta|  
|ValidationErrorEmailRequired|E-posta gereklidir.|  
|WebAuthenticationErrorNoticeHeader|Hata|  
|WebAuthenticationFieldLengthErrorMessage|{0} en fazla {1} uzunluğunda olmalıdır|  
|TextboxLabelEmailFirstName|Ad|  
|ValidationErrorFirstNameRequired|Ad gereklidir.|  
|Validationerrorfirstnamename geçersiz|Geçersiz ad|  
|NoticeInvalidInvitationToken|Lütfen onay bağlantılarının yalnızca 48 saat için geçerli olduğunu unutmayın. Hala bu zaman diliminde çalışıyorsanız, lütfen bağlantının doğru olduğundan emin olun. Bağlantınız süresi dolmuşsa lütfen onaylamaya çalıştığınız eylemi tekrarlayın.|  
|NoticeHeaderInvalidInvitationToken|Geçersiz davet belirteci|  
|NoticeTitleInvalidInvitationToken|Onay hatası|  
|WebAuthenticationLastNameInvalidErrorMessage|Geçersiz Soyadı|  
|TextboxLabelEmailLastName|Soyadı|  
|ValidationErrorLastNameRequired|Soyadı gereklidir.|  
|WebAuthenticationLinkExpiredNotice|Size gönderilen onay bağlantısı süresi doldu. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Parola sıfırlama bağlantınız geçersiz veya zaman aşımına uğradı.|  
|WebAuthenticationLinkExpiredNoticeTitle|Bağlantı gönderildi|  
|WebAuthenticationNewPasswordLabel|Yeni parola|  
|ValidationMessageNewPasswordRequired|Yeni parola gereklidir.|  
|TextboxLabelNotificationsSenderEmail|Bildirim gönderen e-postası|  
|Textboxlabelorganizasyonadi|Kuruluş adı|  
|WebAuthenticationOrganizationRequiredErrorMessage|Kuruluş adı boş|  
|WebAuthenticationPasswordChangedNotice|Parolanız başarıyla güncelleştirildi|  
|WebAuthenticationPasswordChangedNoticeTitle|Parola güncelleştirildi|  
|WebAuthenticationPasswordCompareErrorMessage|Parolalar eşleşmiyor|  
|WebAuthenticationPasswordConfirmLabel|Parolayı onayla|  
|Validationerrorpassworınvaliddetailed|Parola çok zayıf.|  
|WebAuthenticationPasswordLabel|Parola|  
|ValidationErrorPasswordRequired|Parola gereklidir.|  
|WebAuthenticationPasswordResetSendNotice|Parolayı değiştir onay e-postası {0}. Parola değiştirme işlemine devam etmek için lütfen e-postadaki yönergeleri izleyin.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Parola sıfırlama isteğiniz başarıyla işlendi|  
|WebAuthenticationPasswordResetSendNoticeTitle|Parola sıfırlama istendi|  
|WebAuthenticationRequestNotFoundNotice|İstek bulunamadı|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Bildirim gönderen e-postası boş|  
|WebAuthenticationSigninPasswordLabel|Lütfen bir parola girerek değişikliği onaylayın|  
|WebAuthenticationSignupConfirmNotice|Kayıt onay e-postası {0}. < br/\>, hesabınızı etkinleştirmek için lütfen e-postadaki yönergeleri izleyin\> <. bir sonraki birkaç dakika içinde e-posta kutunuza ulaşmazsa, lütfen gereksiz e-postanızı kontrol edin klasörde.|  
|WebAuthenticationSignupConfirmNoticeHeader|Hesabınız başarıyla oluşturuldu|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Kayıt onay e-postası yeniden gönderildi|  
|WebAuthenticationSignupConfirmNoticeTitle|Hesap oluşturuldu|  
|WebAuthenticationTokenRequiredErrorMessage|Belirteç boş|  
|WebAuthenticationUserAlreadyRegisteredNotice|Bu e-postanın sistemde zaten kayıtlı olduğu bir Kullanıcı görünüyor. Parolanızı unuttuysanız, lütfen geri yüklemeyi deneyin veya destek ekibimize başvurun.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Kullanıcı zaten kayıtlı|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Zaten kayıtlı|  
|ButtonLabelChangePassword|Parolayı Değiştir|  
|Buttonlabelchangeaccountınfo|Hesap bilgilerini değiştirme|  
|ButtonLabelCloseAccount|Hesabı kapat|  
|WebAuthenticationInvalidCaptchaErrorMessage|Girilen metin resimdeki metinle eşleşmiyor. Lütfen yeniden deneyin.|  
|Validationerrorcredentialsgeçersiz|E-posta veya parola geçersiz. Lütfen hataları düzeltip yeniden deneyin.|  
|WebAuthenticationRequestIsNotValid|İstek geçerli değil|  
|WebAuthenticationUserIsNotConfirm|Oturum açmayı denemeden önce lütfen kaydınızı onaylayın.|  
|WebAuthenticationInvalidEmailFormated|E-posta geçersiz: {0}|  
|WebAuthenticationUserNotFound|Kullanıcı bulunamadı|  
|WebAuthenticationTenantNotRegistered|Hesabınız, bu portala erişim yetkisine sahip olmayan bir Azure Active Directory kiracıya aittir.|  
|WebAuthenticationAuthenticationFailed|Kimlik doğrulaması başarısız oldu.|  
|WebAuthenticationGooglePlusNotEnabled|Kimlik doğrulaması başarısız oldu. Uygulamayı yetkilendirdiğiniz takdirde, Google Authentication 'ın doğru yapılandırıldığından emin olmak için lütfen yöneticiye başvurun.|  
|ValidationErrorAllowedTenantIsRequired|İzin verilen kiracı gerekli|  
|ValidationErrorTenantIsNotValid|'{0}' Azure Active Directory kiracısı geçerli değil.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|{0} hesabınızı kullanarak oturum açın|  
|WebAuthenticationUserLimitNotice|Bu hizmet izin verilen en fazla kullanıcı sayısına ulaştı. Lütfen hizmetini yükseltmek ve Kullanıcı kaydını yeniden etkinleştirmek için `<a href="mailto:{0}"\>contact the administrator</a\>`.|  
|WebAuthenticationUserLimitNoticeHeader|Kullanıcı kaydı devre dışı|  
|WebAuthenticationUserLimitNoticeTitle|Kullanıcı kaydı devre dışı|  
|WebAuthenticationUserRegistrationDisabledNotice|Kullanıcıların kaydı yönetici tarafından devre dışı bırakıldı. Lütfen dış kimlik sağlayıcısıyla oturum açın.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Kullanıcı kaydı devre dışı|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Kullanıcı kaydı devre dışı|  
|WebAuthenticationSignupPendingConfirmationNotice|Hesabınızın oluşturulmasını tamamlayabilmemiz için önce e-posta adresinizi doğrulamamız gerekir. {0}için bir e-posta gönderdik. Hesabınızı etkinleştirmek için lütfen e-posta içindeki yönergeleri izleyin. E-posta önümüzdeki birkaç dakika içinde gelmezse lütfen gereksiz e-posta klasörünüzü kontrol edin.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|E-posta adresi {0}için onaylanmamış bir hesap bulduk. Hesabınızın oluşturulmasını tamamlaması için e-posta adresinizi doğrulamanız gerekiyor. {0}için bir e-posta gönderdik. Hesabınızı etkinleştirmek için lütfen e-posta içindeki yönergeleri izleyin. E-posta önümüzdeki birkaç dakika içinde gelmezse lütfen gereksiz e-posta klasörünüzü kontrol edin|  
|WebAuthenticationSignupConfirmationAlmostDone|Neredeyse bitti|  
|WebAuthenticationSignupConfirmationEmailSent|{0}için bir e-posta gönderdik. Hesabınızı etkinleştirmek için lütfen e-posta içindeki yönergeleri izleyin. E-posta önümüzdeki birkaç dakika içinde gelmezse lütfen gereksiz e-posta klasörünüzü kontrol edin.|  
|WebAuthenticationEmailSentNotificationMessage|E-posta başarıyla gönderildi {0}|  
|WebAuthenticationNoAadTenantConfigured|Hizmet için Azure Active Directory kiracı yapılandırılmadı.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`kabul ediyorum.|  
|Textblockuserregistrationtermssaðlanan|Lütfen `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>` gözden geçirin|  
|DialogHeadingTermsOfUse|Kullanım Koşulları|  
|ValidationMessageConsentNotAccepted|Devam edebilmeniz için kullanım koşullarını kabul etmeniz gerekir.|  
  
###  <a name="SigninStrings"></a>Signınsthalkalar  
  
|Adı|Metin|  
|----------|----------|  
|WebAuthenticationForgotPassword|Parolanızı mı unuttunuz?|  
|WebAuthenticationIfAdministrator|Yöneticiyseniz `<a href="{0}"\>here</a\>`oturum açmanız gerekir.|  
|WebAuthenticationNotAMember|Henüz üye değil misiniz? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Bu bilgisayarda beni anımsa|  
|WebAuthenticationSigininWithPassword|Kullanıcı adınızla ve parolanızla oturum açın|  
|WebAuthenticationSigninTitle|Oturum aç|  
|WebAuthenticationSignUpNow|Hemen kaydolun|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Adı|Metin|  
|----------|----------|  
|Pagetitlekaydolma|Kaydol|  
|WebAuthenticationAlreadyAMember|Zaten üye misiniz?|  
|WebAuthenticationCreateNewAccount|Yeni bir API Management hesabı oluşturun|  
|WebAuthenticationSigninNow|Şimdi oturum açın|  
|ButtonLabelSignup|Kaydol|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Adı|Metin|  
|----------|----------|  
|SubscriptionCancelConfirmation|Bu aboneliği iptal etmek istediğinizden emin misiniz?|  
|SubscriptionRenewConfirmation|Bu aboneliği yenilemek istediğinizden emin misiniz?|  
|WebDevelopersManageSubscriptions|Abonelikleri yönetme|  
|WebDevelopersPrimaryKey|Birincil anahtar|  
|WebDevelopersRegenerateLink|Yeniden Oluştur|  
|WebDevelopersSecondaryKey|İkincil anahtar|  
|ButtonLabelShowKey|Göster|  
|ButtonLabelRenewSubscription|Yenile|  
|WebDevelopersSubscriptionRequested|{0} istenen|  
|WebDevelopersSubscriptionRequestedState|İstenen|  
|WebDevelopersSubscriptionTableNameHeader|Adı|  
|WebDevelopersSubscriptionTableStateHeader|Eyalet|  
|WebDevelopersUsageStatisticsLink|Analiz raporları|  
|WebDevelopersYourSubscriptions|Abonelikleriniz|  
|SubscriptionPropertyLabelRequestedDate|İstek açık|  
|SubscriptionPropertyLabelStartedDate|Başlangıç tarihi|  
|PageTitleRenameSubscription|Aboneliği yeniden adlandır|  
|SubscriptionPropertyLabelName|Abonelik adı|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Adı|Metin|  
|----------|----------|  
|SectionHeadingCloseAccount|Hesabınız kapatılsın mı?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Gizlenecek|  
|ButtonLabelRegenerateKey|Yeniden Oluştur|  
|InformationMessageKeyWasRegenerated|Bu anahtarı yeniden oluşturmak istediğinizden emin misiniz?|  
|ButtonLabelShowKey|Göster|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Adı|Metin|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profili Güncelleştir|  
|PageTitleUpdateProfile|Hesap bilgilerini güncelleştir|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Adı|Metin|  
|----------|----------|  
|Buttonlabelchangeaccountınfo|Hesap bilgilerini değiştirme|  
|ButtonLabelChangePassword|Parolayı Değiştir|  
|ButtonLabelCloseAccount|Hesabı kapat|  
|TextboxLabelEmail|E-posta|  
|TextboxLabelEmailFirstName|Ad|  
|TextboxLabelEmailLastName|Soyadı|  
|TextboxLabelNotificationsSenderEmail|Bildirim gönderen e-postası|  
|Textboxlabelorganizasyonadi|Kuruluş adı|  
|Subscriptionstateactıve|Etkin|  
|Subscriptionstateiptal edildi|Yürütüldükten|  
|Subscriptionstatevadesi geçildi|Süresi doldu|  
|SubscriptionStateRejected|Reddedilecek|  
|Subscriptionstatereşitlenmiş|İstenen|  
|Subscriptionstateaskıya alındı|Alın|  
|DefaultSubscriptionNameTemplate|{0} (varsayılan)|  
|SubscriptionNameTemplate|Geliştirici erişimi #{0}|  
|TextboxLabelSubscriptionName|Abonelik adı|  
|ValidationMessageSubscriptionNameRequired|Abonelik adı boş olamaz.|  
|Apimanagementuserlimitulaşıldı|Bu hizmet izin verilen en fazla kullanıcı sayısına ulaştı. Lütfen daha yüksek bir fiyatlandırma katmanına yükseltin.|  
  
##  <a name="glyphs"></a>Glif kaynakları  
 API Management geliştirici portalı şablonları, [önyüklemeden Glyphsimgelerindeki](https://getbootstrap.com/components/#glyphicons)glifleri kullanabilir. Bu glif kümesi, [Glyphıcon](https://glyphicons.com/) halfller kümesinden yazı tipi biçiminde 250 karakter üzerinde yer alır. Bu kümeden bir karakter kullanmak için aşağıdaki sözdizimini kullanın.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Karakterlerin tüm listesi için bkz. önyükleme için [Glyphsimgeler](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Sonraki adımlar
Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [şablonları kullanarak API Management Geliştirici Portalını Özelleştirme](api-management-developer-portal-templates.md).
