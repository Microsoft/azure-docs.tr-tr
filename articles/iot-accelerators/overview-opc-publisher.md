---
title: OPC yayımcısı-Azure | Microsoft Docs
description: Bu makale OPC yayımcısının özelliklerine genel bakış sağlar. Azure IoT Hub bir JSON yükü kullanarak kodlanmış JSON telemetri verilerini yayımlamanıza olanak sağlar.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824787"
---
# <a name="what-is-opc-publisher"></a>OPC yayımcısı nedir?

OPC yayımcısı, nasıl yapılacağını gösteren bir başvuru uygulamasıdır:

- Mevcut OPC UA sunucularına bağlanın.
- Azure IoT Hub için bir JSON yükü kullanarak OPC UA pub/Sub biçimindeki OPC UA sunucularından JSON kodlu telemetri verilerini yayımlayın.

Azure IoT Hub istemci SDK 'sının desteklediği aktarım protokollerinden herhangi birini kullanabilirsiniz: HTTPS, AMQP ve MQTT.

Başvuru uygulama şunları içerir:

- Ağınızda bulunan mevcut OPC UA sunucularına bağlanmak için OPC UA *istemcisi* .
- 62222 numaralı bağlantı noktasındaki OPC UA *sunucusu* , nelerin yayımlandığını yönetmek için kullanabileceğiniz ve doğrudan aynı yapmak için IoT Hub doğrudan yöntemler sunmaktadır.

GitHub 'dan [OPC yayımcı başvuru uygulamasını](https://github.com/Azure/iot-edge-opc-publisher) indirebilirsiniz.

Uygulama .NET Core teknolojisi kullanılarak uygulanır ve .NET Core tarafından desteklenen herhangi bir platformda çalıştırılabilir.

OPC yayımcısı, belirli bir etkin tutma isteğine yanıt vermeyen uç noktalara bağlantı kurmak için yeniden deneme mantığı uygular. Örneğin, bir güç kesintisi nedeniyle OPC UA sunucusu yanıt vermemeye başlıyor.

Bir OPC UA sunucusuna her ayrı yayımlama aralığı için, uygulama, bu yayımlama aralığındaki tüm düğümlerin güncelleştirildiği ayrı bir abonelik oluşturur.

OPC yayımcısı, ağ yükünü azaltmak için IoT Hub gönderilen verilerin toplu olarak toplu olarak kullanılmasını destekler. Bu toplu işlem, yalnızca yapılandırılan paket boyutuna ulaşıldığında IoT Hub bir paket gönderir.

Bu uygulama, NuGet paketleri olarak OPC Foundation OPC UA başvuru yığınını kullanır. Lisanslama koşulları için [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) bakın.

### <a name="next-steps"></a>Sonraki adımlar

Artık OPC yayımcısının ne olduğunu öğrendiğinize göre, önerilen sonraki adım [OPC yayımcısını nasıl yapılandıracağınızı](howto-opc-publisher-configure.md)öğrenirsiniz.
