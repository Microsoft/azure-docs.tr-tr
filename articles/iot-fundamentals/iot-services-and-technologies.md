---
title: Azure Nesnelerin İnterneti (IoT) teknolojileri ve çözümleri
description: Azure IoT çözümü oluşturmak için kullanabileceğiniz teknolojiler ve hizmetler koleksiyonunu açıklar.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 311f8164803dd12664aa193f08c8a1095ee07e31
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168714"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>IoT çözümleri oluşturmak için hangi Azure teknolojilerini ve hizmetlerini kullanabilirsiniz?

Azure IoT teknolojileri ve Hizmetleri, kuruluşunuz için dijital dönüştürmeyi etkinleştiren çok çeşitli IoT çözümleri oluşturma seçeneklerini sunar. Örneğin, şunları yapabilirsiniz:

* Güvenli, kurumsal düzeyde bir IoT çözümü oluşturup dağıtmak için, yönetilen bir IoT uygulama platformu olan [Azure IoT Central](https://apps.azureiotcentral.com)kullanın. IoT Central, çözüm geliştirme sürecinizi hızlandırmak için perakende ve sağlık gibi sektöre özgü uygulama şablonlarının bir koleksiyonuna sahiptir.
* Sıfırdan özel bir IoT çözümü oluşturmak için Azure [IoT Hub](../iot-hub/about-iot-hub.md) ve [Azure IoT cihaz SDK 'Ları](../iot-hub/iot-hub-devguide-sdks.md) gibi Azure IoT Platformu hizmetlerini kullanın.

![Azure IoT teknolojileri, hizmetleri ve çözümleri](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[IoT Central uygulama platformu](https://apps.azureiotcentral.com) , kurumsal düzeyde IoT çözümlerinin geliştirilmesi, yönetilmesi ve bakımının yükünü ve maliyetini azaltır. ' Deki özelleştirilebilir Web Kullanıcı arabirimi, cihaz koşullarını izlemenizi, kurallar oluşturmanızı ve milyonlarca cihazı ve bunların yaşam döngüsü boyunca verilerini yönetmenizi sağlar. IoT Central IoT Central içindeki API yüzeyi, IoT çözümünüzü yapılandırmak ve bunlarla etkileşime geçmek için programlı erişim sağlar.

Azure IoT Central, özel IoT çözümleri oluşturmak için kullanabileceğiniz tam olarak yönetilen bir uygulama platformudur. IoT Central çözümler oluşturmak için uygulama şablonlarını kullanır. Genel çözümler ve enerji, sağlık, devlet ve perakende gibi belirli sektörlerde şablonlar vardır. IoT Central uygulama şablonları, daha sonra Temalar, panolar ve görünümlerle özelleştirebileceğiniz bir IoT Central uygulamasını dakikalar içinde dağıtmanıza olanak tanır.

Çözümünüze hızlı bir şekilde bağlanmak için [IoT Için Azure Sertifikalı cihaz kataloğu](https://devicecatalog.azure.com) ' ndan cihazları seçin. Cihazınızı izlemek ve yönetmek için IoT Central Web Kullanıcı arabirimini kullanarak cihazları sağlıklı ve bağlı tutmaya devam edin. IoT Central uygulamanızı diğer iş uygulamalarıyla tümleştirmek için bağlayıcılar ve API 'Leri kullanın.

Tam olarak yönetilen bir uygulama platformu olarak, IoT Central basit ve öngörülebilir bir fiyatlandırma modeline sahiptir.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT çözüm hızlandırıcıları

[Azure IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com) , özelleştirilebilir kurumsal sınıf çözümlerin bir koleksiyonudur. Bu çözümleri olduğu gibi dağıtabilir veya açık kaynaklı Java veya .NET kaynak kodunu kullanarak özel bir IoT çözümü geliştirebilirsiniz.

Azure IoT Çözüm Hızlandırıcıları, IoT çözümünüz üzerinde yüksek düzeyde denetim sağlar. Çözüm Hızlandırıcıları, Azure aboneliğinize dakikalar içinde dağıtabileceğiniz yaygın IoT senaryolarına yönelik önceden oluşturulmuş çözümler içerir. Senaryolar şunlardır:

* Bağlı fabrika
* Cihaz benzetimi

Tüm çözüm hızlandırıcılarına yönelik açık kaynaklı kod tabanı GitHub ' da kullanılabilir. Belirli IoT gereksinimlerinizi karşılayacak çözüm hızlandırıcıyı özelleştirmek için kodu indirin.

Çözüm Hızlandırıcıları Azure IoT Hub gibi Azure hizmetlerini ve Azure aboneliğinizde yönetmeniz gereken Azure depolama alanını kullanır.

## <a name="custom-solutions"></a>Özel çözümler

Sıfırdan bir IoT çözümü oluşturmak veya IoT Central veya çözüm Hızlandırıcısı kullanılarak oluşturulan bir çözümü genişletmek için aşağıdaki Azure IoT teknolojileri ve hizmetlerinden birini veya daha fazlasını kullanın:

### <a name="devices"></a>Cihazlar

[Azure IoT başlangıç setlerinden](https://devicecatalog.azure.com/kits) birini kullanarak IoT cihazlarınızı geliştirin veya [IoT cihaz kataloğu için Azure Sertifikalı](https://devicecatalog.azure.com)bir cihaz seçin. Açık kaynak [cihaz SDK](../iot-hub/iot-hub-devguide-sdks.md)'larını kullanarak katıştırılmış kodunuzu uygulayın. Cihaz SDK 'Ları, Linux, Windows ve gerçek zamanlı işletim sistemleri gibi birden çok işletim sistemini destekler. [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.net](https://github.com/Azure/azure-iot-sdk-csharp)ve [Python](https://github.com/Azure/azure-iot-sdk-python)gibi birden çok programlama dili için SDK 'lar vardır.

[Iot Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md) hizmetini kullanarak cihazlarınız için katıştırılmış kodu nasıl oluşturacağınız hakkında daha fazla basitleşebilir. IoT Tak ve Kullan, çözüm geliştiricilerinin, ekli kod yazmadan cihazları çözümlerle tümleştirmelerini sağlar. IoT Tak ve Kullan 'nin çekirdeğinden, cihaz yeteneklerini tanımlayan bir _cihaz yetenek modeli_ şemadır. Katıştırılmış cihaz kodunuzu oluşturmak ve IoT Central uygulaması gibi bulut tabanlı bir çözümü yapılandırmak için cihaz yetenek modelini kullanın.

[Azure IoT Edge](../iot-edge/about-iot-edge.md) , IoT iş yükünüzün parçalarını Azure bulut hizmetinizdeki cihazlarınıza devretebilmenizi sağlar. IoT Edge çözümünüzde gecikme süresini azaltabilir, cihazlarınızın bulutla değiş tokuşunu azaltabilir ve çevrimdışı senaryolara olanak sağlayabilir. IoT Edge cihazlarını IoT Central ve bazı çözüm hızlandırıcılarından yönetebilirsiniz.

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) , internet 'e bağlı cihazlara yönelik yerleşik iletişim ve güvenlik özellikleriyle güvenli, üst düzey bir uygulama platformudur. Güvenli mikro denetleyici birimini, özel bir Linux tabanlı işletim sistemini ve sürekli ve yenilenebilir güvenlik sağlayan bulut tabanlı bir güvenlik hizmetini içerir.

### <a name="cloud-connectivity"></a>Bulut bağlantısı

[Azure IoT Hub](../iot-hub/about-iot-hub.md) hizmeti, Milyonlarca IoT cihazı ile bulut tabanlı bir çözüm arasında güvenilir ve güvenli çift yönlü iletişimler sunar. [Azure IoT Hub cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md) , IoT Hub için bir yardımcı hizmettir. Bu hizmet, cihazların doğru IoT Hub 'ına insan müdahalesine gerek kalmadan tam zamanında sağlama olanağı sunar. Bu yetenekler, müşterilerin milyonlarca cihazı güvenli ve ölçeklenebilir bir şekilde sağlamasını sağlar.

IoT Hub, çözüm Hızlandırıcıların temel bir bileşenidir ve bunu kullanarak IoT uygulama sorunlarını karşılayabilir:

* Yüksek hacimli cihaz bağlantısı ve yönetimi.
* Yüksek hacimli telemetri alımı.
* Cihazların komuta ve denetimi.
* Cihaz güvenliği uygulama.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Fiziksel ve dijital Worl 'ler arasındaki boşluğun köprülemesi

[Azure dijital TWINS](../digital-twins/overview.md) , fiziksel bir ortamı modellemenize olanak tanıyan bir IoT hizmetidir. Kişiler, boşluklar ve cihazlar arasındaki ilişkileri modellemek için bir uzamsal zeka grafiği kullanır. Dijital ve fiziksel çalışma LDS genelinde verileri birlikte ilişkilendirerek, bağlamsal olarak uyumlu çözümler oluşturabilirsiniz.

IoT Central, gerçek dünyada cihazları ve verileri, kullanıcıların bu bağlı cihazları izlemelerine ve yönetmesine olanak tanıyan dijital modellerle eşitleyecek şekilde dijital TWINS kullanır.

### <a name="data-and-analytics"></a>Veri ve analiz

IoT cihazları genellikle sensörlerden sıcaklık okumaları gibi büyük miktarlarda zaman serisi verileri oluşturur. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) , IoT Hub 'ına bağlanabilir, cihazlarınızdan telemetri akışını okuyabilir, bu verileri saklayabilir ve bunu sorgulayabilir ve görselleştirmenize olanak tanır.

[Azure Maps](../azure-maps/index.yml) , Web ve mobil uygulamalara doğru coğrafi içerik sağlamak için yeni eşleme verilerini kullanan Jeo-uzamsal hizmetler topluluğudur. Uygulamalarınızı oluşturmak için REST API, Web tabanlı bir JavaScript denetimi veya bir Android SDK kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalı bir deneyim için hızlı başlangıçlardan birini deneyin:

- [Azure IoT Central uygulaması oluşturma](../iot-central/core/quick-deploy-iot-central.md)
- [Bir cihazdan IoT Hub 'ına telemetri gönderme](../iot-hub/quickstart-send-telemetry-cli.md)
