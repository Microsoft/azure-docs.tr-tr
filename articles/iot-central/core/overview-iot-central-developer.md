---
title: Azure IoT Central için cihaz geliştirme | Microsoft Docs
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran bir IoT uygulama platformudur. Bu makalede, IoT Central uygulamanıza bağlanmak üzere cihaz geliştirmeye yönelik bir genel bakış sunulmaktadır. Cihazlar cihaz durumunu raporlamak için akış verileri ve özellikleri göndermek üzere telemetri kullanır. IoT Central, bir cihazdaki yazılabilir Özellikler ve çağrı komutları kullanarak cihaz durumunu ayarlayabilir.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: ebd2759d4dfb8ee79130f9b4876eba8d45226d04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718800"
---
# <a name="iot-central-device-development-guide"></a>IoT Central cihaz geliştirme Kılavuzu

*Bu makale, cihaz geliştiricileri için geçerlidir.*

IoT Central bir uygulama, yaşam döngüsü boyunca milyonlarca cihazı izlemenize ve yönetmenize olanak sağlar. Bu kılavuz, IoT Central bağlanan cihazlarda çalıştırılacak kodu uygulayan cihaz geliştiricileri için tasarlanmıştır.

Cihazlar, aşağıdaki temel elemanlar kullanılarak bir IoT Central uygulamayla etkileşim kurar:

- _Telemetri_ , bir cihazın IoT Central için gönderdiği bir veri. Örneğin, bulunan bir sensörden sıcaklık değerleri akışı.
- _Özellikler_ , bir cihazın IoT Central rapor veren durum değerleridir. Örneğin, cihazın geçerli bellenim sürümü. Ayrıca, IoT Central bir hedef sıcaklık gibi cihazda güncelleştirebilirler yazılabilir özelliklere de sahip olabilirsiniz.
- Bir cihaz davranışını denetlemek için IoT Central _komutları_ çağrılır. Örneğin, IoT Central uygulamanız bir cihazı yeniden başlatmak için bir komut çağırabilir.

IoT Central Web Kullanıcı arabirimindeki panoları ve görünümleri yapılandırmak, özellikleri yönetmek ve komutları çağırmak için bir çözüm Oluşturucusu sorumludur.

## <a name="types-of-device"></a>Cihaz türleri

Aşağıdaki bölümlerde, bir IoT Central uygulamasına bağlanabildiğinizi ana cihaz türleri açıklanır:

### <a name="standalone-device"></a>Tek başına cihaz

Tek başına bir cihaz doğrudan IoT Central bağlanır. Tek başına bir cihaz genellikle, kendi yerleşik veya bağlı sensörlerinden IoT Central uygulamanıza telemetri gönderir. Tek başına cihazlar ayrıca özellik değerlerini rapor edebilir, yazılabilir özellik değerlerini alabilir ve komutlara yanıt verebilir.

### <a name="gateway-device"></a>Ağ geçidi cihazı

Bir ağ geçidi cihazı, IoT Central uygulamanıza bağlanan bir veya daha fazla aşağı akış aygıtını yönetir. Aşağı akış cihazları ve ağ geçidi cihazı arasındaki ilişkileri yapılandırmak için IoT Central kullanırsınız. Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızda yeni bir IoT Ağ Geçidi cihaz türü tanımlama](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Edge cihazı

Sınır cihazı doğrudan IoT Central bağlanır, ancak _yaprak cihazlar_ olarak bilinen diğer cihazlar için bir aracı gibi davranır. Bir uç cihaz, genellikle bir aracı olarak hareket eden yaprak cihazlara yakın şekilde bulunur. Edge cihazları kullanan senaryolar şunlardır:

- Edge cihazından bağlanmak üzere IoT Central doğrudan bağlanamadaki cihazları etkinleştirin. Örneğin, bir yaprak cihaz uç cihaza bağlanmak için Bluetooth kullanabilir ve daha sonra IoT Central internet üzerinden bağlanır.
- IoT Central gönderilmeden önce Birleşik telemetri toplayın. Bu yaklaşım, IoT Central veri gönderme maliyetlerini azaltmaya yardımcı olabilir.
- İnternet üzerinden IoT Central bağlanma ile ilişkili gecikme süresini önlemek için yaprak cihazları yerel olarak denetleyin.

Sınır cihazı Ayrıca kendi telemetrisini gönderebilir, özelliklerini raporlar ve yazılabilir Özellik güncelleştirmelerine ve komutlarına yanıt verebilir.

IoT Central, sınır cihazına bağlı yaprak cihazların değil, yalnızca Edge cihazını görür.

Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanıza Azure IoT Edge cihaz ekleme](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Cihaz bağlama

Azure IoT Central, tüm cihaz kayıt ve bağlantılarını yönetmek için [azure IoT Hub cihaz sağlama hizmeti 'ni (DPS)](../../iot-dps/about-iot-dps.md) kullanır.

DPS kullanılması şunları sunar:

- Cihazları ölçeklendirmeye ekleme ve bağlamayı desteklemek için IoT Central.
- Cihazları IoT Central kullanıcı arabirimi aracılığıyla kaydetmeden cihaz kimlik bilgilerini oluşturup cihazları çevrimdışı olarak yapılandırabilirsiniz.
- IoT Central cihazları kaydetmek için kendi cihaz kimliklerinizi kullanmanız gerekir. Kendi cihaz kimliklerinizin kullanılması, mevcut arka ofis sistemleriyle tümleştirmeyi basitleştirir.
- Cihazları IoT Central bağlamak için tek ve tutarlı bir yol.

Daha fazla bilgi edinmek için bkz. [Azure IoT Central](./concepts-get-connected.md) bağlanma ve [en iyi uygulamalar](concepts-best-practices.md).

### <a name="security"></a>Güvenlik

Bir cihaz ile IoT Central uygulamanız arasındaki bağlantı, [paylaşılan erişim imzaları](./concepts-get-connected.md#sas-group-enrollment) ya da sektör standardı [X. 509.440 sertifikaları](./concepts-get-connected.md#x509-group-enrollment)kullanılarak güvenlidir.

### <a name="communication-protocols"></a>İletişim protokolleri

Bir cihazın IoT Central bağlanmak için kullanabileceği iletişim protokolleri MQTT, AMQP ve HTTPS içerir. Dahili olarak, IoT Central cihaz bağlantısını etkinleştirmek için bir IoT Hub 'ı kullanır. Cihaz bağlantısı için IoT Hub desteklediği iletişim protokolleri hakkında daha fazla bilgi için bkz. [iletişim protokolü seçme](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Cihazı uygulama

IoT Central cihaz şablonu, bu türde bir cihazın uygulanması gereken davranışları belirten bir _model_ içerir. Davranışlar telemetri, Özellikler ve komutları içerir.

> [!TIP]
> IoT Central modeli [dijital bir TWINS tanım dili (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON dosyası olarak dışarı aktarabilirsiniz.

Her modelde, gibi benzersiz bir _cihaz ikizi model tanımlayıcısı_ (dtmı) vardır `dtmi:com:example:Thermostat;1` . Bir cihaz IoT Central bağlandığı zaman, uygulayan modelin DTMı 'sini gönderir. IoT Central, ardından doğru cihaz şablonunu cihazla ilişkilendirebilir.

[Iot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) , BIR dtdl modeli uyguladığı zaman bir cihazın izlemesi gereken bir dizi kural tanımlar.

[Azure IoT cihaz SDK 'Ları](#languages-and-sdks) IoT Tak ve kullan kuralları için destek içerir.

### <a name="device-model"></a>Cihaz modeli

Bir cihaz modeli [Dtdl](https://github.com/Azure/opendigitaltwins-dtdl)kullanılarak tanımlanır. Bu dil şunları tanımlamanızı sağlar:

- Cihazın gönderdiği telemetri. Tanım, telemetrinin adını ve veri türünü içerir. Örneğin, bir cihaz sıcaklık telemetrisini çift olarak gönderir.
- Cihazın IoT Central rapor aldığı Özellikler. Bir özellik tanımı, adını ve veri türünü içerir. Örneğin, bir cihaz Vana durumunu Boole olarak bildirir.
- Cihazın IoT Central alabileceği Özellikler. İsteğe bağlı olarak, bir özelliği yazılabilir olarak işaretleyebilirsiniz. Örneğin, IoT Central bir hedef sıcaklık bir cihaza çift olarak gönderilir.
- Bir cihazın yanıt verdiği komutlar. Tanım, komutun adını ve parametrelerinin adlarını ve veri türlerini içerir. Örneğin, bir cihaz yeniden başlatmadan önce kaç saniye bekleneceğini belirten bir reboot komutuna yanıt verir.

DTDL modeli, _bileşen_ olmayan veya _çok bileşen_ modeli olabilir:

- Bileşen olmayan model: basit bir model, gömülü veya basamaklı bileşenleri kullanmaz. Tüm telemetri, özellik ve komutlar tek bir _varsayılan bileşen_ olarak tanımlanmıştır. Bir örnek için bkz. [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) modeli.
- Çoklu bileşen modeli. İki veya daha fazla bileşen içeren daha karmaşık bir model. Bu bileşenler tek bir varsayılan bileşeni ve bir veya daha fazla iç içe geçmiş bileşeni içerir. Bir örnek için bkz. [sıcaklık denetleyicisi](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) modeli.

Daha fazla bilgi için bkz. [ıot Tak ve kullan modelleme Kılavuzu](../../iot-pnp/concepts-modeling-guide.md)

### <a name="conventions"></a>Kurallar

Bir cihaz, IoT Central veri alışverişi yaparken IoT Tak ve Kullan kurallarını izlemelidir. Kurallar şunları içerir:

- IoT Central 'e bağlanırken DTMı 'yi gönderin.
- IoT Central için doğru biçimli JSON yüklerini ve meta verilerini gönderin.
- IoT Central ' den yazılabilir özellikleri ve komutları doğru şekilde yanıtlayın.
- Bileşen komutları için adlandırma kurallarını izleyin.

> [!NOTE]
> Şu anda IoT Central DTDL **Array** ve **Jeo uzamsal** veri türlerini tam olarak desteklemez.

Bir cihazın IoT Central sahip olduğu JSON iletilerinin biçimi hakkında daha fazla bilgi edinmek için bkz. [telemetri, özellik ve komut yükleri](concepts-telemetry-properties-commands.md).

IoT Tak ve Kullan kuralları hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan kuralları](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>Cihaz SDK 'Ları

Cihazınızın davranışını uygulamak için [Azure IoT cihaz SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 'larından birini kullanın. Kod şu şekilde olmalıdır:

- Cihazı DPS 'e kaydedin ve IoT Central uygulamanızdaki iç IoT Hub 'ına bağlanmak için DPS içindeki bilgileri kullanın.
- Cihazın uyguladığı modelin DTMı 'sini duyurur.
- Telemetriyi cihaz modelinin belirttiği biçimde gönderin. IoT Central, görselleştirme ve analizler için Telemetriyi nasıl kullanacağınızı öğrenmek için cihaz şablonundaki modeli kullanır.
- Cihaz ve IoT Central arasındaki özellik değerlerini eşitler. Model, IoT Central bilgileri görüntülemesi için özellik adlarını ve veri türlerini belirtir.
- Modelde belirtilen komutlar için komut işleyicileri uygulayın. Model, cihazın kullanması gereken komut adlarını ve parametreleri belirtir.

Cihaz şablonlarının rolü hakkında daha fazla bilgi için bkz. [cihaz şablonları nelerdir?](./concepts-device-templates.md).

Örnek kod için bkz. [istemci uygulaması oluşturma ve bağlama](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Diller ve SDK 'lar

Desteklenen diller ve SDK 'lar hakkında daha fazla bilgi için bkz. [Azure IoT Hub cihaz SDK 'Larını anlama ve kullanma](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz ve bazı kodlara gitmek istiyorsanız, önerilen sonraki adım, [bir istemci uygulamasını oluşturmak ve Azure IoT Central uygulamanıza bağlamak](./tutorial-connect-device.md)için kullanılır.

IoT Central kullanma hakkında daha fazla bilgi edinmek istiyorsanız, önerilen sonraki adımlar, [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md)ile başlayarak hızlı başlangıç adımlarını denemenize olanak sağlar.
