---
title: Azure IoT Central cihaz bağlantısı | Microsoft Docs
description: Bu makalede, Azure IoT Central cihaz bağlantısıyla ilgili temel kavramlar tanıtılmaktadır
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: c365f367a090f1697b71c51f24679b9ea09561d0
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490021"
---
# <a name="get-connected-to-azure-iot-central"></a>Azure IoT Central'a bağlanma

*Bu makale işleçler ve cihaz geliştiricileri için geçerlidir.*

Bu makalede cihazların bir Azure IoT Central uygulamasına nasıl bağlanacağı açıklanır. Bir cihazın IoT Central verileri alışverişi için önce şunları yapmanız gerekir:

- *Kimlik doğrulaması* yapın. IoT Central uygulamayla kimlik doğrulaması, _paylaşılan erişim imzası (SAS) belirteci_ veya bir _X. 509.440 sertifikası_ kullanır. X. 509.440 sertifikaları üretim ortamlarında önerilir.
- *Kaydolun*. Cihazların IoT Central uygulamasına kayıtlı olması gerekir. Kayıtlı cihazları uygulamasındaki **cihazlar** sayfasında görüntüleyebilirsiniz.
- *Bir cihaz şablonuyla ilişkilendirin*. IoT Central bir uygulamada, cihaz şablonları işleçlerin bağlı cihazları görüntülemek ve yönetmek için kullandığı kullanıcı arabirimini tanımlar.

IoT Central aşağıdaki iki cihaz kayıt senaryosunu destekler:

- *Otomatik kayıt*. Cihaz ilk kez bağlandığında otomatik olarak kaydedilir. Bu senaryo, OEM 'Lerin, önce kaydolmadan bağlanabilecek cihazları toplu olarak üretmesini sağlar. OEM, uygun cihaz kimlik bilgilerini oluşturur ve fabrikadaki cihazları yapılandırır. İsteğe bağlı olarak, cihazı veri göndermeye başlamadan önce onaylaması için bir operatör gerekli kılabilirsiniz. Bu senaryo, uygulamanızda bir X. 509.952 veya SAS _grubu kaydı_ yapılandırmanızı gerektirir.
- *El ile kayıt*. İşleçler **cihazlar** sayfasında tek tek cihazları kaydeder veya [bir CSV dosyasını](howto-manage-devices.md#import-devices) toplu kayıt cihazlarına içeri aktarır. Bu senaryoda, X. 509.952 veya SAS _Grup kaydı_ veya x. 509.440 veya SAS _bireysel kaydını_ kullanabilirsiniz.

IoT Central bağlanan cihazlar *ıot Tak ve kullan kurallarına* uymalıdır. Bu kurallardan biri, bir cihazın bağlandığı sırada uyguladığı cihaz modelinin _model kimliğini_ göndermelidir. Model KIMLIĞI, IoT Central uygulamasının cihazı doğru cihaz şablonuyla ilişkilendirilmesini sağlar.

IoT Central, bağlantı işlemini yönetmek için [Azure IoT Hub cihaz sağlama hizmeti 'ni (DPS)](../../iot-dps/about-iot-dps.md) kullanır. Bir cihaz, uygulamanıza bağlanması gereken bilgileri almak için önce bir DPS uç noktasına bağlanır. Dahili olarak, IoT Central uygulamanız cihaz bağlantısını işlemek için bir IoT Hub 'ı kullanır. DPS kullanılması şunları sunar:

- Cihazları ölçeklendirmeye ekleme ve bağlamayı desteklemek için IoT Central.
- Cihazları IoT Central kullanıcı arabirimi aracılığıyla kaydetmeden cihaz kimlik bilgilerini oluşturup cihazları çevrimdışı olarak yapılandırabilirsiniz.
- IoT Central cihazları kaydetmek için kendi cihaz kimliklerinizi kullanmanız gerekir. Kendi cihaz kimliklerinizin kullanılması, mevcut arka ofis sistemleriyle tümleştirmeyi basitleştirir.
- Cihazları IoT Central bağlamak için tek ve tutarlı bir yol.

Bu makalede aşağıdaki cihaz bağlantısı adımları açıklanmaktadır:

- [X. 509.440 grubu kaydı](#x509-group-enrollment)
- [SAS grubu kaydı](#sas-group-enrollment)
- [Bireysel kayıt](#individual-enrollment)
- [Cihaz kaydı](#device-registration)
- [Cihazı cihaz şablonuyla ilişkilendirme](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X. 509.440 grubu kaydı

Bir üretim ortamında, X. 509.440 sertifikalarının kullanılması, IoT Central için önerilen cihaz kimlik doğrulama mekanizmasıdır. Daha fazla bilgi için bkz. [X. 509.440 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](../../iot-hub/iot-hub-x509ca-overview.md).

Bir cihazı bir X. 509.440 sertifikası ile uygulamanıza bağlamak için:

1. **Sertifikalar (X. 509.440)** kanıtlama türünü kullanan bir *kayıt grubu* oluşturun.
1. Kayıt grubunda bir ara veya kök X. 509.440 sertifikası ekleyin ve doğrulayın.
1. Kayıt grubundaki kök veya ara sertifikadan bir yaprak sertifikası oluşturun. Cihaza bağlanırken yaprak sertifikayı cihazdan gönderin.

Daha fazla bilgi edinmek için bkz [. X. 509.440 sertifikalarıyla cihazları bağlama](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Yalnızca test amaçlı

Yalnızca test için, kök, ara ve cihaz sertifikaları oluşturmak üzere aşağıdaki yardımcı programları kullanabilirsiniz:

- [Azure IoT cihaz sağlama cihaz SDK 'sı Için Araçlar](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): X. 509.440 sertifikalarını ve anahtarlarını oluşturmak ve doğrulamak için kullanabileceğiniz Node.js araçları koleksiyonu.
- Bir DevKit cihazı kullanıyorsanız, bu [komut satırı aracı](https://aka.ms/iotcentral-docs-dicetool) , sertifikaları doğrulamak için IoT Central uygulamanıza EKLEYEBILECEĞINIZ bir CA sertifikası oluşturur.
- [Örnek ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): bir PowerShell ve Bash betikleri koleksiyonu:
  - Bir sertifika zinciri oluşturun.
  - IoT Central uygulamanıza yüklemek için sertifikaları. cer dosyası olarak kaydedin.
  - Doğrulama sertifikasını oluşturmak için IoT Central uygulamasındaki doğrulama kodunu kullanın.
  - Cihaz kimliklerinizi araç için bir parametre olarak kullanarak cihazlarınız için yaprak sertifikaları oluşturun.

## <a name="sas-group-enrollment"></a>SAS grubu kaydı

Bir cihazı cihaz SAS anahtarı ile uygulamanıza bağlamak için:

1. **Paylaşılan erişim imzası (SAS)** kanıtlama türünü kullanan bir *kayıt grubu* oluşturun.
1. Grubu birincil veya ikincil anahtar kayıt grubundan kopyalayın.
1. Azure CLı kullanarak Grup anahtarından bir cihaz anahtarı oluşturun:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Cihaz IoT Central uygulamanıza bağlanırken oluşturulan Cihaz anahtarını kullanın.

## <a name="individual-enrollment"></a>Bireysel kayıt

Her biri kendi kimlik doğrulama kimlik bilgilerine sahip olan cihazları bağlayan müşteriler, bireysel kayıtları kullanır. Tek bir kayıt, bağlanmasına izin verilen tek bir cihaz için bir giriştir. Bireysel kayıtlar, bir X. 509.440 yaprak sertifikası veya SAS belirteçleri (fiziksel veya sanal Güvenilir Platform modülünden) kanıtlama mekanizmaları olarak kullanabilir. Bir cihaz KIMLIĞI harfler, rakamlar ve `-` karakteri içerebilir. Daha fazla bilgi için bkz. [DPS bireysel kayıt](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Bir cihaz için tek bir kayıt oluşturduğunuzda, IoT Central uygulamanızdaki varsayılan grup kayıt seçeneklerine göre önceliklidir.

### <a name="create-individual-enrollments"></a>Bireysel kayıtlar oluşturma

IoT Central, bireysel kayıtlar için aşağıdaki kanıtlama mekanizmalarını destekler:

- **Simetrik anahtar kanıtlama:** Simetrik anahtar kanıtlama, bir cihazın DPS örneğine kimlik doğrulaması için basit bir yaklaşımdır. Simetrik anahtarlar kullanan tek bir kayıt oluşturmak için, cihaz için **cihaz bağlantısı** sayfasını açın, bağlantı yöntemi olarak **bireysel kaydı** ve mekanizma olarak **paylaşılan erişim imzasını (SAS)** seçin. Base64 kodlamalı birincil ve ikincil anahtarlar girin ve değişikliklerinizi kaydedin. Cihazınızı bağlamak için **kimlik kapsamını**, **cihaz kimliğini** ve birincil ya da ikincil anahtarı kullanın.

    > [!TIP]
    > Sınama için, **OpenSSL** kullanarak Base64 kodlamalı anahtarlar oluşturabilirsiniz: `openssl rand -base64 64`

- **X. 509.440 sertifikaları:** X. 509.440 sertifikalarıyla tek bir kayıt oluşturmak için, **cihaz bağlantısı** sayfasını açın, bağlantı yöntemi olarak **bireysel kayıt** ' ı ve **Sertifikalar (X. 509.440)** öğesini seçin. Tek bir kayıt girişiyle kullanılan cihaz sertifikalarının, veren ve Subject CN 'nin cihaz KIMLIĞINE ayarlandığı bir gereksinimi vardır.

    > [!TIP]
    > Sınama için, otomatik olarak imzalanan bir sertifika oluşturmak üzere [Node.jsIçin Azure IoT cihaz sağlama cihaz SDK 'Sı araçlarını ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) kullanabilirsiniz: `node create_test_cert.js device "mytestdevice"`

- **Güvenilir Platform Modülü (TPM) kanıtlama:** [TPM](../../iot-dps/concepts-tpm-attestation.md) , bir tür donanım güvenlik modülüdür. TPM kullanmak, bir cihazı bağlamak için en güvenli yöntemlerle biridir. Bu makalede ayrı, bellenim veya tümleşik TPM kullandığınız varsayılır. Yazılım öykünmesi, prototip oluşturma veya test etme için idealdir, ancak ayrık, bellenim veya tümleşik TPMs ile aynı güvenlik düzeyini sağlamalardır. Üretimde yazılım TPM 'Leri kullanmayın. TPM kullanan tek bir kayıt oluşturmak için, **cihaz bağlantısı** sayfasını açın, bağlantı yöntemi olarak **bireysel kayıt** ' ı ve, mekanizma olarak **TPM 'yi** seçin. TPM onay anahtarını girin ve cihaz bağlantı bilgilerini kaydedin.

## <a name="device-registration"></a>Cihaz kaydı

Bir cihazın IoT Central uygulamasına bağlanabilmesi için önce uygulamanın kayıtlı olması gerekir:

- Cihazlar, ilk bağlandıklarında otomatik olarak kendilerini kaydedebilir. Bu seçeneği kullanmak için, [X. 509.440 grup kaydı](#x509-group-enrollment) veya [SAS grubu kaydı](#sas-group-enrollment)kullanmanız gerekir.
- Bir işleç, uygulamadaki bir cihaz listesini toplu olarak kaydetmek için bir CSV dosyasını içeri aktarabilir.
- Bir operatör, tek bir cihazı uygulamadaki **cihazlar** sayfasına el ile kaydedebilir.

IoT Central, OEM 'Lerin kendilerini otomatik olarak kaydedebileceği cihazları toplu olarak üretmesini sağlar. OEM, uygun cihaz kimlik bilgilerini oluşturur ve fabrikadaki cihazları yapılandırır. Bir müşteri bir cihazı ilk kez açtığında, DPS 'e bağlanır ve ardından cihazı otomatik olarak doğru IoT Central uygulamasına bağlar. İsteğe bağlı olarak, cihaza veri göndermeye başlamadan önce bir işlecin bir operatör tarafından onaylanması gerekir.

> [!TIP]
> **Yönetim > cihaz bağlantısı** sayfasında, **otomatik onaylama** seçeneği bir işlecin, verileri göndermeye başlayabilmesi için cihazı el ile onaylaması gerekip gerekmediğini denetler.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>X. 509.440 sertifikaları kullanan cihazları otomatik olarak kaydetme

1. [X. 509.952 kayıt grubunuza](#x509-group-enrollment)eklediğiniz kök veya ara sertifikayı kullanarak cihazlarınızın yaprak sertifikalarını oluşturun. Cihaz kimliklerini, yaprak sertifikalarda olduğu gibi kullanın `CNAME` . Bir cihaz KIMLIĞI harfler, rakamlar ve `-` karakteri içerebilir.

1. Bir OEM olarak, cihaz KIMLIĞI, oluşturulan bir X. 509.440 yaprak sertifikası ve uygulama **kimliği kapsam** değeri olan her bir cihaz için Flash 'u. Cihaz kodu ayrıca uyguladığı cihaz modelinin model KIMLIĞINI de göndermelidir.

1. Bir cihaza geçtiğinizde, önce IoT Central bağlantı bilgilerini almak için DPS 'e bağlanır.

1. Cihaz, IoT Central uygulamanıza bağlanmak ve bu uygulamaya kaydolmak için DPS 'teki bilgileri kullanır.

IoT Central uygulaması, [kayıtlı cihazı bir cihaz şablonuyla ilişkilendirmek](#associate-a-device-with-a-device-template)için cihaz tarafından GÖNDERILEN model kimliğini kullanır.

### <a name="automatically-register-devices-that-use-sas-tokens"></a>SAS belirteçleri kullanan cihazları otomatik olarak kaydet

1. Grup birincil anahtarını **SAS-IoT-Devices** kayıt grubundan kopyalayın:

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Birincil anahtarı SAS-IoT-Devices kayıt grubundan grupla":::

1. `az iot central device compute-device-key`CIHAZ SAS anahtarlarını oluşturmak için komutunu kullanın. Önceki adımda grup birincil anahtarını kullanın. Cihaz KIMLIĞI harf, sayı ve `-` karakter içerebilir:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Bir OEM olarak, cihaz KIMLIĞI, oluşturulan cihaz SAS anahtarı ve uygulama **kimliği kapsam** değeri olan her bir cihaz için Flash 'u. Cihaz kodu ayrıca uyguladığı cihaz modelinin model KIMLIĞINI de göndermelidir.

1. Bir cihaza geçtiğinizde, ilk olarak IoT Central kayıt bilgilerini almak için DPS 'e bağlanır.

1. Cihaz, IoT Central uygulamanıza bağlanmak ve bu uygulamaya kaydolmak için DPS 'teki bilgileri kullanır.

IoT Central uygulaması, [kayıtlı cihazı bir cihaz şablonuyla ilişkilendirmek](#associate-a-device-with-a-device-template)için cihaz tarafından GÖNDERILEN model kimliğini kullanır.

### <a name="bulk-register-devices-in-advance"></a>Cihazları önceden toplu kaydetme

IoT Central uygulamanızla çok sayıda cihazı kaydetmek için, [cihaz kimliklerini ve cihaz adlarını içeri aktarmak](howto-manage-devices.md#import-devices)üzere bir CSV dosyası kullanın.

Cihazlarınız kimlik doğrulamak için SAS belirteçleri kullanıyorsa, [IoT Central uygulamanızdan BIR CSV dosyası dışarı aktarın](howto-manage-devices.md#export-devices). İçe aktarılmış CSV dosyası, cihaz kimliklerini ve SAS anahtarlarını içerir.

Cihazlarınız kimlik doğrulaması yapmak için X. 509.440 sertifikaları kullanıyorsa, X. 509.952 kayıt grubunuza yüklediğiniz kök veya ara sertifikayı kullanarak cihazlarınız için X. 509.952 yaprak sertifikaları oluşturun. Yaprak sertifikalarda değer olarak içeri aktardığınız cihaz kimliklerini kullanın `CNAME` .

Cihazların, uygulamanız için **kimlik kapsamı** değerini kullanması ve bağlandıklarında BIR model kimliği gönderebilmesi gerekir.

> [!TIP]
> **Kimlik kapsamı** değerini **Yönetim > cihaz bağlantısı**' nda bulabilirsiniz.

### <a name="register-a-single-device-in-advance"></a>Tek bir cihazı önceden Kaydet

Bu yaklaşım, IoT Central veya test cihazlarıyla denemeler yaparken faydalıdır. Tek bir cihazı kaydetmek için **cihazlar** sayfasında **+ Yeni** ' yi seçin. Cihaz bağlantısı SAS anahtarlarını, cihazı IoT Central uygulamanıza bağlamak için kullanabilirsiniz. Kayıtlı bir cihazın bağlantı bilgilerini _CIHAZ SAS anahtarı_ ' nı kopyalayın:

![Tek bir cihaz için SAS anahtarları](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Cihazı cihaz şablonuyla ilişkilendirme

IoT Central cihaz bağlandığı zaman bir cihazı cihaz şablonuyla otomatik olarak ilişkilendirir. Bir cihaz, bağlandığı zaman bir [model kimliği](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) gönderir. IoT Central, bu belirli cihaz modeline ait cihaz şablonunu tanımlamak için model KIMLIĞINI kullanır. Bulma işlemi aşağıdaki gibi kullanılabilir:

1. Cihaz şablonu IoT Central uygulamada zaten yayımlanıyorsa cihaz, cihaz şablonuyla ilişkilendirilir.
1. Cihaz şablonu IoT Central uygulamada zaten yayınlanmamışsa, IoT Central [ortak model deposundaki](https://github.com/Azure/iot-plugandplay-models)cihaz modelini arar. IoT Central modeli bulursa, temel bir cihaz şablonu oluşturmak için onu kullanır.
1. IoT Central modeli ortak model deposunda bulamazsa, cihaz **ilişkilendirilmemiş** olarak işaretlenir. Operatör, cihaz için bir cihaz şablonu oluşturabilir ve ardından ilişkilendirilmemiş cihazı yeni cihaz şablonuna geçirebilir.

Aşağıdaki ekran görüntüsünde, IoT Central bir cihaz şablonunun model KIMLIĞINI nasıl görüntüleyebileceğiniz gösterilmektedir. Bir cihaz şablonunda bir bileşen seçin ve ardından **kimliği görüntüle**' yi seçin:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Termostat cihaz şablonundaki model KIMLIĞINI gösteren ekran görüntüsü.":::

Ortak model deposunda [termostat modelini](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) görüntüleyebilirsiniz. Model KIMLIĞI tanımı şöyle görünür:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Cihaz durumu değerleri

Gerçek bir cihaz IoT Central uygulamasına bağlanırsa, cihaz durumu aşağıdaki gibi değişir:

1. Cihaz durumu ilk olarak **kaydedilir**. Bu durum, cihazın IoT Central oluşturulduğu ve bir cihaz KIMLIĞINE sahip olduğu anlamına gelir. Bir cihaz şu durumlarda kaydedilir:
    - **Cihazlar** sayfasına yeni bir gerçek cihaz eklenir.
    - **Cihazlar sayfasında** **içeri aktar** kullanılarak bir cihaz kümesi eklenir.

1. Geçerli kimlik bilgileriyle IoT Central uygulamanıza bağlı olan cihaz sağlama adımını tamamladığında cihaz durumu **sağlandı** olarak değişir. Bu adımda cihaz, IoT Central uygulamanız tarafından kullanılan IoT Hub bir bağlantı dizesini otomatik olarak almak için DPS kullanır. Cihaz artık IoT Central bağlanabilir ve veri göndermeye başlayabilir.

1. Bir işleç, bir cihazı engelleyebilir. Bir cihaz engellendiğinde, IoT Central uygulamanıza veri gönderemeyecektir. Engellenen cihazların durumu **engellendi**. Bir operatör, verileri göndermeye başlamadan önce cihazı sıfırlamalıdır. Bir operatör bir cihazı engellerse, durum önceki değerine döner, **kaydedilir** **veya sağlanır**.

1. Cihaz durumu **onay bekliyor** Ise, **otomatik onaylama** seçeneğinin devre dışı olduğu anlamına gelir. Bir işlecin, verileri göndermeye başlamadan önce açıkça bir cihaz onaylaması gerekir. **Cihazlar sayfasında el** ile kayıtlı değil, ancak geçerli kimlik bilgileriyle bağlantılı olarak cihaz durumu **onay bekliyor** olacaktır. İşleçler, **Onayla** düğmesini kullanarak bu cihazları **aygıtlar** sayfasından onaylayabilir.

1. Cihaz durumu **ilişkilendirilmemiş** ise, IoT Central bağlanan cihazın ilişkili bir cihaz şablonu olmadığı anlamına gelir. Bu durum genellikle aşağıdaki senaryolarda meydana gelir:

    - Cihaz şablonunu belirtmeden **cihazlar** sayfasında **içeri aktar** kullanılarak bir cihaz kümesi eklenir.
    - Cihaz şablonunu belirtmeden **cihazlar** sayfasında el ile bir cihaz kaydedildi. Ardından cihaz geçerli kimlik bilgileriyle bağlanır.  

    Işleci, **geçirme** düğmesini kullanarak **cihazları cihazlar** sayfasından bir cihaz şablonuyla ilişkilendirebilir.

## <a name="sdk-support"></a>SDK desteği

Azure cihaz SDK 'Ları, cihaz kodunuzu uygulamanız için en kolay yolu sunar. Aşağıdaki cihaz SDK 'Ları kullanılabilir:

- [C için Azure IoT SDK'sı](https://github.com/azure/azure-iot-sdk-c)
- [Python için Azure IoT SDK'sı](https://github.com/azure/azure-iot-sdk-python)
- [Node.js için Azure IoT SDK'sı](https://github.com/azure/azure-iot-sdk-node)
- [Java için Azure IoT SDK'sı](https://github.com/azure/azure-iot-sdk-java)
- [.NET için Azure IoT SDK'sı](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK özellikleri ve IoT Hub bağlantısı

IoT Hub ile tüm cihaz iletişimi aşağıdaki IoT Hub bağlantı seçeneklerini kullanır:

- [Cihazdan buluta mesajlaşma](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Buluttan cihaza mesajlaşma](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Cihaz ikikesi](../../iot-hub/iot-hub-devguide-device-twins.md)

Aşağıdaki tabloda Azure IoT Central cihaz özelliklerinin IoT Hub özellikleriyle nasıl eşleme yapılacağı özetlenmektedir:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetri | Cihazdan buluta mesajlaşma |
| Çevrimdışı komutlar | Buluttan cihaza mesajlaşma |
| Özellik | Cihaz ikizi bildirilen özellikler |
| Özellik (yazılabilir) | Cihaz ikizi istenen ve bildirilen özellikler |
| Komut | Doğrudan yöntemler |

### <a name="protocols"></a>Protokoller

Cihaz SDK 'Ları, bir IoT Hub 'ına bağlanmak için aşağıdaki ağ protokollerini destekler:

- MQTT
- AMQP
- HTTPS

Bu fark protokolleri ve bir seçim hakkında rehberlik hakkında daha fazla bilgi için bkz. [iletişim protokolü seçme](../../iot-hub/iot-hub-devguide-protocols.md).

Cihazınız desteklenen protokollerden herhangi birini kullanamıyorum, protokol dönüştürmesi yapmak için Azure IoT Edge kullanın. IoT Edge, Azure IoT Central uygulamasından işlem yükünü devretmek için diğer uç zekası senaryolarını destekler.

## <a name="security"></a>Güvenlik

Cihazlar ve Azure IoT Central arasında değiş tokuş edilen tüm veriler şifrelenir. IoT Hub, cihaza yönelik IoT Hub uç noktalarına bağlanan bir cihazdan her isteğin kimliğini doğrular. Bir cihaz, kimlik bilgilerinin bağlantı üzerinden değiş tokuşu yapmasını önlemek için imzalı belirteçleri kullanır. Daha fazla bilgi için bkz. [IoT Hub erişimi denetleme](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bazı sonraki adımlar şunlardır:

- Cihaz geliştirmeye yönelik [en iyi uygulamaları](concepts-best-practices.md) gözden geçirin.
- Eğitim bölümünde SAS belirteçlerinin nasıl kullanılacağını gösteren örnek kodları gözden geçirin [: Azure IoT Central uygulamanızda bir istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md)
- [IoT Central uygulama için Node.js cihaz SDK 'sını kullanarak X. 509.952 sertifikalarıyla cihazları bağlamayı](how-to-connect-devices-x509.md) öğrenin
- [Azure CLI kullanarak cihaz bağlantısını izlemeyi](./howto-monitor-devices-azure-cli.md) öğrenin
- [Azure IoT Edge cihazlar ve Azure IoT Central](./concepts-iot-edge.md) hakkında bilgi edinin
