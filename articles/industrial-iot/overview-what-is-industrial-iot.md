---
title: Azure endüstriyel IoT 'ye Genel Bakış
description: Bu makalede endüstriyel IoT 'ye genel bakış sunulmaktadır. Bu, IIoT 'de atölye bağlantısı ve güvenlik bileşenlerini açıklamaktadır.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813281"
---
# <a name="what-is-industrial-iot-iiot"></a>Endüstriyel IoT (IIoT) nedir?

![Endüstriyel IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure endüstriyel Nesnelerin İnterneti (IIoT), bulutun gücünü endüstriyel ve üretim dükkanı halinde tümleştiren bir Azure modülleri ve hizmetleri paketidir. [Açık Platform iletişimleri Birleşik mimarisi (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/)gibi sektör standardı açık arabirimleri kullanarak Azure IIoT, zaten fabrika tabanınızdaki Azure bulutuna kadar çalışan bir dahil olmak üzere varlıklardan ve sensörlerden veri tümleştirme olanağı sağlar. Verilerinizin bulutta olması, dönüşüm açısından iş ve endüstriyel süreçler geliştirmeye yönelik geri bildirimde bulunmak için daha hızlı ve esnek bir şekilde kullanılmasını sağlar.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Azure ile endüstriyel varlıklarınızı bulun, kaydedin ve yönetin

Azure endüstriyel IoT, Tesis işleçleri 'nin bir fabrika ağında OPC UA özellikli sunucularını bulmasına ve Azure IoT Hub 'e kaydolmalarına olanak tanır. Operasyon personeli, dünyanın herhangi bir yerinden fabrika katından etkinliklere abone olabilir ve bu olaylara yanıt verebilir, uyarılar ve alarmlar alabilir ve bunlara gerçek zamanlı olarak tepki verebilir.

IIoT, OPC UA işlevselliği uygulayan bir mikro hizmet kümesi sağlar. Mikro hizmetler REST API 'Leri OPC UA Hizmetleri kenar tarafını yansıtır. Azure Active Directory (AAD) tarafından desteklenen OAUTH kimlik doğrulaması ve yetkilendirme kullanılarak güvenli hale getirilir. Bu, bulut uygulamalarınızın sunucu adres alanlarına gözatmasını veya okuma/yazma değişkenlerini ve HTTPS ve basit OPC UA JSON yüklerini kullanarak yöntemleri yürütmesini sağlar. Edge Hizmetleri Azure IoT Edge modüller olarak uygulanır ve şirket içinde çalıştırılır. Bulut mikro hizmetleri, ASP.NET mikro hizmetleri olarak bir REST arabirimiyle uygulanır ve yönetilen Azure Kubernetes hizmetlerinde veya tek başına Azure App Service çalışır. Hem Edge hem de bulut hizmetlerinde, IIoT, Microsoft Container Registry (MCR) içinde önceden oluşturulmuş Docker kapsayıcıları sağlar ve bu adım müşteri için bu adımı ortadan kaldırır. Kenar ve bulut hizmetleri birbirini kullanır ve birlikte kullanılması gerekir. IIoT, tek bir komutla platformun tamamını dağıtmaya imkan tanıyan kullanımı kolay dağıtım betikleri de sağlar.

Ayrıca, REST API 'Leri, sunulan açık API belirtimi (Swagger) aracılığıyla herhangi bir programlama diliyle birlikte kullanılabilir. Bu, OPC UA 'yi bulut yönetim çözümleriyle tümleştirirken, geliştiricilerin becerileri, ilgi alanları ve mimari seçenekleriyle eşleşen teknolojiyi seçebilmeleri için ücretsizdir. Örneğin, bir alarm ve olay panosu için uygulama geliştiren bir tam yığın Web geliştiricisi, OPC UA SDK, C, C++, Java veya C# üzerinde en fazla şekilde JavaScript veya TypeScript 'te olaylara yanıt vermek için mantık yazabilir.

## <a name="manage-certificates-and-trust-groups"></a>Sertifikaları ve güven gruplarını yönetme

OPC UA istemcisini sunucu iletişimine güvenli hale getirmek için Azure endüstriyel IoT, OPC UA uygulama sertifikalarını ve fabrika kat makineleri ile denetim sistemlerinin güven listelerini yönetir. Hangi istemcinin hangi sunucuyla iletişim kurmasına izin verileceğini kısıtlar. Özel anahtarların depolanması ve sertifikaların imzalanması, donanım tabanlı güvenliği (HSM) destekleyen Azure Key Vault tarafından desteklenir.

## <a name="industrial-iot-components"></a>Endüstriyel IoT bileşenleri

Azure IIoT çözümleri, belirli bileşenlerden oluşturulmuştur. Bunlar aşağıdakileri içerir.

- **En az bir Azure IoT Hub.**
- **Cihazlar IoT Edge.**
- **Endüstriyel Edge modülleri.**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub] ( https://azure.microsoft.com/services/iot-hub/ herhangi bir IoT uygulaması ile yönettiği cihazlar arasında güvenli, çift yönlü iletişimler için bir merkezi ileti hub 'ı görevi görür. Bu, açık kaynak SDK 'Ları ve birden çok protokolü destekleyen açık ve esnek bir bulut platformudur (PaaS). 

Endüstriyel ve iş verilerinizi bir IoT Hub toplamak, verilerinizi güvenli bir şekilde depolamanıza, iş ve verimlilik analizlerini gerçekleştirmenize ve bundan raporlar oluşturmanıza imkan tanır. Ayrıca, birleştirilmiş verilerinize [Power BI](https://powerbi.microsoft.com)gibi Microsoft Azure hizmetleri ve araçları da uygulayabilirsiniz.

### <a name="iot-edge-devices"></a>IoT Edge cihazlar
[Edge hizmetleri](https://azure.microsoft.com/services/iot-edge/) Azure IoT Edge modüller olarak uygulanır ve şirket içinde çalıştırılır. Bulut mikro hizmetleri, ASP.NET mikro hizmetleri olarak bir REST arabirimiyle uygulanır ve yönetilen Azure Kubernetes hizmetlerinde veya tek başına Azure App Service çalışır. Hem Edge hem de bulut hizmetleri için, Microsoft Container Registry (MCR) ' de önceden oluşturulmuş Docker Kapsayıcıları sağladık, bu adım müşteri için kaldırılıyor. Kenar ve bulut hizmetleri birbirini kullanır ve birlikte kullanılması gerekir. Ayrıca, tek bir komutla platformun tamamını dağıtmaya izin veren kullanımı kolay dağıtım betikleri sunuyoruz.

IoT Edge bir cihaz Edge çalışma zamanı ve kenar modülleriyle oluşur.
- **Edge modülleri** , OPC yayımcısı ve OPC ikizi gibi en küçük hesaplama birimi olan Docker kapsayıcılarıdır. 
- **Edge cihazı** , OPC UA sunucusu ve bulutta IoT Hub arasında ortalama işlevi gören bu tür modülleri dağıtmak için kullanılır.

### <a name="industrial-edge-modules"></a>Endüstriyel Edge modülleri
- **OPC yayımcısı**: opc yayımcısı IoT Edge içinde çalışır. OPC UA sunucularına bağlanır ve OPC UA "pub/Sub" biçimindeki bu sunuculardan JSON kodlu telemetri verilerini Azure IoT Hub yayımlar. Azure IoT Hub istemci SDK 'Sı tarafından desteklenen tüm aktarım protokolleri, yani HTTPS, AMQP ve MQTT kullanılabilir.
- **OPC ikizi**: OPC ikizi, bulutu ve Factory ağını bağlamak için Azure IoT Edge ve IoT Hub kullanan mikro hizmetlerden oluşur. OPC Ikizi, REST API 'Leri aracılığıyla endüstriyel cihazların keşif, kayıt ve uzaktan denetimini sağlar. OPC Ikizi, OPC Birleşik mimarisi (OPC UA) SDK 'Sı gerektirmez. Programlama dili belirsiz olur ve sunucusuz bir iş akışına dahil edilebilir.
- **Bulma**: Bulucu kimliğiyle temsil edilen bulma modülü, OPC UA sunucu bulmayı içeren, uç üzerinde bulma hizmetleri sağlar. Bulma yapılandırıldıysa ve etkinleştirilirse, modül IoT Edge ve ekleme hizmeti için telemetri yolu IoT Hub aracılığıyla bir tarama araştırması sonuçlarını gönderir. Hizmet sonuçları işler ve kayıt defterindeki tüm ilgili kimlikleri günceller.

## <a name="next-steps"></a>Sonraki adımlar
Endüstriyel IoT 'nin ne olduğunu öğrendiğinize göre, endüstriyel IoT Platformu ve OPC yayımcısı hakkında bilgi edinebilirsiniz:

> [!div class="nextstepaction"]
> [OPC yayımcısı nedir?](overview-what-is-opc-publisher.md)