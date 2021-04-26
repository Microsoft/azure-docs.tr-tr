---
title: Azure Relay Karma Bağlantılar protokol Kılavuzu | Microsoft Docs
description: Bu makalede, dinleyici ve gönderici rollerinde istemcileri bağlamak için Karma Bağlantılar geçişine sahip istemci tarafı etkileşimleri açıklanmaktadır.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 95eb6a66ac7580f115c85ddb258768b9eef62321
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314227"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Karma Bağlantılar Protokolü

Azure Relay, Azure Service Bus platformunun temel özellik özellikleri ' nin bir biridir. Geçiş özelliğinin yeni _karma bağlantılar_ ÖZELLIĞI, http ve WebSockets temel alınarak güvenli, açık protokol bir geliştir. Özel bir protokol temeli üzerinde oluşturulmuş olan, eşit olarak adlandırılmış _BizTalk Services_ özelliğin yerini alır. Karma Bağlantılar Azure Uygulama Hizmetleri 'ne tümleştirme, olduğu gibi çalışmaya devam edecektir.

Karma Bağlantılar çift yönlü, istek-yanıt ve ikili akış iletişimini ve iki ağa bağlı uygulama arasında basit veri birimi akışını mümkün bir şekilde sunar. Ya da her iki taraf da NAT veya güvenlik duvarları arkasında olabilir.

Bu makalede, dinleyici ve gönderici rollerinde istemcileri bağlamak için Karma Bağlantılar geçişine sahip istemci tarafı etkileşimleri açıklanmaktadır. Ayrıca, dinleyicilerinin yeni bağlantıları ve istekleri nasıl kabul edeceğini de açıklar.

## <a name="interaction-model"></a>Etkileşim modeli

Karma Bağlantılar geçişi, Azure bulutu 'nda tarafların kendi ağ perspektifinden bulabilecekleri ve bağlanabileceği bir buluşma noktası sağlayarak iki tarafı birbirine bağlar. Buluşma noktasına bu ve diğer belgelerde, API 'lerde ve ayrıca Azure portal "hibrit Connection" adı verilir. Karma Bağlantılar hizmet uç noktası, bu makalenin geri kalanı için "hizmet" olarak adlandırılır.

Hizmet, Web yuvası bağlantılarını ve HTTP (S) isteklerini ve yanıtlarını geçişe olanak tanır.

Etkileşim modeli, diğer birçok ağ API 'si tarafından belirlenen terminolojiler. İlk olarak gelen bağlantıları işlemeye hazır olduğunu gösteren bir dinleyici vardır ve sonra bunları geldikçe kabul eder. Diğer tarafta, bir istemci dinleyiciye bağlanır, bu da bağlantının çift yönlü iletişim yolu kurmak için kabul edilmesini bekliyor. "Connect," "Listen" ve "Accept" çoğu yuva API 'Lerinde bulduğunuz şartlardır.

Herhangi bir geçişli iletişim modelinde, bir hizmet uç noktası için giden bağlantılar oluşturan taraf vardır. Bu, "Listener" öğesini de collotial 'da "istemci" yapar ve diğer terminoloji aşırı yüklerine neden olabilir. Bu nedenle Karma Bağlantılar için kullanılan kesin terminoloji aşağıdaki gibidir:

Bir bağlantının her iki tarafındaki programlar, hizmet istemcileri olduklarından "istemciler" olarak adlandırılır. Bağlantıları bekleyen ve kabul eden istemci, "dinleyicisi" veya "dinleyici rolü" olarak kabul edilir. Hizmet aracılığıyla bir dinleyiciye doğru yeni bir bağlantı başlatan istemciye "gönderici" adı verilir veya "Gönderen rolü" olarak adlandırılır.

### <a name="listener-interactions"></a>Dinleyici etkileşimleri

Dinleyicinin hizmet ile beş etkileşimi vardır; tüm hat ayrıntıları, bu makalenin ilerleyen kısımlarında başvuru bölümünde açıklanmaktadır.

Dinleme, kabul etme ve Istek iletileri hizmetten alınır. Yenileme ve ping işlemleri dinleyici tarafından gönderilir.

#### <a name="listen-message"></a>Dinleme iletisi

Bir dinleyicinin bağlantıları kabul etmeye hazır olduğunu göstermek için bir giden WebSocket bağlantısı oluşturur. Bağlantı el sıkışması, geçiş ad alanında yapılandırılmış bir karma bağlantı adını ve bu ad üzerinde "dinler" seçeneğini karşılayan bir güvenlik belirtecini taşır.

WebSocket hizmet tarafından kabul edildiğinde, kayıt tamamlanır ve sonraki tüm etkileşimleri etkinleştirmek için "denetim kanalı" olarak, belirlenen WebSocket etkin tutulur. Hizmet, tek bir karma bağlantı için en fazla 25 eşzamanlı dinleyici sağlar. Appkancaların kotası belirlenecek.

Karma Bağlantılar için, iki veya daha fazla etkin dinleyici varsa, gelen bağlantılar bunlar arasında rastgele sırada dağıtılır; Dengeli dağıtım, en iyi çaba ile denenir.

#### <a name="accept-message"></a>İletiyi kabul et

Bir gönderici hizmette yeni bir bağlantı açtığında hizmet, karma bağlantıda etkin dinleyicilerden birini seçer ve bildirir. Bu bildirim, açık denetim kanalı üzerinden JSON iletisi olarak dinleyiciye gönderilir. İleti, dinleyicinin bağlantıyı kabul etmek için bağlanması gereken WebSocket uç noktasının URL 'sini içerir.

URL, ek bir iş olmadan doğrudan dinleyici tarafından kullanılmalıdır.
Kodlanan bilgiler, gönderenin bağlantının uçtan uca kurulmasını beklemek zorunda olduğu sürece, yalnızca kısa bir süre için geçerlidir. Kabul edilecek en fazla 30 saniye. URL yalnızca bir başarılı bağlantı girişimi için kullanılabilir. Buluşma URL 'SI ile WebSocket bağlantısı kurulduunda, bu WebSocket üzerindeki tüm diğer etkinlikler gönderene ve göndericiye gönderilir. Bu davranış, hizmet tarafından herhangi bir müdahale veya yorum olmadan gerçekleşir.

### <a name="request-message"></a>İstek iletisi

WebSocket bağlantılarına ek olarak, bu yetenek karma bağlantıda açık bir şekilde etkinleştirilirse, dinleyici bir gönderenden gelen HTTP isteği çerçevelerini de alabilir.

HTTP desteğiyle Karma Bağlantılar eklenen dinleyiciler hareketi işlemelidir `request` . `request`Bu nedenle, bağlantı kurulduktan sonra yinelenen zaman aşımı hatalarına neden olan bir dinleyici, gelecekte hizmet tarafından ENGELLENIYOR olabilir.

Http başlık ayrıştırma kitaplıkları JSON ayrıştırıcılarının bir nadir olması nedeniyle, HTTP çerçeve üst bilgisi meta verileri, dinleyici çerçevesi tarafından daha kolay işleme için JSON 'a çevrilir. Yetkilendirme bilgileri de dahil olmak üzere Gönderici ve geçiş HTTP ağ geçidi arasındaki ilişkiyle ilgili olan HTTP meta verileri iletilmez. HTTP istek gövdeleri, ikili WebSocket çerçeveleri olarak saydam olarak aktarılır.

Dinleyici, eşdeğer bir yanıt hareketini kullanarak HTTP isteklerine yanıt verebilir.

İstek/yanıt akışı, varsayılan olarak denetim kanalını kullanır, ancak gerektiğinde farklı bir buluşma WebSocket 'e "yükseltilir". Ayrı WebSocket bağlantıları her bir istemci konuşması için üretilen işi geliştirir, ancak bu, basit istemciler için mümkün olmayan daha fazla bağlantıyla dinleyiciye yük duyar.

Denetim kanalında, istek ve yanıt gövdelerinin boyutu en fazla 64 kB ile sınırlıdır. HTTP üstbilgisi meta verileri toplam 32 kB ile sınırlıdır. İstek veya yanıt bu eşiği aşarsa, dinleyiciyi [kabul etme](#accept-message)ile eşdeğer bir hareket kullanarak bir buluşma WebSocket 'e yükseltmeniz gerekir.

İstekler için hizmet, isteklerin denetim kanalı üzerinden yönlendirilip yönlendirilmeyeceğine karar verir. Bu, bir isteğin 64 kB (üstbilgiler ve gövde) veya ["öbekli" aktarım kodlaması](https://tools.ietf.org/html/rfc7230#section-4.1) ile gönderiliyorsa ve hizmetin 64 KB 'yi aşmasına veya isteği okumak için beklenme nedeni içeriyorsa, ancak bu durum, bir isteği içerir. Hizmet, isteği randevu üzerinden teslim etmeyi seçerse, yalnızca randevu adresini dinleyiciye geçirir.
Daha sonra dinleyici, buluşma WebSocket 'i SAĞLAMALıDıR ve hizmet, her zaman, buluşma WebSocket üzerinden gövdeler dahil tüm isteği teslim eder. Yanıt Ayrıca, buluşma WebSocket ' i de kullanmalıdır.

Denetim kanalını gelen istekler için, dinleyici Denetim kanalının veya buluşma aracılığıyla yanıt verip vermeyeceğine karar verir. Hizmet, her isteğin denetim kanalı üzerinden yönlendirildiği bir buluşma adresi içermelidir. Bu adres yalnızca geçerli istekten yükseltmek için geçerlidir.

Dinleyici yükseltmeyi seçerse, yanıt, belirlenen randevu yuvası üzerinden bağlantı kurar ve bu yanıtı derhal teslim eder.

Buluşma WebSocket 'i kurulduktan sonra, dinleyicinin isteklerin ve yanıtların aynı istemciden daha fazla işlenmesi için bu uygulamayı koruması gerekır. Hizmet, gönderici ile HTTPS yuva bağlantısı devam ettiğinden ve bu gönderenden gelen tüm istekleri korunan WebSocket üzerinden yönlendirdiği sürece, için WebSocket 'in bakımını sürdüder. Dinleyici, arka planda buluşma WebSocket ' i seçerse, sonraki bir isteğin zaten devam ettiğinden bağımsız olarak, hizmet gönderici bağlantısını da kaldırır.

#### <a name="renew-operation"></a>Yenileme işlemi

Dinleyiciyi kaydetmek için kullanılması gereken güvenlik belirteci ve dinleyici etkin durumdayken Denetim kanalının kullanım süresini sürdürmeye devam edebilirsiniz. Belirtecin süre sonu devam eden bağlantıları etkilemez, ancak Denetim kanalının süre sonu sonunda veya kısa bir süre sonra hizmet tarafından bırakılmasına neden olur. "Yenileme" işlemi, Denetim kanalının genişletilmiş dönemler için korunabilmesi için, dinleyicinin denetim kanalı ile ilişkili belirtecin yerini alacak şekilde göndereceği bir JSON iletisidir.

#### <a name="ping-operation"></a>Ping işlemi

Denetim kanalı uzun bir süre boşta kalırsa, yük dengeleyiciler veya NAT 'Lar gibi aracılar TCP bağlantısını bırakabilir. "Ping" işlemi, kanalda ağ rotasında herkesin bağlantının canlı olduğunu anımsatan ve dinleyici için "canlı" test görevi gören küçük miktarda veri göndererek bunu önler. Ping başarısız olursa, denetim kanalı kullanılamaz olarak kabul edilmelidir ve dinleyicinin yeniden yapılandırılması gerekir.

### <a name="sender-interaction"></a>Gönderici etkileşimi

Gönderenin hizmet ile iki etkileşimi vardır: bir Web yuvasını bağlar veya HTTPS üzerinden istek gönderir. İstek, gönderen rolünden bir Web soketinden gönderilemez.

#### <a name="connect-operation"></a>Bağlanma işlemi

"Bağlan" işlemi, hizmet üzerinde bir WebSocket açar, karma bağlantının adını ve (isteğe bağlı olarak varsayılan olarak gereklidir) sorgu dizesinde "Gönder" izni veren bir güvenlik belirteci sağlar. Daha sonra hizmet daha önce açıklanan şekilde dinleyiciyle etkileşime girer ve dinleyici bu WebSocket 'e katılmış bir buluşma bağlantısı oluşturur. WebSocket kabul edildikten sonra, bu WebSocket üzerindeki diğer tüm etkileşimler bağlı bir dinleyiciye sahiptir.

#### <a name="request-operation"></a>İstek işlemi

Özelliğin etkinleştirildiği Karma Bağlantılar için gönderici, dinleyicilerine büyük ölçüde Kısıtlanmamış HTTP istekleri gönderebilir.

Sorgu dizesine veya isteğin bir HTTP üstbilgisine gömülü olan bir geçiş erişim belirteci hariç, geçiş işlemi, geçiş adresi üzerindeki tüm HTTP işlemlerine ve geçiş adresi yolunun tüm soneklerine tamamen saydamdır ve bu, dinleyiciyi uçtan uca yetkilendirme ve hatta [CORS](https://www.w3.org/TR/cors/)gibi http uzantı özellikleri üzerinde tam olarak bırakır.

Geçiş uç noktası ile gönderici yetkilendirmesi varsayılan olarak açıktır, ancak Isteğe bağlıdır. Karma bağlantının sahibi anonim gönderenlerin izin vermeyi seçebilir. Hizmet, yetkilendirme bilgilerini şu şekilde durdurur, inceleyebilir ve bu bilgileri uygular:

1. Sorgu dizesi bir `sb-hc-token` ifade içeriyorsa, Ifade her zaman sorgu dizesinden çıkarılır. Geçiş yetkilendirmesi açıksa değerlendirilir.
2. İstek üst bilgileri bir başlık içeriyorsa `ServiceBusAuthorization` , üst bilgi IFADESI her zaman üstbilgi koleksiyonundan çıkarılır.
   Geçiş yetkilendirmesi açıksa değerlendirilir.
3. Yalnızca geçiş yetkilendirmesi açıksa ve istek üst bilgileri bir `Authorization` başlık içeriyorsa ve önceki ifadelerden hiçbiri yoksa, üst bilgi değerlendirilir ve çıkarılır. Aksi takdirde, `Authorization` her zaman olduğu gibi geçirilir.

Etkin dinleyici yoksa hizmet bir 502 "bozuk ağ geçidi" hata kodu döndürür. Hizmet isteği işlemek üzere görünmezse, hizmet 60 saniyeden sonra bir 504 "ağ geçidi zaman aşımı" döndürür.

### <a name="interaction-summary"></a>Etkileşim Özeti

Bu etkileşim modelinin sonucu gönderici istemcisinin bir dinleyiciye bağlı olan ve başka ön kimlik ya da hazırlık gerektiren bir "temiz" WebSocket ile el sıkışmasını sağlar. Bu model, tüm mevcut WebSocket istemci uygulamalarının, WebSocket istemci katmanına doğru bir şekilde oluşturulmuş bir URL sağlayarak Karma Bağlantılar hizmetinden yararlanmasını sağlar.

Dinleyicinin kabul etme etkileşimi aracılığıyla aldığı buluşma bağlantı WebSocket 'i de temizler ve, çerçevesinin yerel ağ dinleyicilerine ilişkin "kabul etme" işlemlerini ve uzaktan "kabul" işlemlerini Karma Bağlantılar ayırt eden, en az sayıda ek soyutlama ile mevcut bir WebSocket sunucu uygulamasına devredilebilir.

HTTP istek/yanıt modeli, göndericiye Isteğe bağlı yetkilendirme katmanıyla büyük ölçüde kısıtlanmamış bir HTTP protokol yüzeyi alanı sağlar. Dinleyici, bir aşağı akış HTTP isteğine geri dönebilir veya olduğu gibi, HTTP gövdeleri taşıyan ikili çerçeveler ile işlenen önceden ayrıştırılmış bir HTTP isteği üst bilgisi bölümü alır. Yanıtlar aynı biçimi kullanır. 64 KB 'den az istek ve yanıt gövdesi olan etkileşimler, tüm gönderenler için paylaşılan tek bir Web yuvası üzerinden işlenebilir. Daha büyük istekler ve yanıtlar, buluşma modeli kullanılarak işlenebilir.

## <a name="protocol-reference"></a>Protokol başvurusu

Bu bölümde, daha önce açıklanan protokol etkileşimlerinin ayrıntıları açıklanmaktadır.

Tüm WebSocket bağlantıları, 443 numaralı bağlantı noktasında, genellikle bazı WebSocket çerçevesi veya API tarafından soyutlanarak HTTPS 1,1 ' den yükseltme olarak yapılır. Buradaki açıklama, belirli bir çerçeve önermeksizin uygulama nötr olarak tutulur.

### <a name="listener-protocol"></a>Dinleyici Protokolü

Dinleyici Protokolü iki bağlantı hareketlerinden ve üç ileti işlemden oluşur.

#### <a name="listener-control-channel-connection"></a>Dinleyici denetim kanalı bağlantısı

Denetim kanalı şu şekilde bir WebSocket bağlantısı oluşturularak açılır:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

, `namespace-address` Genellikle formun karma bağlantısını barındıran Azure Relay ad alanının tam etki alanı adıdır `{myname}.servicebus.windows.net` .

Sorgu dizesi parametre seçenekleri aşağıdaki gibidir.

| Parametre        | Gerekli | Açıklama
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Evet      | Dinleyici rolü için parametre **SB-HC-Action = dinleme** olmalıdır
| `{path}`         | Yes      | Bu dinleyiciyi kaydettirmek için önceden yapılandırılmış karma bağlantının URL kodlu ad alanı yolu. Bu ifade, sabit `$hc/` yol kısmına eklenir.
| `sb-hc-token`    | Yes\*    | Dinleyici, **dinleme** hakkını karşılayan ad alanı veya karma bağlantı için GEÇERLI, URL kodlamalı Service Bus paylaşılan erişim belirteci sağlamalıdır.
| `sb-hc-id`       | Hayır       | Bu istemci tarafından sağlanan isteğe bağlı KIMLIK, uçtan uca tanılama izlemesini sağlar.

WebSocket bağlantısı, karma bağlantı yolunun kaydedilmediği veya geçersiz ya da eksik bir belirteç ya da başka bir hata nedeniyle başarısız olursa, normal HTTP 1,1 durum geri bildirim modeli kullanılarak hata geri bildirimi sağlanır. Durum açıklaması, Azure destek personeline iletilebiliyor bir hata izleme kimliği içeriyor:

| Kod | Hata          | Açıklama
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Bulunamadı      | Karma bağlantı yolu geçersiz veya temel URL hatalı biçimlendirilmiş.
| 401  | Yetkisiz   | Güvenlik belirteci eksik veya hatalı biçimlendirilmiş ya da geçersiz.
| 403  | Yasak      | Bu eylem için güvenlik belirteci bu yol için geçerli değil.
| 500  | İç hata | Hizmette bir sorun oluştu.

WebSocket bağlantısı, başlangıçta kurulduktan sonra hizmet tarafından kasıtlı olarak kapalıysa, bunu yapmanın nedeni, uygun bir WebSocket protokol hata kodu ile birlikte bir izleme KIMLIĞI de içeren açıklayıcı bir hata iletisiyle iletilir. Hizmet, bir hata durumuyla karşılaşmadan denetim kanalını kapatmayacak. Tüm temiz kapatmalar istemci tarafından denetlenir.

| WS durumu | Açıklama
| --------- | -------------------------------------------------------------------------------
| 1001      | Karma bağlantı yolu silinmiş veya devre dışı bırakılmış.
| 1008      | Güvenlik belirtecinin süresi doldu, bu nedenle yetkilendirme ilkesi ihlal edildi.
| 1011      | Hizmette bir sorun oluştu.

#### <a name="accept-handshake"></a>El sıkışma kabul et

"Kabul" bildirimi, hizmet tarafından daha önce oluşturulmuş denetim kanalı üzerinden bir WebSocket metin çerçevesinde JSON iletisi olarak dinleyiciye gönderilir. Bu iletiye yanıt yok.

İleti şu anda aşağıdaki özellikleri tanımlayan "Accept" adlı bir JSON nesnesi içerir:

* **Adres** : gelen bir bağlantıyı kabul etmek üzere hizmete WebSocket oluşturmak IÇIN kullanılan URL dizesi.
* **ID** : Bu bağlantı için benzersiz tanımlayıcı. KIMLIK gönderen istemcisi tarafından sağlandıysa, gönderenin sağladığı değer, aksi takdirde sistem tarafından oluşturulan bir değerdir.
* **Connectheaders** : gönderen tarafından geçiş uç noktasına sağlanan tüm http üstbilgileri, ayrıca sec-WebSocket-Protocol ve SEC-WebSocket-Extensions üst bilgilerini içerir.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

JSON iletisinde belirtilen adres URL 'SI dinleyici tarafından gönderici yuvasını kabul etme veya reddetme için WebSocket oluşturmak üzere kullanılır.

##### <a name="accepting-the-socket"></a>Yuva kabul ediliyor

Kabul etmek için, dinleyici, girilen adrese bir WebSocket bağlantısı kurar.

"Kabul et" iletisi bir başlık içeriyorsa `Sec-WebSocket-Protocol` , bu protokolü destekliyorsa dinleyicinin yalnızca WebSocket 'i kabul etmesi beklenmektedir. Ayrıca, WebSocket kurulduğu için üst bilgiyi ayarlar.

Aynı üstbilgi için de geçerlidir `Sec-WebSocket-Extensions` . Çerçeve bir uzantıyı destekliyorsa, üst bilgiyi `Sec-WebSocket-Extensions` uzantı için gereken El sıkışmanın sunucu tarafı yanıtı olarak ayarlanmalıdır.

Kabul etme yuvasını oluşturmak için URL 'nin olduğu gibi kullanılması gerekir, ancak aşağıdaki parametreleri içerir:

| Parametre      | Gerekli | Açıklama
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Evet      | Bir yuvayı kabul etmek için parametresi olmalıdır `sb-hc-action=accept`
| `{path}`       | Yes      | (aşağıdaki paragrafa bakın)
| `sb-hc-id`     | Hayır       | Önceki **kimlik** açıklamasına bakın.

`{path}` , bu dinleyicinin kaydedileceği önceden yapılandırılmış karma bağlantının URL kodlu ad alanı yoludur. Bu ifade, sabit `$hc/` yol kısmına eklenir.

`path`İfade, bir sonek ve eğik çizgiden sonra kayıtlı adı izleyen bir sorgu dizesi ifadesiyle genişletilebilir.
Bu parametre, gönderici istemcisinin HTTP üstbilgilerini içermesi mümkün olmadığında, dağıtım bağımsız değişkenlerini kabul eden dinleyiciye geçmesini sağlar. Bu işlem, dinleyici çerçevesinin sabit yol bölümünü ve kayıtlı adı yoldan ayrıştırır ve geri kalan bir sorgu dizesi bağımsız değişkeni olmadan, `sb-` bağlantının kabul edilip edilmeyeceğini belirleyerek uygulamanın kullanımına açık hale getirir.

Daha fazla bilgi için, aşağıdaki "Gönderen Protokolü" bölümüne bakın.

Bir hata varsa hizmet aşağıdaki gibi yanıt verebilir:

| Kod | Hata          | Açıklama
| ---- | -------------- | -----------------------------------
| 403  | Yasak      | URL geçerli değil.
| 500  | İç hata | Hizmette bir sorun oluştu

 Bağlantı kurulduktan sonra, gönderen WebSocket kapandığında veya aşağıdaki durum ile sunucu WebSocket 'i kapatır:

| WS durumu | Açıklama                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Gönderen istemcisi bağlantıyı kapatır.                                    |
| 1001      | Karma bağlantı yolu silinmiş veya devre dışı bırakılmış.                        |
| 1008      | Güvenlik belirtecinin süresi doldu, bu nedenle yetkilendirme ilkesi ihlal edildi. |
| 1011      | Hizmette bir sorun oluştu.                                            |

##### <a name="rejecting-the-socket"></a>Yuva reddediliyor

 İletiyi inceledikten sonra yuva reddedildiğinde, `accept` reddetme nedenine yönelik durum kodu ve durum açıklamasının göndericiye akabilmesi için benzer bir el sıkışma gerekir.

 Buradaki protokol tasarımı seçeneği, dinleyici istemci uygulamalarının bir WebSocket istemcisine güvenmeye devam edebilmesi ve fazladan, çıplak bir HTTP istemcisi kullanılmasına gerek kalmaması için bir WebSocket el sıkışması (tanımlı bir hata durumunda sona erdirmek üzere tasarlanmıştır) kullanmaktır.

 Yuva reddetmek için, istemci iletiden adres URI 'sini alır `accept` ve buna aşağıdaki şekilde iki sorgu dizesi parametresi ekler:

| Param                   | Gerekli | Açıklama                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-statusCode        | Yes      | Sayısal HTTP durum kodu.                |
| SB-HC-statusDescription | Yes      | Red için insan tarafından okunabilen neden. |

Sonuç URI 'SI daha sonra bir WebSocket bağlantısı kurmak için kullanılır.

Doğru bir şekilde tamamlanırken, hiçbir WebSocket oluşturulmadığından HTTP hata kodu 410 ile bu el sıkışma özellikle başarısız olur. Bir sorun yanlış olursa aşağıdaki kodlar hatayı anlatmaktadır:

| Kod | Hata          | Açıklama                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Yasak      | URL geçerli değil.                |
| 500  | İç hata | Hizmette bir sorun oluştu. |

#### <a name="request-message"></a>İstek iletisi

`request`İleti, hizmet tarafından denetim kanalı üzerinden dinleyiciye gönderilir. Aynı ileti ayrıca, yeniden bir kez kurulduktan sonra buluşma WebSocket üzerinden gönderilir.

`request`İki bölümden oluşur: bir başlık ve ikili gövde çerçeveleri.
Gövde yoksa gövde çerçeveleri atlanır. Boolean `body` özelliği, istek iletisinde bir gövdenin mevcut olup olmadığını gösterir.

İstek gövdesi olan bir istek için, yapı şöyle görünebilir:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Dinleyicinin birden çok ikili çerçeveye bölünen istek gövdesini alma işlemesi gerekır (bkz. [WebSocket parçaları](https://tools.ietf.org/html/rfc6455#section-5.4)).
FIN bayrağı ayarlanmış bir ikili çerçeve alındığında istek sona erer.

Gövdesi olmayan bir istek için yalnızca bir metin çerçevesi vardır.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

İçin JSON içeriği `request` aşağıdaki gibidir:

* **Adres** URI dizesi. Bu istek için kullanılacak randevu adresidir. Gelen istek 64 kB 'tan büyükse, bu iletinin geri kalanı boş bırakılır ve istemcinin aşağıda açıklanan işleme bir buluşma el sıkışma eşdeğerini başlatması gerekır `accept` . Hizmet daha sonra, `request` belirlenen Web soketi üzerinde de tamamlanır. Yanıtın 64 kB 'yi aşması bekleniyorsa, dinleyicinin de bir buluşma anlaşması başlatması ve yanıtı Kurulu Web soketi üzerinden aktarması gerekır.
* **id** – dize. Bu istek için benzersiz tanımlayıcı.
* **RequestHeaders** : Bu nesne, gönderen tarafından bitiş noktasına VERILEN tüm HTTP üstbilgilerini ve [yukarıda](#request-operation)açıklandığı gibi yetkilendirme bilgilerini ve ağ geçidiyle kesinlikle bağlantıyla ilişkili üst bilgileri içerir. Özellikle, [RFC7230](https://tools.ietf.org/html/rfc7230)içinde tanımlanan veya ayrılan tüm üstbilgiler `Via` çıkarılır ve iletilmez:

  * `Connection` (RFC7230, Bölüm 6,1)
  * `Content-Length`  (RFC7230, Section 3.3.2)
  * `Host`  (RFC7230, Bölüm 5,4)
  * `TE`  (RFC7230, Bölüm 4,3)
  * `Trailer`  (RFC7230, Bölüm 4,4)
  * `Transfer-Encoding`  (RFC7230, Section 3.3.1)
  * `Upgrade` (RFC7230, Bölüm 6,7)
  * `Close`  (RFC7230, Bölüm 8,1)

* **Requesttarget** – dize. Bu özellik isteğin  ["Istek hedefini" (RFC7230, bölüm 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) tutar. Bu, tüm önekli parametrelerden çıkarılır olan sorgu dizesi bölümünü içerir `sb-hc-` .
* **Yöntem** -dize. Bu, [RFC7231 başına, Bölüm 4 '](https://tools.ietf.org/html/rfc7231#section-4)te istek yöntemidir. `CONNECT`Yöntem kullanılmamalıdır.
* **gövde** – Boolean. Bir veya daha fazla ikili gövde çerçevesi olup olmadığını gösterir.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>İsteklere yanıt verme

Alıcı yanıt vermelidir. Bağlantıyı sürdürirken, isteklere yanıt vermek için tekrarlanan hata, dinleyiciye müdahale edilmesine yol açabilir.

Yanıtlar herhangi bir sırada gönderilebilir, ancak her isteğin 60 saniye içinde yanıtlanması veya teslimin başarısız olarak bildirilmesi gerekir. 60 saniyelik son tarih, `response` çerçeve hizmet tarafından alınana kadar sayılır. Birden çok ikili çerçeveye sahip devam eden bir yanıt 60 saniyeden uzun bir süre boşta bırakılamaz veya sonlandırılır.

İstek denetim kanalı üzerinden alınmışsa, yanıtın, isteğin alındığı yerden Denetim kanalında gönderilmesi ya da bir buluşma kanalı üzerinden gönderilmesi gerekir.

Yanıt, "Response" adlı bir JSON nesnesidir. Gövde içeriğini işlemeye yönelik kurallar, bu `request` iletiyle ve özelliği temel alınarak tam olarak benzer `body` .

* **RequestId** – dize. Gerekli. `id` `request` Yanıt verilen iletinin Özellik değeri.
* **StatusCode** – sayı. Gerekli. bildirimin sonucunu gösteren sayısal bir HTTP durum kodu. [502 "bozuk ağ geçidi"](https://tools.ietf.org/html/rfc7231#section-6.6.3) ve [504 "ağ geçidi zaman aşımı"](https://tools.ietf.org/html/rfc7231#section-6.6.5)dışında, [RFC7231, Bölüm 6 '](https://tools.ietf.org/html/rfc7231#section-6) nın tüm durum kodlarına izin verilir.
* **StatusDescription** -dize. Seçim. HTTP durum-RFC7230 başına kod nedeni tümceciği [, Bölüm 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **ResponseHeaders** – dış http yanıtında ayarlanacak http üst bilgileri.
  `request`' De olduğu gibi, RFC7230 tanımlı üst bilgiler kullanılmamalıdır.
* **gövde** – Boolean. İkili gövde çerçevesinin takip edilip edilmeyeceğini belirtir.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Buluşma aracılığıyla yanıt verme

64 kB 'ı aşan yanıtlar için, yanıt bir buluşma yuvası üzerinden teslim alınmalıdır. Ayrıca, istek 64 kB 'yi aşarsa ve `request` yalnızca adres alanını içeriyorsa, ' yi elde etmek için bir buluşma yuvası oluşturulmalıdır `request` . Bir buluşma soketi oluşturulduktan sonra, ilgili istemciden gelen yanıtların ve bu ilgili istemciden gelen isteklerin yanıtları devam ederken buluşma yuvası üzerinden teslim edilmesi gerekır.

`address`İçindeki URL, `request` buluşma yuvasını oluşturmak için olduğu gibi kullanılmalıdır, ancak aşağıdaki parametreleri içerir:

| Parametre      | Gerekli | Açıklama
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Evet      | Bir yuvayı kabul etmek için parametresi olmalıdır `sb-hc-action=request`

Bir hata varsa hizmet aşağıdaki gibi yanıt verebilir:

| Kod | Hata           | Açıklama
| ---- | --------------- | -----------------------------------
| 400  | Geçersiz Istek | Tanınmayan eylem veya URL geçerli değil.
| 403  | Yasak       | URL 'nin süresi doldu.
| 500  | İç hata  | Hizmette bir sorun oluştu

 Bağlantı kurulduktan sonra, istemci HTTP yuvası kapandığında veya aşağıdaki durum ile sunucu WebSocket 'i kapatır:

| WS durumu | Açıklama                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Gönderen istemcisi bağlantıyı kapatır.                                    |
| 1001      | Karma bağlantı yolu silinmiş veya devre dışı bırakılmış.                        |
| 1008      | Güvenlik belirtecinin süresi doldu, bu nedenle yetkilendirme ilkesi ihlal edildi. |
| 1011      | Hizmette bir sorun oluştu.                                            |


#### <a name="listener-token-renewal"></a>Dinleyici belirteci yenileme

Dinleyici belirtecinin kullanım süresini sona ermek üzereyken, bir metin çerçevesi iletisini, belirlenen denetim kanalı aracılığıyla hizmete göndererek değiştirebilir. İleti `renewToken` Şu anda aşağıdaki özelliği tanımlayan adlı BIR JSON nesnesi içerir:

* **belirteç** : ad alanı veya karma bağlantı için **dinleme** HAKKıNı engelleyen geçerli, URL kodlu Service Bus paylaşılan erişim belirteci.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Belirteç doğrulaması başarısız olursa, erişim reddedilir ve bulut hizmeti denetim kanalı WebSocket 'sini bir hata ile kapatır. Aksi takdirde yanıt yoktur.

| WS durumu | Açıklama                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Güvenlik belirtecinin süresi doldu, bu nedenle yetkilendirme ilkesi ihlal edildi. |

### <a name="web-socket-connect-protocol"></a>Web yuvası bağlantı protokolü

Gönderen protokolü, dinleyicinin kurulma yöntemiyle etkili bir şekilde aynıdır.
Amaç, uçtan uca WebSocket için en yüksek saydamlığa sahiptir. Bağlanılacak adres, dinleyiciyle aynıdır, ancak "eylem" farklıdır ve belirtecin farklı bir izni olması gerekir:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

_Ad alanı-adresi_ , genellikle formun karma bağlantısını barındıran Azure Relay ad alanının tam etki alanı adıdır `{myname}.servicebus.windows.net` .

İstek, uygulama tanımlı olanlar da dahil olmak üzere rastgele ek HTTP üstbilgileri içerebilir. Sağlanan tüm üst bilgiler dinleyiciye akar ve `connectHeader` **Accept** denetim iletisinin nesnesinde bulunabilir.

Sorgu dizesi parametre seçenekleri aşağıdaki gibidir:

| Param          | Gerekli mi? | Açıklama
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Evet       | Gönderen rolü için parametresi olmalıdır `sb-hc-action=connect` .
| `{path}`       | Yes       | (aşağıdaki paragrafa bakın)
| `sb-hc-token`  | Yes\*     | Dinleyici, **Gönder** hakkını karşılayan ad alanı veya karma bağlantı için GEÇERLI, URL kodlamalı Service Bus paylaşılan erişim belirteci sağlamalıdır.
| `sb-hc-id`     | Hayır        | Uçtan uca tanılama izlemeyi sağlayan ve kabul etme el sıkışması sırasında dinleyici için kullanılabilir hale getirilen isteğe bağlı bir KIMLIK.

 , `{path}` Bu dinleyicinin kaydedileceği önceden yapılandırılmış karma BAĞLANTıNıN URL kodlamalı ad alanı yoludur. `path`İfade, daha fazla iletişim kurmak için bir sonek ve sorgu dizesi ifadesiyle genişletilebilir. Karma bağlantı yolun altına kayıtlıysa `hyco` , `path` ifadeye `hyco/suffix?param=value&...` sonra burada tanımlanan sorgu dizesi parametreleri gelebilir. Ardından, bir bütün ifade aşağıdaki gibi olabilir:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

`path`İfade, "kabul" Denetim iletisinde yer alan adres URI 'sindeki dinleyiciye geçirilir.

WebSocket bağlantısı, karma bağlantı yolu kaydedilmediğinde, geçersiz veya eksik bir belirteç ya da başka bir hata nedeniyle başarısız olursa, normal HTTP 1,1 durum geri bildirim modeli kullanılarak hata geri bildirimi sağlanır. Durum açıklaması, Azure destek personeline iletilebiliyor bir hata izleme KIMLIĞI içeriyor:

| Kod | Hata          | Açıklama
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Bulunamadı      | Karma bağlantı yolu geçersiz veya temel URL hatalı biçimlendirilmiş.
| 401  | Yetkisiz   | Güvenlik belirteci eksik veya hatalı biçimlendirilmiş ya da geçersiz.
| 403  | Yasak      | Güvenlik belirteci bu yol için geçerli değil ve bu eylem için.
| 500  | İç hata | Hizmette bir sorun oluştu.

WebSocket bağlantısı, başlangıçta kurulduktan sonra hizmet tarafından kasıtlı olarak kapalıysa, bunu yapmanın nedeni, uygun bir WebSocket protokol hata kodu ile birlikte bir izleme KIMLIĞI de içeren açıklayıcı bir hata iletisiyle iletilir.

| WS durumu | Açıklama
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Dinleyici, yuvayı kapatır.
| 1001      | Karma bağlantı yolu silinmiş veya devre dışı bırakılmış.
| 1008      | Güvenlik belirtecinin süresi doldu, bu nedenle yetkilendirme ilkesi ihlal edildi.
| 1011      | Hizmette bir sorun oluştu.

### <a name="http-request-protocol"></a>HTTP istek Protokolü

HTTP istek protokolü, protokol yükseltmeleri haricinde rastgele HTTP isteklerine izin verir.
HTTP istekleri, karma bağlantı WebSocket istemcileri için kullanılan $hc indüzeltmesini olmadan varlığın normal çalışma zamanı adresine işaret edilir.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

_Ad alanı-adresi_ , genellikle formun karma bağlantısını barındıran Azure Relay ad alanının tam etki alanı adıdır `{myname}.servicebus.windows.net` .

İstek, uygulama tanımlı olanlar da dahil olmak üzere rastgele ek HTTP üstbilgileri içerebilir. RFC7230 içinde doğrudan tanımlanmış olanlar haricinde ( [istek iletisini](#request-message)görüntüle) dinleyiciye Flow ve `requestHeader` **istek** iletisinin nesnesinde bulunan tüm sağlanan üst bilgiler.

Sorgu dizesi parametre seçenekleri aşağıdaki gibidir:

| Param          | Gerekli mi? | Açıklama
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Evet\*     | Dinleyici, **Gönder** hakkını karşılayan ad alanı veya karma bağlantı için GEÇERLI, URL kodlamalı Service Bus paylaşılan erişim belirteci sağlamalıdır.

Belirteç, `ServiceBusAuthorization` veya `Authorization` http üst bilgisinde de yapılabilir. Karma bağlantı anonim isteklere izin verecek şekilde yapılandırıldıysa belirteç atlanabilir.

Hizmet, doğru bir HTTP proxy 'si olarak olmasa bile bir ara sunucu olarak davrandığı için, `Via` var olan `Via` üst bilgiyi [RFC7230, Bölüm 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1)ile uyumlu bir üst bilgi ya da açıklama ekler.
Hizmet, ana bilgisayar adı ' nı ' a ekler `Via` .

| Kod | İleti  | Açıklama                    |
| ---- | -------- | ------------------------------ |
| 200  | Tamam       | İstek en az bir dinleyici tarafından işlendi.  |
| 202  | Kabul edildi | İstek en az bir dinleyici tarafından kabul edildi. |

Bir hata oluşursa, hizmet aşağıdaki gibi yanıt verebilir. Yanıtın hizmetten mı yoksa dinleyicisinden mı geldiği, üstbilginin varlığı aracılığıyla tanımlanabilir `Via` . Üst bilgi varsa, yanıt dinleyicisinden olur.

| Kod | Hata           | Açıklama
| ---- | --------------- |--------- |
| 404  | Bulunamadı       | Karma bağlantı yolu geçersiz veya temel URL hatalı biçimlendirilmiş.
| 401  | Yetkisiz    | Güvenlik belirteci eksik veya hatalı biçimlendirilmiş ya da geçersiz.
| 403  | Yasak       | Güvenlik belirteci bu yol için geçerli değil ve bu eylem için.
| 500  | İç hata  | Hizmette bir sorun oluştu.
| 503  | Hatalı ağ geçidi     | İstek herhangi bir dinleyiciye yönlendirilemedi.
| 504  | Ağ geçidi zaman aşımı | İstek bir dinleyiciye yönlendirildi, ancak dinleyici, gerekli sürede alındı bilgisini bildiremedi.

## <a name="next-steps"></a>Sonraki adımlar

* [Geçiş hakkında SSS](relay-faq.yml)
* [Ad alanı oluşturma](relay-create-namespace-portal.md)
* [.NET kullanmaya başlama](relay-hybrid-connections-dotnet-get-started.md)
* [Node kullanmaya başlama](relay-hybrid-connections-node-get-started.md)
