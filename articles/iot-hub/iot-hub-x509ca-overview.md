---
title: Azure IoT Hub X. 509.440 CA güvenliğine genel bakış | Microsoft Docs
description: Genel Bakış-X. 509.440 sertifika yetkililerini kullanarak IoT Hub cihazları kimlik doğrulaması yapma.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150706"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>X.509 CA Sertifikaları ile Cihaz Kimlik Doğrulaması

Bu makalede, IoT Hub bağlanan cihazların kimliğini doğrulamak için X. 509.440 sertifika yetkilisi (CA) sertifikalarının nasıl kullanılacağı açıklanır.  Bu makalede şunları öğreneceksiniz:

* X. 509.440 CA sertifikası alma
* IoT Hub için X. 509.952 CA sertifikasını kaydetme
* X. 509.440 CA sertifikalarını kullanarak cihazları imzalama
* X. 509.440 CA ile imzalanan cihazların kimliği nasıl doğrulanır

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Genel Bakış

X. 509.440 CA özelliği, bir sertifika yetkilisi (CA) kullanarak IoT Hub cihaz kimlik doğrulamasının yapılmasını sağlar. İlk cihaz kayıt sürecini büyük ölçüde basitleştirir ve cihaz üretimi sırasında lojistik zinciri sağlar. [Bu senaryo makalesinde, cihaz kimlik doğrulaması Için X. 509.440 CA sertifikalarının kullanılmasıyla ilgili değer hakkında daha fazla bilgi edinin](iot-hub-x509ca-concept.md) .  Bu senaryo, şu adımların neden olduğunu anlatan devam etmeden önce bu senaryo makalesini okumanızı öneririz.

## <a name="prerequisite"></a>Önkoşul

X. 509.440 CA özelliğinin kullanılması için bir IoT Hub hesabınızın olması gerekir.  Henüz bir [IoT Hub örneği yoksa nasıl oluşturacağınızı öğrenin](quickstart-send-telemetry-dotnet.md) .

## <a name="how-to-get-an-x509-ca-certificate"></a>X. 509.440 CA sertifikası alma

X. 509.440 CA sertifikası, cihazlarınızın her biri için Sertifika zincirinin en üstünde yer aldığı bir sertifikadır.  Bu uygulamayı nasıl kullanmak istediğinize bağlı olarak bir tane satın alabilir veya oluşturabilirsiniz.

Üretim ortamında, bir ortak kök sertifika yetkilisinden bir X. 509.440 CA sertifikası satın almanızı öneririz. CA sertifikasının satın alınması, cihazlarınızın yasallığı için güvenilir bir üçüncü taraf görevi gören kök CA 'nın avantajına sahiptir. Cihazlarınızı, üçüncü taraf ürün veya hizmetleriyle etkileşim kurması beklenen bir açık IoT ağının parçası olarak düşünüyorsanız bu seçeneği göz önünde bulundurun.

Ayrıca, deneme için otomatik olarak imzalanan bir X. 509.440 CA veya kapalı IoT ağlarında kullanım için de oluşturabilirsiniz.

X. 509.952 CA sertifikanızı nasıl edindiğinizden bağımsız olarak, karşılık gelen özel anahtar gizliliğini ve her zaman korunmasını sağlayın.  Bu, X. 509.440 CA kimlik doğrulamasında güven oluşturma güveninde gereklidir.

Bu özellik açıklaması boyunca deneme için kullanabileceğiniz [kendinden imzalı BIR CA sertifikası oluşturmayı](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)öğrenin.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Cihazları sertifika güven zincirinde imzala

X. 509.952 CA sertifikasının sahibi, başka bir ara CA 'yı açan bir ara CA 'yı şifreli olarak imzalayabilir ve bu nedenle son ara CA, bir cihazı imzalayarak bu işlemi sonlandırana kadar devam eder. Sonuç, bir sertifika güven zinciri olarak bilinen, basamaklı bir sertifika zinciridir. Gerçek hayatta bu, imzalama cihazları doğrultusunda güvenin temsili olarak oynatılır. Bu temsilciyi, bir gözetim şifreli olmayan değişken zinciri oluşturduğundan ve imzalama anahtarlarının paylaşılmasını önleyen için önemlidir.

![img-genel-CERT-güven zinciri](./media/generic-cert-chain-of-trust.png)

Cihaz Sertifikası (yaprak sertifikası da denir *),* IoT cihazını Azure IoT Hub kaydederken kullanılan **cihaz kimliğine** () sahip olmalıdır `CN=deviceId` . Bu ayar kimlik doğrulaması için gereklidir.

Cihazları imzalarken [bir sertifika zinciri oluşturma](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) hakkında bilgi edinin.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>IoT Hub için X. 509.952 CA sertifikasını kaydetme

Kayıt ve bağlantı sırasında cihazlarınızın kimliğini doğrulamak için kullanılacak olan IoT Hub X. 509.952 CA sertifikanızı kaydedin.  X. 509.440 CA sertifikasını kaydetmek, sertifika dosyası yükleme ve sahip olma kanıtını içeren iki adımlı bir işlemdir.

Karşıya yükleme işlemi, sertifikanızı içeren bir dosyayı karşıya yüklemeyi gerektirir.  Bu dosya asla hiçbir özel anahtar içermemelidir.

Elinde bulunan adım kanıtı, siz ve IoT Hub arasında bir şifreleme sınaması ve yanıt işlemi içerir.  Bu, dijital sertifika içeriklerinin genel olduğu ve bu nedenle gizlice dinleyen saldırılarına açık olduğu için IoT Hub CA sertifikasına gerçekten sahip olduğunuz konusunda emin olmak istiyor.  CA sertifikasının karşılık gelen özel anahtarıyla imzalamanız gereken rastgele bir zorluk oluşturarak bunu yapması gerekir.  Özel anahtar gizli anahtarını koruduysanız ve daha önce önerdikleri gibi korunuyorsa, yalnızca siz bu adımı tamamlamaya yönelik bilgilere sahip olursunuz. Özel anahtarların gizliliği, bu yöntemdeki güvenin kaynağıdır.  Sınamayı imzaladıktan sonra, sonuçları içeren bir dosyayı karşıya yükleyerek bu adımı izleyin.

[CA sertifikanızı nasıl kaydedeceğinizi](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) öğrenin

## <a name="how-to-create-a-device-on-iot-hub"></a>IoT Hub cihaz oluşturma

Cihaz kimliğe bürünmesi için IoT Hub, hangi cihazların beklendiğini bilmesini gerektirir.  Bunu, IoT Hub cihaz kayıt defterinde bir cihaz girişi oluşturarak yapabilirsiniz.  Bu işlem, IoT Hub [cihaz sağlama hizmeti](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)kullanılırken otomatikleştirilir. 

[IoT Hub içinde el ile cihaz oluşturma](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)hakkında bilgi edinin.

IoT Hub 'ınız için bir X. 509.952 cihazı oluşturma

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>X. 509.440 CA sertifikalarıyla imzalanan cihazların kimliğini doğrulama

X. 509.952 CA sertifikası kayıtlı ve bir sertifika güven zincirinde oturum açan cihazlar ile, ilk kez bile cihaz bağlandığı zaman cihaz kimlik doğrulaması olur.  X. 509.952 CA imzalı bir cihaz bağlanırsa, doğrulama için sertifika zincirini karşıya yükler. Zincir tüm ara CA ve cihaz sertifikalarını içerir.  Bu bilgilerle, IoT Hub iki adımlı bir işlemde cihazın kimliğini doğrular.  IoT Hub şifreleme, iç tutarlılık için sertifika zincirini doğrular ve ardından cihaza bir çok itiraz zorluğu yayınlar.  IoT Hub, cihazı başarılı bir şekilde cihazdan güvenilir bir yanıt sağlaması üzerinde bildirir.  Bu bildirim, cihazın özel anahtarının korunduğunu ve yalnızca cihazın bu zorluk başarıyla yanıt verebildiğini varsayar.  Özel anahtarları korumak için cihazlarda donanım güvenli modüller (HSM) gibi güvenli yongalarının kullanılmasını öneririz.

IoT Hub başarılı bir cihaz bağlantısı, kimlik doğrulama sürecini tamamlar ve ayrıca uygun bir kurulumu da kapsar.

[Bu cihaz bağlantısı adımını nasıl tamamlayacağınızı](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)öğrenin.

## <a name="next-steps"></a>Sonraki Adımlar

IoT 'de [X. 509.440 CA kimlik doğrulamasının değeri](iot-hub-x509ca-concept.md) hakkında bilgi edinin.

IoT Hub [cihaz sağlama hizmeti](../iot-dps/index.yml)'ni kullanmaya başlayın.