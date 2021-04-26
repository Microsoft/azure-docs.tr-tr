---
title: Azure IoT Edge için modüller geliştirin | Microsoft Docs
description: Çalışma zamanı ve IoT Hub iletişim kurabilen Azure IoT Edge için özel modüller geliştirin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6274d0f0265220877938775ab6efd72ff22b84e4
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031141"
---
# <a name="develop-your-own-iot-edge-modules"></a>Kendi IoT Edge modüllerinizi geliştirin

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge modüller, diğer Azure hizmetleriyle bağlanabilir ve daha büyük bulut veri işlem hattınızla katkıda bulunabilir. Bu makalede, IoT Edge çalışma zamanı ve IoT Hub ve dolayısıyla Azure bulutunun geri kalanı ile iletişim kurmak için nasıl modül geliştirebileceğiniz açıklanır.

## <a name="iot-edge-runtime-environment"></a>IoT Edge çalışma zamanı ortamı

IoT Edge çalışma zamanı, birden çok IoT Edge modülünün işlevselliğini tümleştirme ve bunları IoT Edge cihazlara dağıtma altyapısını sağlar. Herhangi bir program IoT Edge modülü olarak paketlenebilir. IoT Edge iletişim ve yönetim işlevlerine tam olarak yararlanmak için, modülde çalışan bir program, yerel IoT Edge hub 'ına bağlanmak için Azure IoT cihaz SDK 'sını kullanabilir.
::: moniker range=">=iotedge-2020-11"
Modüller, yerel IoT Edge hub MQTT aracısına bağlanmak için herhangi bir MQTT istemcisini de kullanabilir.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Programınızı bir IoT Edge modülü olarak paketleme

Programınızı bir IoT Edge cihazına dağıtmak için öncelikle Kapsayıcılı ve Docker ile uyumlu bir altyapıda çalıştırılmalıdır. IoT Edge Docker 'ın arkasındaki açık kaynaklı projeyi Docker ile uyumlu altyapı [olarak kullanır.](https://github.com/moby/moby) Docker ile için kullandığınız parametreler IoT Edge modüllerinizle geçirilebilir. Daha fazla bilgi için bkz. [IoT Edge modüller için kapsayıcı oluşturma seçeneklerini yapılandırma](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>IoT Edge hub 'ı kullanma

IoT Edge hub 'ı iki ana işlev sağlar: proxy IoT Hub ve yerel iletişimler.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Bir modülden IoT Edge hub 'ına bağlanma

Bir modülden yerel IoT Edge hub 'ına bağlanmak, tüm istemcilerle aynı bağlantı adımlarını içerir. Daha fazla bilgi için bkz. [IoT Edge hub 'ına bağlanma](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

AMQP veya MQTT üzerinden IoT Edge yönlendirmeyi kullanmak için, Azure IoT SDK ' dan ModuleClient ' ı kullanabilirsiniz. Modueclient örneklerinin IoT cihazlarını IoT Hub bağlama biçimine benzer şekilde modülünüzü cihazda çalışan IoT Edge hub 'ına bağlamak için bir ModuleClient örneği oluşturun. Moduleclient sınıfı ve iletişim yöntemleri hakkında daha fazla bilgi için bkz. tercih ettiğiniz SDK dili için API başvurusu: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)veya [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

IoT Edge MQTT Aracısı 'nı kullanmak için, kendi MQTT istemcinizi alıp IoT Edge Daemon iş yükü API 'sinden aldığınız bilgilerle bağlantıyı başlatmanız gerekir. <!--Need to add details here-->

MQTT aracısına yönlendirme veya yayımlama/abone olma arasında seçim yapma hakkında daha fazla bilgi için bkz. [Yerel iletişim](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>MQTT aracı temelleri

#### <a name="send-a-message-on-a-user-defined-topic"></a>Kullanıcı tanımlı bir konuya ileti gönderin

MQTT Aracısı IoT Edge, tüm Kullanıcı tanımlı konularda ileti yayımlayabilirsiniz. Bunu yapmak için, modülünüzü belirli konularda yayımlamak üzere yetkilendirin ve iş yükü API 'sinden MQTT aracısına bağlanırken parola olarak kullanılacak bir belirteç alın ve son olarak, yetkili konular üzerinde iletileri istediğiniz MQTT istemcisiyle yayımlayın.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Kullanıcı tanımlı bir konu üzerinde ileti alma

MQTT Aracısı IoT Edge, alma iletileri benzerdir. Önce modülünüzün belirli konulara abone olmak için yetkilendirildiğinden emin olun, ardından iş yükü API 'sinden MQTT aracısına bağlanırken parola olarak kullanılacak bir belirteç alın ve son olarak, yetkili konular 'daki iletilere, tercih ettiğiniz MQTT istemcisiyle abone olun.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub temel elemanlar

IoT Hub bir cihaza bir modül örneğini görür ve bunun anlamı:

* [cihazdan buluta iletileri](../iot-hub/iot-hub-devguide-messaging.md)gönderebilir;
* özellikle kendi kimliğine hedeflenmiş [doğrudan Yöntemler](../iot-hub/iot-hub-devguide-direct-methods.md) alabilir.
* Bu, farklı bir modül ikizi sahiptir ve [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md) ve bu cihazın diğer modül TWINS 'i ile yalıtılmış.

Şu anda modüller, buluttan cihaza iletileri alamıyor veya karşıya dosya yükleme özelliğini kullanamıyor.

Modül yazarken IoT Edge hub 'ına bağlanabilir ve bir cihaz uygulamasıyla IoT Hub kullanırken olduğu gibi IoT Hub temel elemanlar kullanabilirsiniz. IoT Edge modüller ve IoT cihaz uygulamaları arasındaki tek fark, cihaz kimliği yerine modül kimliğine başvurmanız gerekir.

#### <a name="device-to-cloud-messages"></a>Cihazdan buluta iletiler

Bir IoT Edge modülü, bir yerel aracı olarak davranan ve iletileri buluta yayan IoT Edge hub 'ı aracılığıyla buluta ileti gönderebilir. Cihazdan buluta iletileri karmaşık şekilde işlemeyi etkinleştirmek için, IoT Edge bir modül diğer modüller veya cihazlar tarafından gönderilen iletileri ayrıca kendi yerel IoT Edge hub 'ına ve işlenen verilerle yeni iletiler gönderebilirler. Bu nedenle, yerel işleme işlem hatları oluşturmak için IoT Edge modüllerden oluşan zincirler oluşturulabilir.

Yönlendirmeyi kullanarak cihazdan buluta telemetri iletileri göndermek için Azure IoT SDK 'sının ModuleClient ' ı kullanın. Azure IoT SDK ile her modülün, özel MQTT konularıyla eşlenen modül *girişi* ve *Çıkış* uç noktaları kavramı vardır. Yöntemini kullanın `ModuleClient.sendMessageAsync` ve bu işlem, modülünüzün çıkış uç noktasına iletiler gönderir. Ardından, bu çıkış uç noktasını IoT Hub göndermek için edgeHub 'da bir yol yapılandırın.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT aracısına sahip cihazdan buluta telemetri iletileri göndermek, Kullanıcı tanımlı konularda ileti yayımlamaya benzer, ancak modülünüzün aşağıdaki IoT Hub özel konusunu kullanmaktır: `devices/<device_name>/<module_name>/messages/events` . Yetkilendirmeler uygun şekilde ayarlanmalıdır. MQTT Köprüsü Ayrıca bu konudaki iletileri buluta iletecek şekilde yapılandırılmalıdır.

::: moniker-end

Yönlendirmeyi kullanarak iletileri işlemek için, önce başka bir uç noktadan gelen iletileri (modül veya cihazdan) modülünüzün giriş uç noktasına göndermek üzere bir yol ayarlayın, ardından modülünüzün giriş uç noktasında iletileri dinleyin. Her yeni ileti geri geldiğinde Azure IoT SDK 'Sı tarafından bir geri çağırma işlevi tetiklenir. İletinizi bu geri arama işleviyle işleyin ve isteğe bağlı olarak modül uç nokta kuyruğunuza yeni iletiler gönderin.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT Aracısı kullanılarak ileti işleme, Kullanıcı tanımlı konularda iletilere abone olunmasıyla benzerdir, ancak modülünüzün çıkış kuyruğunun IoT Edge özel konuları kullanılarak yapılır: `devices/<device_name>/<module_name>/messages/events` . Yetkilendirmeler uygun şekilde ayarlanmalıdır. İsteğe bağlı olarak, tercih ettiğiniz konularda yeni iletiler gönderebilirsiniz.

::: moniker-end

#### <a name="twins"></a>İkizlerini

TWINS, IoT Hub tarafından belirtilen temel elemanların biridir. Meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere durum bilgilerini depolayan JSON belgeleri vardır. Her modülün veya cihazın kendi ikizi vardır.

Azure IoT SDK ile bir modül ikizi almak için `ModuleClient.getTwin` yöntemini çağırın.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT istemcisiyle bir modül ikizi almak için, ikizi alma tipik bir MQTT deseninin olmadığı için biraz daha fazla iş dahil edilir. Modülün öncelikle IoT Hub özel konuya abone olması gerekir `$iothub/twin/res/#` . Bu konu adı IoT Hub devralınır ve tüm cihazların/modüllerin aynı konuya abone olması gerekir. Cihazların birbirini ikizi aldığı anlamına gelmez. IoT Hub ve edgeHub, tüm cihazlar aynı konu adını dinlerken bile hangi ikizi teslim edileceğini bilir. Abonelik yapıldıktan sonra, isteğin KIMLIĞI ile aşağıdaki IoT Hub özel konuya bir ileti yayımlayarak modülün ikizi için istekte bulunması gerekir `$iothub/twin/GET/?$rid=1234` . Bu istek KIMLIĞI, istenen verilerle birlikte IoT Hub tarafından geri gönderilecek rastgele bir KIMLIK (yani, bir GUID). Bu, bir istemcinin isteklerini yanıtlarla eşleştirmesine yönelik bir istekdir. Sonuç kodu, başarılı olduğunda 200 olarak kodlanan HTTP benzeri bir durum kodudur.

::: moniker-end

Azure IoT SDK ile bir modül ikizi Düzeltme Eki almak için bir geri çağırma işlevi uygulayın ve bunu `ModuleClient.moduleTwinCallback` Azure ıOT SDK 'sının yöntemine kaydedin. böylece, geri çağırma işlevinizin bir ikizi yaması her seferinde tetiklenmesi gerekir.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Herhangi bir MQTT istemcisiyle bir modül ikizi Patch almak için, işlem tam TWINS almaya oldukça benzerdir: bir istemcinin özel IoT Hub konusuna abone olması gerekir `$iothub/twin/PATCH/properties/desired/#` . Abonelik yapıldıktan sonra, IoT Hub istenen bölümün bir değişikliğini gönderdiğinde, istemci onu alır.

::: moniker-end

#### <a name="receive-direct-methods"></a>Doğrudan Yöntemler al

Azure IoT SDK ile doğrudan bir yöntem almak için, bir geri çağırma işlevi uygulayın ve bunu `ModuleClient.methodCallback` Azure ıOT SDK 'sının yöntemine kaydedin. böylece, geri çağırma işlevinizin doğrudan bir yöntemin geldiği her seferinde tetiklenmesi gerekir.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Herhangi bir MQTT istemcisiyle doğrudan bir yöntemi almak için, işlem ikizi yamaları almaya çok benzer. İstemcinin çağrıyı aldığından emin olması gerekir ve aynı anda bazı bilgileri geri gönderebilirler. Abone olunacak özel IoT Hub konusu `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Dil ve mimari desteği

IoT Edge, gereksinimlerinize uyan senaryoyu oluşturabilmeniz için birden çok işletim sistemini, cihaz mimarilerini ve geliştirme dillerini destekler. Özel IoT Edge modülleri geliştirmeye yönelik seçeneklerinizi anlamak için bu bölümü kullanın. [IoT Edge için geliştirme ve test ortamınızı hazırlama](development-environment.md)konusundaki her dilin araç desteği ve gereksinimleri hakkında daha fazla bilgi edinebilirsiniz.

### <a name="linux"></a>Linux

Aşağıdaki tablodaki tüm diller için IoT Edge AMD64 ve ARM32 Linux kapsayıcıları için geliştirmeyi destekler.

| Geliştirme dili | Geliştirme araçları |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>ARM64 Linux kapsayıcıları için geliştirme ve hata ayıklama desteği [genel önizlemeye](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)sunuldu. Daha fazla bilgi için bkz. [Visual Studio Code IoT Edge modüllerini geliştirme ve hata ayıklama (Önizleme)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Aşağıdaki tablodaki tüm diller için IoT Edge AMD64 Windows kapsayıcıları için geliştirmeyi destekler.

| Geliştirme dili | Geliştirme araçları |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (hata ayıklama özellikleri yok)<br>Visual Studio 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1,1 LTS, Windows kapsayıcılarını destekleyen son sürüm kanaldır. Sürüm 1,2 ' den başlayarak Windows kapsayıcıları desteklenmez.

Windows kapsayıcılarıyla geliştirme hakkında daha fazla bilgi için bu makalenin [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) sürümüne bakın.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Sonraki adımlar

[Geliştirme ve test ortamınızı IoT Edge için hazırlama](development-environment.md)

[IoT Edge için C# modülleri geliştirmek için Visual Studio 'Yu kullanma](how-to-visual-studio-develop-module.md)

[IoT Edge için modüller geliştirmek üzere Visual Studio Code kullanın](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK'larını anlama ve kullanma](../iot-hub/iot-hub-devguide-sdks.md)
