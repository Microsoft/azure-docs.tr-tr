---
title: Azure IoT Hub TLS desteği
description: IoT Hub ile iletişim kuran cihazlar ve hizmetler için güvenli TLS bağlantıları kullanma hakkında bilgi edinin
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: jlian
ms.openlocfilehash: 6a02b97957cc0599e2960cba551b536e83d1a902
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222564"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT Hub 'de Aktarım Katmanı Güvenliği (TLS) desteği

IoT Hub IoT cihazlarından ve hizmetlerinden gelen bağlantıları güvenli hale getirmek için Aktarım Katmanı Güvenliği 'ni (TLS) kullanır. TLS protokolünün üç sürümü şu anda desteklenmektedir. 1,0, 1,1 ve 1,2 sürümleri.

TLS 1,0 ve 1,1 eski olarak değerlendirilir ve kullanımdan kaldırma için planlanmaktadır. Daha fazla bilgi için bkz. [IoT Hub IÇIN TLS 1,0 ve 1,1 kullanımdan](iot-hub-tls-deprecating-1-0-and-1-1.md)kaldırılıyor. Sonraki sorunları önlemek için IoT Hub bağlanırken TLS 1,2 ' i tek TLS sürümü olarak kullanın.

## <a name="iot-hubs-server-tls-certificate"></a>IoT Hub Server TLS sertifikası

Bir TLS anlaşması sırasında, istemcileri bağlamak için RSA anahtarlı sunucu sertifikaları sunar IoT Hub. Kökü, Baltimore Sitrust kök CA 'dır. Son olarak, artık yeni ara sertifika yetkilileri (ICA) tarafından verilmek üzere TLS sunucu sertifikamızda bir değişiklik yaptık. Daha fazla bilgi için bkz. [IoT Hub TLS sertifikası güncelleştirme](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="4kb-size-limit-on-renewal"></a>4 KB yenileme sırasında boyut sınırı

IoT Hub sunucu tarafı sertifikalarının yenilenmesi sırasında, IoT Hub hizmeti tarafında `Server Hello` 4KB 'ın boyutunu aşmamak için bir denetim yapılır. Bir istemcide, gelen TLS en fazla içerik uzunluğu arabelleği için en az 4KB RAM ayarlanmış olmalıdır. bu sayede, 4KB sınırı için ayarlanan mevcut cihazların sertifika Yenilemesindeki şekilde çalışmaya devam etmesi gerekir. Kısıtlanmış cihazlarda IoT Hub, önizleme aşamasında [en fazla TLS parça uzunluğu anlaşmasını](#tls-maximum-fragment-length-negotiation-preview)destekler. 

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Eliptik Eğri Şifreleme (ECC) sunucusu TLS sertifikası (Önizleme)

IoT Hub ECC Server TLS sertifikası genel önizleme için kullanılabilir. RSA sertifikalarına benzer güvenlik sunarken, ECC sertifika doğrulaması (yalnızca ECC şifre paketleriyle birlikte) %40 daha az işlem, bellek ve bant genişliği kullanır. Bu tasarruflar, daha küçük profilleri ve belleği nedeniyle IoT cihazları için önemlidir ve ağ bant genişliği sınırlı ortamlarındaki kullanım durumlarını destekler. 

IoT Hub ECC sunucu sertifikasını önizlemek için:

1. [Önizleme modu üzerinde yeni bir IoT Hub oluşturun](iot-hub-preview-mode.md).
1. [İstemcinizi](#tls-configuration-for-sdk-and-iot-edge) *yalnızca* ECDSA şifre paketleri içerecek şekılde yapılandırın ve tüm RSA paketlerini *hariç tutun* . ECC sertifikası genel önizlemesi için desteklenen şifre paketleri şunlardır:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. İstemcinizi Preview IoT Hub 'ına bağlayın.

## <a name="tls-12-enforcement-available-in-select-regions"></a>TLS 1,2 zorlaması seçim bölgelerinde kullanılabilir

Ek güvenlik için, IoT Hub 'larınızı *yalnızca* TLS sürüm 1,2 kullanan istemci bağlantılarına izin verecek ve [şifre paketlerinin](#cipher-suites)kullanımını zorunlu kılacak şekilde yapılandırın. Bu özellik yalnızca şu bölgelerde desteklenir:

* Doğu ABD
* Orta Güney ABD
* Batı ABD 2
* US Gov Arizona
* US Gov Virginia (TLS 1.0/1.1 desteği bu bölgede kullanılamıyor-TLS 1,2 zorlamasının etkinleştirilmesi veya IoT Hub 'ın oluşturulması başarısız olması gerekir)

TLS 1,2 zorlamayı etkinleştirmek için, [Azure Portal Içinde IoT Hub oluşturma](iot-hub-create-through-portal.md)bölümündeki adımları izleyin, ancak

- Yukarıdaki listeden bir **bölge** seçin.
- **Yönetim > gelişmiş > Aktarım Katmanı Güvenliği (TLS)-> en düşük TLS sürümü**' nün altında **1,2**' u seçin. Bu ayar yalnızca desteklenen bölgede oluşturulan IoT Hub 'ı için görüntülenir.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="IoT Hub 'ı oluşturma sırasında TLS 1,2 zorlaması 'nı nasıl kullanacağınızı gösteren ekran görüntüsü":::

Oluşturma için ARM şablonunu kullanmak için, desteklenen bölgelerin herhangi birinde yeni bir IoT Hub sağlayın ve `minTlsVersion` özelliğini `1.2` kaynak belirtiminde olarak ayarlayın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Bu yapılandırmayı kullanan oluşturulan IoT Hub kaynak, 1,0 ve 1,1 TLS sürümlerini kullanarak bağlanmayı deneyen cihaz ve hizmet istemcilerini reddeder. Benzer şekilde, `ClientHello` ileti [Önerilen şifrelemelerin](#cipher-suites)hiçbirini LISTELEMEZ ise TLS el sıkışması reddedilir.

> [!NOTE]
> `minTlsVersion`Özelliği salt okunurdur ve IoT Hub kaynağınız oluşturulduktan sonra değiştirilemez. Bu nedenle, *Tüm* IoT cihazlarınızın ve hizmetlerinizin TLS 1,2 ile uyumlu olduğunu ve [Önerilen şifrelemeleri](#cipher-suites) önceden test etmek ve doğrulamak önemlidir.
> 
> `minTlsVersion`Yük devretme sonrasında, IoT Hub özelliği, coğrafi olarak eşleştirilmiş bölgenin yük devretmesinde etkili olmaya devam edecektir.

## <a name="cipher-suites"></a>Şifre paketleri

Yalnızca TLS 1,2 kabul edecek şekilde yapılandırılmış IoT Hub 'Ları, aşağıdaki önerilen şifre paketlerinin kullanımını da zorunlu tutar:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1,2 zorlaması için yapılandırılmayan IoT Hub 'Ları için, TLS 1,2, aşağıdaki şifre paketleriyle birlikte hala geçerlidir:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

İstemci, sırasında kullanmak üzere daha yüksek şifre paketlerinin bir listesini önerebilir `ClientHello` . Ancak, bunlardan bazıları IoT Hub (örneğin,) tarafından desteklenmiyor olabilir `ECDHE-ECDSA-AES256-GCM-SHA384` . Bu durumda IoT Hub, istemcinin tercihini izlemeye çalışacaktır, ancak sonunda şifre paketini görüşmelidir `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>SDK ve IoT Edge için TLS yapılandırması

IoT Hub istemci SDK 'lerinde TLS 1,2 ve izin verilen şifrelemeleri yapılandırmak için aşağıdaki bağlantıları kullanın.

| Dil | TLS 1,2 destekleyen sürümler | Belgeler |
|----------|------------------------------------|---------------|
| C        | Etiket 2019-12-11 veya üzeri            | [Bağlantı](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Sürüm 2.0.0 veya daha yeni             | [Bağlantı](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Sürüm 1.21.4 veya daha yeni            | [Bağlantı](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Sürüm 1.19.0 veya daha yeni            | [Bağlantı](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Sürüm 1.12.2 veya daha yeni            | [Bağlantı](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge cihazlar, IoT Hub iletişim kurarken TLS 1,2 kullanacak şekilde yapılandırılabilir. Bu amaçla [IoT Edge belge sayfasını](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)kullanın.

## <a name="device-authentication"></a>Cihaz kimlik doğrulaması

Başarılı bir TLS el sıkışması sonrasında, IoT Hub simetrik anahtar veya X. 509.440 sertifikası kullanarak bir cihazın kimliğini doğrulayabilirler. Sertifika tabanlı kimlik doğrulaması için, bu, ECC dahil olmak üzere herhangi bir X. 509.952 sertifikası olabilir. IoT Hub sertifikayı, sağladığınız parmak izine veya sertifika yetkilisine (CA) göre doğrular. Daha fazla bilgi için bkz. [desteklenen X. 509.440 sertifikaları](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>En fazla TLS parça uzunluğu anlaşması (Önizleme)

IoT Hub Ayrıca, bazen TLS çerçeve boyutu anlaşması olarak bilinen TLS maksimum parça uzunluğu anlaşmasını destekler. Bu özellik genel önizleme aşamasındadır. 

Varsayılan 2 ^ 14 bayttan küçük bir değere en fazla düz metin parça uzunluğu belirtmek için bu özelliği kullanın. Anlaşılırken IoT Hub ve istemci, tüm parçaların anlaşmalı uzunluktan daha küçük olmasını sağlamak için fragmenting iletileri başlatır. Bu davranış, işlem veya bellek kısıtlı cihazlara yardımcı olur. Daha fazla bilgi için bkz. [RESMI TLS uzantısı özelliği](https://tools.ietf.org/html/rfc6066#section-4).

Bu genel önizleme özelliği için resmi SDK desteği henüz kullanılamıyor. Başlamak için

1. [Önizleme modu üzerinde yeni bir IoT Hub oluşturun](iot-hub-preview-mode.md).
1. OpenSSL kullanırken, parça boyutunu belirtmek için [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) çağırın.
1. İstemcinizi önizleme IoT Hub bağlayın.

## <a name="next-steps"></a>Sonraki adımlar

- IoT Hub güvenlik ve erişim denetimi hakkında daha fazla bilgi edinmek için bkz. [IoT Hub erişimi denetleme](iot-hub-devguide-security.md).
- Cihaz kimlik doğrulaması için x509 sertifikası kullanma hakkında daha fazla bilgi için bkz [. X. 509.952 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](iot-hub-x509ca-overview.md)
