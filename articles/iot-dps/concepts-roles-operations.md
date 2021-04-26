---
title: Cihaz sağlama hizmeti IoT Hub-roller ve işlemler
description: Bu makalede IoT cihaz sağlama hizmeti 'ni (DPS) kullanarak ve IoT çözümü geliştirme sırasında ilgili rollere ve işlemlere yönelik kavramsal bir genel bakış sunulmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 322d6b590863d8065454c0439c5d899107a6abe7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784974"
---
# <a name="roles-and-operations"></a>Roller ve işlemler

Bir IoT çözümünü geliştirme aşamaları, üretim süresi, Sevkiyat, Gümrük süreci vb. gibi üretim gerçekçi nedenlerle hafta veya aya yayılabilir. Bunlara ek olarak, dahil edilen çeşitli varlıkları verilen birçok rol arasında etkinliklere yayılabilir. Bu konu, her aşamayla ilgili çeşitli rol ve işlemlere daha ayrıntılı bir bakış sağlar ve akışı sıralı diyagramda gösterir. 

Sağlama Ayrıca, [kanıtlama mekanizmasını](concepts-service.md#attestation-mechanism)etkinleştirmeye özgü gereksinimleri cihaz üreticisine da koyar. Üretim işlemleri Ayrıca otomatik sağlama aşamaları zamanlamalarından bağımsız olarak, özellikle de otomatik sağlama yapıldıktan sonra yeni cihazların temin olduğu durumlarda de gerçekleşebilir.

Sol taraftaki içindekiler tablosunda, uygulamalı deneyim aracılığıyla otomatik sağlamayı açıklamaya yardımcı olmak üzere bir dizi hızlı başlangıç sağlanır. Öğrenme sürecini kolaylaştırmak/basitleştirmek için, yazılım kayıt ve kayıt için fiziksel bir cihazın benzetimini yapmak üzere kullanılır. Bazı hızlı başlangıçları, hızlı Başlangıçlarının benzetimiyle dolayı, mevcut olmayan roller için işlemler de dahil olmak üzere birden çok rol için işlemleri karşılamanız gerekir.

| Rol | İşlem | Description |
|------| --------- | ------------|
| Üretici | Kimliği ve kayıt URL 'sini kodla | Kullanılan kanıtlama mekanizmasına bağlı olarak, üretici cihaz kimliği bilgilerini ve cihaz sağlama hizmeti kayıt URL 'sini kodlamadan sorumludur.<br><br>**Hızlı başlangıç**: cihaz benzetildiğinden üretici rolü yoktur. Örnek bir kayıt uygulaması kodlamak için kullanılan bu bilgileri nasıl alacağınız hakkında ayrıntılar için geliştirici rolüne bakın. |
| | Cihaz kimliği sağlama | Cihaz kimliği bilgisinin kaynağı olarak, üretici onu işleçle (veya belirlenen bir aracı) iletmekten veya API aracılığıyla cihaz sağlama hizmetine doğrudan kaydetmekten sorumludur.<br><br>**Hızlı başlangıç**: cihaz benzetildiğinden üretici rolü yoktur. Cihaz kimliğini nasıl alacağınız hakkındaki ayrıntılar için bkz. operatör rolü, cihaz sağlama hizmeti Örneğinizde sanal bir cihazı kaydetmek için kullanılır. |
| Operatör | Otomatik sağlamayı yapılandırma | Bu işlem, otomatik sağlama işleminin ilk aşamasına karşılık gelir.<br><br>**Hızlı başlangıç**: Azure aboneliğinizdeki cihaz sağlama hizmetini ve IoT Hub örneklerini yapılandırarak operatör rolünü gerçekleştirirsiniz. |
|  | Cihaz kimliğini kaydet | Bu işlem, otomatik sağlamanın ikinci aşamasına karşılık gelir.<br><br>**Hızlı başlangıç**: sanal cihazınızı cihaz sağlama hizmeti Örneğinizde kaydederek operatör rolünü gerçekleştirirsiniz. Cihaz kimliği, hızlı başlangıçta (TPM veya X. 509.440) benzetilen kanıtlama yöntemi tarafından belirlenir. Kanıtlama ayrıntıları için geliştirici rolüne bakın. |
| Cihaz sağlama hizmeti,<br>IoT Hub | \<all operations\> | Fiziksel cihazlarla bir üretim uygulamasında ve sanal cihazlarla hızlı başlangıçlarda, bu roller Azure aboneliğinizde yapılandırdığınız IoT Hizmetleri aracılığıyla yerine getirilir. IoT Hizmetleri fiziksel ve sanal cihazların sağlanmasından farklı olduğu için roller/işlemler tamamen aynı şekilde çalışır. |
| Geliştirici | Kayıt yazılımı oluşturma/dağıtma | Bu işlem, otomatik sağlamanın üçüncü aşamasına karşılık gelir. Geliştirici, uygun SDK 'yı kullanarak, kayıt yazılımını cihaza oluşturmaktan ve dağıtmaktan sorumludur.<br><br>**Hızlı başlangıç**: oluşturduğunuz örnek kayıt uygulaması, iş istasyonunuzda çalışan seçtiğiniz platform/diliniz için gerçek bir cihaza benzetir (fiziksel bir cihaza dağıtmak yerine). Kayıt uygulaması, fiziksel bir cihaza dağıtılan bir işlem ile aynı işlemleri gerçekleştirir. Kanıtlama yöntemini (TPM veya X. 509.440 sertifikası), Ayrıca cihaz sağlama hizmeti örneğinizin kayıt URL 'sini ve "KIMLIK kapsamını" belirtirsiniz. Cihaz kimliği, belirttiğiniz yönteme göre çalışma zamanında SDK kanıtlama mantığı tarafından belirlenir: <ul><li>**TPM kanıtlama** -geliştirme iş istasyonunuz bir [TPM simülatörü uygulaması](quick-create-simulated-device.md)çalıştırır. Bir kez çalışmaya başladıktan sonra, TPM 'nin "onay anahtarını" ve "kayıt KIMLIĞI" ni kullanarak cihaz kimliğini kaydetme için ayrı bir uygulama kullanılır. SDK kanıtlama mantığı, kimlik doğrulama ve kayıt doğrulama için imzalı bir SAS belirteci sunmak üzere kayıt sırasında simülatörü de kullanır.</li><li>**X509 kanıtlama** - [bir sertifikayı oluşturmak](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)için bir araç kullanın. Oluşturulduktan sonra, kayıt sırasında kullanım için gereken sertifika dosyasını oluşturursunuz. SDK kanıtlama mantığı, kayıt sırasında, kimlik doğrulaması ve kayıt doğrulaması için de sertifikayı kullanır.</li></ul> |
| Cihaz | Önyükleme ve kayıt | Bu işlem, otomatik sağlamanın üçüncü aşamasına karşılık gelir ve geliştirici tarafından oluşturulan cihaz kayıt yazılımı tarafından yerine getirilir. Ayrıntılar için geliştirici rolüne bakın. İlk önyüklemeden sonra: <ol><li>Uygulama, geliştirme sırasında belirtilen genel URL ve "KIMLIK kapsamı" hizmeti başına cihaz sağlama hizmeti örneğiyle bağlanır.</li><li>Bağlandıktan sonra cihaz, kayıt sırasında belirtilen kanıtlama yöntemine ve kimliğe göre doğrulanır.</li><li>Kimlik doğrulandıktan sonra cihaz, sağlama hizmeti örneği tarafından belirtilen IoT Hub örneğine kaydedilir.</li><li>Kayıt başarıyla tamamlandığında, IoT Hub iletişim için kayıt uygulamasına benzersiz bir cihaz KIMLIĞI ve IoT Hub uç noktası döndürülür.</li><li> Bu cihaz buradan, yapılandırma için ilk [cihaz ikizi](~/articles/iot-hub/iot-hub-devguide-device-twins.md) durumunu alabilir ve telemetri verilerini raporlama işlemine başlayabilirsiniz.</li></ol>**Hızlı başlangıç**: cihaz benzetildiğinden, kayıt yazılımı geliştirme iş istasyonunuzda çalışır.|

Aşağıdaki diyagramda cihaz otomatik sağlama sırasında roller ve işlem sıralaması özetlenmektedir:
<br><br>
[![Bir cihaz için otomatik sağlama sırası](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> İsteğe bağlı olarak, üretici cihaz sağlama hizmeti API 'Lerini (Işleci yerine) kullanarak "cihaz kimliğini kaydetme" işlemini de gerçekleştirebilir. Bu sıralama ve daha fazlası hakkında ayrıntılı bir açıklama için bkz. [Azure IoT videosu Ile sıfır Touch cihaz kaydı](https://youtu.be/cSbDRNg72cU?t=2460) (işaret 41:00 ' den başlayarak)

## <a name="roles-and-azure-accounts"></a>Roller ve Azure hesapları

Her rolün bir Azure hesabıyla nasıl eşlendiğine yönelik senaryo bağımlıdır ve söz konusu olabilecek çok sayıda senaryo vardır. Aşağıdaki yaygın desenler, rollerinin bir Azure hesabına genel olarak nasıl eşlendiğine ilişkin genel bir bakış sağlamaya yardımcı olur.

#### <a name="chip-manufacturer-provides-security-services"></a>Yonga üreticisi güvenlik hizmetleri sağlar

Bu senaryoda, üretici, düzey bir müşteriler için güvenliği yönetir. Bu senaryo, ayrıntılı güvenliği yönetmesi gerekmiyorsa bu düzey bir müşteri tarafından tercih edilebilir. 

Üretici donanım güvenlik modüllerine (HSM 'ler) güvenlik sunar. Bu güvenlik, zaten DPS örnekleri ve kayıt grupları kurulumuna sahip olan potansiyel müşterilerden anahtar, sertifika vb. alan üreticisini içerebilir. Üretici, müşterileri için de bu güvenlik bilgilerini oluşturabilir.

Bu senaryoda, iki Azure hesabı söz konusu olabilir:

- **Hesap #1**: büyük olasılıkla operatör ve geliştirici rolleri genelinde bir ölçüde paylaşılıyor. Bu taraf, üreticiden HSM yongaları satın alabilir. Bu yongalar, hesap #1 ilişkili olan DPS örneklerine işaret edilir. DPS kayıtları sayesinde, bu taraf, DPS cihaz kayıt ayarlarını yeniden yapılandırarak, cihazları birden çok düzey iki müşteriye kiralayabilir. Bu taraf Ayrıca, Son Kullanıcı arka uç sistemleri için cihaz telemetrisine erişmek üzere ile arabirim sağlamak için ayrılmış IoT Hub 'ları içerebilir. Bu ikinci durumda ikinci bir hesap gerekmeyebilir.

- **Hesap #2**: son kullanıcılar, düzey iki müşterinin kendi IoT Hub 'ları olabilir. Hesap #1 ilişkili taraf, kiralanan cihazları yalnızca bu hesaptaki doğru hub 'a yönlendirir. Bu yapılandırma, Azure hesapları arasında DPS ve IoT Hub 'larını bağlamayı gerektirir ve bu, Azure Resource Manager şablonlarıyla yapılabilir.

#### <a name="all-in-one-oem"></a>Hepsi bir arada OEM

Üretici, yalnızca tek bir üretici hesabının gerekli olduğu "hepsi bir arada OEM" olabilir. Üretici, güvenlik ve sağlama uçtan uca işleme alır.

Üretici, cihaz satın alan müşterilere bulut tabanlı bir uygulama sağlayabilir. Bu uygulama, üretici tarafından ayrılan IoT Hub ile arabirim oluşturacak.

Havalandırma makineleri veya otomatik kahve makineleri bu senaryoya yönelik örnekleri temsil eder.




## <a name="next-steps"></a>Sonraki adımlar

İlgili otomatik sağlama hızlı başlangıçlarından yola çalıştığınızda, bu makaleye bir başvuru noktası olarak yer işareti eklemek yararlı olabilir. 

"Hizmet yapılandırması" aşamasında size kılavuzluk eden yönetim araç tercihinizi en iyi şekilde karşılayacak "otomatik sağlamayı ayarla" hızlı başlangıcını tamamlayarak başlayın:

- [Azure CLı kullanarak otomatik sağlamayı ayarlama](quick-setup-auto-provision-cli.md)
- [Azure portal kullanarak otomatik sağlamayı ayarlama](quick-setup-auto-provision.md)
- [Kaynak Yöneticisi şablonu kullanarak otomatik sağlamayı ayarlama](quick-setup-auto-provision-rm.md)

Sonra cihaz kanıtlama mekanizmanıza ve cihaz sağlama hizmeti SDK/dil tercihlerinize uygun bir "cihaz sağlama" hızlı başlangıcı ile devam edin. Bu hızlı başlangıçta, "cihaz kaydı" ve "cihaz kaydı ve yapılandırma" aşamaları üzerinden gezintiniz: 

| Cihaz kanıtlama mekanizması | Hızlı başlangıç SDK/dil | 
| ------------------------------- | -------------------- |
| Simetrik anahtar | [,](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| X. 509.440 sertifikası | [,](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Benzetimli Güvenilir Platform Modülü (TPM) | [,](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




