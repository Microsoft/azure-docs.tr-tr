---
title: Azure IoT Hub güvenliğini anlama | Microsoft Docs
description: Geliştirici Kılavuzu-cihaz uygulamaları ve arka uç uygulamaları için IoT Hub erişimi denetleme. X. 509.440 sertifikalarına yönelik güvenlik belirteçleri ve desteğiyle ilgili bilgiler içerir.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: fa1aa8c560f4b9cc48c7a6a761abe4d69d5d0265
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773166"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub’a erişimi denetleme

Bu makalede IoT Hub 'ınızı güvenli hale getirme seçenekleri açıklanmaktadır. IoT Hub her bir IoT Hub uç noktasına erişim vermek için *izinleri* kullanır. İzinler, işlevleri temel alarak bir IoT Hub 'ına erişimi sınırlar.

Bu makale şunları tanıtır:

* IoT Hub 'ınıza erişmek için bir cihaza veya arka uç uygulamasına vereceğiniz farklı izinler.
* Kimlik doğrulama işlemi ve izinleri doğrulamak için kullandığı belirteçler.
* Belirli kaynaklarla erişimi sınırlandırmak için kimlik bilgilerini kapsam.
* X. 509.440 sertifikaları için IoT Hub desteği.
* Mevcut cihaz kimliği kayıt defterleri veya kimlik doğrulama düzenlerini kullanan özel cihaz kimlik doğrulama mekanizmaları.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub uç noktalarından birine erişmek için uygun izinlere sahip olmanız gerekir. Örneğin, bir cihaz IoT Hub gönderdiği her iletiyle birlikte güvenlik kimlik bilgilerini içeren bir belirteç içermelidir.

## <a name="access-control-and-permissions"></a>Erişim denetimi ve izinler

Aşağıdaki yollarla [izin](#iot-hub-permissions) verebilirsiniz:

* **IoT Hub düzeyi paylaşılan erişim ilkeleri**. Paylaşılan erişim ilkeleri, [izinlerin](#iot-hub-permissions)herhangi bir birleşimini verebilir. [Azure Portal](https://portal.azure.com)ilkeleri, [IoT Hub kaynak REST API 'lerini](/rest/api/iothub/iothubresource)kullanarak veya [az IoT Hub ilkesi](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI kullanarak belirleyebilirsiniz. Yeni oluşturulan bir IoT Hub 'ı aşağıdaki varsayılan ilkelere sahiptir:
  
  | Paylaşılan Erişim İlkesi | İzinler |
  | -------------------- | ----------- |
  | iothubowner | Tüm izinler |
  | hizmet | **Serviceconnect** izinleri |
  | cihaz | **Deviceconnect** izinleri |
  | registryRead | **Registryread** izinleri |
  | registryReadWrite | **Registryread** ve **registrywrite** izinleri |

* **Cihaz başına güvenlik kimlik bilgileri**. Her bir IoT Hub, bu kimlik kayıt defterindeki her bir cihaz için bir [kimlik kayıt defteri](iot-hub-devguide-identity-registry.md) içerir, ilgili cihaz uç noktaları kapsamındaki **deviceconnect** izinleri veren güvenlik kimlik bilgilerini yapılandırabilirsiniz.

Örneğin, tipik bir IoT çözümünde:

* Cihaz Yönetimi bileşeni *Registryreadwrite* ilkesini kullanır.
* Olay işlemcisi bileşeni, *hizmet* ilkesini kullanır.
* Çalışma zamanı cihazı iş mantığı bileşeni, *hizmet* ilkesini kullanır.
* Ayrı cihazlar IoT Hub 'ının kimlik kayıt defterinde depolanan kimlik bilgilerini kullanarak bağlanır.

> [!NOTE]
> Ayrıntılı bilgi için bkz. [izinler](#iot-hub-permissions) .

## <a name="authentication"></a>Authentication

Azure IoT Hub, paylaşılan erişim ilkelerine ve kimlik kayıt defteri güvenlik kimlik bilgilerine karşı bir belirteci doğrulayarak uç noktalara erişim izni verir.

Simetrik anahtarlar gibi güvenlik kimlik bilgileri hiçbir şekilde kablo üzerinden gönderilmez.

> [!NOTE]
> Azure IoT Hub kaynak sağlayıcısı, [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)tüm sağlayıcılar gibi Azure aboneliğiniz aracılığıyla güvenli hale getirilir.

Güvenlik belirteçleri oluşturma ve kullanma hakkında daha fazla bilgi için bkz. [IoT Hub güvenlik belirteçleri](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokol özellikleri

MQTT, AMQP ve HTTPS gibi desteklenen her protokol, belirteçleri farklı şekillerde aktarır.

MQTT kullanılırken, Connect paketi, Kullanıcı adı alanında ClientID `{iothubhostname}/{deviceId}` ve Password alanındaki bir SAS belirteci olarak DeviceID 'yi içerir. `{iothubhostname}`IoT Hub 'ının tam CName 'i olmalıdır (örneğin, contoso.azure-devices.net).

[AMQP](https://www.amqp.org/)kullanırken, IoT Hub [SASL PLAIN](https://tools.ietf.org/html/rfc4616) ve [AMQP talepler tabanlı güvenliği](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)destekler.

AMQP talepler tabanlı güvenlik kullanıyorsanız, standart bu belirteçlerin nasıl iletilyapılacağını belirtir.

SASL PLAIN için **Kullanıcı adı** şu olabilir:

* `{policyName}@sas.root.{iothubName}`IoT Hub düzeyi belirteçleri kullanıyorsanız.
* `{deviceId}@sas.{iothubname}`cihaz kapsamlı belirteçler kullanılıyorsa.

Her iki durumda da, parola alanı [IoT Hub güvenlik belirteçleri](iot-hub-devguide-security.md#security-tokens)bölümünde açıklandığı gibi belirteci içerir.

HTTPS, **Yetkilendirme** isteği üstbilgisine geçerli bir belirteç ekleyerek kimlik doğrulamasını uygular.

#### <a name="example"></a>Örnek

Kullanıcı adı (DeviceID, büyük/küçük harfe duyarlıdır):`iothubname.azure-devices.net/DeviceId`

Parola ( [cihaz Gezgini](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ARACıYLA bir SAS belirteci OLUŞTURABILIRSINIZ; CLI uzantısı komutu [az IoT Hub Generate-SAS-Token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)veya [Visual Studio Code için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) , hizmete bağlanırken belirteçleri otomatik olarak oluşturur. Bazı durumlarda, Azure IoT SDK 'Ları tüm protokolleri veya tüm kimlik doğrulama yöntemlerini desteklemez.

### <a name="special-considerations-for-sasl-plain"></a>SASL PLAIN için özel konular

AMQP ile SASL PLAIN kullanırken, IoT Hub 'ına bağlanan bir istemci her TCP bağlantısı için tek bir belirteç kullanabilir. Belirtecin süresi dolarsa, TCP bağlantısı hizmetten bağlantıyı keser ve bir yeniden bağlantı tetikler. Bu davranış, bir arka uç uygulaması için sorunlu olmasa da bir cihaz uygulaması için aşağıdaki nedenlerden dolayı zararlıdır:

* Ağ geçitleri genellikle birçok cihaz adına bağlanır. SASL PLAIN kullanırken, IoT Hub 'ına bağlanan her cihaz için ayrı bir TCP bağlantısı oluşturulması gerekir. Bu senaryo, güç ve ağ kaynaklarının tüketimini önemli ölçüde artırır ve her bir cihaz bağlantısının gecikmesini artırır.

* Kaynak kısıtlı cihazlar, her belirtecin süresi dolduktan sonra yeniden bağlanmak için kaynakların arttığı artışdan olumsuz etkilenir.

## <a name="scope-iot-hub-level-credentials"></a>Kapsam IoT Hub düzeyi kimlik bilgileri

Kısıtlanmış bir kaynak URI 'SI ile belirteçler oluşturarak IoT Hub düzeyi güvenlik ilkelerinin kapsamını belirleyebilirsiniz. Örneğin, cihazdan buluta iletiler gönderen uç nokta **/Devices/{deviceid}/ileti/Events**olur. Ayrıca, resourceURI **/Devices/{deviceid}** olan bir belirteci Imzalamak Için **Deviceconnect** izinleriyle IoT Hub düzeyi paylaşılan erişim ilkesi de kullanabilirsiniz. Bu yaklaşım yalnızca cihaz **DeviceID**adına ileti göndermek için kullanılabilen bir belirteç oluşturur.

Bu mekanizma [Event Hubs yayımcı ilkesine](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)benzer ve özel kimlik doğrulama yöntemleri uygulamanıza olanak sağlar.

## <a name="security-tokens"></a>Güvenlik belirteçleri

IoT Hub, cihazlarda anahtar gönderilmesini önlemek için cihazların ve hizmetlerin kimliğini doğrulamak üzere güvenlik belirteçlerini kullanır. Ayrıca, güvenlik belirteçleri zaman geçerliliği ve kapsamında sınırlıdır. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) özel yapılandırma gerektirmeden belirteçleri otomatik olarak oluşturur. Bazı senaryolarda güvenlik belirteçlerini doğrudan oluşturup kullanmanız gerekir. Bu tür senaryolar şunlardır:

* MQTT, AMQP veya HTTPS yüzeylerinin doğrudan kullanımı.

* [Özel cihaz kimlik doğrulaması](iot-hub-devguide-security.md#custom-device-and-module-authentication)bölümünde açıklandığı gibi, belirteç hizmeti deseninin uygulanması.

IoT Hub Ayrıca cihazların [X. 509.440 sertifikalarını](iot-hub-devguide-security.md#supported-x509-certificates)kullanarak IoT Hub kimlik doğrulamasına olanak tanır.

### <a name="security-token-structure"></a>Güvenlik belirteci yapısı

IoT Hub ' deki belirli işlevlere cihazlara ve hizmetlere zamana sınırlı erişim vermek için güvenlik belirteçlerini kullanırsınız. IoT Hub bağlanma yetkisini almak için, cihazlar ve hizmetler, paylaşılan erişim veya simetrik anahtarla imzalanmış güvenlik belirteçleri göndermelidir. Bu anahtarlar kimlik kayıt defterinde bir cihaz kimliğiyle birlikte depolanır.

Paylaşılan erişim anahtarı ile imzalanmış bir belirteç, paylaşılan erişim ilkesi izinleriyle ilişkili tüm işlevlere erişim verir. Bir cihaz kimliğinin simetrik anahtarıyla imzalanan bir belirteç yalnızca ilişkili cihaz kimliği için **Deviceconnect** izni verir.

Güvenlik belirtecinin biçimi aşağıdaki biçimdedir:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Beklenen değerler şunlardır:

| Value | Açıklama |
| --- | --- |
| imza |Şu biçimdeki bir HMAC-SHA256 imza dizesi: `{URL-encoded-resourceURI} + "\n" + expiry`. **Önemli**: Anahtarın Base64 olarak kodu çözülür ve HMAC-SHA256 hesaplamayı gerçekleştirmek için anahtar olarak kullanılır. |
| ResourceURI |Bu belirteçle erişilebilen bitiş noktalarının, IoT Hub 'ın ana bilgisayar adı (protokol yok) ile başlayan URI ön eki (segmente göre). Örneğin, `myHub.azure-devices.net/devices/device1` |
| kaç |Süre 00:00:00 UTC 'den bu yana 1 Ocak 1970 ' de geçen saniye sayısı için UTF8 dizeleri. |
| {URL-Encoded-resourceURI} |Küçük harf URL 'SI-küçük harf Kaynak URI 'sinin kodlaması |
| PolicyName |Bu belirtecin başvurduğu paylaşılan erişim ilkesinin adı. Belirteç, cihaz kayıt defteri kimlik bilgilerine başvuruyorsa yok. |

**Ön eke göz önüne**dön: URI öneki, karakteriyle değil, segmente göre hesaplanır. Örneğin `/a/b` , için bir `/a/b/c` ön `/a/bc`ektir.

Aşağıdaki Node. js kod parçacığında, girdilerden belirteç `resourceUri, signingKey, policyName, expiresInMins`hesaplayan **generatesastoken** adlı bir işlev gösterilmektedir. Sonraki bölümlerde farklı belirteç kullanım durumları için farklı girişlerin nasıl başlatılacağını ayrıntılı olarak anlatılmaktadır.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Bir karşılaştırma olarak, bir güvenlik belirteci oluşturmak için eşdeğer Python kodu şunlardır:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Önkoşullar için yükleme yönergeleri aşağıda verilmiştir.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


Güvenlik belirteci oluşturmak C# için içindeki işlevleri şunlardır:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Belirtecin zaman geçerliliği IoT Hub makinelerde doğrulandıktan sonra, belirteci üreten makinenin saatinin en az olması gerekir.

### <a name="use-sas-tokens-in-a-device-app"></a>Bir cihaz uygulamasında SAS belirteçlerini kullanma

Güvenlik belirteçleriyle IoT Hub **Deviceconnect** izinleri almanın iki yolu vardır: [kimlik kayıt defterinden simetrik bir cihaz anahtarı](#use-a-symmetric-key-in-the-identity-registry)kullanın veya [paylaşılan erişim anahtarı](#use-a-shared-access-policy)kullanın.

Cihazlardan erişilebilen tüm işlevlerin, ön `/devices/{deviceId}`uçlarla uç noktalar üzerinde tasarlanarak sunulduğunu unutmayın.

> [!IMPORTANT]
> IoT Hub belirli bir cihazın kimlik doğrulamasını yapmanın tek yolu, cihaz kimliği simetrik anahtar kullanmaktır. Cihaz işlevselliğine erişmek için paylaşılan erişim ilkesi kullanıldığında, çözüm güvenlik belirtecini güvenilen bir alt bileşen olarak veren bileşeni göz önünde bulundurmalıdır.

Cihaza yönelik uç noktalar (Protokolden bağımsız olarak) vardır:

| Uç Nokta | İşlevi |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Cihazdan buluta iletiler gönderme. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Buluttan cihaza iletileri alın. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Kimlik kayıt defterinde bir simetrik anahtar kullanın

Bir belirteç oluşturmak için bir cihaz kimliğinin simetrik anahtarı kullanılırken, belirtecin PolicyName (`skn`) öğesi atlanır.

Örneğin, tüm cihaz işlevselliğine erişmek için oluşturulan bir belirteç aşağıdaki parametrelere sahip olmalıdır:

* Kaynak URI 'SI `{IoT hub name}.azure-devices.net/devices/{device id}`:,
* imzalama anahtarı: `{device id}` kimlik için herhangi bir simetrik anahtar,
* ilke adı yok,
* herhangi bir süre sonu süresi.

Önceki Node. js işlevini kullanan bir örnek şöyle olabilir:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Device1 için tüm işlevlere erişim izni veren sonuç şöyle olacaktır:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> [Cihaz Gezgini](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ARACıYLA bir SAS belirteci oluşturmak mümkündür; CLI uzantısı komutu [az IoT Hub Generate-SAS-Token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)veya [Visual Studio Code için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Paylaşılan erişim ilkesi kullanma

Paylaşılan erişim ilkesinden bir belirteç oluşturduğunuzda, `skn` alanı ilkenin adı olarak ayarlayın. Bu ilke **Deviceconnect** izni vermelidir.

Cihaz işlevselliğine erişmek için paylaşılan erişim ilkeleri kullanmanın iki ana senaryosu şunlardır:

* [bulut protokolü ağ geçitleri](iot-hub-devguide-endpoints.md),
* özel kimlik doğrulama düzenlerini uygulamak için kullanılan [belirteç Hizmetleri](iot-hub-devguide-security.md#custom-device-and-module-authentication) .

Paylaşılan erişim ilkesi potansiyel olarak herhangi bir cihaz olarak bağlanma erişimi sağlayabileceği için, güvenlik belirteçleri oluştururken doğru kaynak URI 'sinin kullanılması önemlidir. Bu ayar özellikle belirteç Hizmetleri için önemlidir, bu da belirteç Kaynak URI 'sini kullanarak belirli bir cihaza kapsam içermelidir. Bu nokta, tüm cihazlarda trafiği zaten destekledikleri için protokol ağ geçitleri için daha az uygundur.

Örnek olarak, **cihaz** adlı önceden oluşturulmuş paylaşılan erişim ilkesini kullanan bir belirteç hizmeti, aşağıdaki parametrelerle bir belirteç oluşturur:

* Kaynak URI 'SI `{IoT hub name}.azure-devices.net/devices/{device id}`:,
* imzalama anahtarı: `device` ilkenin anahtarlarından biri,
* ilke adı: `device`,
* herhangi bir süre sonu süresi.

Önceki Node. js işlevini kullanan bir örnek şöyle olabilir:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Device1 için tüm işlevlere erişim izni veren sonuç şöyle olacaktır:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Bir protokol ağ geçidi, tüm cihazlar için aynı belirteci kullanarak yalnızca kaynak URI 'sini olarak `myhub.azure-devices.net/devices`ayarlar.

### <a name="use-security-tokens-from-service-components"></a>Hizmet bileşenlerinden güvenlik belirteçlerini kullanma

Hizmet bileşenleri yalnızca daha önce açıklandığı gibi, gerekli izinleri veren paylaşılan erişim ilkeleri kullanılarak güvenlik belirteçleri oluşturabilir.

Uç noktalarda sunulan hizmet işlevleri aşağıda verilmiştir:

| Uç Nokta | İşlevi |
| --- | --- |
| `{iot hub host name}/devices` |Cihaz kimliklerini oluşturun, güncelleştirin, alın ve silin. |
| `{iot hub host name}/messages/events` |Cihazdan buluta iletileri alma. |
| `{iot hub host name}/servicebound/feedback` |Buluttan cihaza iletiler için geri bildirim alın. |
| `{iot hub host name}/devicebound` |Buluttan cihaza iletileri gönderme. |

Örnek olarak, **Registryread** adlı önceden oluşturulmuş paylaşılan erişim ilkesi kullanılarak oluşturulan bir hizmet, aşağıdaki parametrelerle bir belirteç oluşturur:

* Kaynak URI 'SI `{IoT hub name}.azure-devices.net/devices`:,
* imzalama anahtarı: `registryRead` ilkenin anahtarlarından biri,
* ilke adı: `registryRead`,
* herhangi bir süre sonu süresi.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Tüm cihaz kimliklerini okumak için erişim izni veren sonuç şöyle olacaktır:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Desteklenen X. 509.440 sertifikaları

Bir sertifika parmak izini veya bir sertifika yetkilisini (CA) Azure IoT Hub karşıya yükleyerek IoT Hub bir cihazın kimliğini doğrulamak için herhangi bir X. 509.952 sertifikası kullanabilirsiniz. Sertifika parmak izleri kullanılarak kimlik doğrulaması yalnızca gösterilen parmak izinin yapılandırılan parmak izine eşleştiğini doğrular. Sertifika yetkilisini kullanarak kimlik doğrulaması, sertifika zincirini doğrular. 

Desteklenen sertifikalar şunlardır:

* **Var olan bir X. 509.440 sertifikası**. Cihazda ilişkili bir X. 509.440 sertifikası zaten olabilir. Cihaz, IoT Hub kimlik doğrulaması yapmak için bu sertifikayı kullanabilir. Parmak izi veya CA kimlik doğrulamasıyla birlikte geçerlidir. 

* **CA-Imzalanmış X. 509.440 sertifikası**. Bir cihazı tanımlamak ve IoT Hub kimlik doğrulaması yapmak için, bir sertifika yetkilisi (CA) tarafından oluşturulan ve imzalanan bir X. 509.440 sertifikası kullanabilirsiniz. Parmak izi veya CA kimlik doğrulamasıyla birlikte geçerlidir.

* **Kendi kendine oluşturulmuş ve otomatik olarak Imzalanan bir X-509 sertifikası**. Bir cihaz üreticisi veya şirket içi dağıtıcı, bu sertifikaları oluşturabilir ve buna karşılık gelen özel anahtarı (ve sertifikayı) cihaza depolayabilirler. Bu amaçla [OpenSSL](https://www.openssl.org/) ve [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) yardımcı programı gibi araçları kullanabilirsiniz. Yalnızca parmak izi kimlik doğrulamasıyla birlikte kullanılabilir. 

Bir cihaz, kimlik doğrulaması için bir X. 509.440 sertifikası veya güvenlik belirteci kullanabilir, ancak ikisini birden kullanamazsınız.

Sertifika yetkilisini kullanarak kimlik doğrulaması hakkında daha fazla bilgi için bkz. [X. 509.440 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Bir cihaza bir X. 509.440 sertifikası kaydetme

[İçin C# Azure IoT hizmeti SDK 'sı](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (sürüm 1.0.8 +), kimlik doğrulaması için bir X. 509.440 sertifikası kullanan bir cihazın kaydedilmesini destekler. Cihazların içeri/dışarı aktarılması gibi diğer API 'Ler de X. 509.440 sertifikalarını destekler.

Cihazlarda X. 509.440 sertifikalarını yapılandırmak için [az IoT Hub Device-ıDENTITY](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) CLI uzantı komutunu da kullanabilirsiniz.

### <a name="c-support"></a>C\# desteği

**Registrymanager** sınıfı, bir cihazı kaydetmek için programlı bir yol sağlar. Özellikle, **Adddeviceasync** ve **updatedeviceasync** yöntemleri, bir cihazı IoT Hub Identity kayıt defterine kaydetmenizi ve güncelleştirmenizi sağlar. Bu iki yöntem bir **cihaz** örneğini giriş olarak alır. **Cihaz** sınıfı, birincil ve ikincil X. 509.952 sertifikası parmak izlerini belirtmenizi sağlayan bir **kimlik doğrulama** özelliği içerir. Parmak izi, X. 509.440 sertifikasının bir SHA256 karmasını temsil eder (ikili DER kodlaması kullanılarak depolanır). Birincil parmak izi veya ikincil bir parmak izi ya da her ikisi belirtme seçeneğiniz vardır. Birincil ve ikincil parmak izleri, sertifika geçişi senaryolarını işlemek için desteklenir.

X. 509.440 sertifika parmak\# izi kullanarak bir cihazı kaydetmek için örnek C kod parçacığı aşağıda verilmiştir:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Çalışma zamanı işlemleri sırasında bir X. 509.440 sertifikası kullanın

[.Net Için Azure IoT cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (sürüm 1.0.11 +), X. 509.440 sertifikalarının kullanımını destekler.

### <a name="c-support"></a>C\# desteği

**DeviceAuthenticationWithX509Certificate** sınıfı, bir X. 509.440 sertifikası kullanarak **deviceclient** örneklerinin oluşturulmasını destekler. X. 509.440 sertifikası, özel anahtarı içeren PFX 'de (PKCS #12 olarak da adlandırılır) olmalıdır.

Örnek kod parçacığı aşağıda verilmiştir:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Özel cihaz ve modül kimlik doğrulaması

[Belirteç](iot-hub-devguide-security.md#security-tokens)kullanarak cihaz başına/modül güvenlik kimlik bilgilerini ve erişim denetimini yapılandırmak için IoT Hub [kimlik kayıt defterini](iot-hub-devguide-identity-registry.md) kullanabilirsiniz. Bir IoT çözümünde zaten özel kimlik kayıt defteri ve/veya kimlik doğrulama düzeni varsa, bu altyapıyı IoT Hub ile bütünleştirmek için bir *belirteç hizmeti* oluşturmayı düşünün. Bu şekilde, çözümünüzde diğer IoT özelliklerini kullanabilirsiniz.

Belirteç hizmeti özel bir bulut hizmetidir. *Cihaz kapsamlı* veya *Modül kapsamlı* belirteçler oluşturmak Için **deviceconnect** veya **moduleconnect** izinleriyle IoT Hub *paylaşılan erişim ilkesi* kullanır. Bu belirteçler, bir cihazın ve modülün IoT Hub 'ınıza bağlanmasını sağlar.

![Belirteç hizmeti deseninin adımları](./media/iot-hub-devguide-security/tokenservice.png)

Belirteç hizmeti deseninin ana adımları şunlardır:

1. IoT Hub 'ınız için **deviceconnect** veya **moduleconnect** izinleriyle IoT Hub paylaşılan erişim ilkesi oluşturun. Bu ilkeyi [Azure Portal](https://portal.azure.com) veya program aracılığıyla oluşturabilirsiniz. Belirteç hizmeti, oluşturduğu belirteçleri imzalamak için bu ilkeyi kullanır.

2. Bir cihaz/modülün IoT Hub 'ınıza erişmesi gerektiğinde, belirteç hizmetinizden imzalı bir belirteç ister. Cihaz, belirteç oluşturmak için kullanılan cihaz/modül kimliğini belirleyebilmek için özel kimlik kayıt defteriniz/kimlik doğrulama şemanızdaki kimlik doğrulaması yapabilir.

3. Belirteç hizmeti bir belirteç döndürür. `/devices/{deviceId}` Belirteç, veya `/devices/{deviceId}/module/{moduleId}` olarak `resourceURI`kullanılarak oluşturulur, `deviceId` veya `moduleId` kimliği doğrulanmış modül olarak. Belirteç hizmeti, belirteci oluşturmak için paylaşılan erişim ilkesini kullanır.

4. Cihaz/modül, doğrudan IoT Hub ile belirtecini kullanır.

> [!NOTE]
> Belirteç hizmetinizde bir belirteç oluşturmak için [Sharedaccesssignaturebuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) veya Java Class [Iothubservicesastoken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) .NET sınıfını kullanabilirsiniz.

Belirteç hizmeti, belirteç süre sonunu istendiği gibi ayarlayabilir. Belirtecin süresi sona erdiğinde, IoT Hub 'ı cihaz/modül bağlantısını mühürler. Ardından, cihaz/modülün belirteç hizmetinden yeni bir belirteç istemesi gerekir. Kısa süre sonu zamanı hem cihaz/modül hem de belirteç hizmeti üzerindeki yükü artırır.

Hub 'ınıza bağlanmak üzere bir cihaz/modül için, bağlantı kurmak için bir anahtar olmasa bile, onu IoT Hub Identity kayıt defterine eklemeniz gerekir. Bu nedenle, [kimlik kayıt defterinde](iot-hub-devguide-identity-registry.md)cihaz/modül kimliklerini etkinleştirerek veya devre dışı bırakarak cihaz başına/modül başına erişim denetimini kullanmaya devam edebilirsiniz. Bu yaklaşım uzun süre sonu süreleriyle belirteçleri kullanmanın risklerini azaltır.

### <a name="comparison-with-a-custom-gateway"></a>Özel bir ağ geçidiyle karşılaştırma

Belirteç hizmeti düzeni, IoT Hub ile özel kimlik kayıt defteri/kimlik doğrulama düzeni uygulamak için önerilen yoldur. IoT Hub çözüm trafiğinin çoğunu işlemeye devam ettiğinden bu model önerilir. Ancak, özel kimlik doğrulama düzeni protokolle intertwined, tüm trafiği işlemek için *özel bir ağ geçidine* ihtiyacınız olabilir. Bu tür bir senaryoya örnek olarak [Aktarım Katmanı Güvenliği (TLS) ve önceden paylaşılan anahtarlar (PSKs)](https://tools.ietf.org/html/rfc4279)kullanılıyor. Daha fazla bilgi için bkz. [protokol ağ geçidi](iot-hub-protocol-gateway.md) makalesi.

## <a name="reference-topics"></a>Başvuru konuları:

Aşağıdaki başvuru konuları, IoT Hub 'ınıza erişimi denetleme hakkında daha fazla bilgi sağlar.

## <a name="iot-hub-permissions"></a>IoT Hub izinleri

Aşağıdaki tabloda, IoT Hub 'ınıza erişimi denetlemek için kullanabileceğiniz izinler listelenmektedir.

| İzin | Notlar |
| --- | --- |
| **RegistryRead** |Kimlik kayıt defterine okuma erişimi verir. Daha fazla bilgi için bkz. [kimlik kayıt defteri](iot-hub-devguide-identity-registry.md). <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **RegistryReadWrite** |Kimlik kayıt defterine okuma ve yazma erişimi verir. Daha fazla bilgi için bkz. [kimlik kayıt defteri](iot-hub-devguide-identity-registry.md). <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **ServiceConnect** |Bulut hizmetine yönelik iletişim ve izleme uç noktalarına erişim izni verir. <br/>Cihazdan buluta iletileri alma, buluttan cihaza ileti gönderme ve ilgili teslim bildirimleri alma izni verir. <br/>Karşıya dosya yükleme için teslim bildirimleri alma izni verir. <br/>Etiketleri ve istenen özellikleri güncelleştirmek, bildirilen özellikleri almak ve sorguları çalıştırmak için TWINS 'e erişme izni verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **DeviceConnect** |Cihaza yönelik uç noktalara erişim izni verir. <br/>Cihazdan buluta iletiler gönderme ve buluttan cihaza iletileri alma izni verir. <br/>Bir cihazdan karşıya dosya yükleme gerçekleştirme izni verir. <br/>Cihaz ikizi istenen özellik bildirimlerini alma ve cihaz ikizi bildirilen özelliklerini güncelleştirme izni verir. <br/>Karşıya dosya yükleme gerçekleştirme izni verir. <br/>Bu izin cihazlar tarafından kullanılır. |

## <a name="additional-reference-material"></a>Ek başvuru malzemeleri

IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md) , her bir IoT Hub 'ının çalışma zamanı ve yönetim işlemleri için sunduğu çeşitli uç noktaları açıklar.

* [Daraltma ve Kotalar](iot-hub-devguide-quotas-throttling.md) , IoT Hub hizmetine uygulanan kotaları ve azaltma davranışlarını açıklar.

* [Azure IoT cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md) , IoT Hub etkileşimde bulunan cihaz ve hizmet uygulamaları geliştirirken kullanabileceğiniz çeşitli dil SDK 'larını listeler.

* [IoT Hub sorgu dili](iot-hub-devguide-query-language.md) , cihaz WINS ve işleriniz hakkında IoT Hub bilgi almak için kullanabileceğiniz sorgu dilini açıklar.

* [MQTT desteği IoT Hub](iot-hub-mqtt-support.md) MQTT protokolü için IoT Hub desteği hakkında daha fazla bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Erişimi IoT Hub nasıl denetleceğini öğrendiğinize göre, aşağıdaki IoT Hub Geliştirici Kılavuzu konularıyla karşılaşabilirsiniz:

* [Durum ve yapılandırmaların eşitlenmesi için cihaz ikizlerini kullanma](iot-hub-devguide-device-twins.md)
* [Bir cihazda doğrudan yöntem çağırma](iot-hub-devguide-direct-methods.md)
* [Birden fazla cihazda işleri zamanlama](iot-hub-devguide-jobs.md)

Bu makalede açıklanan kavramların bazılarını denemek istiyorsanız aşağıdaki IoT Hub öğreticilerine bakın:

* [Azure IoT Hub’ı kullanmaya başlayın](quickstart-send-telemetry-node.md)
* [IoT Hub ile buluttan cihaza ileti gönderme](iot-hub-csharp-csharp-c2d.md)
* [Cihazdan buluta iletileri IoT Hub işleme](tutorial-routing.md)
