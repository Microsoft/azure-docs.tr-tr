---
title: IoT Hub 'ınızın IP adresini anlama | Microsoft Docs
description: IoT Hub IP adresinizi ve özelliklerini sorgulamayı öğrenin. IoT Hub 'ınızın IP adresi, olağanüstü durum kurtarma veya bölgesel yük devretme gibi belirli senaryolar sırasında değiştirilebilir.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874180"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP adresleri

Ortak uç noktaların IoT Hub IP adresi önekleri, düzenli aralıklarla _AzureIoTHub_ [Service etiketi](../virtual-network/service-tags-overview.md)altında yayımlanır.

> [!NOTE]
> Azure IoT Hub, şirket içi ağların içine dağıtılan cihazlar için özel uç noktalarla VNET bağlantısı tümleştirmesini destekler. Daha fazla bilgi için bkz. [VNET için IoT Hub desteği](./virtual-network-support.md) .


Bu IP adresi öneklerini, çeşitli ağ yalıtımı hedefleri uygulamak üzere IoT Hub ile cihazlarınız veya Ağ varlıkları arasındaki bağlantıyı denetlemek için kullanabilirsiniz:

| Hedef | İlgili senaryolar | Yaklaşım |
|------|-----------|----------|
| Cihazlarınızın ve hizmetlerinizin yalnızca IoT Hub uç noktalarıyla iletişim kurmasını sağlayın | [Cihazdan buluta](./iot-hub-devguide-messaging.md)ve [buluttan cihaza](./iot-hub-devguide-messages-c2d.md) mesajlaşma, [doğrudan Yöntemler](./iot-hub-devguide-direct-methods.md), [Cihaz ve modül TWINS](./iot-hub-devguide-device-twins.md) ve [cihaz akışları](./iot-hub-device-streams-overview.md) | IoT Hub ve Olay Hub 'ı IP adresi öneklerini bulma ve bu IP adresi önekleri için cihazlarınızda ' ve hizmetler ' güvenlik duvarı ayarında ızın verme kurallarını yapılandırma için _AzureIoTHub_ ve _EventHub_ hizmet etiketlerini kullanın; cihazların veya hizmetlerin iletişim kurmasını istemediğiniz trafiği diğer hedef IP adreslerine bırakın. |
| IoT Hub cihaz uç noktanızın yalnızca cihazlarınızdan ve ağ varlıklarınızdan bağlantı aldığından emin olun | [Cihazdan buluta](./iot-hub-devguide-messaging.md)ve [buluttan cihaza](./iot-hub-devguide-messages-c2d.md) mesajlaşma, [doğrudan Yöntemler](./iot-hub-devguide-direct-methods.md), [Cihaz ve modül TWINS](./iot-hub-devguide-device-twins.md) ve [cihaz akışları](./iot-hub-device-streams-overview.md) | Cihazlarınızdan ve ağ varlık IP adreslerinden gelen bağlantılara izin vermek için IoT Hub [IP filtresi özelliğini](iot-hub-ip-filtering.md) kullanın (bkz. [sınırlamalar](#limitations-and-workarounds) bölümü). | 
| Rotalarınızın özel uç nokta kaynaklarına (depolama hesapları, hizmet veri yolu ve Olay Hub 'ları) yalnızca ağ varlıklarınızdan erişilebildiğinden emin olun | [İleti yönlendirme](./iot-hub-devguide-messages-d2c.md) | Bağlantıyı kısıtlama (örneğin, [güvenlik duvarı kuralları](../storage/common/storage-network-security.md), [özel bağlantılar](../private-link/private-endpoint-overview.md)veya [hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md)aracılığıyla) için kaynak kılavuzunu izleyin; IoT Hub IP adresi öneklerini bulma ve kaynağınızın güvenlik duvarı yapılandırmasındaki bu IP önekleri için ızın verme kuralları ekleme (bkz. [sınırlamalar](#limitations-and-workarounds) bölümü) için _AzureIoTHub_ Service etiketlerini kullanın. |



## <a name="best-practices"></a>En iyi uygulamalar

* Cihazlarınızda ızın verme kuralları eklerken, [uygun protokoller tarafından kullanılan belirli bağlantı noktalarını](./iot-hub-devguide-protocols.md#port-numbers)sağlamak en iyisidir.

* IoT Hub 'ının IP adresi önekleri değişebilir. Bu değişiklikler, etkin olmadan önce hizmet etiketleri aracılığıyla düzenli olarak yayımlanır. Bu nedenle, en son hizmet etiketlerini düzenli olarak almak ve kullanmak için işlem geliştirmeniz önemlidir. Bu işlem, [hizmet etiketleri bulma API 'si](../virtual-network/service-tags-overview.md#service-tags-on-premises)aracılığıyla otomatikleştirilebilir. Service Tags bulma API 'sinin hala önizlemede olduğunu ve bazı durumlarda etiketlerin ve IP adreslerinin tam listesini oluşturmadığını unutmayın. Keşif API 'SI genel kullanıma hazır olana kadar, [INDIRILEBILIR JSON biçiminde hizmet etiketlerini](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)kullanmayı göz önünde bulundurun. 

* AzureIoTHub kullanın *. [ bölge adı]* etiketi, belirli bir bölgedeki IoT Hub uç noktaları tarafından kullanılan IP öneklerini belirler. Veri merkezi olağanüstü durum kurtarmaya veya [bölgesel yük devretmeye](iot-hub-ha-dr.md) yönelik hesaba gitmek için IoT Hub coğrafi ÇIFT bölgesinin IP öneklerine yönelik bağlantıların de etkinleştirildiğinden emin olun.

* IoT Hub 'de güvenlik duvarı kuralları ayarlamak, IoT Hub Azure CLı ve PowerShell komutlarını çalıştırmak için gereken bağlantıyı engelleyebilir. Bunu önlemek için, istemcilerin IP adresi öneklerine ızın ver kuralları ekleyerek CLı veya PowerShell istemcilerinin IoT Hub iletişim kurmasını sağlayabilirsiniz.  


## <a name="limitations-and-workarounds"></a>Sınırlamalar ve geçici çözümler

* IoT Hub IP Filtresi özelliği 100 kurallara sınırlıdır. Bu sınır, Azure müşteri desteği aracılığıyla istekler aracılığıyla oluşturulabilir. 

* Yapılandırılmış [IP filtreleme kurallarınız](iot-hub-ip-filtering.md) , IoT Hub 'ının yerleşik Olay Hub 'ı uç noktasında değil, yalnızca IoT Hub IP uç noktalarına uygulanır. Ayrıca, iletilerinizin depolandığı Olay Hub 'ında IP filtrelemesinin uygulanmasını istiyorsanız, istediğiniz IP filtreleme kurallarınızı doğrudan yapılandırabileceğiniz bir olay hub 'ı kaynağını izleyebilirsiniz. Bunu yapmak için kendi olay hub 'ınızın kaynağını sağlamanız ve [ileti yönlendirmeyi](./iot-hub-devguide-messages-d2c.md) , IoT Hub yerleşik Olay Hub 'ı yerine bu kaynağa göndermek için ayarlamanız gerekir. Son olarak, yukarıdaki tabloda açıklandığı gibi, ileti yönlendirme işlevini etkinleştirmek için, IoT Hub IP adresi öneklerinden sağlanan olay hub kaynağınız için bağlantıya izin vermeniz gerekir.

* Bir depolama hesabına yönlendirdiğinde, IoT Hub IP adresi öneklerinden gelen trafiğe izin vermek, yalnızca depolama hesabı IoT Hub olarak farklı bir bölgedeyse mümkündür.

## <a name="support-for-ipv6"></a>IPv6 için destek 

IPv6 Şu anda IoT Hub desteklenmiyor.
