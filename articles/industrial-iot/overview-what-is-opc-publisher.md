---
title: Microsoft OPC yayımcısı
description: Bu makalede OPC Publisher Edge modülüne genel bakış sunulmaktadır.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816179"
---
# <a name="what-is-the-opc-publisher"></a>OPC yayımcısı nedir?

OPC yayımcısı, endüstriyel varlıklar ve Microsoft Azure bulutu arasındaki boşluğu bağlayan, tam olarak desteklenen bir Microsoft ürünüdür. OPC UA özellikli varlıkları veya endüstriyel bağlantı yazılımlarını Microsoft Azure buluta bağlayarak bunu yapar. Azure IoT Hub topladığı telemetri verilerini IEC62541 OPC UA PubSub standart biçimi (sürüm 2,6 ' den başlayarak) gibi çeşitli biçimlerde yayımlar. OPC yayımcısı, Azure IoT Edge bir modül olarak veya düz Docker üzerinde bir kapsayıcı olarak çalışır. .NET platformlar arası çalışma zamanının kullandığından, hem Linux hem de Windows 10 ' da yerel olarak çalışır.

OPC yayımcısı, nasıl yapılacağını gösteren bir başvuru uygulamasıdır:

- Mevcut OPC UA sunucularına bağlanın.
- Azure IoT Hub bir JSON yükü kullanarak OPC UA pub/Sub biçimindeki OPC UA sunucularından JSON kodlu telemetri verilerini yayımlayın.

HTTPS, AMQP ve MQTT gibi Azure IoT Hub istemci SDK 'sının desteklediği aktarım protokollerinden herhangi birini kullanabilirsiniz.

Başvuru uygulamasına aşağıdakiler dahildir.

- Ağınızda bulunan mevcut OPC UA sunucularına bağlanmak için OPC UA *istemcisi* .
- 62222 numaralı bağlantı noktasındaki OPC UA *sunucusu* , nelerin yayımlandığını yönetmek için kullanabileceğiniz ve doğrudan aynı yapmak için IoT Hub doğrudan yöntemler sunmaktadır.

GitHub 'dan [OPC yayımcı başvuru uygulamasını](https://github.com/Azure/iot-edge-opc-publisher) indirebilirsiniz.

Uygulama .NET Core teknolojisi kullanılarak uygulanır ve .NET Core tarafından desteklenen herhangi bir platformda çalıştırılabilir.

## <a name="what-does-the-opc-publisher-do"></a>OPC yayımcısı ne yapar?

OPC yayımcısı, belirli bir etkin tutma isteğine yanıt vermeyen uç noktalara bağlantı kurmak için yeniden deneme mantığı uygular. Örneğin, bir güç kesintisi nedeniyle OPC UA sunucusu yanıt vermemeye başlıyor.

Bir OPC UA sunucusuna her ayrı yayımlama aralığı için, uygulama, bu yayımlama aralığındaki tüm düğümlerin güncelleştirildiği ayrı bir abonelik oluşturur.

OPC yayımcısı, ağ yükünü azaltmak için IoT Hub gönderilen verilerin toplu olarak toplu olarak kullanılmasını destekler. Bu toplu işlem, yalnızca yapılandırılan paket boyutuna ulaşıldığında IoT Hub bir paket gönderir.

Bu uygulama, NuGet paketleri olarak OPC Foundation OPC UA başvuru yığınını kullanır. [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/)Lisanslama koşulları için bkz..

## <a name="next-steps"></a>Sonraki adımlar
Artık OPC yayımcısının ne olduğunu öğrendiğinize göre, bunu dağıtarak kullanmaya başlayın:

> [!div class="nextstepaction"]
> [OPC yayımcısını tek başına modda dağıtma](tutorial-publisher-deploy-opc-publisher-standalone.md)
