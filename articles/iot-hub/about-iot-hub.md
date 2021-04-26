---
title: Azure IoT Hub Tanıtımı | Microsoft Docs
description: Azure IoT Hub hakkında bilgi edinin. Bu IoT hizmeti, ölçeklendirilebilir veri alımı, cihaz yönetimi ve güvenlik için oluşturulmuştur.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 86a373844b370cc9f9ce31dc65b2039a81279803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454779"
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT Hub nedir?

IoT Hub, bulutta barındırılan ve IoT uygulamanız ve yönettiği cihazlar arasındaki iki yönlü iletişim için merkezi ileti hub’ı görevi gören, yönetilen bir hizmettir. Milyonlarca IoT cihazı ve bulutta barındırılan çözüm arka ucu arasında güvenilir ve güvenli iletişim sunan IoT çözümleri derlemek için Azure IoT Hub’ı kullanabilirsiniz. Hemen hemen her cihazı IoT Hub’a bağlayabilirsiniz.

IoT Hub, hem cihazdan buluta hem de buluttan cihaza iletişimi destekler. IoT Hub, cihazdan buluta telemetri, cihazlardan dosya yükleme ve buluttan cihazlarınızı denetlemeye yönelik istek-yanıt yöntemleri gibi birden fazla ileti desenini destekler. IoT Hub izleme; cihaz oluşturma, cihaz hataları ve cihaz bağlantıları gibi olayları izleyerek çözümünüzün durumunu korumanıza yardımcı olur.

IoT Hub'ın özellikleri, sağlık hizmetleri sektöründeki değerli varlıkların izlenmesinde, üretilmesinde ve ofis binası kullanımının izlenmesinde kullanılan endüstriyel ekipmanların yönetimi gibi ölçeklendirilebilir, tam özellikli IoT çözümleri derlemenize yardımcı olur.

## <a name="scale-your-solution"></a>Çözümünüzü ölçeklendirme

IoT Hub, IoT iş yüklerinizi desteklemek için saniye başına milyonlarca eş zamanlı cihazı ve milyonlarca etkinliği ölçeklendirir. IoT Hub ölçeklendirme hakkında daha fazla bilgi için bkz. [IoT Hub ölçeklendirme](iot-hub-scaling.md). IoT Hub tarafından sunulan birden fazla hizmet katmanı hakkında daha fazla bilgi edinmek ve ölçeklenebilirlik ihtiyaçlarınıza en uygun şekilde uyum sağlamak için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/iot-hub/)göz atın.

## <a name="secure-your-communications"></a>İletişimlerinizin güvenliğini sağlama

IoT Hub, cihazlarınızın veri göndermesi için size güvenli bir iletişim kanalı sunar.

* Cihaz başına kimlik doğrulaması, her cihazın IoT Hub’a güvenli şekilde bağlanmasına ve her cihazın güvenli şekilde yönetilmesine olanak sağlar.

* Cihaz erişimi üzerinde tam denetime sahip olur ve cihaz başına bağlantıları denetleyebilirsiniz.

* [IoT Hub Cihazı Sağlama Hizmeti](../iot-dps/index.yml), cihaz ilk önyüklenirken otomatik olarak cihazları doğru IoT hub’a sağlar.

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

* [Azure Event Grid](../event-grid/index.yml): İşletmenizin önemli olaylara güvenilir, ölçeklendirilebilir ve güvenli bir şekilde tepki vermesini sağlamak için.

* [Azure Logic Apps](../logic-apps/index.yml): İş süreçlerinizi otomatikleştirmek için.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md): Çözümünüze makine öğrenmesi ve yapay zeka modelleri eklemek için.

* [Azure Stream Analytics](../stream-analytics/index.yml): Cihazlarınızdan veri akışı üzerinde gerçek zamanlı analiz hesaplamaları çalıştırmak için.

## <a name="configure-and-control-your-devices"></a>Cihazlarınızı yapılandırma ve denetleme

Yerleşik işlevler dizisiyle IoT Hub’a bağlı cihazlarınızı yönetebilirsiniz.

* Tüm cihazlarınız için cihaz meta verilerini ve durum bilgilerini depolayın, eşitleyin ve sorgulayın.

* Cihaz başına veya cihazların genel özelliklerine göre cihaz durumunu ayarlayın.

* İleti yönlendirme tümleştirmesi ile cihaz tarafından bildirilen durum değişikliğine otomatik olarak yanıt verin.

## <a name="make-your-solution-highly-available"></a>Çözümünüzü yüksek oranda kullanılabilir hale getirme

%99,9 [IoT Hub için Hizmet Düzeyi Sözleşmesi](https://azure.microsoft.com/support/legal/sla/iot-hub/) vardır. [Azure SLA](https://azure.microsoft.com/support/legal/sla/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="connect-your-devices"></a>Cihazlarınızı bağlama

Cihazlarınız üzerinde çalıştırılan ve IoT Hub ile etkileşim kuran uygulamalar derlemek için [Azure IoT cihaz SDK’sı](./iot-hub-devguide-sdks.md) kitaplıklarını kullanın. Desteklenen platformlar arasında birden çok Linux dağıtımı, Windows ve gerçek zamanlı işletim sistemleri yer alır. Desteklenen diller:

* C
* Gömülü C
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

IoT Hub ve cihaz SDK 'Ları cihazları bağlamak için [Azure ıot Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md) kurallarını destekler. IoT Tak ve Kullan Cihazları IoT Tak ve Kullan özellikli uygulamalar için yeteneklerini tanıtmak üzere bir cihaz modeli kullanır. Cihaz modeli, çözüm oluşturucuların akıllı cihazları el ile herhangi bir yapılandırma olmadan çözümleriyle tümleştirmelerini sağlar.

Çözümünüz cihaz kitaplıklarını kullanamazsa cihazlar, hub’ınıza yerel olarak bağlanmak için MQTT v3.1.1, HTTPS 1.1 veya AMQP 1.0 protokollerini kullanabilir.

Çözümünüz, desteklenen protokollerden birini kullanamıyorsa, özel protokolleri desteklemek için IoT Hub’ı genişletebilirsiniz:

* [Azure IoT Edge](../iot-edge/index.yml)’i kullanarak, edge üzerinde protokol çevirisi gerçekleştirmek için bir alan ağ geçidi oluşturun.

* Bulutta protokol çevirisi gerçekleştirmek için [Azure IoT protokolü ağ geçidini](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) özelleştirin.

## <a name="quotas-and-limits"></a>Kotalar ve sınırlar

Her Azure aboneliği, hizmet kötüye kullanımını önlemek için varsayılan kota sınırları içerir ve bu sınırlar, IoT çözümünüzün kapsamını etkileyebilir. Abonelik başına geçerli sınır, abonelik başına 50 IoT Hub 'dır. Destek birimiyle görüşerek kota artışı isteyebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub kotaları ve azaltma](iot-hub-devguide-quotas-throttling.md). Kota limitleri hakkında daha fazla bilgi için aşağıdaki makalelerden birine bakın:

* [Azure abonelik hizmeti sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub azaltma ve siz](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>Azure Stack Hub üzerindeki IoT Hub (önizleme)

Azure Stack hub 'da (Önizleme) IoT Hub karma IoT çözümleri oluşturmanıza olanak tanır. IoT Hub, IoT uygulamanız ile yönettiği cihazlar arasındaki çift yönlü iletişim için merkezi bir ileti hub 'ı görevi gören yönetilen bir hizmettir. IoT cihazları ve şirket içi çözümleriniz arasında güvenilir ve güvenli iletişimlerle IoT çözümleri oluşturmak için Azure Stack hub 'ında IoT Hub kullanabilirsiniz.

Azure Stack hub 'ında IoT Hub genel önizleme sırasında ücretsizdir. Daha fazla bilgi için [Azure Stack hub 'ına genel bakış IoT Hub](/azure-stack/operator/iot-hub-rp-overview)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Uçtan uca IoT çözümünü denemek için IoT Hub hızlı başlangıçlarına göz atın:

* [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md)

Azure IoT ile IoT çözümleri oluşturma ve dağıtma yolları hakkında daha fazla bilgi edinmek için şu adresi ziyaret edin:

* [Temel bilgiler: Azure IoT teknolojileri ve çözümleri](../iot-fundamentals/iot-services-and-technologies.md).