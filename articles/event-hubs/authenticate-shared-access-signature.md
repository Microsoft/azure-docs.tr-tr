---
title: Paylaşılan erişim imzaları ile Azure Event Hubs erişimi kimlik doğrulaması
description: Bu makalede, paylaşılan erişim imzalarını kullanarak Event Hubs kaynaklarına erişimin nasıl doğrulandığını gösterilmektedir.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986505"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Paylaşılan erişim imzalarını (SAS) kullanarak Event Hubs kaynaklarına erişimin kimliğini doğrulama
Paylaşılan erişim imzası (SAS), paylaşılan erişim imzasına sahip istemcilere verdiğiniz erişim türü üzerinde ayrıntılı denetim sağlar. SAS içinde ayarlayabileceğiniz bazı denetimler aşağıda verilmiştir: 

- Başlangıç saati ve sona erme saati dahil olmak üzere SAS 'ın geçerli olduğu Aralık.
- SAS tarafından verilen izinler. Örneğin, bir Event Hubs ad alanı için SAS, dinleme iznini verebilir, ancak Send iznini alabilir.
- Yalnızca geçerli kimlik bilgileri sunan istemciler, bir olay hub 'ına veri gönderebilir.
- İstemci başka bir istemcinin kimliğine bürünemedi.
- Standart dışı bir istemcinin bir olay hub 'ına veri göndermesini engellenebilir.

Bu makalede SAS kullanılarak Event Hubs kaynaklarına erişimin doğrulanması ele alınmaktadır. SAS kullanarak Event Hubs kaynaklarına erişimi **Yetkilendirme** hakkında bilgi edinmek için [Bu makaleye](authorize-access-shared-access-signature.md)bakın. 

> [!NOTE]
> Microsoft, Azure AD kimlik bilgilerini, paylaşılan erişim imzalarını kullanmak yerine en iyi güvenlik uygulaması olarak mümkün olduğunca kullanmanızı önerir ve bu da daha kolay tehlikeye girebilir. Event Hubs kaynaklarınıza ayrıntılı erişim sağlamak için paylaşılan erişim imzaları (SAS) kullanmaya devam edebilirsiniz, ancak Azure AD SAS belirteçlerini yönetmeye gerek kalmadan benzer yetenekler sunar veya güvenliği aşılmış bir SAS iptal etme konusunda endişelenmenize gerek kalmaz.
> 
> Azure Event Hubs 'de Azure AD tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure ad kullanarak Event Hubs erişimi yetkilendirme](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>SAS kimlik doğrulaması için yapılandırma
EventHubs paylaşılan erişim yetkilendirme kuralını bir Event Hubs ad alanında veya bir varlıkta (Olay Hub 'ı örneği veya bir olay hub 'ında Kafka konusu) yapılandırabilirsiniz. Bir tüketici grubunda paylaşılan erişim yetkilendirme kuralını yapılandırma Şu anda desteklenmiyor, ancak tüketici grubuna erişim sağlamak için bir ad alanı veya varlıkta yapılandırılan kuralları kullanabilirsiniz. 

Aşağıdaki görüntüde, yetkilendirme kurallarının örnek varlıklarda nasıl uygulandığı gösterilmektedir. 

![Yetkilendirme kuralını Yapılandır](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Bu örnekte, örnek Event Hubs ad alanı (ExampleNamespace) iki varlığa sahiptir: EH1 ve topic1. Yetkilendirme kuralları hem varlık düzeyinde hem de ad alanı düzeyinde tanımlanır.  

ManageRuleNS, sendRuleNS ve listenRuleNS yetkilendirme kuralları hem Event hub örneği EH1 hem de T1 konusu için geçerlidir. ListenRule-Eh ve sendRule-Eh yetkilendirme kuralları yalnızca Event hub örneği için geçerlidir EH1 ve sendRuleT yetkilendirme kuralı yalnızca topic1 konu başlığı için geçerlidir. 

SendRuleNS yetkilendirme kuralı kullanılırken, istemci uygulamaları hem EH1 hem de topic1 'e gönderebilir. SendRuleT yetkilendirme kuralı kullanıldığında, yalnızca topic1 'e ayrıntılı erişim sağlar ve bu nedenle, bu kuralı kullanan istemci uygulamaları artık EH1 'e gönderemezler ancak yalnızca topic1.

## <a name="generate-a-shared-access-signature-token"></a>Paylaşılan erişim Imza belirteci oluştur 
Bir yetkilendirme kuralı adı adına erişimi olan tüm istemciler ve imzalama anahtarlarından biri bir SAS belirteci oluşturabilir. Belirteç, aşağıdaki biçimde bir dize oluşturarak oluşturulur:

- `se`  – Token süre sonu anında. Belirtecin süresi 1 1970 Ocak 00:00:00 ' de (UNIX dönemi), bu yana beklenen saniye sayısı
- `skn` – SAS anahtar adı olan yetkilendirme kuralının adı.
- `sr` – Erişilmekte olan kaynağın URI 'SI.
- `sig` İmza.

İmza-dize, kaynak URI üzerinden (önceki bölümde açıklandığı gibi kapsam) ve CRLF ile ayrılmış olarak belirteç süre sonu anlık öğesinin dize gösterimine göre hesaplanan SHA-256 karmasıdır.

Karma hesaplama aşağıdaki sözde koda benzer ve 256 bit/32 baytlık karma değer döndürür. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Belirteç, karma değeri aynı parametrelerle yeniden hesaplanabilmesi, böylece veren 'in geçerli bir imzalama anahtarına sahip olduğu doğrulanıyor.

Kaynak URI 'SI, erişimin talep aldığı Service Bus kaynağın tam URI 'sidir. Örneğin, http:// <namespace> . ServiceBus.Windows.net/ <entityPath> veya `sb://<namespace>.servicebus.windows.net/<entityPath>;` olan `http://contoso.servicebus.windows.net/eventhubs/eh1` .

URI yüzde kodlamalı olmalıdır.

İmzalama için kullanılan paylaşılan erişim yetkilendirme kuralı, bu URI tarafından belirtilen varlıkta veya hiyerarşik üst öğelerinden biri ile yapılandırılmış olmalıdır. Örneğin, `http://contoso.servicebus.windows.net/eventhubs/eh1` veya `http://contoso.servicebus.windows.net` Önceki örnekte.

Bir SAS belirteci, imza dizesinde kullanılan ön ekli tüm kaynaklar için geçerlidir <resourceURI> .

> [!NOTE]
> Paylaşılan erişim ilkesini kullanarak Event Hubs için bir erişim belirteci oluşturabilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim yetkilendirme ilkesi](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>İlkeden imza (belirteç) oluşturma 
Aşağıdaki bölümde, paylaşılan erişim imzası ilkelerini kullanarak bir SAS belirteci oluşturma gösterilmektedir.

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>JAVA

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>SAS ile Event Hubs yayımcılarının kimliğini doğrulama 
Olay yayımcısı, bir olay hub 'ı için sanal uç noktası tanımlar. Yayımcı yalnızca bir olay hub 'ına ileti göndermek ve ileti almak için kullanılabilir.

Genellikle, bir olay hub 'ı istemci başına bir yayımcı kullanır. Bir olay hub 'ının yayımcılarından birine gönderilen tüm iletiler, bu olay hub 'ında sıraya alınır. Yayımcılar ayrıntılı erişim denetimini etkinleştirir.

Her Event Hubs istemciye, istemciye yüklenen benzersiz bir belirteç atanır. Belirteçler, her benzersiz belirtecin farklı benzersiz yayımcıya erişim izni verdiği şekilde üretilir. Belirteç tutan bir istemci yalnızca bir yayımcıya ve başka bir yayımcıya bağlanabilir. Birden çok istemci aynı belirteci paylaşıyorsa, her biri yayımcıyı paylaşır.

Tüm belirteçler SAS anahtarlarıyla atanır. Genellikle, tüm belirteçler aynı anahtarla imzalanır. İstemciler, istemcilerin bu belirteçleri üretmelerini önleyen anahtardan haberdar değildir. İstemciler, süreleri dolana kadar aynı belirteçlerde çalışır.

Örneğin, yalnızca Event Hubs göndermek/yayımlamak üzere kapsamı belirlenmiş yetkilendirme kurallarını tanımlamak için bir gönderme yetkilendirme kuralı tanımlamanız gerekir. Bu, bir ad alanı düzeyinde yapılabilir veya belirli bir varlığa (Olay Hub 'ları örneği veya konu başlığı) daha ayrıntılı kapsam sağlayabilir. Bir istemci veya bu tür ayrıntılı erişim kapsamına sahip bir uygulama Event Hubs yayımcı olarak adlandırılır. Bunu yapmak için aşağıdaki adımları izleyin:

1. Kendisine **gönderme** kapsamını atamak için yayımlamak istediğiniz VARLıKTA bir SAS anahtarı oluşturun. Daha fazla bilgi için bkz. [paylaşılan erişim yetkilendirme ilkeleri](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Adım içinde oluşturulan anahtarı kullanarak belirli bir yayımcının süre sonu zamanına sahip bir SAS belirteci oluşturun.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Yalnızca bir varlığa ve belirtecin erişim izni verdiği yayımcıya gönderebilen yayımcı istemcisine belirteç sağlayın.

    Belirtecin süresi dolduktan sonra istemci, varlığa gönderme/yayımlama erişimini kaybeder. 


> [!NOTE]
> Önerilmese de, bir olay hub 'ına veya bir ad alanına erişim izni veren belirteçlerle donabilme mümkündür. Bu belirteci tutan herhangi bir cihaz, doğrudan bu olay hub 'ına ileti gönderebilir. Ayrıca, cihaz bu olay hub 'ına gönderilmeden silinemez.
> 
> Belirli ve ayrıntılı kapsamları sağlamak her zaman önerilir.

> [!IMPORTANT]
> Belirteçler oluşturulduktan sonra, her istemci kendi benzersiz belirteciyle sağlanır.
>
> İstemci bir olay hub 'ına veri gönderdiğinde, isteği belirteç ile Etiketler. Bir saldırganın belirteci bırakıp çalmasını önlemek için, istemci ile Olay Hub 'ı arasındaki iletişim şifrelenmiş bir kanal üzerinden gerçekleşmelidir.
> 
> Bir belirteç saldırgan tarafından çalınırsa saldırgan, belirtecinin çalındığı istemciyi taklit edebilir. Yayımcı listeleme, farklı bir yayımcı kullanan yeni bir belirteç alıncaya kadar istemciyi kullanılamaz hale getirir.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>SAS ile Event Hubs tüketicilerinin kimliğini doğrulama 
Event Hubs üreticileri tarafından oluşturulan verilerden kullanılan arka uç uygulamalarının kimliğini doğrulamak için, Event Hubs belirteci kimlik doğrulaması, istemcilerinin, Event Hubs ad alanına veya Olay Hub örneğine veya konusuna atanmış olan **Yönetim** haklarına veya **dinleme** ayrıcalıklarına sahip olmasını gerektirir. Veriler, tüketici grupları kullanılarak Event Hubs tüketilecektir. SAS ilkesi size ayrıntılı kapsam sağlarken, bu kapsam yalnızca varlık düzeyinde tanımlanır ve tüketici düzeyinde değildir. Bu, ad alanı düzeyinde tanımlanan ayrıcalıkların veya Olay Hub 'ının örneği ya da konu düzeyi söz konusu varlığın tüketici gruplarına uygulanacak anlamına gelir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [SAS kullanarak yetkilendirme](authenticate-shared-access-signature.md)
- [Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yetkilendirme](authenticate-shared-access-signature.md)
- [Event Hubs hakkında daha fazla bilgi edinin](event-hubs-about.md)

Aşağıdaki ilgili makalelere bakın:

- [Azure Active Directory kullanarak bir uygulamadan Azure Event Hubs istek kimliklerini doğrulama](authenticate-application.md)
- [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)
- [Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-shared-access-signature.md)
