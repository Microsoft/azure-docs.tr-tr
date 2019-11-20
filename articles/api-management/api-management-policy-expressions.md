---
title: Azure API Management ilke ifadeleri | Microsoft Docs
description: Azure API Management 'de ilke ifadeleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: e9e6eff4c527ff2e22be57ebc1eb3dcdb3c4e0ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241985"
---
# <a name="api-management-policy-expressions"></a>API Management İlkesi ifadeleri
Bu makalede, ilke ifadeleri sözdizimi 7 C# ' dir. Her bir ifadenin örtük olarak sağlanmış [bağlam](api-management-policy-expressions.md#ContextVariables) değişkenine ve .NET Framework türlerin izin verilen bir [alt kümesine](api-management-policy-expressions.md#CLRTypes) erişimi vardır.

Daha fazla bilgi için:

- Bkz. arka uç hizmetinize bağlam bilgilerini sağlama. [Sorgu dizesi ayarla parametresini](api-management-transformation-policies.md#SetQueryStringParameter) kullanın ve bu bilgileri sağlamak için [http üst bilgi ilkelerini ayarlayın](api-management-transformation-policies.md#SetHTTPheader) .
- Belirteç taleplerine göre işlemlere erişimi önceden yetkilendirmek için bkz. [JWT](api-management-access-restriction-policies.md#ValidateJWT) ilkesini nasıl kullanacağınızı öğrenin.
- İlkelerin nasıl değerlendirileceğini ve bu değerlendirmelerinin sonuçlarını görmek için [API denetçisi](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) izlemesini nasıl kullanacağınızı öğrenin.
- API Management yanıtı önbelleğe almayı yapılandırmak için, bkz. bir [önbellekten al](api-management-caching-policies.md#GetFromCache) ve [önbellek ilkelerine depola](api-management-caching-policies.md#StoreToCache) ilkeleri kullanma. Desteklenen hizmetin `Cache-Control` yönergesinde belirtilen arka uç hizmetinin yanıt önbelleklemesi ile eşleşen bir süre ayarlayın.
- Bkz. içerik filtrelemeyi gerçekleştirme. [Denetim akışını](api-management-advanced-policies.md#choose) ve [gövde ilkelerini ayarla](api-management-transformation-policies.md#SetBody) ' yı kullanarak arka uçta alınan yanıttan veri öğelerini kaldırın.
- İlke deyimlerini indirmek için bkz. [api-Management-Samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub deposu.


## <a name="Syntax"></a>Sözdizimi
Tek deyim ifadeleri `@(expression)` ' dır, burada `expression` iyi biçimlendirilmiş C# bir ifade deyimidir.

Çok deyimli ifadeler `@{expression}` ' a alınır. Çok deyimli ifadeler içindeki tüm kod yollarının `return` ifadesiyle bitmesi gerekir.

## <a name="PolicyExpressionsExamples"></a>Örnekler

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="PolicyExpressionsUsage"></a>Kullanımıyla
İfadeler, herhangi [bir API Management ilkesinde](api-management-policies.md) öznitelik değerleri veya metin değerleri olarak kullanılabilir (ilke başvurusu aksini belirtmedikleri sürece).

> [!IMPORTANT]
> İlke ifadelerini kullandığınızda, ilke tanımlandığında ilke ifadelerinin yalnızca sınırlı doğrulaması vardır. İfadeler çalışma zamanında ağ geçidi tarafından yürütülür, ilke ifadeleri tarafından oluşturulan tüm özel durumlar bir çalışma zamanı hatasına neden oluşur.

## <a name="CLRTypes"></a>İlke ifadelerinde izin verilen .NET Framework türleri
Aşağıdaki tablo, ilke ifadelerinde izin verilen .NET Framework türlerini ve üyelerini listelemektedir.

|Tür|Desteklenen Üyeler|
|--------------|-----------------------|
|Newtonsoft. JSON. Formatting|Tümü|
|Newtonsoft. JSON. JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft. JSON. LINQ. Extensions|Tümü|
|Newtonsoft. JSON. LINQ. JArray|Tümü|
|Newtonsoft. JSON. LINQ. JConstructor|Tümü|
|Newtonsoft. JSON. LINQ. JContainer|Tümü|
|Newtonsoft. JSON. LINQ. JObject|Tümü|
|Newtonsoft. JSON. Linq. JProperty|Tümü|
|Newtonsoft. JSON. LINQ. JRaw|Tümü|
|Newtonsoft. JSON. LINQ. JToken|Tümü|
|Newtonsoft. JSON. LINQ. JTokenType|Tümü|
|Newtonsoft. JSON. LINQ. JValue|Tümü|
|System. Array|Tümü|
|System. BitConverter|Tümü|
|System. Boolean|Tümü|
|System. Byte|Tümü|
|System. Char|Tümü|
|System. Collections. Generic. Dictionary < TKey, TValue >|Tümü|
|System. Collections. Generic. HashSet @ no__t-0T >|Tümü|
|System. Collections. Generic. ICollection @ no__t-0T >|Tümü|
|System. Collections. Generic. IDictionary < TKey, TValue >|Tümü|
|System. Collections. Generic. IEnumerable @ no__t-0T >|Tümü|
|System. Collections. Generic. IEnumerator @ no__t-0T >|Tümü|
|System. Collections. Generic. IList @ no__t-0T >|Tümü|
|System. Collections. Generic. IReadOnlyCollection @ no__t-0T >|Tümü|
|System. Collections. Generic. IReadOnlyDictionary < TKey, TValue >|Tümü|
|System. Collections. Generic. ISet @ no__t-0T >|Tümü|
|System. Collections. Generic. KeyValuePair < TKey, TValue >|Tümü|
|System. Collections. Generic. List @ no__t-0T >|Tümü|
|System. Collections. Generic. Queue @ no__t-0T >|Tümü|
|System. Collections. Generic. Stack @ no__t-0T >|Tümü|
|System. Convert|Tümü|
|System. DateTime|(Oluşturucu), ekleme, AddDays, AddHours, AddMilliseconds, AddMinutes, Addaylar, AddSeconds, AddTicks, addyear, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, ıdaylightsavingtime, IsLeapYear, MaxValue, milisaniyelik, dakika, MinValue, month, şimdi , Parse, Second, çıkart, ticks, TimeOfDay, bugün, ToString, UtcNow, Year|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Tümü|
|System. Decimal|Tümü|
|System. Double|Tümü|
|System. Exception|Tümü|
|System. Guid|Tümü|
|System. Int16|Tümü|
|System. Int32|Tümü|
|System. Int64|Tümü|
|System. ıO. StringReader|Tümü|
|System. ıO. StringWriter|Tümü|
|System. LINQ. sıralanabilir|Tümü|
|System. Math|Tümü|
|System. MidpointRounding|Tümü|
|System .net. Webugiyi|Tümü|
|System. Nullable|Tümü|
|System. Random|Tümü|
|System. SByte|Tümü|
|System. Security. Cryptography. AsymmetricAlgorithm|Tümü|
|System. Security. Cryptography. CipherMode|Tümü|
|System. Security. Cryptography. HashAlgorithm|Tümü|
|System. Security. Cryptography. HashAlgorithmName|Tümü|
|System. Security. Cryptography. HMAC|Tümü|
|System. Security. Cryptography. HMACMD5|Tümü|
|System. Security. Cryptography. HMACSHA1|Tümü|
|System. Security. Cryptography. HMACSHA256|Tümü|
|System. Security. Cryptography. HMACSHA384|Tümü|
|System. Security. Cryptography. HMACSHA512|Tümü|
|System. Security. Cryptography. KeyedHashAlgorithm|Tümü|
|System. Security. Cryptography. MD5|Tümü|
|System. Security. Cryptography. OID|Tümü|
|System. Security. Cryptography. doldurma Ingmode|Tümü|
|System. Security. Cryptography. RNGCryptoServiceProvider|Tümü|
|System. Security. Cryptography. RSA|Tümü|
|System. Security. Cryptography. RSAEncryptionPadding|Tümü|
|System. Security. Cryptography. Rsatifturepekleniyor|Tümü|
|System. Security. Cryptography. SHA1|Tümü|
|System. Security. Cryptography. SHA1Managed|Tümü|
|System. Security. Cryptography. SHA256|Tümü|
|System. Security. Cryptography. SHA256Managed|Tümü|
|System. Security. Cryptography. SHA384|Tümü|
|System. Security. Cryptography. SHA384Managed|Tümü|
|System. Security. Cryptography. SHA512 olur|Tümü|
|System. Security. Cryptography. SHA512Managed|Tümü|
|System. Security. Cryptography. SymmetricAlgorithm|Tümü|
|System. Security. Cryptography. X509Certificates. PublicKey|Tümü|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Tümü|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Name|
|System. Security. Cryptography. X509Certificates. X509Certificate|Tümü|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Tümü|
|System. Security. Cryptography. X509Certificates. X509ContentType|Tümü|
|System. Security. Cryptography. X509Certificates. X509NameType|Tümü|
|System. Single|Tümü|
|System. String|Tümü|
|System. StringComparer|Tümü|
|System. StringComparison|Tümü|
|System. Stringsptoptions|Tümü|
|System. Text. Encoding|Tümü|
|System. Text. RegularExpressions. Capture|Dizin, uzunluk, değer|
|System. Text. RegularExpressions. CaptureCollection|Sayı, öğe|
|System. Text. RegularExpressions. Group|Yakalamalar, başarılı|
|System. Text. RegularExpressions. GroupCollection|Sayı, öğe|
|System. Text. RegularExpressions. Match|Boş, gruplar, sonuç|
|System. Text. RegularExpressions. Regex|(Oluşturucu), IsMatch, Eşleştir, eşleşmeler, Değiştir, Unkaçış, Böl|
|System. Text. RegularExpressions. RegexOptions|Tümü|
|System. Text. StringBuilder|Tümü|
|System. TimeSpan|Tümü|
|System. TimeZone|Tümü|
|System. TimeZoneInfo. AdjustmentRule|Tümü|
|System. TimeZoneInfo. geçişli Tiontime|Tümü|
|System. TimeZoneInfo|Tümü|
|System. Tuple|Tümü|
|System. UInt16|Tümü|
|System. UInt32|Tümü|
|System. UInt64|Tümü|
|System. Uri|Tümü|
|System. UriPartial|Tümü|
|System. xml. LINQ. Extensions|Tümü|
|System. xml. LINQ. XAttribute|Tümü|
|System. xml. Linq. XCData|Tümü|
|System. xml. LINQ. XComment|Tümü|
|System. xml. LINQ. XContainer|Tümü|
|System. xml. LINQ. XDeclaration|Tümü|
|System. xml. Linq. XDocument|Tümü, şunun dışında: yükleme|
|System. xml. LINQ. XDocumentType|Tümü|
|System. xml. LINQ. XElement|Tümü|
|System. xml. LINQ. XName|Tümü|
|System. xml. Linq. XNamespace|Tümü|
|System. xml. LINQ. XNode|Tümü|
|System. xml. Linq. XNodeDocumentOrderComparer|Tümü|
|System. xml. Linq. XNodeEqualityComparer|Tümü|
|System. xml. LINQ. XObject|Tümü|
|System. xml. Linq. Xprocessingyönergesi|Tümü|
|System. xml. LINQ. XText|Tümü|
|System. xml. XmlNodeType|Tümü|

## <a name="ContextVariables"></a>Bağlam değişkeni
@No__t-0 adlı bir değişken, her ilke [ifadesinde](api-management-policy-expressions.md#Syntax)örtülü olarak kullanılabilir. Üyeleri `\request` ile ilgili bilgiler sağlar. @No__t-0 üyelerinin tümü salt okunurdur.

|Bağlam değişkeni|İzin verilen Yöntemler, Özellikler ve parametre değerleri|
|----------------------|-------------------------------------------------------|
|bağlam|[API](#ref-context-api): [ıapi](#ref-iapi)<br /><br /> [Dağıtım](#ref-context-deployment)<br /><br /> Geçen: zaman damgası ve geçerli saat değeri arasındaki TimeSpan-Time aralığı<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Çalışmasını](#ref-context-operation)<br /><br /> [Ürünüyle](#ref-context-product)<br /><br /> [İsteyen](#ref-context-request)<br /><br /> RequestId: Guid-benzersiz istek tanımlayıcısı<br /><br /> [Yanıtıyla](#ref-context-response)<br /><br /> [Aboneliğiniz](#ref-context-subscription)<br /><br /> Zaman damgası: DateTime-isteğin alındığı zaman içinde nokta<br /><br /> İzleme: bool-izlemenin açık veya kapalı olup olmadığını gösterir <br /><br /> [Kullanıcı](#ref-context-user)<br /><br /> [Değişkenler](#ref-context-variables): IReadOnlyDictionary < dize, nesne ><br /><br /> void trace (ileti: dize)|
|<a id="ref-context-api"></a>bağlam. 'Sindeki|Kimlik: dize<br /><br /> IsCurrentRevision: bool<br /><br />  Ad: dize<br /><br /> Yol: dize<br /><br /> Düzeltme: dize<br /><br /> ServiceUrl: [Iurl](#ref-iurl)<br /><br /> Sürüm: dize |
|<a id="ref-context-deployment"></a>bağlam. Dağıtmak|Bölge: dize<br /><br /> HizmetAdı: dize<br /><br /> Sertifikalar: IReadOnlyDictionary < dize, X509Certificate2 >|
|<a id="ref-context-lasterror"></a>bağlam. LastError|Kaynak: dize<br /><br /> Neden: dize<br /><br /> İleti: dize<br /><br /> Kapsam: dize<br /><br /> Bölüm: dize<br /><br /> Yol: dize<br /><br /> PolicyId: dize<br /><br /> Bağlam hakkında daha fazla bilgi için. LastError, bkz. [hata işleme](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>bağlam. Çalışmasını|Kimlik: dize<br /><br /> Yöntem: dize<br /><br /> Ad: dize<br /><br /> UrlTemplate: dize|
|<a id="ref-context-product"></a>bağlam. Ürünüyle|API 'ler: IEnumerable <[ıapi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Gruplar: IEnumerable <[ıgroup](#ref-igroup)\><br /><br /> Kimlik: dize<br /><br /> Ad: dize<br /><br /> Durum: enum ProductState {Notyayýmlandý}<br /><br /> SubscriptionLimit: int?<br /><br /> Abonelik gerekli: bool|
|<a id="ref-context-request"></a>bağlam. İsteyen|Body: [ımessagebody](#ref-imessagebody) veya `null`, istekte bir gövde yoksa.<br /><br /> Sertifika: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Üstbilgiler](#ref-context-request-headers): IReadOnlyDictionary < dize, dize [] ><br /><br /> IPAddress: dize<br /><br /> MatchedParameters: IReadOnlyDictionary < dize, dize ><br /><br /> Yöntem: dize<br /><br /> OriginalUrl: [Iurl](#ref-iurl)<br /><br /> URL: [Iurl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>dize bağlamı. Request. Headers. GetValueOrDefault (headerName: String, defaultValue: String)|headerName: dize<br /><br /> defaultValue: dize<br /><br /> Üst bilgi bulunamazsa, virgülle ayrılmış istek üst bilgi değerlerini veya `defaultValue` döndürür.|
|<a id="ref-context-response"></a>bağlam. Yanıtıyla|Gövde: [ımessagebody](#ref-imessagebody)<br /><br /> [Üstbilgiler](#ref-context-response-headers): IReadOnlyDictionary < dize, dize [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: dize|
|<a id="ref-context-response-headers"></a>dize bağlamı. Response. Headers. GetValueOrDefault (headerName: String, defaultValue: String)|headerName: dize<br /><br /> defaultValue: dize<br /><br /> Üst bilgi bulunamazsa, virgülle ayrılmış yanıt üst bilgi değerlerini veya `defaultValue` döndürür.|
|<a id="ref-context-subscription"></a>bağlam. Aboneliğiniz|CreatedTime: DateTime<br /><br /> EndDate: DateTime<br /><br /> Kimlik: dize<br /><br /> Anahtar: dize<br /><br /> Ad: dize<br /><br /> PrimaryKey: String<br /><br /> SecondaryKey: dize<br /><br /> StartDate: TarihSaat?|
|<a id="ref-context-user"></a>bağlam. Kullanıcısını|E-posta: dize<br /><br /> FirstName: dize<br /><br /> Gruplar: IEnumerable <[ıgroup](#ref-igroup)\><br /><br /> Kimlik: dize<br /><br /> Kimlikler: IEnumerable <[ıuserıdentity](#ref-iuseridentity)\><br /><br /> LastName: dize<br /><br /> Note: dize<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>Iapi|Kimlik: dize<br /><br /> Ad: dize<br /><br /> Yol: dize<br /><br /> Protokoller: IEnumerable < dize @ no__t-0<br /><br /> ServiceUrl: [Iurl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>Igroup|Kimlik: dize<br /><br /> Ad: dize|
|<a id="ref-imessagebody"></a>Imessagebody|As < T @ no__t-0 (preserveContent: bool = false): burada T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> @No__t-0 ve `context.Response.Body.As<T>` yöntemleri, belirtilen `T` türündeki bir istek ve yanıt iletisi gövdelerini okumak için kullanılır. Varsayılan olarak, yöntemi özgün ileti gövdesi akışını kullanır ve çağrıldıktan sonra kullanılamaz hale getirir. Yöntemin gövde akışının bir kopyası üzerinde çalışmasını sağlayarak bunun önüne geçmek için `preserveContent` parametresini `true` olarak ayarlayın. Bir örnek görmek için [buraya](api-management-transformation-policies.md#SetBody) gidin.|
|<a id="ref-iurl"></a>Iurl|Ana bilgisayar: dize<br /><br /> Yol: dize<br /><br /> Bağlantı noktası: int<br /><br /> [Sorgu](#ref-iurl-query): IReadOnlyDictionary < dize, dize [] ><br /><br /> QueryString: String<br /><br /> Düzen: dize|
|<a id="ref-iuseridentity"></a>Iuserıdentity|Kimlik: dize<br /><br /> Sağlayıcı: dize|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Üstbilgi: dize<br /><br /> Sorgu: dize|
|<a id="ref-iurl-query"></a>String Iurl. Query. GetValueOrDefault (queryParameterName: String, defaultValue: String)|queryParameterName: dize<br /><br /> defaultValue: dize<br /><br /> Parametre bulunmazsa, virgülle ayrılmış sorgu parametresi değerlerini veya `defaultValue` döndürür.|
|<a id="ref-context-variables"></a>T bağlamı. Variables. GetValueOrDefault < T @ no__t-1 (variableName: String, defaultValue: T)|variableName: dize<br /><br /> defaultValue: T<br /><br /> Değişken bulunamazsa, `T` veya `defaultValue` türüne dönüştürme değişken değer döndürür.<br /><br /> Bu yöntem, belirtilen tür döndürülen değişkenin gerçek türüyle eşleşmiyorsa bir özel durum oluşturur.|
|BasicAuthCredentials AsBasic (giriş: Bu dize)|Giriş: dize<br /><br /> Giriş parametresi geçerli bir HTTP temel kimlik doğrulaması yetkilendirme isteği üst bilgisi değeri içeriyorsa, yöntem `BasicAuthCredentials`; türünde bir nesne döndürür Aksi takdirde yöntem null değerini döndürür.|
|bool TryParseBasic (giriş: Bu dize, sonuç: Out BasicAuthCredentials)|Giriş: dize<br /><br /> Sonuç: Out BasicAuthCredentials<br /><br /> Giriş parametresi istek üstbilgisinde geçerli bir HTTP temel kimlik doğrulaması yetkilendirme değeri içeriyorsa, yöntem `true` döndürür ve sonuç parametresi `BasicAuthCredentials` türünde bir değer içerir. Aksi takdirde Yöntem `false` ' i döndürür.|
|BasicAuthCredentials|Parola: dize<br /><br /> UserID: String|
|JWT AsJwt (giriş: Bu dize)|Giriş: dize<br /><br /> Giriş parametresi geçerli bir JWT belirteci değeri içeriyorsa, yöntem `Jwt`; türünde bir nesne döndürür Aksi takdirde Yöntem `null` döndürür.|
|bool TryParseJwt (giriş: Bu dize, sonuç: Out JWT)|Giriş: dize<br /><br /> Sonuç: Out JWT<br /><br /> Giriş parametresi geçerli bir JWT belirteci değeri içeriyorsa, yöntem `true` döndürür ve sonuç parametresi `Jwt` türünde bir değer içerir. Aksi takdirde Yöntem `false` ' i döndürür.|
|JWT|Algoritma: dize<br /><br /> Hedef kitle: IEnumerable < String @ no__t-0<br /><br /> Talepler: IReadOnlyDictionary < dize, dize [] ><br /><br /> ExpirationTime: DateTime<br /><br /> Kimlik: dize<br /><br /> Veren: dize<br /><br /> Issuedat: DateTime<br /><br /> NotBefore: DateTime?<br /><br /> Subject: dize<br /><br /> Tür: dize|
|String JWT. Claim. GetValueOrDefault (claimName: String, defaultValue: String)|claimName: dize<br /><br /> defaultValue: dize<br /><br /> Üst bilgi bulunamazsa, virgülle ayrılmış talep değerlerini veya `defaultValue` döndürür.|
|Byte [] Encrypt (Input: this Byte [], alg: String, Key: Byte [], IV: Byte [])|şifrelenecek giriş düz metin<br /><br />alg-simetrik şifreleme algoritmasının adı<br /><br />anahtar şifreleme anahtarı<br /><br />IV-başlatma vektörü<br /><br />Şifrelenmiş düz metin döndürür.|
|Byte [] şifreleyin (input: Bu Byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|şifrelenecek giriş düz metin<br /><br />alg-şifreleme algoritması<br /><br />Şifrelenmiş düz metin döndürür.|
|Byte [] şifreleyin (input: Bu Byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|şifrelenecek giriş düz metin<br /><br />alg-şifreleme algoritması<br /><br />anahtar şifreleme anahtarı<br /><br />IV-başlatma vektörü<br /><br />Şifrelenmiş düz metin döndürür.|
|Byte [] şifre çözme (giriş: Bu Byte [], alg: dize, anahtar: Byte [], IV: Byte [])|Şifresi çözülecek Input-şifresi üzerinde anlaşılamadı metni<br /><br />alg-simetrik şifreleme algoritmasının adı<br /><br />anahtar şifreleme anahtarı<br /><br />IV-başlatma vektörü<br /><br />Düz metin döndürür.|
|Byte [] şifre çözme (giriş: Bu Byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|Şifresi çözülecek Input-şifresi üzerinde anlaşılamadı metni<br /><br />alg-şifreleme algoritması<br /><br />Düz metin döndürür.|
|Byte [] şifre çözme (giriş: Bu Byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|Şifresi çözülecek Input-şifresi üzerinde anlaşılamadı metni<br /><br />alg-şifreleme algoritması<br /><br />anahtar şifreleme anahtarı<br /><br />IV-başlatma vektörü<br /><br />Düz metin döndürür.|
|bool Verifynoiptali (giriş: Bu System. Security. Cryptography. X509Certificates. X509Certificate2)|Sertifika iptal durumunu denetlemeden bir X. 509.440 zinciri doğrulaması gerçekleştirir.<br /><br />Giriş-sertifika nesnesi<br /><br />Doğrulama başarılı olursa `true` döndürür; doğrulama başarısız olursa 1 @no__t.|


## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API Management ilkeler](api-management-howto-policies.md)
+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)
