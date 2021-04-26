---
title: Azure IoT Hub MQTT 5 desteği (Önizleme)
description: IoT Hub MQTT 5 desteği hakkında bilgi edinin
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603557"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5 desteğe genel bakış (Önizleme)

**Sürüm:** 2,0 **api-Version:** 2020-10-01-Preview

Bu belge MQTT sürüm 5,0 protokolü üzerinden IoT Hub veri düzlemi API 'sini tanımlar. Bu API 'deki tüm tanımlar için bkz. [API başvurusu](iot-hub-mqtt-5-reference.md) .

## <a name="prerequisites"></a>Önkoşullar

- Yeni bir IoT Hub 'ında, MQTT 5 ' i denemek için [Önizleme modunu etkinleştirin](iot-hub-preview-mode.md) .
- [MQTT 5 belirtiminin](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) önceki bilgileri gereklidir.

## <a name="level-of-support-and-limitations"></a>Destek düzeyi ve sınırlamalar

MQTT 5 için IoT Hub desteği önizleme aşamasındadır ve aşağıdaki yollarla sınırlıdır (açıkça belirtilmedikçe, özellikler aracılığıyla istemciye iletilir `CONNACK` ):

- Henüz resmi [Azure IoT Hub CIHAZ SDK](iot-hub-devguide-sdks.md) desteği yok.
- Abonelik tanımlayıcıları desteklenmez.
- Paylaşılan abonelikler desteklenmez.
- `RETAIN` desteklenmez.
- `Maximum QoS`, `1` değeridir.
- `Maximum Packet Size``256 KiB`(işlem başına daha fazla kısıtlamaya tabidir).
- Atanan Istemci kimlikleri desteklenmez.
- `Keep Alive` sınırlıdır `19 min` (libir denetim için en fazla gecikme: `28.5 min` ).
- `Topic Alias Maximum`, `10` değeridir.
- `Response Information` desteklenmez; `CONNACK` `Response Information` özelliği Contains olsa bile özelliği `CONNECT` döndürmez `Request Response Information` .
- `Receive Maximum` (ile birlikte izin verilen en fazla bekleyen kabul edilmemiş `PUBLISH` paket sayısı (istemci-sunucu yönünde) `QoS: 1` `16` .
- Tek bir istemcide en fazla abonelik olabilir `50` .
  Sınıra ulaşıldığında, `SUBACK` `0x97` abonelikler için neden kodu döndürür (Kota aşıldı).

## <a name="connection-lifecycle"></a>Bağlantı yaşam döngüsü

### <a name="connection"></a>Bağlantı

Bu API 'yi kullanarak bir istemciyi IoT Hub bağlamak için, MQTT 5 belirtimine göre bağlantı kurun.
İstemci, `CONNECT` başarılı TLS el sıkışması sonrasında paketi 30 saniye içinde göndermelidir veya sunucu bağlantıyı kapatır.
Aşağıda bir paket örneği verilmiştir `CONNECT` :

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` özelliği gereklidir ve hangi kimlik doğrulama yönteminin kullanıldığını tanımlar. Kimlik doğrulama yöntemi hakkında daha fazla bilgi için bkz. [kimlik doğrulaması](#authentication).
- `Authentication Data` Özellik işleme bağımlıdır `Authentication Method` . `Authentication Method`Olarak ayarlanırsa `SAS` , `Authentication Data` gereklidir ve geçerli bir imza içermelidir. Kimlik doğrulama verileri hakkında daha fazla bilgi için bkz. [kimlik doğrulaması](#authentication).
- `api-version` özelliği gereklidir ve bu belirtim 'in uygulanabilmesi için bu belirtim üst bilgisinde belirtilen API sürümü değerine ayarlanmalıdır.
- `host` özelliği, kiracının ana bilgisayar adını tanımlar. SNı uzantısı, TLS el sıkışması sırasında Istemci Merhaba kaydında sunulmadığı müddetçe gereklidir
- `sas-at` bağlantı zamanını tanımlar.
- `sas-expiry` Belirtilen SAS için sona erme saati tanımlar.
- `client-agent` isteğe bağlı olarak, bağlantıyı oluşturan istemciyle ilgili bilgileri iletişim kurar.

> [!NOTE]
> `Authentication Method` ve büyük harfli adlara sahip belirtim genelinde diğer özellikler, MQTT 5 ' teki birinci sınıf özelliklerdir. Bunlar MQTT 5 belirtiminde Ayrıntılar bölümünde açıklanmıştır. `api-version` ve Dash 'teki diğer özellikler, IoT Hub API 'sine özgü Kullanıcı özellikleridir.

IoT Hub, `CONNACK` kimlik doğrulama ve bağlantıyı destekleyecek verileri getirme işlemini tamamladıktan sonra paket ile yanıt verir. Bağlantı başarıyla kurulduysa, şöyle `CONNACK` görünür:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Bu `CONNACK` paket özellikleri [MQTT 5 belirtimini](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)izler. IoT Hub yeteneklerini yansıtır.

### <a name="authentication"></a>Kimlik Doğrulaması

`Authentication Method`İstemci üzerindeki özelliği, `CONNECT` Bu bağlantı için ne tür bir kimlik doğrulaması kullandığını tanımlar:

- `SAS` -Paylaşılan erişim Imzası `CONNECT` , özelliği içinde sağlanır `Authentication Data` ,
- `X509` -istemci, istemci sertifikası kimlik doğrulamasını kullanır.

 Kimlik doğrulama yöntemi istemcinin yapılandırılan yöntemiyle IoT Hub eşleşmezse kimlik doğrulaması başarısız olur.

> [!NOTE]
> Bu API `Authentication Method` , özelliğin paket içinde ayarlanmasını gerektirir `CONNECT` . `Authentication Method`Özellik sağlanmazsa, bağlantı yanıt vererek başarısız olur `Bad Request` .

Önceki API sürümlerinde kullanılan Kullanıcı adı/parola kimlik doğrulaması desteklenmiyor.

#### <a name="sas"></a>SAS

SAS tabanlı kimlik doğrulaması ile istemci, bağlantı bağlamı imzasını sağlamalıdır. Bu, MQTT bağlantısının gerçekliğini kanıtlar. İmza, IoT Hub istemci yapılandırmasındaki iki kimlik doğrulama anahtarlarından birini veya [paylaşılan erişim ilkesinin](iot-hub-devguide-security.md)iki paylaşılan erişim anahtarını temel almalıdır.

İmzalanacak dize şöyle oluşturulmalıdır:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` SNı uzantısından (TLS el sıkışması sırasında istemci Hello kaydında istemci tarafından sunulur) veya `host` paketteki Kullanıcı özelliği ile türetilir `CONNECT` .
- `Client Id` paketteki Istemci tanımlayıcısıdır `CONNECT` .
- `sas-policy` -varsa, kimlik doğrulaması için kullanılan IoT Hub erişim ilkesini tanımlar. Paket üzerinde Kullanıcı özelliği olarak kodlanır `CONNECT` . İsteğe bağlı: atlanırsa, bunun yerine cihaz kayıt defterindeki kimlik doğrulama ayarlarının kullanılacağı anlamına gelir.
- `sas-at` -varsa, bağlantı zamanını belirtir-geçerli zaman. Bu, paketteki türünün Kullanıcı özelliği olarak kodlanır `time` `CONNECT` .
- `sas-expiry` kimlik doğrulaması için sona erme saatini tanımlar. Bu, `time` pakette yazılı bir Kullanıcı özelliğidir `CONNECT` . Bu özellik gereklidir.

İsteğe bağlı parametreler için, atlanırsa, imzalanacak dize yerine boş dizenin kullanılması gerekır.

HMAC-SHA256, bir cihazın simetrik anahtarlarından birine göre dizeyi karma hale almak için kullanılır. Karma değeri daha sonra özelliğin değeri olarak ayarlanır `Authentication Data` .

#### <a name="x509"></a>X509

`Authentication Method`Özelliği olarak ayarlandıysa `X509` , IoT Hub, belirtilen istemci sertifikasına göre bağlantının kimliğini doğrular.

#### <a name="reauthentication"></a>Yeniden

SAS tabanlı kimlik doğrulaması kullanılırsa, kısa süreli kimlik doğrulama belirteçlerini kullanmanızı öneririz. Bağlantının kimlik doğrulamasını yapmak ve süre sonu nedeniyle bağlantıyı kesmeyi önlemek için, istemci, `AUTH` paketi `Reason Code: 0x19` (yeniden kimlik doğrulama) ile birlikte göndererek yeniden kimliğini doğrulaması gerekir

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Kuralın

- `Authentication Method` İlk kimlik doğrulaması için kullanılan ile aynı olmalıdır
- bağlantının kimliği, paylaşılan erişim Ilkesi tabanlı SAS kullanılarak doğrulandıktan sonra, yeniden kimlik doğrulaması sırasında kullanılan imza aynı ilkeyi temel almalıdır.

Yeniden kimlik doğrulaması başarılı olursa IoT Hub `AUTH` paketi `Reason Code: 0x00` (başarılı) ile gönderir. Aksi takdirde, IoT Hub `DISCONNECT` paketi `Reason Code: 0x87` (yetkilendirilmemiş) ile gönderir ve bağlantıyı kapatır.

### <a name="disconnection"></a>Bağlantıyı

Sunucu, birkaç nedenden dolayı istemcinin bağlantısını kesebilir:

- istemci, doğrudan negatif onay (veya Yanıt) ile yanıt vermek olanaksız bir şekilde yanlış çalışıyor,
- Sunucu bağlantının durumunu güncel tutamakta başarısız oldu,
- aynı kimliğe sahip istemci bağlandı.

Sunucunun MQTT 5,0 belirtiminde tanımlanan herhangi bir neden koduyla bağlantısı kesilebilir. Önemli bahsetmeler:

- `135` (Yetkilendirilmemiş) yeniden kimlik doğrulaması başarısız olduğunda, geçerli SAS belirtecinin süresi dolar veya cihazın kimlik bilgileri değişikliği
- `142` (Oturum devralındı) aynı istemci kimliğine sahip yeni bağlantı açıldı.
- `159` (Bağlantı hızı aşıldı) IoT Hub 'a yönelik bağlantı hızı aştığında  
- `131` (Uygulamaya özgü hata), bu API 'de tanımlanan özel hatalar için kullanılır. `status` ve `reason` Özellikler, bağlantının kesilmesi nedeniyle ilgili diğer ayrıntıları iletmek için kullanılacaktır (Ayrıntılar için bkz. [Yanıt](#response) ).

## <a name="operations"></a>Operations

Bu API 'deki tüm işlevler işlem olarak ifade edilir. Telemetriyi Gönder işlemi örneği aşağıda verilmiştir:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Bu API 'deki işlemlerin tüm belirtimi için bkz. [API başvurusu](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Bu belirtimde bulunan tüm örnekler, istemcinin perspektifinden gösterilmiştir. İmza `->` , istemci gönderme paketi, `<-` alma anlamına gelir.

### <a name="message-topics-and-subscriptions"></a>İleti konuları ve abonelikleri

Bu API 'deki işlem iletilerinde kullanılan konular ile başlar `$iothub/` .
MQTT Aracısı semantiği bu işlemlere uygulanmaz (Ayrıntılar için bkz. "[ \$ Ile başlayan konular ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)).
`$iothub/`Bu API 'de tanımlanmayan ile başlayan konular desteklenmez:

- Tanımsız konuya ileti gönderme Yanıt ile sonuçlanır `Not Found` (aşağıdaki Ayrıntılar Için [yanıta](#response) bakın),
- Tanımsız konuya abone olmak ile sonuçlanır `SUBACK` `Reason Code: 0x8F` (konu filtresi geçersiz).

Konu adları ve özellik adları büyük/küçük harfe duyarlıdır ve tam eşleşme olmalıdır. Örneğin, `$iothub/telemetry/` iken desteklenmez `$iothub/telemetry` .

> [!NOTE]
> Kapsamındaki aboneliklerde joker karakterler `$iothub/..` desteklenmez. Diğer bir deyişle, bir istemci veya hizmetine abone olamaz `$iothub/+` `$iothub/#` . Bunun denenmeye çalışılması ile sonuçlanır `SUBACK` `Reason Code: 0xA2` (joker, abonelik desteklenmez). Yalnızca tek kesimli joker karakterler ( `+` ), söz konusu işlemler için konu adı içindeki yol parametreleri yerine desteklenir.

### <a name="interaction-types"></a>Etkileşim türleri

Bu API 'deki tüm işlemler, iki etkileşim türünden birine dayalıdır:

- İsteğe bağlı onay (MessageAck) içeren ileti
- Request-Response (ReqRep)

İşlemler ayrıca yöne göre farklılık gösterir (Exchange 'deki ilk iletinin yönüne göre belirlenir):

- İstemciden sunucuya (C2S)
- Sunucudan Istemciye (s2c)

Örneğin, "bildirim içeren Ileti" türündeki Istemci-sunucu işlemi, tanıtıcı doğrudan yöntem Request-Response türünün sunucudan Istemciye işlemidir.

#### <a name="message-acknowledgement-interactions"></a>İleti-onay etkileşimleri

İsteğe bağlı onay (MessageAck) etkileşimi içeren ileti, `PUBLISH` MQTT içindeki ve paketlerinin bir değişimi olarak ifade edilir `PUBACK` . Bildirim isteğe bağlıdır ve gönderici, ile paketi göndererek isteği istememek üzere seçim gösterebilir `PUBLISH` `QoS: 0` .

> [!NOTE]
> `PUBACK`Paketteki Özellikler `Maximum Packet Size` istemci tarafından bildirildiği için kesilmelidir, IoT Hub verilen sınıra sığabilecek kadar çok kullanıcı özelliği olarak kalır. Önce listelenen kullanıcı özelliklerinin daha sonra listelenenlerden daha yüksek bir şekilde gönderilmesi daha yüksektir. `Reason String` özelliği en düşük önceliğe sahiptir.

##### <a name="example-of-simple-messageack-interaction"></a>Basit MessageAck etkileşimi örneği

İleti:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Onay (başarılı):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response etkileşimleri

Request-Response (ReqRep) etkileşimlerinde, hem Istek hem de yanıt `PUBLISH` ile paketlere çeviri yapar `QoS: 0` .

`Correlation Data` özellik her ikisinde de ayarlanmalıdır ve yanıt paketini Istek paketiyle eşleştirmek için kullanılır.

Bu API, `$iothub/responses` Tüm ReqRep işlemleri için tek bir yanıt konusunu kullanır. İstemciden sunucuya işlemler için bu konudan abone olma/aboneliği kaldırma gerekli değildir-sunucu, tüm istemcilerin abone olduğunu varsayar.

##### <a name="example-of-simple-reqrep-interaction"></a>Basit ReqRep etkileşimi örneği

İstek:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Yanıt (başarılı):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep etkileşimleri `PUBLISH` `QoS: 1` istek veya yanıt iletisi olarak paketleri desteklemez. Istek `PUBLISH` sonuçları yanıt olarak gönderiliyor `Bad Request` .

Özellikte desteklenen uzunluk üst sınırı `Correlation Data` 16 bayttır. `Correlation Data` `PUBLISH` Paketteki Özellik 16 bayttan daha uzun bir değere ayarlanmışsa, IoT Hub `DISCONNECT` Outcome ile gönderir `Bad Request` ve bağlantıyı kapatır. Bu davranış yalnızca bu API içinde değiştirilen paketler için geçerlidir.

> [!NOTE]
> Bağıntı verileri rastgele bir bayt sırasıdır, örn. UTF-8 dizesi olarak garanti edilmez.
>
> ReqRep yanıt için önceden tanımlanmış konuları kullanın; Istek paketindeki yanıt konu özelliği `PUBLISH` (gönderen tarafından ayarlandıysa) yok sayılır.

IoT Hub, tüm istemci-sunucu ReqRep işlemleri için istemciyi yanıt konularına otomatik olarak abone olur. İstemci yanıt konusunun aboneliğini açıkça kaldırsa bile, IoT Hub aboneliğin otomatik olarak tekrar olduğunu belirtir. Sunucudan istemciye yönelik ReqRep etkileşimleri için hala cihazın abone olması gerekir.

### <a name="message-properties"></a>İleti özellikleri

İşlem özellikleri-sistem veya Kullanıcı tanımlı-MQTT 5 ' te paket özellikleri olarak ifade edilir.

Kullanıcı özelliği adları büyük/küçük harfe duyarlıdır ve tam olarak tanımlandığı şekilde yazılmalıdır. Örneğin, `Trace-ID` iken desteklenmez `trace-id` .

Belirtim dışında ve önek olmadan Kullanıcı özellikleri olan istekler `@` hatayla sonuçlanır.

Sistem Özellikleri, birinci sınıf özellikleri (örneğin, `Content Type` ) veya Kullanıcı özellikleri olarak kodlanır. Belirtim desteklenen sistem özelliklerinin ayrıntılı bir listesini sağlar.
Belirtimde açıkça belirtilmediği takdirde, tüm ilk sınıf özellikleri yok sayılır.

Kullanıcı tanımlı özelliklere izin verildiğinde, adları biçimini izlemelidir `@{property name}` . Kullanıcı tanımlı özellikler yalnızca geçerli UTF-8 dize değerlerini destekler. Örneğin, `MyProperty1` değeri olan özelliği `15` Kullanıcı özelliği olarak ad ve değer olarak kodlanmalıdır `@MyProperty` `15` .

IoT Hub Kullanıcı özelliğini tanımıyorsa, bir hata olarak değerlendirilir IoT Hub ve ile ( `PUBACK` `Reason Code: 0x83` uygulamaya özgü hata) ve `status: 0100` (hatalı istek) ile yanıt verir. Bildirim istenmedi (QoS: 0), `DISCONNECT` aynı hata ile paket geri gönderilir ve bağlantı sonlandırılır.

Bu API aşağıdaki veri türlerini tanımlar `string` :

- `time`: bu yana milisaniye sayısı `1970-01-01T00:00:00.000Z` . Örneğin, `1600987195320` için `2020-09-24T22:39:55.320Z`
- `u32`: işaretsiz 32-bit tamsayı numarası,
- `u64`: işaretsiz 64-bit tamsayı numarası,
- `i32`: imzalanan 32 bit tamsayı numarası.

### <a name="response"></a>Yanıt

Etkileşimler farklı sonuçlar elde edebilir: `Success` , `Bad Request` , `Not Found` ve diğerleri.
Sonuçlar, Kullanıcı özelliğine göre birbirinden ayrılır `status` . `Reason Code``PUBACK`paketlerde (MessageAck etkileşimleri için), `status` mümkün olduğu durumlarda anlam ile eşleşir.

> [!NOTE]
> İstemci `Request Problem Information: 0` , Connect paketinde belirtirse, `PUBACK` özelliği de dahil olmak üzere MQTT 5 belirtimiyle uyumlu olmak için paketlere hiçbir Kullanıcı özelliği gönderilmeyecektir `status` . Bu durumda, istemci, `Reason Code` bildirim pozitif mi yoksa negatif mi olduğunu tespit edebilir. 

Her etkileşime bir varsayılan (veya başarılı) sahiptir. , `Reason Code` `0` Ve `status` "ayarlanmamış" özelliğine sahiptir. Aksi durumda:.

- MessageAck etkileşimleri için `PUBACK` `Reason Code` 0x0 (başarı) dışında bir alan alır. `status` sonucu açıklamak için özelliği mevcut olabilir.
- ReqRep etkileşimleri için, yanıt `PUBLISH` `status` özellik kümesini alır.
- Doğrudan ile MessageAck etkileşimlerine yanıt verme yöntemi olmadığından `QoS: 0` , `DISCONNECT` yanıt bilgileri ve ardından Bağlantıyı Kes ile birlikte paket gönderilir.

Örnekler:

Hatalı Istek (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Yetkilendirilmemiş (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Yetkilendirilmemiş (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Gerektiğinde, IoT Hub aşağıdaki kullanıcı özelliklerini ayarlar:

- `status` -IoT Hub işlemin durumu için genişletilmiş kod. Bu kod, sonuçları ayırt etmek için kullanılabilir.
- `trace-id` – işlem için izleme KIMLIĞI; IoT Hub, iç araştırma için kullanılabilecek işlemle ilgili ek Tanılamalar tutabilir.
- `reason` -insanlar tarafından belirtilen bir durumda işlemin neden sonlandırıldığına ilişkin daha fazla bilgi sağlayan insan tarafından okunabilen ileti `status` .

> [!NOTE]
> İstemci `Maximum Packet Size` , Connect paketindeki özelliği çok küçük bir değere ayarlarsa, tüm Kullanıcı özellikleri uygun olmayabilir ve pakette görünmez.
> 
> `reason` yalnızca kişiler için tasarlanmıştır ve istemci mantığındaki kullanılmamalıdır. Bu API, iletilerin uyarı veya sürüm değişikliği olmadan herhangi bir noktada değiştirilmesini sağlar.
>
> İstemci `RequestProblemInformation: 0` , Connect paketinde gönderiyorsa, Kullanıcı özellikleri [MQTT 5 belirtimine](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)göre bildirimlerin içine dahil değildir.

#### <a name="status-code"></a>Durum kodu

`status` Özellik işlem için durum kodunu taşır. Makine okuma verimliliği için en iyi duruma getirilmiştir.
Bu, benzer dize olarak kodlanmış iki baytlık işaretsiz tamsayıdan oluşur `0501` .
Kod yapısı (bit eşlem):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Bayraklar için ilk bayt kullanılır:

- bit 0 ve 1 sonuç türünü belirtir:
  - `00` -başarılı
  - `01` -istemci hatası
  - `10` -sunucu hatası
- bit 2: `1` hatanın yeniden denendiğini belirtir
- bit 3 ila 7 ayrılmıştır ve olarak ayarlanması gerekir `0`

İkinci bayt gerçek ayrı yanıt kodu içerir. Farklı bayraklara sahip hata kodları aynı ikinci bayt değerine sahip olabilir. Örneğin,,,,, `0001` , `0101` `0201` `0301` hata kodları farklı anlamlara sahip olabilir.

Örneğin, `Too Many Requests` bir istemcedir ve kendi kodu ile yeniden denenebilir bir hata `1` . Değeri veya olur `0000 0101 0000 0001` `0x0501` .

İstemciler, işlemin başarıyla yapılıp yapılmayacağını belirlemek için tür bitleri kullanabilir. İstemciler, yeniden denenip yeniden denenmeyeceğine karar vermek için yeniden denenebilir bit de kullanabilir.

## <a name="recommendations"></a>Öneriler

### <a name="session-management"></a>Oturum yönetimi

`CONNACK` paket, `Session Present` sunucunun önceden oluşturulmuş oturumu geri yükleyip kullanmadığını belirtecek özelliği taşır. Bu özelliği kullanarak, konuların abone olup olmadığını veya abonelik daha önce yapıldıktan sonra abone ol ' a atlayın.

' `Session Present` İ kullanmaya devam etmek için, istemci, yaptığı abonelikleri takip etmelidir (yani, paket gönderildi `SUBSCRIBE` ve `SUBACK` başarılı bir neden kodu ile alındı) veya tek bir Exchange içindeki tüm konulara abone olduğunuzdan emin olun `SUBSCRIBE` / `SUBACK` . Aksi halde, istemci iki `SUBSCRIBE` paket gönderirse ve sunucu tarafından yalnızca bir tane başarıyla işleniyorsa sunucu, `Session Present: 1` `CONNACK` istemci aboneliklerinin yalnızca bir parçası kabul edildiğinde içinde iletişim kurar.

Daha eski bir istemci sürümünün tüm konulara abone olmadığı bir durum oluşmasını engellemek için, istemci davranışı değiştiğinde (örneğin, bellenim güncelleştirmesi 'nin bir parçası olarak) koşulsuz bir şekilde abone olmak daha iyidir. Ayrıca, eski aboneliklerin gerisinde kalmasını sağlamak için (izin verilen en yüksek abonelik sayısından alınan), artık kullanımda olmayan aboneliklerden açıkça aboneliği kaldırın.

### <a name="batching"></a>Toplu İşleme

Bir toplu ileti göndermek için özel biçim yoktur. TLS ve ağlarda kaynak yoğunluklu işlemlerin yükünü azaltmak için, paketleri `PUBLISH` `PUBACK` `SUBSCRIBE` temel alınan TLS/TCP yığınına teslim etmeden önce paket (,, vb.) paketleyin. Ayrıca, istemci konu diğer adını "Batch" içinde daha kolay hale getirebilirsiniz:

- Bağlantı için birinci pakette tüm konu adını yerleştirin `PUBLISH` ve konu diğer adını bununla ilişkilendirin.
- Boş konu adı ve konu diğer adı özelliği ile aynı konu için aşağıdaki paketleri koyun.

## <a name="migration"></a>Geçiş

Bu bölümde, [önceki MQTT API 'siyle](iot-hub-mqtt-support.md)karşılaştırılan API 'deki değişiklikler listelenir.

- Aktarım Protokolü MQTT 5 ' tir. Daha önce MQTT 3.1.1.
- SAS kimlik doğrulaması için bağlam bilgileri, `CONNECT` imzayla birlikte kodlanması yerine doğrudan pakette bulunur.
- Kimlik doğrulama yöntemi, kullanılan kimlik doğrulama yöntemini göstermek için kullanılır.
- Paylaşılan erişim Imzası, kimlik doğrulama verileri özelliğine konur. Daha önce parola alanı kullanıldı.
- İşlemler için konular farklıdır:
  - Telemetri: `$iothub/telemetry` yerine `devices/{Client Id}/messages/events` ,
  - Komutlar: `$iothub/commands` yerine `devices/{Client Id}/messages/devicebound` ,
  - Patch Ikizi bildirildi: `$iothub/twin/patch/reported` yerine `$iothub/twin/PATCH/properties/reported` ,
  - Bildirim Ikizi Istenen durum değiştirildi: `$iothub/twin/patch/desired` yerine `$iothub/twin/PATCH/properties/desired` .
- İstemci-sunucu isteği-yanıt işlemleri için abonelik, yanıt konusu gerekli değildir.
- Konu adı segmentinde özellikleri kodlamak yerine Kullanıcı özellikleri kullanılır.
- Özellik adları, özel ön ek olan kısaltmalar yerine "Dash-Case" adlandırma kuralına göre yazılmış. Kullanıcı tanımlı özellikler artık bunun yerine ön ek gerektirir. Örneğin, `$.mid` Şu anda `message-id` `myProperty1` olur `@myProperty1` .
- Bağıntı verileri özelliği, `$rid` konu başlığı altında kodlanmış Özellik yerine istek-yanıt işlemleri için istek ve yanıt iletilerini ilişkilendirmek için kullanılır.
- `iothub-connection-auth-method` özellik artık telemetri olaylarında damgalı değil.
- C2D komutları cihazdan abonelik olmadığında temizlenmez. Cihaz abone olmaya veya süreleri dolana kadar sıraya alınır.

## <a name="examples"></a>Örnekler

### <a name="send-telemetry"></a>Telemetri gönderme

İleti:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Olumlu

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternatif onay (daraltılmış):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Get ikizi 'in durumunu gönder

İstek:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Yanıt (başarılı):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Yanıt (izin verilmiyor):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Doğrudan yöntem çağrısını işle

İstek:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Yanıt (başarılı):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` ayarlanmadı-bu bir başarı yanıtı.

Cihaz kullanılamıyor yanıtı:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>QoS 0 kullanılırken hata oluştu, Bölüm 1

İstek:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Yanıt:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>QoS 0 kullanılırken hata oluştu, Bölüm 2

İstek:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Yanıt:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Sonraki adımlar

- MQTT 5 Preview API başvurusunu gözden geçirmek için bkz. [IoT Hub veri düzlemi MQTT 5 API başvurusu](iot-hub-mqtt-5-reference.md).
- Bir C# örneğini izlemek için bkz. [GitHub örnek deposu](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).