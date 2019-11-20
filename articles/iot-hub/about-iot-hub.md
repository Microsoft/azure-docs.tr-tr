---
title: Azure IoT Hub Tanıtımı | Microsoft Docs
description: Azure IoT Hub hakkında bilgi edinin. Bu IoT hizmeti, ölçeklendirilebilir veri alımı, cihaz yönetimi ve güvenlik için oluşturulmuştur.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 0dde7bd9bd3372f0f5eddbbd56727eca3ae4a9dd
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950458"
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT Hub nedir?

IoT Hub, bulutta barındırılan ve IoT uygulamanız ve yönettiği cihazlar arasındaki iki yönlü iletişim için merkezi ileti hub’ı görevi gören, yönetilen bir hizmettir. Milyonlarca IoT cihazı ve bulutta barındırılan çözüm arka ucu arasında güvenilir ve güvenli iletişim sunan IoT çözümleri derlemek için Azure IoT Hub’ı kullanabilirsiniz. Hemen hemen her cihazı IoT Hub’a bağlayabilirsiniz.

IoT Hub, hem cihazdan buluta hem de buluttan cihaza iletişimi destekler. IoT Hub, cihazdan buluta telemetri, cihazlardan dosya yükleme ve buluttan cihazlarınızı denetlemeye yönelik istek-yanıt yöntemleri gibi birden fazla ileti desenini destekler. IoT Hub izleme; cihaz oluşturma, cihaz hataları ve cihaz bağlantıları gibi olayları izleyerek çözümünüzün durumunu korumanıza yardımcı olur.

IoT Hub'ın özellikleri, sağlık hizmetleri sektöründeki değerli varlıkların izlenmesinde, üretilmesinde ve ofis binası kullanımının izlenmesinde kullanılan endüstriyel ekipmanların yönetimi gibi ölçeklendirilebilir, tam özellikli IoT çözümleri derlemenize yardımcı olur.

## <a name="scale-your-solution"></a>Çözümünüzü ölçeklendirme

IoT Hub, IoT iş yüklerinizi desteklemek için saniye başına milyonlarca eş zamanlı cihazı ve milyonlarca etkinliği ölçeklendirir. IoT Hub ölçeklendirme hakkında daha fazla bilgi için bkz. [IoT Hub ölçeklendirme](iot-hub-scaling.md?branch=release-iotbasic). IoT Hub tarafından sunulan birden fazla hizmet katmanı hakkında daha fazla bilgi edinmek ve ölçeklenebilirlik ihtiyaçlarınıza en uygun şekilde uyum sağlamak için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/iot-hub/)göz atın.

## <a name="secure-your-communications"></a>İletişimlerinizin güvenliğini sağlama

IoT Hub, cihazlarınızın veri göndermesi için size güvenli bir iletişim kanalı sunar.

* Cihaz başına kimlik doğrulaması, her cihazın IoT Hub’a güvenli şekilde bağlanmasına ve her cihazın güvenli şekilde yönetilmesine olanak sağlar.

* Cihaz erişimi üzerinde tam denetime sahip olur ve cihaz başına bağlantıları denetleyebilirsiniz.

* [IoT Hub Cihazı Sağlama Hizmeti](https://docs.microsoft.com/azure/iot-dps/), cihaz ilk önyüklenirken otomatik olarak cihazları doğru IoT hub’a sağlar.

* Birden fazla kimlik doğrulaması türü, çeşitli cihaz özelliklerini destekler:

  * IoT çözümünüzü hızla kullanmaya başlamak için SAS belirteci tabanlı kimlik doğrulaması.

  * Güvenli, standartlara dayalı kimlik doğrulaması için bireysel X.509 sertifika kimlik doğrulaması.

  * Basit ve standartlara dayalı kayıt için X.509 CA kimlik doğrulaması.

## <a name="route-device-data"></a>Cihaz verilerini yönlendirme

Yerleşik ileti yönlendirme işlevi size otomatik kurallara dayalı ileti yayma esnekliği sunar:

* Hub 'ınızın cihaz telemetrisini göndereceğini denetlemek için [ileti yönlendirmeyi](iot-hub-devguide-messages-d2c.md) kullanın.

* Birden fazla uç noktaya iletileri yönlendirmek için ek maliyet yoktur.

* Herhangi bir kod yönlendirme kuralı, özel ileti dağıtıcı kodunun yerini almaz.

## <a name="integrate-with-other-services"></a>Diğer hizmetlerle tümleştirme

Eksiksiz, uçtan uca çözümler derlemek için IoT Hub’ı diğer Azure hizmetleriyle tümleştirebilirsiniz. Örneğin, aşağıdakileri kullanın:

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/): İşletmenizin önemli olaylara güvenilir, ölçeklendirilebilir ve güvenli bir şekilde tepki vermesini sağlamak için.

* [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/): İş süreçlerinizi otomatikleştirmek için.

* [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/): Çözümünüze makine öğrenmesi ve yapay zeka modelleri eklemek için.

* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/): Cihazlarınızdan veri akışı üzerinde gerçek zamanlı analiz hesaplamaları çalıştırmak için.

## <a name="configure-and-control-your-devices"></a>Cihazlarınızı yapılandırma ve denetleme

Yerleşik işlevler dizisiyle IoT Hub’a bağlı cihazlarınızı yönetebilirsiniz.

* Tüm cihazlarınız için cihaz meta verilerini ve durum bilgilerini depolayın, eşitleyin ve sorgulayın.

* Cihaz başına veya cihazların genel özelliklerine göre cihaz durumunu ayarlayın.

* İleti yönlendirme tümleştirmesi ile cihaz tarafından bildirilen durum değişikliğine otomatik olarak yanıt verin.

## <a name="make-your-solution-highly-available"></a>Çözümünüzü yüksek oranda kullanılabilir hale getirme

%99,9 [IoT Hub için Hizmet Düzeyi Sözleşmesi](https://azure.microsoft.com/support/legal/sla/iot-hub/) vardır. [Azure SLA](https://azure.microsoft.com/support/legal/sla/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="connect-your-devices"></a>Cihazlarınızı bağlama

Cihazlarınız üzerinde çalıştırılan ve IoT Hub ile etkileşim kuran uygulamalar derlemek için [Azure IoT cihaz SDK’sı](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) kitaplıklarını kullanın. Desteklenen platformlar arasında birden çok Linux dağıtımı, Windows ve gerçek zamanlı işletim sistemleri yer alır. Desteklenen diller:

* C
* C#
* Java
* Python
* Node.js.

IoT Hub ve cihaz SDK’ları, cihazları bağlamak için aşağıdaki protokolleri destekler:

* HTTPS
* AMQP
* WebSockets üzerinden AMQP
* MQTT
* WebSockets üzerinden MQTT

Çözümünüz cihaz kitaplıklarını kullanamazsa cihazlar, hub’ınıza yerel olarak bağlanmak için MQTT v3.1.1, HTTPS 1.1 veya AMQP 1.0 protokollerini kullanabilir.

Çözümünüz, desteklenen protokollerden birini kullanamıyorsa, özel protokolleri desteklemek için IoT Hub’ı genişletebilirsiniz:

* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)’i kullanarak, edge üzerinde protokol çevirisi gerçekleştirmek için bir alan ağ geçidi oluşturun.

* Bulutta protokol çevirisi gerçekleştirmek için [Azure IoT protokolü ağ geçidini](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) özelleştirin.

## <a name="quotas-and-limits"></a>Kotalar ve sınırlar

Her Azure aboneliği, hizmet kötüye kullanımını önlemek için varsayılan kota sınırları içerir ve bu sınırlar, IoT çözümünüzün kapsamını etkileyebilir. Abonelik başına geçerli sınır, abonelik başına 50 IoT Hub 'dır. Destek birimiyle görüşerek kota artışı isteyebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub kotaları ve azaltma](iot-hub-devguide-quotas-throttling.md). Kota limitleri hakkında daha fazla bilgi için aşağıdaki makalelerden birine bakın:

* [Azure aboneliği hizmet sınırları](../azure-subscription-service-limits.md)

* [IoT Hub azaltma ve siz](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>Sonraki adımlar

Uçtan uca IoT çözümünü denemek için IoT Hub hızlı başlangıçlarına göz atın:

* [Hızlı Başlangıç: Bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-node.md)