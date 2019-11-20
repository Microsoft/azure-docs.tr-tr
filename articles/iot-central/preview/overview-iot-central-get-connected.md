---
title: Azure IoT Central cihaz bağlantısı | Microsoft Docs
description: Bu makalede, Azure IoT Central cihaz bağlantısıyla ilgili temel kavramlar tanıtılmaktadır
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ef0e4c9070733266349a37e863c48901eae90c16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894597"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>Azure IoT Central 'a bağlanın (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede Microsoft Azure IoT Central cihaz bağlantısıyla ilgili temel kavramlar tanıtılmaktadır.

Azure IoT Central, tüm cihaz kayıt ve bağlantılarını yönetmek için [azure IoT Hub cihaz sağlama hizmeti 'ni (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) kullanır.

DPS kullanılması şunları sunar:

- Cihazları ölçeklendirmeye ekleme ve bağlamayı desteklemek için IoT Central.
- Cihazları IoT Central kullanıcı arabirimi aracılığıyla kaydetmeden cihaz kimlik bilgilerini oluşturup cihazları çevrimdışı olarak yapılandırabilirsiniz.
- Paylaşılan erişim imzaları (SAS) kullanılarak bağlanacak cihazlar.
- Endüstri standardı X. 509.440 sertifikalarını kullanarak bağlanacak cihazlar.
- IoT Central cihazları kaydetmek için kendi cihaz kimliklerinizi kullanmanız gerekir. Kendi cihaz kimliklerinizin kullanılması, mevcut arka ofis sistemleriyle tümleştirmeyi basitleştirir.
- Cihazları IoT Central bağlamak için tek ve tutarlı bir yol.

Bu makalede aşağıdaki kullanım durumları açıklanmaktadır:

- [SAS kullanarak tek bir cihazı hızlı bir şekilde bağlama](#connect-a-single-device)
- [SAS kullanarak cihazları ölçeklendirmeye bağlama](#connect-devices-at-scale-using-sas)
- [X. 509.440 sertifikalarını kullanarak cihazları ölçeklendirmeye bağlayın](#connect-devices-using-x509-certificates) bu, üretim ortamları için önerilen yaklaşımdır.
- [Önce cihazları kaydetmeden Bağlan](#connect-without-registering-devices)
- [IoT Tak ve Kullan özelliklerini kullanarak cihazları bağlama](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Tek bir cihazı bağlama

Bu yaklaşım, IoT Central veya test cihazlarıyla denemeler yaparken faydalıdır. Cihaz sağlama hizmeti 'ni (DPS) kullanarak bir cihazı IoT Central uygulamanıza bağlamak için IoT Central uygulamanızdaki cihaz bağlantı bilgilerini kullanabilirsiniz. Aşağıdaki diller için örnek DPS cihaz istemci kodunu bulabilirsiniz:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>SAS kullanarak cihazları ölçeklendirmeye bağlama

Cihazları SAS kullanarak ölçeği IoT Central 'e bağlamak için, cihazları kaydetmeniz ve ayarlamanız gerekir:

### <a name="register-devices-in-bulk"></a>Cihazları toplu olarak kaydetme

IoT Central uygulamanızla çok sayıda cihazı kaydetmek için, [cihaz kimliklerini ve cihaz adlarını içeri aktarmak](howto-manage-devices.md#import-devices)üzere bir CSV dosyası kullanın.

İçeri aktarılan cihazların bağlantı bilgilerini almak için [IoT Central uygulamanızdan BIR CSV dosyası dışarı aktarın](howto-manage-devices.md#export-devices).

> [!NOTE]
> Cihazları öncelikle IoT Central kaydetmeden nasıl bağlayabileceğinizi öğrenmek için, bkz. [önce cihazları kaydetmeden Bağlan](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Cihazlarınızı ayarlama

Cihazlarınızı IoT Central uygulamanıza bağlamak ve IoT 'e veri göndermek üzere cihaz kodunuzda dışarı aktarma dosyasından bağlantı bilgilerini kullanın. Cihazları bağlama hakkında daha fazla bilgi için bkz. [sonraki adımlar](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>X. 509.440 sertifikalarını kullanarak cihazları bağlama

Bir üretim ortamında, X. 509.440 sertifikalarının kullanılması, IoT Central için önerilen cihaz kimlik doğrulama mekanizmasıdır. Daha fazla bilgi için bkz. [X. 509.440 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](../../iot-hub/iot-hub-x509ca-overview.md).

Aşağıdaki adımlarda, X. 509.440 sertifikaları kullanarak cihazların IoT Central nasıl bağlanacağı açıklanır:

1. IoT Central uygulamanızda cihaz sertifikaları oluşturmak için kullandığınız _Ara veya kök X. 509.440 sertifikasını ekleyin ve doğrulayın_ :

    - **Yönetim > cihaz bağlantısı > Sertifikalar (X. 509.440)** sayfasına gidin ve yaprak cihaz sertifikalarını oluşturmak Için kullandığınız x. 509.440 root veya ara sertifika ekleyin.

      ![Bağlantı ayarları](media/overview-iot-central-get-connected/connection-settings.png)

      Bir güvenlik ihlali varsa veya birincil sertifikanız süresi dolacak şekilde ayarlandıysa, kapalı kalma süresini azaltmak için ikincil sertifikayı kullanın. Birincil sertifikayı güncelleştirdiğinizde ikincil sertifikayı kullanarak cihaz sağlamaya devam edebilirsiniz.

    - Sertifika sahipliğinin doğrulanması, sertifikanın yükleyici 'nin sertifikanın özel anahtarına sahip olmasını sağlar. Sertifikayı doğrulamak için:
        - Kod oluşturmak için **doğrulama kodu** ' nun yanındaki düğmeyi seçin.
        - Önceki adımda oluşturduğunuz doğrulama koduyla bir X. 509.952 doğrulama sertifikası oluşturun. Sertifikayı bir. cer dosyası olarak kaydedin.
        - İmzalı doğrulama sertifikasını karşıya yükleyin ve **Doğrula**' yı seçin.

          ![Bağlantı ayarları](media/overview-iot-central-get-connected/verify-cert.png)

1. IoT Central uygulamanızda _cihazları içeri ve dışarı aktarmak_ IÇIN bir CSV dosyası kullanın.

1. _Cihazlarınızı ayarlayın._ Karşıya yüklenen kök sertifikayı kullanarak yaprak sertifikaları oluşturun. Yaprak sertifikalarındaki CNAME değeri olarak **CIHAZ kimliğini** kullanın. Cihaz KIMLIĞI küçük harf olmalıdır. Ardından cihazları sağlama hizmeti bilgileriyle programlayabilirsiniz. İlk için bir cihaz açıldığında, DPS IoT Central uygulamanızın bağlantı bilgilerini alır.

### <a name="further-reference"></a>Daha fazla başvuru

- RaspberryPi için örnek uygulama [.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [C 'de örnek cihaz istemcisi.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Yalnızca test amaçlı

Yalnızca test için bu yardımcı programları, CA sertifikaları ve cihaz sertifikaları oluşturmak için kullanabilirsiniz.

- Bir DevKit cihazı kullanıyorsanız, bu [komut satırı aracı](https://aka.ms/iotcentral-docs-dicetool) , sertifikaları doğrulamak için IoT Central uygulamanıza EKLEYEBILECEĞINIZ bir CA sertifikası oluşturur.

- Bu [komut satırı aracını](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) kullanarak şunları yapın:
  - Bir sertifika zinciri oluşturun. GitHub makalesindeki 2. adımı izleyin.
  - IoT Central uygulamanıza yüklemek için sertifikaları. cer dosyası olarak kaydedin.
  - Doğrulama sertifikasını oluşturmak için IoT Central uygulamasındaki doğrulama kodunu kullanın. GitHub makalesindeki 3. adımı izleyin.
  - Cihaz kimliklerinizi araç için bir parametre olarak kullanarak cihazlarınız için yaprak sertifikaları oluşturun. GitHub makalesindeki 4. adımı izleyin.

## <a name="connect-without-registering-devices"></a>Cihazları kaydettirmeden Bağlan

IoT Central temel bir senaryo, OEM 'Lerin, önce kaydolmadan bir IoT Central uygulamasına bağlanabilecek cihazları toplu olarak üretmesine yöneliktir. Üretici, uygun kimlik bilgilerini üretmeli ve fabrikadaki cihazları yapılandırmalıdır. Bir cihaz ilk kez açıldığında bir IoT Central uygulamasına otomatik olarak bağlanır. Bir IoT Central operatörü, verileri göndermeden önce cihazı onaylamalıdır.

Aşağıdaki diyagramda bu akış özetlenmektedir:

![Bağlantı ayarları](media/overview-iot-central-get-connected/device-connection-flow1.png)

Aşağıdaki adımlarda bu işlem daha ayrıntılı olarak açıklanır. Bu adımlar, cihaz kimlik doğrulaması için SAS veya X. 509.440 sertifikaları kullanıyor olmanıza bağlı olarak biraz farklılık gösterir:

1. Bağlantı ayarlarınızı yapılandırın:

    - **X. 509.440 sertifikaları:** [kök/ara sertifikayı ekleyin ve doğrulayın](#connect-devices-using-x509-certificates) ve aşağıdaki adımda cihaz sertifikalarını oluşturmak için kullanın.
    - **SAS:** Birincil anahtarı kopyalayın. Bu anahtar, IoT Central uygulaması için Grup SAS anahtarıdır. Aşağıdaki adımda cihaz SAS anahtarlarını oluşturmak için anahtarı kullanın.
    ![bağlantı ayarları SAS](media/overview-iot-central-get-connected/connection-settings-sas.png)

1. Cihaz kimlik bilgilerinizi oluşturma
    - **Sertifikalar X. 509.440:** IoT Central uygulamanıza eklediğiniz kök veya ara sertifikayı kullanarak cihazlarınız için yaprak sertifikaları oluşturun. Alt durum **CIHAZ kimliğini** , yaprak sertifikalarda CNAME olarak kullandığınızdan emin olun. Yalnızca test amaçlı olarak, cihaz sertifikaları oluşturmak için bu [komut satırı aracını](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) kullanın.
    - **SAS:** Cihaz SAS anahtarları oluşturmak için bu [komut satırı aracını](https://www.npmjs.com/package/dps-keygen) kullanın. Önceki adımda grup **birincil anahtarını** kullanın. Cihaz KIMLIĞI küçük harf olmalıdır.

      [Anahtar Oluşturucu yardımcı programını](https://github.com/Azure/dps-keygen)yüklemek için şu komutu çalıştırın:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Grup SAS birincil anahtarından bir cihaz anahtarı oluşturmak için aşağıdaki komutu çalıştırın:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Cihazlarınızı ayarlamak için, her cihazı **kapsam kimliği**, **cihaz kimliği**ve **X. 509.440 cihaz sertifikası** veya **SAS anahtarı**ile birlikte Flash yapın.

1. Sonra, IoT Central uygulamanıza bağlanmak için cihazı açın. Bir cihaza geçtiğinizde, ilk olarak IoT Central kayıt bilgilerini almak için DPS 'e bağlanır.

1. Bağlı cihaz başlangıçta **cihazlar** sayfasında **ilişkilendirilmemiş** olarak görüntülenir. Cihaz sağlama durumu **kaydedilir**. Cihazı uygun cihaz şablonuna **geçirin** ve IoT Central uygulamanıza bağlanmak için cihazı onaylayın. Cihaz daha sonra IoT Hub bir bağlantı dizesi alabilir ve veri göndermeye başlayabilir. Cihaz sağlama artık tamamlanmıştır ve sağlama durumu artık **sağlandı**.

## <a name="individual-enrollment-based-device-connectivity"></a>Ayrı kayıt tabanlı cihaz bağlantısı

Her cihaz için ayrı kayıt kimlik doğrulama kimlik bilgileri olan cihazları bağlayan müşteriler için bu seçenek vardır. Tek bir kayıt, bağlanabilme tek bir cihaz için giriştir. Bireysel kayıtlar, kanıtlama mekanizması olarak X. 509.440 yaprak sertifikalarını veya SAS belirteçlerini (fiziksel veya sanal TPM 'den) kullanabilir. Tek bir kayıtta bulunan cihaz KIMLIĞI (diğer adıyla kayıt KIMLIĞI) alfasayısal, küçük harf ve kısa çizgi içerebilir. [Burada](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)bireysel kayıtlar hakkında daha fazla bilgi edinin.

> [!NOTE]
> Bir cihaz için tek bir kayıt oluşturduğunuzda, uygulamanızda varsayılan grup kayıt tabanlı belirlediğimizi karşıladığımızı (SAS, x509) üzerinden öncelik kazanır.

### <a name="creating-individual-enrollments"></a>Bireysel kayıtlar oluşturma
IoT Central aşağıdaki kanıtlama mekanizmalarını destekler

1. **Simetrik anahtar kanıtlama:** Simetrik anahtar kanıtlama, cihaz sağlama hizmeti örneğiyle bir cihazın kimliğini doğrulamaya yönelik basit bir yaklaşımdır. Simetrik Anahtarlarla bireysel bir kayıt oluşturmak için; Bağlan iletişim kutusunu açın, bireysel kayıt ve mekanizma "SAS" i seçin ve birincil ve Ikincil anahtarları girin. SAS anahtarları Base64 kodlamalı olmalıdır. Bu, simetrik anahtarlar ve bireysel kayıtlar kullanarak cihaz sağlamak üzere cihaz kodunuzu yazmaya yardımcı olmak için kod örneklerine [bağlantı](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) sağlar.
1. **X. 509.440 sertifikaları:** X. 509.440 sertifikalarında başlık, üretimi ölçeklendirmenin harika bir yolu olan sertifika tabanlı bir kanıtlama mekanizmasıdır. Simetrik Anahtarlarla bireysel kayıt oluşturmak için "X. 509.440" bireysel kayıt ve mekanizmasını seçin ve birincil ve ikincil sertifikaları karşıya yükleyin ve kaydı oluşturmak için kaydedin. Bu, x509 kullanarak cihazları sağlamak üzere cihaz kodunuzu yazmaya yardımcı olmak için kod örneklerine [bağlantı](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) sağlar. [Tek bir kayıt](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) girişiyle kullanılan cihaz sertifikalarının, konu adının tek tek kayıt GIRIŞININ cihaz kimliğine (diğer ADıYLA kayıt kimliği) ayarlanmış olması gerekir.
1. **TPM kanıtlama:** TPM Güvenilir Platform Modülü anlamına gelir ve bir tür donanım güvenlik modülü (HSM) ve cihazlarınızı bağlamak için en güvenli yöntemlerle biridir.  Bu makalede ayrı, bellenim veya tümleşik TPM kullandığınız varsayılır. Yazılım öykünmesi, prototip oluşturma veya test etme için uygundur, ancak ayrık, bellenim veya tümleşik TPM 'Ler ile aynı güvenlik düzeyini sağlamalardır. Üretimde yazılım TPMs kullanılması önerilmez. Simetrik Anahtarlarla bireysel kayıt oluşturmak için bireysel kayıt ve "TPM" mekanizmasını seçin ve kayıt oluşturmak için onay anahtarlarını girin. TPMs türleri hakkında daha fazla bilgi için [buradan](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)TPM kanıtlama hakkında daha fazla bilgi edinebilirsiniz. TPM kullanarak cihazları sağlamak üzere cihaz kodunuzu yazmaya yardımcı olması için kod örneklerine [bağlantı](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) aşağıda verilmiştir. TPM tabanlı kanıtlama oluşturmak için, onay anahtarını yazın ve kaydedin.

## <a name="connect-devices-with-iot-plug-and-play"></a>IoT Tak ve Kullan cihazları bağlama

IoT Tak ve Kullan 'in temel özelliklerinden biri IoT Central cihaz bağlantısı üzerinde cihaz şablonlarını otomatik olarak ilişkilendirebilme özelliğidir. Cihaz kimlik bilgileri ile birlikte cihazlar artık, cihaz kaydı çağrısının bir parçası olarak **Capabilitymodelıd** 'yi gönderebilir ve IoT Central cihaz şablonunu bulup ilişkilendirebilir. Bulma işlemi aşağıdaki sırayı izler:

1. IoT Central uygulamasında zaten yayımlandıysa cihaz şablonuyla ilişkilendirir.
1. Yayınlanan ve sertifikalı yetenek modellerinin ortak deposundan getirir.

Aşağıda, cihazın DPS kayıt araması sırasında gönderileceği ek yükün biçimi verilmiştir

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Cihazların otomatik olarak bağlanması, modeli bulması ve veri göndermeye başlaması için otomatik onaylama seçeneğinin etkinleştirilmesi gerektiğini unutmayın.

## <a name="device-status"></a>Cihaz durumu

Gerçek bir cihaz IoT Central uygulamasına bağlanırsa, cihaz durumu aşağıdaki gibi değişir:

1. Cihaz durumu ilk olarak **kaydedilir**. Bu durum, cihazın IoT Central oluşturulduğu ve bir cihaz KIMLIĞINE sahip olduğu anlamına gelir. Bir cihaz şu durumlarda kaydedilir:
    - **Cihazlar** sayfasına yeni bir gerçek cihaz eklenir.
    - **Cihazlar sayfasında** **içeri aktar** kullanılarak bir cihaz kümesi eklenir.

1. Geçerli kimlik bilgileriyle IoT Central uygulamanıza bağlı olan cihaz sağlama adımını tamamladığında cihaz durumu **sağlandı** olarak değişir. Bu adımda, cihaz IoT Hub bir bağlantı dizesi alır. Cihaz artık IoT Hub bağlanabilir ve veri göndermeye başlayabilir.

1. Bir işleç, bir cihazı engelleyebilir. Bir cihaz engellendiğinde, IoT Central uygulamanıza veri gönderemeyecektir. Engellenen cihazların durumu **engellendi**. Bir operatör, verileri göndermeye başlamadan önce cihazı sıfırlamalıdır. Bir operatör bir cihazı engellerse, durum önceki değerine döner, **kaydedilir** **veya sağlanır**.

1. Cihaz durumu **onay bekliyor** , bu da **otomatik onaylama** seçeneğinin devre dışı bırakıldığı ve IoT Central bağlanan tüm cihazların bir operatör tarafından açıkça onaylanabileceği anlamına gelir. **Cihazlar sayfasında el** ile kayıtlı değil, ancak geçerli kimlik bilgileriyle bağlantılı olarak cihaz durumu **onay bekliyor**olacaktır. İşleçler, **Onayla** düğmesini kullanarak bu cihazları **aygıtlar** sayfasından onaylayabilir.

1. Cihaz durumu **ilişkilendirilmemiş** olduğundan, IoT Central bağlanan cihazların kendileriyle Ilişkili bir cihaz şablonu olmadığı anlamına gelir. Bu genellikle aşağıdaki senaryolarda olur:
    - Cihaz şablonunu belirtmeden **cihazlar** sayfasında **içeri aktarma** kullanılarak bir cihaz kümesi ekleniyor
    - Cihazlar sayfasında, geçerli kimlik bilgileriyle bağlantılı ancak kayıt sırasında şablon KIMLIĞINI **belirtmeden cihazlar sayfasında** el ile kayıtlı değil.  
Işleci, **geçirme** düğmesini kullanarak bir cihazı **cihazlar** sayfasından bir şablonla ilişkilendirebilir.

## <a name="sdk-support"></a>SDK desteği

Azure cihaz SDK 'Ları, cihaz kodunuzu uygulamanız için en kolay yolu sunar. Aşağıdaki cihaz SDK 'Ları kullanılabilir:

- [C için Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python için Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node. js için Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java için Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET için Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK özellikleri ve IoT Hub bağlantısı

IoT Hub ile tüm cihaz iletişimi aşağıdaki IoT Hub bağlantı seçeneklerini kullanır:

- [Cihazdan buluta mesajlaşma](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Cihaz ikikesi](../../iot-hub/iot-hub-devguide-device-twins.md)

Aşağıdaki tabloda Azure IoT Central cihaz özelliklerinin IoT Hub özellikleriyle nasıl eşleme yapılacağı özetlenmektedir:

| Azure IoT Central | Azure IoT Hub'ı |
| ----------- | ------- |
| Ölçüm: telemetri | Cihazdan buluta mesajlaşma |
| Cihaz özellikleri | Cihaz ikizi bildirilen özellikler |
| Ayarlar | Cihaz ikizi istenen ve bildirilen özellikler |

Cihaz SDK 'larını kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanıza bir DevDiv Kit cihazını bağlama](howto-connect-devkit.md) örnek kodu.

### <a name="protocols"></a>Protokoller

Cihaz SDK 'Ları, bir IoT Hub 'ına bağlanmak için aşağıdaki ağ protokollerini destekler:

- MQTT
- AMQP
- HTTPS

Bu fark protokolleri ve bir seçim hakkında rehberlik hakkında daha fazla bilgi için bkz. [iletişim protokolü seçme](../../iot-hub/iot-hub-devguide-protocols.md).

Cihazınız desteklenen protokollerden herhangi birini kullanamıyorum, protokol dönüştürmesi yapmak için Azure IoT Edge kullanabilirsiniz. IoT Edge, Azure IoT Central uygulamasından kenara işlem yükünü devretmek için diğer uç zekası senaryolarını destekler.

## <a name="security"></a>Güvenlik

Cihazlar ve Azure IoT Central arasında değiş tokuş edilen tüm veriler şifrelenir. IoT Hub, cihaza yönelik IoT Hub uç noktalarına bağlanan bir cihazdan her isteğin kimliğini doğrular. Bir cihaz, kimlik bilgilerinin bağlantı üzerinden değiş tokuşu yapmasını önlemek için imzalı belirteçleri kullanır. Daha fazla bilgi için bkz. [IoT Hub erişimi denetleme](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central cihaz bağlantısı hakkında bilgi edindiğinize göre, önerilen sonraki adımlar aşağıda verilmiştir:

- [Bir DevKit cihazını hazırlama ve bağlama](howto-connect-devkit.md)
- [C SDK: cihaz Istemci SDK 'sını sağlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
