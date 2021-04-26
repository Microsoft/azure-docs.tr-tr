---
title: IoT Tak ve Kullan 'ye giriş | Microsoft Docs
description: IoT Tak ve Kullan hakkında bilgi edinin. IoT Tak ve Kullan, akıllı IoT cihazlarının yeteneklerini bildirmesine olanak sağlayan bir açık modelleme dilini temel alır. IoT cihazları, bulut çözümlerine bağlandıklarında cihaz modeli olarak adlandırılan bu bildirimi sunar. Bulut çözümü daha sonra herhangi bir kod yazmadan cihazı otomatik olarak anlayabilir ve bununla etkileşime başlayabilir.
author: rido-min
ms.author: rmpablos
ms.date: 03/21/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 91a09db16524ebc7e4c04069b69b1c42c67538c6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739719"
---
# <a name="what-is-iot-plug-and-play"></a>IoT Tak Çalıştır nedir?

IoT Tak Çalıştır çözüm oluşturucularının el ile hiçbir yapılandırma gerçekleştirmeden akıllı cihazları kendi çözümlerine tümleştirmesine olanak tanır. IoT Tak ve Kullan 'nin temel tarafında, bir cihazın, yeteneklerini IoT Tak ve Kullan özellikli bir uygulamaya tanıtmak için kullandığı bir cihaz _modelidir_ . Bu model, şunları tanımlayan bir dizi öğe olarak yapılandırılmıştır:

- Bir cihazın veya diğer varlıkların salt okunurdur veya yazılabilir durumunu temsil eden _özelliklerdir_ . Örneğin, bir cihaz seri numarası salt okunurdur ve bir termostat üzerinde hedef sıcaklık yazılabilir bir özellik olabilir.
- Bir cihaz _tarafından yayılan,_ verilerin düzenli algılayıcı okumalar, zaman zaman hatası veya bilgi iletisi olup olmadığı.
- Bir cihazda yapılabilen bir işlevi veya işlemi tanımlayan _Komutlar_ . Örneğin, bir komut bir ağ geçidini yeniden başlatır veya uzak bir kamerayı kullanarak bir resim alabilir.

Bu öğeleri, daha kolay işbirliği yapmak ve geliştirmeyi hızlandırmak için modeller genelinde yeniden kullanmak üzere arabirimlerde gruplandırabilirsiniz.

IoT Tak ve Kullan [Azure dijital TWINS](../digital-twins/overview.md)ile çalışmasını sağlamak Için, [dijital TWINS tanım dili (dtdl)](https://github.com/Azure/opendigitaltwins-dtdl)kullanarak modeller ve arabirimler tanımlarsınız. IoT Tak ve Kullan ve DTDL, topluluk için açıktır ve Microsoft, müşteriler, iş ortakları ve sektörle işbirliği ile gelir. Her ikisi de JSON-LD ve RDF gibi açık W3C standartlarına dayalıdır ve bu da hizmetler ve araçlar arasında daha kolay benimsenme sağlar.

IoT Tak ve Kullan ve DTDL kullanmanın ek maliyeti yoktur. [Azure IoT Hub](../iot-hub/about-iot-hub.md) ve diğer Azure hizmetleri için standart ücretler aynı kalır.

Bu makalede şunları özetlenmektedir:

- IoT Tak ve Kullan kullanan bir projeyle ilişkili tipik roller.
- Uygulamanızda IoT Tak ve Kullan cihazları kullanma.
- IoT Tak ve Kullan destekleyen bir IoT cihaz uygulaması geliştirme.

## <a name="user-roles"></a>Kullanıcı rolleri

IoT Tak ve Kullan iki tür geliştirici için yararlıdır:

- _Çözüm Oluşturucu_ , Azure IoT Hub ve diğer Azure kaynaklarını kullanarak bir IoT çözümü geliştirmekten ve tümleştirilecek IoT cihazlarının tanımlanmasından sorumludur.
- Bir _cihaz Oluşturucu_ çözümünüze bağlı bir cihazda çalışan kodu oluşturur.

## <a name="use-iot-plug-and-play-devices"></a>IoT Tak ve Kullan cihazları kullanma

Bir çözüm Oluşturucusu olarak, IoT Tak ve Kullan cihazları kullanan bulutta barındırılan bir IoT çözümü geliştirmek için [IoT Central](../iot-central/core/overview-iot-central.md) veya [IoT Hub](../iot-hub/about-iot-hub.md) kullanabilirsiniz.

IoT Central Web Kullanıcı arabirimi, cihaz koşullarını izlemenizi, kurallar oluşturmanızı ve milyonlarca cihazı ve bunların yaşam döngüsü boyunca verilerini yönetmenizi sağlar. IoT Tak ve Kullan cihazları, cihazlarınızı izlemek ve denetlemek için özelleştirilebilen panoları kullanabileceğiniz bir IoT Central uygulamasına doğrudan bağlanır. Ayrıca, DTDL modellerini oluşturmak ve düzenlemek için IoT Central Web Kullanıcı arabirimindeki cihaz şablonlarını da kullanabilirsiniz.

IoT Hub-yönetilen bir bulut hizmeti-IoT uygulamanız ve cihazlarınız arasında güvenli, çift yönlü iletişim için bir ileti hub 'ı işlevi görür. IoT Tak ve Kullan cihazını bir IoT Hub 'ına bağladığınızda, DTDL modelinde tanımlanan telemetri, özellik ve komutları görüntülemek için [Azure IoT gezgin](./howto-use-iot-explorer.md) aracını kullanabilirsiniz.

Bir Windows veya Linux Gateway 'e eklenmiş var olan sensörlarınız varsa, bu sensörlerden bağlantı kurmak ve cihaz yazılımı/bellenimi ( [Desteklenen protokoller](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)için) yazmak zorunda kalmadan IoT Tak ve kullan cihazları oluşturmak için [IoT Tak ve Kullan köprüsü](./concepts-iot-pnp-bridge.md)' ne yararlanabilirsiniz.

## <a name="develop-an-iot-device-application"></a>IoT cihaz uygulaması geliştirme

Bir cihaz Oluşturucu olarak IoT Tak ve Kullan destekleyen bir IoT donanım ürünü geliştirebilirsiniz. İşlem üç temel adımı içerir:

1. Cihaz modelini tanımlayın. [Dtdl](https://github.com/Azure/opendigitaltwins-dtdl)kullanarak cihazınızın yeteneklerini TANıMLAYAN bir JSON dosyaları kümesi yazar. Model, fiziksel ürün gibi bir bütün varlığı açıklar ve bu varlık tarafından uygulanan arabirimlerin kümesini tanımlar. Arabirimler, bir cihaz tarafından desteklenen telemetri, özellik ve komutları benzersiz bir şekilde tanımlayan paylaşılan sözleşmelerdir. Arabirimler, farklı modeller arasında yeniden kullanılabilir.

1. Cihaz yazılımını veya bellenimi, telemetri, Özellikler ve komutlarının IoT Tak ve Kullan kurallarını takip eden bir şekilde yazar. Bir Windows veya Linux ağ geçidine bağlı mevcut sensörleri bağlıyorsanız, [ıot Tak ve Kullan köprüsü](./concepts-iot-pnp-bridge.md) bu adımı basitleştirebilir.

1. Cihaz MQTT bağlantısının bir parçası olarak model KIMLIĞINI duyurur. Azure IoT SDK 'Sı, bağlantı zamanında model KIMLIĞINI sağlamak için yeni yapılar içerir.

> [!Important]
> IoT Tak ve Kullan cihazların WebSockets üzerinden MQTT veya MQTT kullanması gerekir. AMQP veya HTTP gibi diğer protokoller IoT Tak ve Kullan cihazları uygulamak için geçerli değildir.

## <a name="device-certification"></a>Cihaz sertifikasyonu

[Iot Tak ve kullan Cihaz Sertifikası programı](../certification/program-requirements-pnp.md) , bir cihazın IoT Tak ve kullan sertifika gereksinimlerini karşıladığını doğrular. [Azure IoT cihaz kataloğuna yönelik ortak sertifikalı](https://aka.ms/devicecatalog)sertifikalı bir cihaz ekleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan 'ye genel bir bakış edinolduğunuza göre, önerilen sonraki adım hızlı başlangıçlardan birini denemektedir:

- [IoT Hub’a cihaz bağlama](./quickstart-connect-device.md)
- [Çözümünüzden bir cihazla etkileşim kurma](./quickstart-service.md)
