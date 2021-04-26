---
title: IoT cihaz sağlama hizmeti 'nde güvenlik uç noktaları | Microsoft Docs
description: Kavramlar-arka uç uygulamaları için IoT cihaz sağlama hizmeti 'ne (DPS) erişimi denetleme. Güvenlik belirteçleri hakkında bilgi içerir.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954333"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Cihazı Sağlama Hizmeti’ne erişimi denetleme

Bu makalede IoT cihaz sağlama hizmetinizi güvenli hale getirme seçenekleri açıklanmaktadır. Sağlama Hizmeti, her uç noktaya erişim vermek için *izinleri* kullanır. İzinler, işlevselliği temel alarak bir hizmet örneğine erişimi sınırlar.

Bu makalede şunları açıklanmaktadır:

* Bir arka uç uygulamasına, sağlama hizmetinize erişmek için vereceğiniz farklı izinler.
* Kimlik doğrulama işlemi ve izinleri doğrulamak için kullandığı belirteçler.

### <a name="when-to-use"></a>Kullanılması gereken durumlar

Sağlama Hizmeti uç noktalarına erişmek için uygun izinlere sahip olmanız gerekir. Örneğin, bir arka uç uygulaması, hizmete gönderdiği her iletiyle birlikte güvenlik kimlik bilgilerini içeren bir belirteç içermelidir.

## <a name="access-control-and-permissions"></a>Erişim denetimi ve izinler

Aşağıdaki yollarla [izin](#device-provisioning-service-permissions) verebilirsiniz:

* **Paylaşılan erişim yetkilendirme ilkeleri**. Paylaşılan erişim ilkeleri, [izinlerin](#device-provisioning-service-permissions)herhangi bir birleşimini verebilir. [Azure Portal][lnk-management-portal]ilkeleri tanımlayabilir veya [CIHAZ sağlama hizmeti REST API 'lerini][lnk-resource-provider-apis]kullanarak programlı bir şekilde kullanabilirsiniz. Yeni oluşturulan bir sağlama hizmeti aşağıdaki varsayılan ilkeye sahiptir:

* **provisioningserviceowner**: tüm izinlerle ilke.

> [!NOTE]
> Ayrıntılı bilgi için bkz. [izinler](#device-provisioning-service-permissions) .

## <a name="authentication"></a>Kimlik Doğrulaması

Azure IoT Hub cihaz sağlama hizmeti, paylaşılan erişim ilkelerine karşı bir belirteci doğrulayarak uç noktalara erişim izni verir. Simetrik anahtarlar gibi güvenlik kimlik bilgileri hiçbir şekilde kablo üzerinden gönderilmez.

> [!NOTE]
> Cihaz sağlama hizmeti kaynak sağlayıcısı, [Azure Resource Manager][lnk-azure-resource-manager]tüm sağlayıcılar gibi Azure aboneliğiniz aracılığıyla güvenli hale getirilir.

Güvenlik belirteçleri oluşturma ve kullanma hakkında daha fazla bilgi için sonraki bölüme bakın.

HTTP desteklenen tek protokoldür ve **Yetkilendirme** isteği üstbilgisine geçerli bir belirteç ekleyerek kimlik doğrulamasını uygular.

#### <a name="example"></a>Örnek
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT cihaz sağlama hizmeti SDK 'ları][lnk-sdks] , hizmete bağlanırken belirteçleri otomatik olarak oluşturur.

## <a name="security-tokens"></a>Güvenlik belirteçleri

Cihaz sağlama hizmeti, kablolu anahtar göndermekten kaçınmak için hizmetlerin kimliğini doğrulamak üzere güvenlik belirteçlerini kullanır. Ayrıca, güvenlik belirteçleri zaman geçerliliği ve kapsamında sınırlıdır. [Azure IoT cihaz sağlama hizmeti SDK 'ları][lnk-sdks] özel yapılandırma gerektirmeden belirteçleri otomatik olarak oluşturur. Bazı senaryolarda güvenlik belirteçlerini doğrudan oluşturup kullanmanız gerekir. Bu tür senaryolar, HTTP yüzeyinin doğrudan kullanımını içerir.

### <a name="security-token-structure"></a>Güvenlik belirteci yapısı

IoT cihaz sağlama hizmeti 'nde hizmetler için zamana sınırlı erişim sağlamak üzere güvenlik belirteçlerini kullanırsınız. Sağlama hizmetine bağlanma yetkilendirmesi almak için, hizmetler paylaşılan erişim veya simetrik anahtarla imzalanmış güvenlik belirteçleri göndermelidir.

Paylaşılan erişim anahtarı ile imzalanmış bir belirteç, paylaşılan erişim ilkesi izinleriyle ilişkili tüm işlevlere erişim verir. 

Güvenlik belirtecinin biçimi aşağıdaki biçimdedir:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Beklenen değerler şunlardır:

| Değer | Açıklama |
| --- | --- |
| imza |Şu biçimdeki bir HMAC-SHA256 imza dizesi: `{URL-encoded-resourceURI} + "\n" + expiry` . **Önemli**: anahtarın Base64 olarak kodu çözülür ve HMAC-SHA256 hesaplamayı gerçekleştirmek için anahtar olarak kullanılır.|
| kaç |Süre 00:00:00 UTC 'den bu yana 1 Ocak 1970 ' de geçen saniye sayısı için UTF8 dizeleri. |
| {URL-Encoded-resourceURI} | Küçük harf URL 'SI-küçük harf Kaynak URI 'sinin kodlaması. IoT cihaz sağlama hizmeti 'nin (protokol yok) ana bilgisayar adıyla başlayarak, bu belirteçle erişilebilen uç noktaların URI ön eki (kesimine göre). Örneğin, `mydps.azure-devices-provisioning.net`. |
| PolicyName |Bu belirtecin başvurduğu paylaşılan erişim ilkesinin adı. |

**Ön eke Not**: URI öneki, karakterle değil, segmente göre hesaplanır. Örneğin `/a/b` , için bir ön ektir `/a/b/c` `/a/bc` .

Aşağıdaki Node.js kod parçacığında, girdilerden belirteç hesaplayan **Generatesastoken** adlı bir işlev gösterilmektedir `resourceUri, signingKey, policyName, expiresInMins` . Sonraki bölümlerde farklı belirteç kullanım durumları için farklı girişlerin nasıl başlatılacağını ayrıntılı olarak anlatılmaktadır.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Bir karşılaştırma olarak, bir güvenlik belirteci oluşturmak için eşdeğer Python kodu şunlardır:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> IoT cihaz sağlama hizmeti makinelerinde belirtecin zaman geçerliliği doğrulandıktan sonra, belirteci üreten makinenin saatinin en az olması gerekir.

### <a name="use-security-tokens-from-service-components"></a>Hizmet bileşenlerinden güvenlik belirteçlerini kullanma

Hizmet bileşenleri yalnızca daha önce açıklandığı gibi, gerekli izinleri veren paylaşılan erişim ilkeleri kullanılarak güvenlik belirteçleri oluşturabilir.

Uç noktalarda sunulan hizmet işlevleri aşağıda verilmiştir:

| Uç Nokta | İşlev |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Cihaz sağlama hizmeti ile cihaz kayıt işlemleri sağlar. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Cihaz kayıt gruplarını yönetmeye yönelik işlemler sağlar. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Cihaz kayıtlarının durumunu almak ve yönetmek için işlemler sağlar. |


Örnek olarak, kayıtları **kayıtoku** adlı önceden oluşturulmuş bir paylaşılan erişim ilkesi kullanılarak oluşturulan bir hizmet, aşağıdaki parametrelerle bir belirteç oluşturur:

* Kaynak URI 'SI: `{mydps}.azure-devices-provisioning.net` ,
* imzalama anahtarı: ilkenin anahtarlarından biri `enrollmentread` ,
* ilke adı: `enrollmentread` ,
* herhangi bir süre sonu süresi. backn

![Portalda cihaz sağlama hizmeti örneğiniz için bir paylaşılan erişim ilkesi oluşturma][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Tüm kayıt kayıtlarını okumak için erişim izni veren sonuç şöyle olacaktır:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Başvuru konuları:

Aşağıdaki başvuru konuları, IoT cihazı sağlama hizmetinize erişimi denetleme hakkında daha fazla bilgi sağlar.

### <a name="device-provisioning-service-permissions"></a>Cihaz sağlama hizmeti izinleri

Aşağıdaki tabloda, IoT cihazı sağlama hizmetinize erişimi denetlemek için kullanabileceğiniz izinler listelenmektedir.

| İzin | Notlar |
| --- | --- |
| **ServiceConfig** |Hizmet yapılandırmasını değiştirme erişimi verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **Kayıtları oku** |Cihaz kayıtları ve kayıt gruplarına okuma erişimi verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **EnrollmentWrite** |Cihaz kayıtları ve kayıt gruplarına yazma erişimi verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **RegistrationStatusRead** |Cihaz kayıt durumuna okuma erişimi verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |
| **RegistrationStatusWrite**  |Cihaz kayıt durumuna silme erişimi verir. <br/>Bu izin, arka uç bulut Hizmetleri tarafından kullanılır. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/