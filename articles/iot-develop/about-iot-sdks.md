---
title: Azure IoT cihaz SDK 'Sı seçeneklerine genel bakış
description: Geliştirme rolünüze ve görevlerinize göre hangi Azure IoT cihaz SDK 'sının kullanılacağını öğrenin.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876466"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT cihaz SDK 'larına genel bakış

Azure IoT cihaz SDK 'Ları, bir dizi cihaz istemci kitaplığı, Geliştirici Kılavuzu, örnek ve belge kümesidir. Cihaz SDK 'Ları cihazları Azure IoT hizmetlerine programlı bir şekilde bağlamanıza yardımcı olur.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Çeşitli Azure IoT SDK 'larını gösteren diyagram":::

Diyagramda gösterildiği gibi, cihazınızın ve programlama dili gereksinimlerinizin sığması için kullanabileceğiniz birkaç cihaz SDK 'Sı vardır. Uygun cihaz SDK 'sını seçmeye yönelik yönergeler, [HANGI SDK 'nın kullanmalıyım?](#which-sdk-should-i-use)bölümünde bulunur. Ayrıca, bulut tabanlı uygulamanızı arka uçta Azure IoT hizmetleriyle bağlamak için Azure IoT hizmeti SDK 'Ları de mevcuttur. Bu makale, cihaz SDK 'larına odaklanır, ancak [burada](#service-sdks)Azure hizmet SDK 'ları hakkında daha fazla bilgi edinebilirsiniz.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Neden Azure IoT cihaz SDK 'larını kullanmalıyım?

Cihazları Azure IoT 'e bağlamak için özel bir bağlantı katmanı oluşturabilir veya Azure IoT cihaz SDK 'larını kullanabilirsiniz. Azure IoT cihaz SDK 'larını kullanmanın çeşitli avantajları vardır:

| Geliştirme maliyeti &nbsp; &nbsp; &nbsp;&nbsp; | Özel bağlantı katmanı | Azure IoT cihaz SDK 'Ları |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Destek | Oluşturduğunuz her şeyi desteklemek ve belgelemek gerekir | Microsoft desteğine erişimi vardır (GitHub, Microsoft Q&A, Microsoft Docs, müşteri destek takımları) |
| Yeni Özellikler | Özel ara yazılıma yeni Azure özellikleri ekleme gereksinimi | , Microsoft 'un IoT SDK 'larına sürekli eklediği yeni özelliklerden hemen yararlanabilir |
| Yatırım | Özelleştirilmiş bir sürümü tasarlamak, derlemek, test etmek ve sürdürmek için yüzlerce saat gömülü geliştirmeyi yatırım | , Ücretsiz, açık kaynaklı araçlardan yararlanabilir. SDK 'lar ile ilişkili tek maliyet öğrenme eğrisiydi. |

## <a name="which-sdk-should-i-use"></a>Hangi SDK kullanmalıyım?

Azure IoT cihaz SDK 'Ları, C, C#, Java, Node.js ve Python gibi popüler programlama dillerinde mevcuttur. Bir SDK seçtiğinizde, cihaz özellikleri ve takımınızın programlama diliyle öğrenilmesi gibi başlıca iki önemli sorun vardır.

### <a name="device-capabilities"></a>Cihaz özellikleri

Bir SDK seçerken, kullanmakta olduğunuz cihazların sınırlarını göz önünde bulundurmanız gerekir. Kısıtlanmış bir cihaz, tek bir mikro denetleyiciye (MCU) ve sınırlı belleğe sahip olan bir aygıttır. Kısıtlanmış bir cihaz kullanıyorsanız, [katıştırılmış C SDK 'sını](#embedded-c-sdk)kullanmanızı öneririz. Bu SDK, Azure IoT 'ye bağlanmak için en düşük yetenek kümesini sağlamak üzere tasarlanmıştır. Katıştırılmış cihazınız için en iyi duruma getirilmiş bileşenleri (MQTT istemcisi, TLS ve yuva kitaplıkları) da seçebilirsiniz. Kısıtlanmış cihazınız Ayrıca Azure RTOS çalıştırıyorsa, Azure IoT ara yazılımını kullanarak Azure IoT 'ye bağlanabilirsiniz. Azure RTOS ara yazılımı, Azure RTOS cihazınızın buluta bağlanmasını kolaylaştırmak için, ekli C SDK 'sını ek işlevlerle sarmalayan.

Kısıtlanmış olmayan bir cihaz, .NET veya Python gibi bir dil çalışma zamanını desteklemek için bir işletim sistemi çalıştırabilen daha sağlam bir CPU 'ya sahiptir. Kısıtlanmış olmayan bir cihaz kullanıyorsanız, ana düşünce dile göre belirlenir.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Takımınızın programlama diliyle benzerlik

Azure IoT cihaz SDK 'Ları, tercih ettiğiniz dili seçebilmeniz için birden çok dilde uygulanır. Cihaz SDK 'Ları, diğer tanıdık dile özgü araçlarla de tümleştirilir. Tanıdık bir geliştirme dili ve araçlarıyla çalışabilmeniz için, takımınızın araştırma, prototipleme, ürün geliştirme ve devam eden bakımın geliştirme döngüsünü en iyi hale getirmesine olanak sağlar.

Mümkün olduğunda, geliştirme ekibinize bildiğiniz bir SDK seçin. Tüm Azure IoT SDK 'Ları açık kaynaktır ve takımınızın belirli bir SDK 'ya işlemeden önce değerlendirmesi ve test edebilmesi için birkaç örnek mevcuttur.

## <a name="how-can-i-get-started"></a>Kullanmaya nasıl başlayabilirim?

Başlangıç yeri, Azure cihaz SDK 'larının GitHub depoları araştırılacak. Ayrıca, Azure IoT 'ye telemetri göndermek için bir SDK 'Yı hızlı bir şekilde nasıl kullanacağınızı gösteren bir [hızlı](quickstart-send-telemetry-python.md) başlangıcı deneyebilirsiniz.

Kullanmaya başlamak için seçenekleriniz, sahip olduğunuz cihaz türüne bağlıdır:
- Kısıtlanmış cihazlar için [katıştırılmış C SDK 'sını](#embedded-c-sdk)kullanın. 
- Azure RTOS üzerinde çalışan cihazlar için [Azure RTOS ara yazılımı](#azure-rtos-middleware)ile geliştirme yapabilirsiniz. 
- Kısıtlanmış olmayan cihazlarda [BIR SDK 'yı](#unconstrained-device-sdks) tercih ettiğiniz bir dilde seçebilirsiniz. 

### <a name="constrained-device-sdks"></a>Kısıtlanmış cihaz SDK 'Ları
Bu SDK 'lar, sınırlı işlem veya bellek kaynakları olan cihazlarda çalışmak üzere özelleştirilmiştir. Ortak cihaz türleri hakkında daha fazla bilgi edinmek için bkz. [Azure IoT cihaz türlerine genel bakış](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Gömülü C SDK 'Sı
* [GitHub deposu](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Örnekler](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Başvuru Belgeleri](https://azure.github.io/azure-sdk-for-c/)
* [Katıştırılmış C SDK 'sını oluşturma](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Kısıtlanmış cihazlar için boyut grafiği](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS ara yazılımı

* [GitHub deposu](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Başlangıç kılavuzlarından](https://github.com/azure-rtos/getting-started) ve [daha fazla örnek](https://github.com/azure-rtos/samples)
* [Başvuru Belgeleri](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Kısıtlanmış olmayan cihaz SDK 'Ları
Bu SDK 'lar, daha yüksek sıralı bir dil çalışma zamanını destekleyebilen herhangi bir cihazda çalıştırılabilir. Bu, bilgisayarlar, Raspberry pu ve akıllı telefonlar gibi cihazları içerir. Bunlar öncelikle dile göre farklılaştırırlar, böylece ekibinize ve senaryonuza en uygun kitaplığı seçebilmenizi sağlayabilirsiniz.

#### <a name="c-device-sdk"></a>C cihaz SDK 'Sı
* [GitHub deposu](https://github.com/Azure/azure-iot-sdk-c)
* [Örnekler](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Paketler](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Başvuru Belgeleri](/azure/iot-hub/iot-c-sdk-ref/)
* [Edge modülü başvuru belgeleri](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [C cihaz SDK 'sını derle](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [C SDK 'sını diğer platformlara taşıma](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* Çapraz derleme ve farklı platformlarda çalışmaya başlama hakkında bilgi için [geliştirici belgeleri](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)
* [Azure IoT Hub C SDK kaynak tüketim bilgileri](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# cihaz SDK 'Sı

* [GitHub deposu](https://github.com/Azure/azure-iot-sdk-csharp)
* [Örnekler](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Başvuru Belgeleri](/dotnet/api/microsoft.azure.devices)
* [Edge modülü başvuru belgeleri](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java cihaz SDK 'Sı

* [GitHub deposu](https://github.com/Azure/azure-iot-sdk-java)
* [Örnekler](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Başvuru Belgeleri](/java/api/com.microsoft.azure.sdk.iot.device)
* [Edge modülü başvuru belgeleri](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js cihaz SDK 'Sı

* [GitHub deposu](https://github.com/Azure/azure-iot-sdk-node)
* [Örnekler](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Paket](https://www.npmjs.com/package/azure-iot-device)
* [Başvuru Belgeleri](/javascript/api/azure-iot-device/)
* [Edge modülü başvuru belgeleri](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python cihaz SDK 'Sı

* [GitHub deposu](https://github.com/Azure/azure-iot-sdk-python)
* [Örnekler](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Paket](https://pypi.org/project/azure-iot-device/)
* [Başvuru Belgeleri](/python/api/azure-iot-device)
* [Edge modülü başvuru belgeleri](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Hizmet SDK'ları
Azure IoT Ayrıca, cihazları yönetmek, Öngörüler elde etmek, verileri görselleştirmek ve daha fazlasını yapmak için çözüm tarafı uygulamalar oluşturmanıza olanak tanıyan hizmet SDK 'Ları sunar. Bu SDK 'lar her bir Azure IoT hizmetine özgüdür ve geliştirme deneyiminizi basitleştirmek için C#, Java, JavaScript ve Python 'da kullanılabilir. 

#### <a name="iot-hub"></a>IoT Hub

IoT Hub hizmeti SDK 'Ları, cihazları ve güvenliği yönetmek için IoT Hub kolayca etkileşimde bulunan uygulamalar oluşturmanıza olanak tanır. Bu SDK 'Ları, buluttan cihaza iletiler göndermek, cihazlarınızdaki doğrudan yöntemleri çağırmak, cihaz özelliklerini güncelleştirmek ve daha fazlasını yapmak için kullanabilirsiniz.

IoT Hub hakkında daha [**fazla bilgi edinin**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Bir cihazı denetlemeyi deneyin**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub Service SDK**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [paketi](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [başvuru belgeleri](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub Service SDK**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [paketi](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [başvuru belgeleri](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub Service SDK**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [paketi](https://www.npmjs.com/package/azure-iothub)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [başvuru belgeleri](/javascript/api/azure-iothub/)

**Python IoT Hub Service SDK**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [paketi](https://pypi.python.org/pypi/azure-iot-hub/)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [başvuru belgeleri](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure dijital TWINS, tüm ortamların dijital modellerini temel alan bilgi grafiklerinin oluşturulmasına izin veren bir hizmet olarak platform (PaaS) sunumudur. Bu ortamlar binalar, oluşturucular, gruplar, enerji ağları, railler, lekeler ve daha fazlası gibi tüm şehirlerde bile olabilir. Bu dijital modeller, daha iyi ürünler, iyileştirilmiş işlemler, düşük maliyetler ve müşteri deneyimlerini ele alan Öngörüler elde etmek için kullanılabilir. Azure IoT, Azure dijital TWINS 'in gücünü kullanan uygulamalar oluşturmayı kolaylaştıran hizmet SDK 'Ları sunmaktadır.

[**Azure dijital TWINS hakkında daha fazla bilgi edinin**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Bir ADT uygulamasını kodla**](../digital-twins/tutorial-code.md)

**C# ADT hizmeti SDK 'sı**: [GitHub depo](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [paketi](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [başvuru belgeleri](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT hizmeti SDK 'sı**: [GitHub depo](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [paketi](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [örnekleri](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [başvuru belgeleri](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT Service SDK**: [GitHub depo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [paketi](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [başvuru belgeleri](/javascript/api/@azure/digital-twins-core/)

**Python ADT hizmeti SDK 'sı**: [GitHub depo](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [paketi](https://pypi.org/project/azure-digitaltwins-core/)  |  [örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [başvuru belgeleri](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Cihaz Sağlama Hizmeti

IoT Hub cihaz sağlama hizmeti (DPS), doğru IoT Hub 'a, Kullanıcı müdahalesi gerektirmeden tam zamanında sağlama sağlayan, IoT Hub için bir yardımcı hizmettir. DPS, milyonlarca cihazın güvenli ve ölçeklenebilir bir şekilde sağlanması için izin vermez. DPS hizmet SDK 'Ları, kayıt grupları oluşturup toplu işlemler yaparak cihazlarınızı güvenli bir şekilde yönetebilen uygulamalar oluşturmanıza olanak tanır.

[**Cihaz sağlama hizmeti**](../iot-dps/index.yml)  |  hakkında daha fazla bilgi edinin [ **X. 509.440 cihazları için bir grup kaydı oluşturmayı deneyin**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# cihaz sağlama hizmeti SDK 'sı**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [paketi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [başvuru belgeleri](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java cihaz sağlama hizmeti SDK 'sı**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [paketi](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [başvuru belgeleri](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Cihaz sağlama hizmeti SDK 'sıNode.js**: [GitHub depo](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [paketi](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [örnekleri](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [başvuru belgeleri](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Sonraki Adımlar

* [Hızlı başlangıç: bir cihazı IoT Central bağlama (Python)](quickstart-send-telemetry-python.md)
* [Hızlı başlangıç: bir cihazı IoT Hub bağlama (Python)](quickstart-send-telemetry-cli-python.md)
* [Katıştırılmış geliştirme ile çalışmaya başlama](quickstart-device-development.md)
* [Azure IoT SDK 'larını kullanarak geliştirme avantajları](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) hakkında daha fazla bilgi edinin