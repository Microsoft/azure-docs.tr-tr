---
title: Azure IoT Hub Identity kayıt defterini anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub Identity kayıt defterinin açıklaması ve cihazlarınızı yönetmek için nasıl kullanılacağı. Cihaz kimliklerinin toplu olarak içeri ve dışarı aktarılması hakkında bilgi içerir.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 42def04db63d81bdb3eff8098daa8c75924bffec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502088"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>IoT Hub 'ınızdaki kimlik kayıt defterini anlayın

Her IoT Hub 'ına, IoT Hub 'ına bağlanmasına izin verilen cihazlar ve modüller hakkında bilgi depolayan bir kimlik kayıt defteri bulunur. Bir cihaz ya da modülün bir IoT Hub 'ına bağlanabilmesi için, IoT Hub 'ının kimlik kayıt defterindeki bu cihaz veya modül için bir giriş olmalıdır. Bir cihaz ya da modülün kimlik kayıt defterinde depolanan kimlik bilgilerini temel alarak IoT Hub ile kimlik doğrulaması yapması gerekir.

Kimlik kayıt defterinde depolanan cihaz veya modül KIMLIĞI büyük/küçük harfe duyarlıdır.

Yüksek düzeyde, kimlik kayıt defteri, cihaz veya modül kimlik kaynaklarının REST özellikli bir koleksiyonudur. Kimlik kayıt defterine bir girdi eklediğinizde IoT Hub, uçuş, buluttan cihaza iletileri içeren sıra gibi cihaz başına kaynakların bir kümesini oluşturur.

Aşağıdakileri yapmanız gerektiğinde kimlik kayıt defteri 'ni kullanın:

* IoT Hub 'ınıza bağlanan cihazları veya modülleri sağlayın.
* Hub 'ın cihazına veya modüle yönelik uç noktalara cihaz başına/modül başına erişimi denetleme.

> [!NOTE]
> * Kimlik kayıt defteri uygulamaya özgü hiçbir meta veri içermiyor.
> * Modül kimliği ve modül ikizi genel önizlemede. Genel kullanıma hazır olduğunda modül kimliğinde aşağıdaki özellik desteklenecektir.
>

## <a name="identity-registry-operations"></a>Kimlik kayıt defteri işlemleri

IoT Hub Identity kayıt defteri aşağıdaki işlemleri açığa çıkarır:

* Cihaz veya modül kimliği oluşturma
* Cihazı veya modül kimliğini güncelleştirme
* KIMLIĞE göre cihaz veya modül kimliğini alma
* Cihazı veya modül kimliğini silme
* En fazla 1000 kimliği listeleyin
* Cihaz kimliklerini Azure Blob depolamaya aktarma
* Azure Blob depolamadan cihaz kimliklerini içeri aktarma

Tüm bu işlemler [RFC7232](https://tools.ietf.org/html/rfc7232)' de belirtildiği gibi iyimser eşzamanlılık kullanabilir.

> [!IMPORTANT]
> Bir IoT Hub 'ının kimlik kayıt defterindeki tüm kimlikleri almanın tek yolu [dışa aktarma](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) işlevini kullanmaktır.

IoT Hub Identity kayıt defteri:

* Herhangi bir uygulama meta verisi içermez.
* , Anahtar olarak **DeviceID** veya **ModuleID** kullanılarak bir sözlük gibi erişilebilir.
* , İfade eden sorguları desteklemez.

IoT çözümünün genellikle uygulamaya özgü meta verileri içeren ayrı bir çözüme özgü deposu vardır. Örneğin, bir akıllı bina çözümünde çözüme özgü mağaza, bir sıcaklık algılayıcısı dağıtılan odayı kaydeder.

> [!IMPORTANT]
> Yalnızca cihaz yönetimi ve sağlama işlemleri için kimlik kayıt defterini kullanın. Çalışma zamanında yüksek aktarım hızı işlemleri, kimlik kayıt defterinde işlem gerçekleştirmeye bağlı olmamalıdır. Örneğin, bir komut göndermeden önce bir cihazın bağlantı durumunu denetlemek desteklenen bir model değildir. Kimlik kayıt defteri ve [Cihaz sinyal](iot-hub-devguide-identity-registry.md#device-heartbeat) deseninin [azaltma hızlarını](iot-hub-devguide-quotas-throttling.md) denetlediğinizden emin olun.

## <a name="disable-devices"></a>Cihazları devre dışı bırakma

Kimlik kayıt defterindeki bir kimliğin **Status** özelliğini güncelleştirerek cihazları devre dışı bırakabilirsiniz. Genellikle, bu özelliği iki senaryoda kullanırsınız:

* Bir sağlama düzenleme işlemi sırasında. Daha fazla bilgi için bkz. [cihaz sağlama](iot-hub-devguide-identity-registry.md#device-provisioning).

* Herhangi bir nedenle, bir cihazın tehlikede olduğunu veya yetkisiz olduğunu düşünün.

Bu özellik modüller için kullanılamaz.

## <a name="import-and-export-device-identities"></a>Cihaz kimliklerini içeri ve dışarı aktarma

Cihaz kimliklerini bir IoT Hub 'ının kimlik kayıt defterinden toplu olarak dışarı aktarmak için [IoT Hub kaynak sağlayıcısı uç noktasındaki](iot-hub-devguide-endpoints.md) zaman uyumsuz işlemleri kullanın. Dışarı aktarmalar, kimlik kayıt defterinden okunan cihaz kimliği verilerini kaydetmek için müşterinin sağladığı bir blob kapsayıcısını kullanan uzun süredir çalışan işlerdir.

Cihaz kimliklerini bir IoT Hub 'ının kimlik kayıt defterine toplu olarak içeri aktarmak için [IoT Hub kaynak sağlayıcısı uç noktasındaki](iot-hub-devguide-endpoints.md) zaman uyumsuz işlemleri kullanın. İçeri aktarmalar, kimlik kayıt defterine cihaz kimlik verileri yazmak için müşterinin sağladığı bir blob kapsayıcısında veri kullanan uzun süredir çalışan işlerdir.

İçeri ve dışarı aktarma API 'Leri hakkında daha fazla bilgi için bkz. [IoT Hub kaynak sağlayıcısı REST API 'leri](/rest/api/iothub/iothubresource). İşleri içeri ve dışarı aktarma hakkında daha fazla bilgi edinmek için bkz. [IoT Hub cihaz kimliklerinin toplu yönetimi](iot-hub-bulk-identity-mgmt.md).

Cihaz kimlikleri, hizmet API 'SI aracılığıyla bir IoT Hub [REST API](/rest/api/iothub/service/jobs/createimportexportjob) veya IoT Hub [hizmeti SDK](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)'larından biri aracılığıyla da aktarılabilir ve içeri aktarılabilir.

## <a name="device-provisioning"></a>Cihaz sağlama

Belirli bir IoT çözümünün depoladığı cihaz verileri, bu çözümün belirli gereksinimlerine bağlıdır. Ancak, minimum olarak bir çözümün cihaz kimliklerini ve kimlik doğrulama anahtarlarını depolaması gerekir. Azure IoT Hub, kimlikler, kimlik doğrulama anahtarları ve durum kodları gibi her bir cihaz için değerleri depolayabilen bir kimlik kayıt defteri içerir. Bir çözüm, ek cihaz verilerini depolamak için tablo depolama, BLOB depolama veya Cosmos DB gibi diğer Azure hizmetlerini kullanabilir.

*Cihaz sağlama* , ilk cihaz verilerini çözümünüzdeki depolara ekleme işlemidir. Yeni bir cihazın hub 'ınıza bağlanmasını sağlamak için IoT Hub kimlik kayıt defterine bir cihaz KIMLIĞI ve anahtarı eklemeniz gerekir. Sağlama sürecinin bir parçası olarak, diğer çözüm depolarında cihaza özgü verileri de başlatmak gerekebilir. Ayrıca, Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak bir veya daha fazla IoT Hub 'ı için insan müdahalesi gerektirmeden tam zamanında sağlama olanağı sağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [sağlama hizmeti belgeleri](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Cihaz sinyali

IoT Hub Identity kayıt defteri **ConnectionState** adlı bir alan içerir. Yalnızca geliştirme ve hata ayıklama sırasında **ConnectionState** alanını kullanın. IoT çözümleri, çalışma zamanında alanı sorgulayamaz. Örneğin, bir cihazın bir cihazdan cihaza ileti veya SMS göndermeden önce bağlı olup olmadığını denetlemek için **ConnectionState** alanını sorgulamayın. Uyarıları almak ve cihaz bağlantı durumunu izlemek için Event Grid üzerindeki [ **cihaz bağlantısı kesilen** olaya](iot-hub-event-grid.md#event-types) abone olmayı öneririz. Bu [öğreticiyi](iot-hub-how-to-order-connection-state-events.md) kullanarak cihaz bağlantılı ve cihaz bağlantısı kesilen olayları ıot çözümünüzdeki IoT Hub tümleştirme hakkında bilgi edinebilirsiniz.

IoT çözümünüzün bir cihazın bağlı olup olmadığını bilmeleri gerekiyorsa, *sinyal modelini* uygulayabilirsiniz.
Sinyal modelinde cihaz, her sabit sürede (örneğin, her saat en az bir kez) cihazdan buluta iletiler gönderir. Bu nedenle, bir cihazda gönderilecek veri olmasa bile, hala boş bir cihazdan buluta ileti gönderir (genellikle sinyal olarak tanımlayan bir özellik ile). Hizmet tarafında, çözüm her bir cihaz için alınan son sinyalle bir harita tutar. Çözüm, cihazdan beklenen sürede bir sinyal iletisi almazsa, cihazda bir sorun olduğunu varsayar.

Daha karmaşık bir uygulama, [Azure izleyici](../azure-monitor/index.yml) 'den ve bağlanmaya veya iletişim kurmaya çalışan, ancak başarısız olan cihazları tanımlamak için [Azure Kaynak durumu](../service-health/resource-health-overview.md) bilgileri içerebilir. Daha fazla bilgi edinmek için bkz. [izleyici IoT Hub](monitor-iot-hub.md) ve [IoT Hub kaynak durumunu denetleme](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). Sinyal modelini uyguladığınızda, IoT Hub kotaları kontrol ettiğinizden emin olun [ve kısıtlar](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Bir IoT çözümü yalnızca buluttan cihaza iletiler gönderileceğini ve iletilerin büyük cihaz kümelerine yayınlanıp göndermeyeceğini tespit etmek için bağlantı durumunu kullanıyorsa, daha basit *kısa süre sonu zaman* modelini kullanmayı göz önünde bulundurun. Bu model, sinyal modelini kullanarak bir cihaz bağlantı durumu kayıt defteri ile aynı sonuca ulaşır, daha verimli hale getirir. İleti bildirimleri talep ederseniz IoT Hub, hangi cihazların ileti alabileceğini ve hangilerinin olmadığını size bildirebilir.

## <a name="device-and-module-lifecycle-notifications"></a>Cihaz ve modül yaşam döngüsü bildirimleri

IoT Hub, yaşam döngüsü bildirimleri gönderilerek bir cihaz kimliği oluşturulduğunda veya silindiğinde IoT çözümünüze bildirimde bulunabilir. Bunu yapmak için, IoT çözümünüzün bir rota oluşturması ve veri kaynağını *DeviceLifecycleEvents*' e eşit olarak ayarlaması gerekir. Varsayılan olarak, yaşam döngüsü bildirimleri gönderilmez, diğer bir deyişle, böyle bir yol önceden mevcut değildir. *DeviceLifecycleEvents*'e eşit veri kaynağıyla bir yol oluşturarak, her iki cihaz kimliği ve modül kimliği için yaşam döngüsü olayları gönderilir; Ancak, ileti içerikleri, olayların modül kimlikleri veya cihaz kimlikleri için oluşturulup oluşturulmayacağı göre farklılık gösterir.  IoT Edge modüller için, modül kimliği oluşturma akışının diğer modüllerden farklı olduğu unutulmamalıdır; bu nedenle IoT Edge modülleri için, güncelleştirilmiş IoT Edge modül kimliği için karşılık gelen IoT Edge aygıtı çalışıyorsa, oluşturma bildirimi yalnızca gönderilir. Diğer tüm modüller için, IoT Hub tarafında modül kimliği her güncelleştirildiğinde yaşam döngüsü bildirimleri gönderilir.  Bildirim iletisi özellikleri ve gövdesi içerir.

Özellikler: Ileti sistemi özelliklerine sembol ön eki eklenir `$` .

Cihaz için bildirim iletisi:

| Name | Değer |
| --- | --- |
|$content türü | uygulama/json |
|$iothub-enqueuedtime |  Bildirimin gönderildiği zaman |
|$iothub-ileti-kaynak | deviceLifecycleEvents |
|$content kodlaması | UTF-8 |
|opType | **createdeviceıdentity** veya **deletedeviceıdentity** |
|hubName | IoT Hub adı |
|deviceId | Cihazın KIMLIĞI |
|operationTimestamp | ISO8601 işlem zaman damgası |
|ıothub-Message-Schema | deviceLifecycleNotification |

Gövde: Bu bölüm JSON biçimindedir ve oluşturulan cihaz kimliğinin ikizi temsil eder. Örneğin,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Modül için bildirim iletisi:

| Name | Değer |
| --- | --- |
$content türü | uygulama/json |
$iothub-enqueuedtime |  Bildirimin gönderildiği zaman |
$iothub-ileti-kaynak | moduleLifecycleEvents |
$content kodlaması | UTF-8 |
opType | **createmoduleıdentity** veya **deletemoduleıdentity** |
hubName | IoT Hub adı |
Modül kimliği | Modülün KIMLIĞI |
operationTimestamp | ISO8601 işlem zaman damgası |
ıothub-Message-Schema | moduleLifecycleNotification |

Gövde: Bu bölüm JSON biçimindedir ve oluşturulan modül kimliğinin ikizi temsil eder. Örneğin,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Cihaz kimliği özellikleri

Cihaz kimlikleri aşağıdaki özelliklerle JSON belgeleri olarak temsil edilir:

| Özellik | Seçenekler | Açıklama |
| --- | --- | --- |
| deviceId |gerekli, güncelleştirmeler üzerinde salt okunurdur |ASCII 7 bit alfasayısal karakter ve belirli özel karakterler için büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda): `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| GenerationID |gerekli, salt okunurdur |128 karakter uzunluğunda bir IoT Hub 'ı tarafından üretilen, büyük/küçük harfe duyarlı dize. Bu değer, silinen ve yeniden oluşturulduğu zaman, cihazları aynı **DeviceID** ile ayırt etmek için kullanılır. |
| özelliği |gerekli, salt okunurdur |[RFC7232](https://tools.ietf.org/html/rfc7232)başına cihaz kimliği Için zayıf ETag 'i temsil eden bir dize. |
| 'u |isteğe bağlı |Kimlik doğrulama bilgilerini ve güvenlik malzemelerini içeren bileşik bir nesne. |
| Auth. symkey |isteğe bağlı |Base64 biçiminde depolanan, birincil ve ikincil anahtar içeren bileşik bir nesne. |
| durum |gerekli |Erişim göstergesi. **Etkinleştirilebilir** veya **devre dışı** bırakılabilir. **Etkinleştirilirse**, cihazın bağlanmasına izin verilir. **Devre dışı** bırakılırsa, bu cihaz cihaza yönelik herhangi bir uç noktaya erişemez. |
| statusReason |isteğe bağlı |Cihaz kimliği durumunun nedenini depolayan 128 karakter uzunluğunda bir dize. Tüm UTF-8 karakterlerine izin verilir. |
| statusUpdateTime |salt okunur |Son durum güncelleştirmesinin tarih ve saatini gösteren zamana bağlı bir gösterge. |
| connectionState |salt okunur |Bağlantı durumunu gösteren bir alan: **bağlı** veya **bağlantısı kesildi**. Bu alan, cihaz bağlantı durumunun IoT Hub görünümünü temsil eder. **Önemli**: Bu alan yalnızca geliştirme/hata ayıklama amacıyla kullanılmalıdır. Bağlantı durumu yalnızca MQTT veya AMQP kullanan cihazlar için güncelleştirilir. Ayrıca, protokol düzeyinde pingler (MQTT pingleri veya AMQP pingler) temel alır ve en fazla 5 dakikalık bir gecikme olabilir. Bu nedenlerden dolayı, bağlı olarak bildirilen ancak bağlantısı kesilen cihazlar gibi hatalı pozitif sonuçlar olabilir. |
| connectionStateUpdatedTime |salt okunur |Bağlantı durumunun güncelleştirildiği tarihi ve son saati gösteren zamana bağlı bir gösterge. |
| lastActivityTime |salt okunur |Cihazın bağlandığı, aldığı veya ileti gönderdiği tarihi ve son saati gösteren zamana bağlı bir gösterge. Bu özellik sonunda tutarlıdır, ancak 5 ila 10 dakikaya kadar gecikebilir. Bu nedenle, üretim senaryolarında kullanılmamalıdır. |

> [!NOTE]
> Bağlantı durumu yalnızca bağlantı durumunun IoT Hub görünümünü temsil edebilir. Bu durum için güncelleştirmeler ağ koşullarına ve yapılandırmalara bağlı olarak gecikebilir.

> [!NOTE]
> Şu anda cihaz SDK 'Ları, `+` `#` **DeviceID**'deki ve karakterlerinin kullanımını desteklemez.

## <a name="module-identity-properties"></a>Modül kimliği özellikleri

Modül kimlikleri aşağıdaki özelliklerle JSON belgeleri olarak temsil edilir:

| Özellik | Seçenekler | Açıklama |
| --- | --- | --- |
| deviceId |gerekli, güncelleştirmeler üzerinde salt okunurdur |ASCII 7 bit alfasayısal karakter ve belirli özel karakterler için büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda): `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| Modül kimliği |gerekli, güncelleştirmeler üzerinde salt okunurdur |ASCII 7 bit alfasayısal karakter ve belirli özel karakterler için büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda): `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| GenerationID |gerekli, salt okunurdur |128 karakter uzunluğunda bir IoT Hub 'ı tarafından üretilen, büyük/küçük harfe duyarlı dize. Bu değer, silinen ve yeniden oluşturulduğu zaman, cihazları aynı **DeviceID** ile ayırt etmek için kullanılır. |
| özelliği |gerekli, salt okunurdur |[RFC7232](https://tools.ietf.org/html/rfc7232)başına cihaz kimliği Için zayıf ETag 'i temsil eden bir dize. |
| 'u |isteğe bağlı |Kimlik doğrulama bilgilerini ve güvenlik malzemelerini içeren bileşik bir nesne. |
| Auth. symkey |isteğe bağlı |Base64 biçiminde depolanan, birincil ve ikincil anahtar içeren bileşik bir nesne. |
| durum |gerekli |Erişim göstergesi. **Etkinleştirilebilir** veya **devre dışı** bırakılabilir. **Etkinleştirilirse**, cihazın bağlanmasına izin verilir. **Devre dışı** bırakılırsa, bu cihaz cihaza yönelik herhangi bir uç noktaya erişemez. |
| statusReason |isteğe bağlı |Cihaz kimliği durumunun nedenini depolayan 128 karakter uzunluğunda bir dize. Tüm UTF-8 karakterlerine izin verilir. |
| statusUpdateTime |salt okunur |Son durum güncelleştirmesinin tarih ve saatini gösteren zamana bağlı bir gösterge. |
| connectionState |salt okunur |Bağlantı durumunu gösteren bir alan: **bağlı** veya **bağlantısı kesildi**. Bu alan, cihaz bağlantı durumunun IoT Hub görünümünü temsil eder. **Önemli**: Bu alan yalnızca geliştirme/hata ayıklama amacıyla kullanılmalıdır. Bağlantı durumu yalnızca MQTT veya AMQP kullanan cihazlar için güncelleştirilir. Ayrıca, protokol düzeyinde pingler (MQTT pingleri veya AMQP pingler) temel alır ve en fazla 5 dakikalık bir gecikme olabilir. Bu nedenlerden dolayı, bağlı olarak bildirilen ancak bağlantısı kesilen cihazlar gibi hatalı pozitif sonuçlar olabilir. |
| connectionStateUpdatedTime |salt okunur |Bağlantı durumunun güncelleştirildiği tarihi ve son saati gösteren zamana bağlı bir gösterge. |
| lastActivityTime |salt okunur |Cihazın bağlandığı, aldığı veya ileti gönderdiği tarihi ve son saati gösteren zamana bağlı bir gösterge. |

> [!NOTE]
> Şu anda cihaz SDK 'Ları, `+` `#` **DeviceID** ve **ModuleID** içindeki ve karakterlerinin kullanımını desteklemez.

## <a name="additional-reference-material"></a>Ek başvuru malzemeleri

IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md) , her bir IoT Hub 'ının çalışma zamanı ve yönetim işlemleri için sunduğu çeşitli uç noktaları açıklar.

* [Daraltma ve Kotalar](iot-hub-devguide-quotas-throttling.md) , IoT Hub hizmetine uygulanan kotaları ve azaltma davranışlarını açıklar.

* [Azure IoT cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md) , IoT Hub etkileşimde bulunan cihaz ve hizmet uygulamaları geliştirirken kullanabileceğiniz çeşitli dil SDK 'larını listeler.

* [IoT Hub sorgu dili](iot-hub-devguide-query-language.md) , cihaz WINS ve işleriniz hakkında IoT Hub bilgi almak için kullanabileceğiniz sorgu dilini açıklar.

* [MQTT desteği IoT Hub](iot-hub-mqtt-support.md) MQTT protokolü için IoT Hub desteği hakkında daha fazla bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub Identity kayıt defterini nasıl kullanacağınızı öğrendiğinize göre, aşağıdaki IoT Hub Geliştirici Kılavuzu konularıyla ilgileniyor olabilirsiniz:

* [IoT Hub’a erişimi denetleme](iot-hub-devguide-security.md)

* [Durum ve yapılandırmaların eşitlenmesi için cihaz ikizlerini kullanma](iot-hub-devguide-device-twins.md)

* [Bir cihazda doğrudan yöntem çağırma](iot-hub-devguide-direct-methods.md)

* [Birden fazla cihazda işleri zamanlama](iot-hub-devguide-jobs.md)

Bu makalede açıklanan kavramların bazılarını denemek için aşağıdaki IoT Hub öğreticisine bakın:

* [Azure IoT Hub’ı kullanmaya başlayın](quickstart-send-telemetry-dotnet.md)

Tam zamanında sağlama işlemini etkinleştirmek üzere IoT Hub cihaz sağlama hizmetini kullanarak araştırmak için, bkz.: 

* [Azure IoT Hub Cihazı Sağlama Hizmeti](https://azure.microsoft.com/documentation/services/iot-dps)
