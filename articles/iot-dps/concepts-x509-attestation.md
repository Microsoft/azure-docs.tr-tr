---
title: Azure IoT Hub cihaz sağlama hizmeti-X. 509.440 sertifika kanıtlama
description: Cihaz sağlama hizmeti (DPS) ve IoT Hub ile X. 509.440 sertifika kanıtlama kullanımı ile ilgili kavramları açıklar
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9eee315aac28847710662b463add7d6e68d8d505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967304"
---
# <a name="x509-certificate-attestation"></a>X.509 sertifikası kanıtlama

Bu makale, cihazları X. 509.440 sertifika kanıtlama kullanarak sağlarken dahil edilen cihaz sağlama hizmeti (DPS) kavramlarına genel bir bakış sunar. Bu makale, bir cihazı dağıtıma hazırlamayla ilgili olarak tüm personbuna yöneliktir.

X. 509.440 sertifikaları, bir donanım güvenlik modülü HSM 'de depolanabilir.

> [!TIP]
> Üretim aşamasındaki cihazlarınızda bulunan X. 509.440 sertifikası gibi gizli dizileri güvenli bir şekilde depolamak için cihazlarla bir HSM kullanmanız önemle önerilir.


## <a name="x509-certificates"></a>X. 509.440 sertifikaları

Bir kanıtlama mekanizması olarak X. 509.440 sertifikalarını kullanmak, üretimi ölçeklendirmek ve cihaz sağlamayı basitleştirmek için mükemmel bir yoldur. X. 509.440 sertifikaları genellikle, zincirdeki her bir sertifikanın bir sonraki daha yüksek sertifikanın özel anahtarıyla imzalandığı ve bu şekilde otomatik olarak imzalanan bir kök sertifikada sonlandıralındığı bir sertifika güven zinciri halinde düzenlenir. Bu düzenleme, bir cihazda yüklü olan son varlık "yaprak" sertifikasına her bir ara CA aracılığıyla bir güvenilen kök sertifika yetkilisi (CA) tarafından oluşturulan kök sertifikadan temsilci bir güven zinciri oluşturur. Daha fazla bilgi için bkz. [X. 509.440 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](../iot-hub/iot-hub-x509ca-overview.md). 

Genellikle sertifika zinciri cihazlarla ilişkilendirilmiş bazı mantıksal veya fiziksel hiyerarşiyi temsil eder. Örneğin, bir üretici şunları içerebilir:
- otomatik olarak imzalanan kök CA sertifikası verme
- Her bir fabrika için benzersiz bir ara CA sertifikası oluşturmak üzere kök sertifikayı kullanma
- Tesisteki her üretim satırı için benzersiz bir ara CA sertifikası oluşturmak üzere her bir fabrikasının sertifikasını kullanın
- son olarak, satır üzerinde üretilen her bir cihaz için benzersiz bir cihaz (son varlık) sertifikası oluşturmak üzere üretim satırı sertifikasını kullanın. 

Daha fazla bilgi edinmek için bkz. [IoT sektöründe X. 509.440 CA sertifikalarının kavramsal olarak anlaşılmasına](../iot-hub/iot-hub-x509ca-concept.md)bakın. 

### <a name="root-certificate"></a>Kök sertifika

Kök sertifika, bir sertifika yetkilisini (CA) temsil eden kendinden imzalı bir X. 509.440 sertifikasıdır. Sertifika zincirinin sonlandıralım veya güven çıpasıdır. Kök sertifikalar bir kuruluş tarafından kendi kendine verilebilir veya bir kök sertifika yetkilisinden satın alınabilir. Daha fazla bilgi edinmek için bkz. [X. 509.440 CA sertifikalarını alma](../iot-hub/iot-hub-security-x509-get-started.md#get-x509-ca-certificates). Kök sertifika, kök CA sertifikası olarak da adlandırılabilir.

### <a name="intermediate-certificate"></a>Ara sertifika

Ara sertifika, kök sertifika tarafından imzalanmış bir X. 509.440 sertifikasıdır (veya zincirinde kök sertifikaya sahip başka bir ara sertifika tarafından). Bir zincirdeki son ara sertifika, yaprak sertifikayı imzalamak için kullanılır. Ara sertifika, ara CA sertifikası olarak da adlandırılır.

##### <a name="why-are-intermediate-certs-useful"></a>Ara sertifikalar neden kullanışlıdır?
Ara sertifikalar çeşitli şekillerde kullanılır. Örneğin, ara sertifikalar, cihazları ürün hatları, müşterileri satın alan müşteriler, şirket bölümleri veya fabrikalar için gruplamak üzere kullanılabilir. 

Contoso 'nun, *ContosoRootCert* adlı kök sertifikayı kullanarak kendi ortak anahtar ALTYAPıSıNA (PKI) sahip büyük bir kuruluş olduğunu düşünün. Contoso 'nun her bir yan kuruluşu, *ContosoRootCert* tarafından imzalanan kendi ara sertifikasına sahiptir. Böylece her bir yan kuruluş, her bir cihazın yaprak sertifikalarını imzalamak için ara sertifikasını kullanır. Bu senaryoda, contoso, *ContosoRootCert* ile birlikte doğrulanan tek bir DPS örneğini [kullanabilir.](./how-to-verify-certificates.md) Her bir yan kuruluş için bir kayıt grubuna sahip olabilirler. Bu şekilde, her bir yan kuruluş sertifikaların doğrulanması konusunda endişelenmenize gerek kalmaz.


### <a name="end-entity-leaf-certificate"></a>Son varlık "yaprak" sertifikası

Yaprak sertifikası veya son varlık sertifikası, sertifika sahibini tanımlar. Sertifika zincirinde kök sertifikaya ve sıfır veya daha fazla ara sertifikaya sahiptir. Yaprak sertifikası diğer sertifikaları imzalamak için kullanılmaz. Cihazı, sağlama hizmetine benzersiz olarak tanımlar ve bazen cihaz sertifikası olarak adlandırılır. Kimlik doğrulama sırasında cihaz, hizmetten gelen bir itiraz kanıtını yanıtlamak için bu sertifikayla ilişkili özel anahtarı kullanır.

[Tek bir kayıt](./concepts-service.md#individual-enrollment) girişiyle kullanılan yaprak sertifikaların, **konu adının** ayrı kayıt girişinin kayıt kimliğine ayarlanması gerekir. Bir [kayıt grubu](./concepts-service.md#enrollment-group) girdisiyle kullanılan yaprak sertifikaları, kayıt grubundaki kimliği doğrulanmış cihazın **kayıt kayıtlarında** GÖSTERILECEK olan Istenen cihaz kimliğine ayarlanmış **konu adına** sahip olmalıdır.

Daha fazla bilgi edinmek için bkz. [X. 509.440 CA sertifikaları ile imzalanmış cihazların kimliğini doğrulama](../iot-hub/iot-hub-x509ca-overview.md#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>X. 509.440 sertifikalarıyla sağlama hizmetine cihaz erişimini denetleme

Sağlama Hizmeti, X. 509.440 kanıtlama mekanizmasıyla cihaz erişimini denetlemek için kullanabileceğiniz iki kayıt türünü kullanıma sunar:  

- [Ayrı kayıt](./concepts-service.md#individual-enrollment) girdileri, belirli bir cihazla ilişkili cihaz sertifikasıyla yapılandırılır. Bu girişler belirli cihazlar için kayıtları denetler.
- [Kayıt grubu](./concepts-service.md#enrollment-group) girdileri, belirli bir ara veya kök CA sertifikası ile ilişkilendirilir. Bu girişler, kendi sertifika zincirinde ara veya kök sertifikaya sahip tüm cihazların kayıtlarını denetler. 

#### <a name="dps-device-chain-requirements"></a>DPS cihaz zinciri gereksinimleri

Bir cihaz bir kayıt grubu kullanarak DPS 'e kayıt girişiminde bulunduğunda, cihazın yaprak sertifikadan sertifika zincirini, sahip olma [kanıtını](how-to-verify-certificates.md)doğrulayan bir sertifikaya gönderebilmesi gerekir. Aksi takdirde, kimlik doğrulaması başarısız olur.

Örneğin, yalnızca kök sertifika doğrulanırsa ve kayıt grubuna bir ara sertifika yüklenirse, cihazın yaprak sertifikadan sertifika zincirini doğrulanmış kök sertifikaya kadar bir şekilde sunması gerekir. Bu sertifika zinciri, içindeki tüm ara sertifikaları içerir. DPS sertifika zincirini doğrulanmış bir sertifikaya geçemıyorsa kimlik doğrulaması başarısız olur.

Örneğin, bir cihaz için aşağıdaki cihaz zincirini kullanarak bir şirketi düşünün.

![Örnek cihaz sertifika zinciri](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Yalnızca kök sertifika doğrulanır ve *intermediate2* sertifikası kayıt grubuna yüklenir.

![Örnek kök doğrulandı](./media/concepts-x509-attestation/example-root-verified.png) 

Cihaz, sağlama sırasında yalnızca aşağıdaki cihaz zincirini gönderirse, kimlik doğrulaması başarısız olur. DPS, *intermediate1* sertifikasının geçerliliğini varsayarak kimlik doğrulamasını deneyemediği için

![Örnek başarısız sertifika zinciri](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Cihaz, sağlama sırasında tam cihaz zincirini aşağıdaki gibi gönderirse, DPS cihaz kimlik doğrulamasını deneyebilir.

![Örnek cihaz sertifika zinciri](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Ara Sertifikalar [, birlikte bulunan ile de](how-to-verify-certificates.md)doğrulanabilir.


#### <a name="dps-order-of-operations-with-certificates"></a>Sertifikalarla gerçekleştirilen işlemlerin DPS sırası
Bir cihaz sağlama hizmetine bağlanırsa, hizmet daha az özel kayıt girişi üzerinde daha belirli kayıt girdilerine öncelik verir. Diğer bir deyişle, cihaz için tek bir kayıt varsa, sağlama hizmeti bu girişi uygular. Cihaz için tek bir kayıt yoksa ve cihazın sertifika zincirindeki ilk ara sertifika için bir kayıt grubu varsa, hizmet bu girişi uygular, ve bu şekilde, köke olan zinciri kapatır. Hizmet, bulduğu ilk geçerli girişi uygular, örneğin:

- Bulunan ilk kayıt girdisi etkinse hizmet cihazı sağlar.
- Bulunan ilk kayıt girdisi devre dışıysa, hizmet cihazı sağlamaz.  
- Cihazın sertifika zincirindeki herhangi bir sertifika için kayıt girişi bulunmazsa, hizmet cihazı sağlamaz. 

Sertifika zincirlerinin bu mekanizması ve hiyerarşik yapısı, tek tek cihazlar ve cihaz grupları için erişimi nasıl denetleyebileceği konusunda güçlü esneklik sağlar. Örneğin, aşağıdaki sertifika zincirleriyle beş cihaz düşünün: 

- *Cihaz 1*: kök sertifika-> sertifika A > cihaz 1 sertifikası
- *Cihaz 2*: kök sertifika-> sertifika A-> cihaz 2 sertifikası
- *Cihaz 3*: kök sertifika-> sertifika A > cihaz 3 sertifikası
- *Cihaz 4*: kök sertifika-> sertifika B-> cihaz 4 sertifikası
- *Aygıt 5*: kök sertifika-> sertifika B-> cihaz 5 sertifikası

Başlangıçta, tüm beş cihaz için erişimi etkinleştirmek üzere kök sertifika için tek bir etkin grup kayıt girişi oluşturabilirsiniz. Sertifika B daha sonra tehlikeye atılırsa, sertifika B için, *cihaz 4* ve *Cihaz 5* ' in kaydedilmesini engellemek için devre dışı bırakılmış bir kayıt grubu girişi oluşturabilirsiniz. Hala sonraki *Cihaz 3* ' ün güvenliği tehlikeye girerse, sertifikası için devre dışı bırakılmış bir kayıt girişi oluşturabilirsiniz. Bu, *Cihaz 3*' e erişimi iptal eder, ancak hala *cihaz 1* ve *cihaz 2* ' nin kaydetmesine izin verir.