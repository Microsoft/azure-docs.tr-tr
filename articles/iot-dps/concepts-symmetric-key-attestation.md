---
title: Azure IoT Hub cihaz sağlama hizmeti-simetrik anahtar kanıtlama
description: Bu makalede, IoT cihaz sağlama hizmeti kullanılarak simetrik anahtar kanıtlama hakkında kavramsal bir genel bakış sunulmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: b1a849732539dbc9e066bee7cc20141f56ffe10c
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348364"
---
# <a name="symmetric-key-attestation"></a>Simetrik anahtar kanıtlama

Bu makalede, cihaz sağlama hizmeti ile simetrik anahtarlar kullanılırken kimlik kanıtlama süreci açıklanmaktadır. 

Simetrik anahtar kanıtlama, cihaz sağlama hizmeti örneğiyle bir cihazın kimliğini doğrulamaya yönelik basit bir yaklaşımdır. Bu kanıtlama yöntemi, cihaz sağlama için yeni olan veya katı güvenlik gereksinimleri olmayan geliştiriciler için bir "Hello World" deneyimini temsil eder. [TPM](concepts-tpm-attestation.md) veya [X. 509.440 sertifikası](concepts-security.md#x509-certificates) kullanan cihaz kanıtlama daha güvenlidir ve daha sıkı güvenlik gereksinimleri için kullanılmalıdır.

Simetrik anahtar kayıtları Ayrıca, Azure IoT aracılığıyla buluta önyükleme yapmak için sınırlı güvenlik işlevselliğine sahip eski cihazlar için harika bir yol sağlar. Eski cihazlarla simetrik anahtar kanıtı hakkında daha fazla bilgi için bkz. [eski cihazlarla simetrik anahtarlar kullanma](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Simetrik anahtar oluşturma

Varsayılan olarak, cihaz sağlama hizmeti, yeni kayıtlar **otomatik oluşturma anahtarları** seçeneği etkinken varsayılan 32 bayt uzunluğundaki yeni simetrik anahtarlar oluşturur.

![Simetrik anahtarları otomatik oluştur](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Bu seçeneği devre dışı bırakarak kayıtlar için kendi simetrik anahtarlarınızı de sağlayabilirsiniz. Kendi simetrik anahtarlarınızı belirtirken, anahtarlarınızın 16 bayt ve 64 bayt arasında bir anahtar uzunluğu olması gerekir. Ayrıca, simetrik anahtarların geçerli Base64 biçiminde sağlanması gerekir.



## <a name="detailed-attestation-process"></a>Ayrıntılı kanıtlama işlemi

Cihaz sağlama hizmeti ile simetrik anahtar kanıtlama, cihazları tanımlamak için IoT Hub 'ları tarafından desteklenen aynı [güvenlik belirteçleri](../iot-hub/iot-hub-devguide-security.md#security-token-structure) kullanılarak gerçekleştirilir. Bu güvenlik belirteçleri, [paylaşılan erişim imzası (SAS) belirteçleridir](../service-bus-messaging/service-bus-sas.md). 

SAS belirteçlerinin simetrik anahtar kullanılarak oluşturulan karma *imzası* vardır. İmza, kanıtlama sırasında sunulan bir güvenlik belirtecinin gerçek olup olmadığını doğrulamak için cihaz sağlama hizmeti tarafından yeniden oluşturulur.

SAS belirteçleri aşağıdaki biçimdedir:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Her belirtecin bileşenleri şunlardır:

| Value | Açıklama |
| --- | --- |
| imza |HMAC-SHA256 imza dizesi. Bireysel kayıtlar için bu imza, karmayı gerçekleştirmek için simetrik anahtar (birincil veya ikincil) kullanılarak oluşturulur. Kayıt grupları için, karmayı gerçekleştirmek için kayıt grubu anahtarından türetilmiş bir anahtar kullanılır. Karma, Şu biçimdeki bir ileti üzerinde gerçekleştirilir: `URL-encoded-resourceURI + "\n" + expiry`. **Önemli**: HMAC-SHA256 hesaplamayı gerçekleştirmek için kullanılmadan önce anahtar, Base64 olarak çözülebilir olmalıdır. Ayrıca, imza sonucu URL kodlamalı olmalıdır. |
| ResourceURI |Cihaz sağlama hizmeti örneği için kapsam KIMLIĞIYLE başlayarak bu belirteçle erişilebilen kayıt uç noktası URI 'SI. Örneğin, `{Scope ID}/registrations/{Registration ID}` |
| kaç |Süre 00:00:00 UTC 'den bu yana 1 Ocak 1970 ' de geçen saniye sayısı için UTF8 dizeleri. |
| {URL-Encoded-resourceURI} |Küçük harf URL 'SI-küçük harf Kaynak URI 'sinin kodlaması |
| PolicyName |Bu belirtecin başvurduğu paylaşılan erişim ilkesinin adı. Simetrik anahtar kanıtlama ile sağlama sırasında kullanılan ilke adı **kayıt**olur. |

Bir cihaz tek bir kayıtla karşı test edildiğinde, cihaz SAS belirteci için karma imza oluşturmak üzere ayrı kayıt girişinde tanımlanan simetrik anahtarı kullanır.

SAS belirteci oluşturan kod örnekleri için bkz. [güvenlik belirteçleri](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Simetrik anahtar kanıtlama için güvenlik belirteçleri oluşturma, Azure IoT C SDK 'Sı tarafından desteklenir. Tek bir kayıt ile test etmek için Azure IoT C SDK 'sını kullanan bir örnek için bkz. [simetrik anahtarlarla sanal cihaz sağlama](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Grup kayıtları

Grup kayıtlarına yönelik simetrik anahtarlar, sağlama sırasında doğrudan cihazlar tarafından kullanılmaz. Bunun yerine, bir kayıt grubuna ait cihazlar, türetilmiş bir cihaz anahtarı kullanılarak temin sağlar. 

İlk olarak, bir kayıt grubu ile her bir cihaz atsınaması için benzersiz bir kayıt KIMLIĞI tanımlanmıştır. Kayıt KIMLIĞI için geçerli karakterler, küçük harf alfasayısal ve tire ('-'). Bu kayıt KIMLIĞI, cihazı tanımlayan benzersiz bir şeydir. Örneğin, eski bir cihaz birçok güvenlik özelliğini desteklemiyor olabilir. Eski cihazda, bu cihazı benzersiz bir şekilde tanımlamak için kullanılabilecek bir MAC adresi veya seri numarası bulunabilir. Bu durumda, bir kayıt KIMLIĞI aşağıdaki gibi MAC adresi ve seri numarasından oluşabilir:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Bu tam örnek, [simetrik anahtarlar kullanılarak eski cihazlarda sağlama](how-to-legacy-device-symm-key.md) makalesinde kullanılır.

Cihaz için bir kayıt KIMLIĞI tanımlandıktan sonra, kayıt kimliği için simetrik anahtar, bir türetilmiş cihaz anahtarı üretmek için kayıt kimliğinin [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) karmasını hesaplamak üzere kullanılır. Kayıt KIMLIĞI karması aşağıdaki C# kodla gerçekleştirilebilir:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Sonuçta elde edilen cihaz anahtarı, kanıtlama için kullanılacak bir SAS belirteci oluşturmak için kullanılır. Bir kayıt grubundaki her cihaz, benzersiz bir türetilmiş anahtardan oluşturulan bir güvenlik belirtecini kullanarak test etmek için gereklidir. Kayıt grubu simetrik anahtarı kanıtlama için doğrudan kullanılamaz.

#### <a name="installation-of-the-derived-device-key"></a>Türetilmiş cihaz anahtarı yüklemesi

İdeal olarak, cihaz anahtarları fabrikada alınır ve yüklenir. Bu yöntem, grup anahtarını cihaza dağıtılan hiçbir yazılıma hiçbir şekilde dahil etmez. Cihaza bir MAC adresi veya seri numarası atandığında, anahtar türetilebilir ve cihaza eklenebilir, ancak üretici bunu depolamayı seçer.

Her cihaz kayıt KIMLIĞINI grup kayıt anahtarıyla (**K**) karma olarak bir fabrikada oluşturulan cihaz anahtarlarının bir tablosunu gösteren aşağıdaki diyagramı göz önünde bulundurun. 

![Bir fabrikadan atanan cihaz anahtarları](./media/concepts-symmetric-key-attestation/key-diversification.png)

Her bir cihazın kimliği, fabrikada yüklenen kayıt KIMLIĞI ve türetilmiş cihaz anahtarı tarafından temsil edilir. Cihaz anahtarı hiçbir şekilde başka bir konuma kopyalanmaz ve grup anahtarı hiçbir şekilde bir cihazda depolanmaz.

Cihaz anahtarları fabrikada yüklenmemişse, cihaz kimliğini güvenli bir şekilde depolamak için bir [donanım güvenlik modülü HSM](concepts-security.md#hardware-security-module) 'nin kullanılması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Simetrik anahtar kanıtını öğrenmiş olduğunuza göre, daha fazla bilgi edinmek için aşağıdaki makalelere göz atın:

* [Hızlı Başlangıç: Simetrik Anahtarlarla sanal cihaz sağlama](quick-create-simulated-device-symm-key.md)
* [Otomatik sağlama ile ilgili kavramlar hakkında bilgi edinin](./concepts-auto-provisioning.md)
* [Otomatik sağlamayı kullanmaya başlayın](./quick-setup-auto-provision.md) 
