---
title: Azure IoT Central kurallar üzerinde Web kancaları oluşturun | Microsoft Docs
description: Kurallar başlatıldığında diğer uygulamalara otomatik olarak bildirimde bulunan Azure IoT Central Web kancaları oluşturun.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953674"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central kurallarında Web kancası eylemleri oluşturma

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Web kancaları, uzaktan izleme ve bildirimler için IoT Central uygulamanızı diğer uygulama ve hizmetlere bağlamanıza olanak tanır. Web kancaları, IoT Central uygulamanızda her bir kural tetiklendiğinde bağlandığınız diğer uygulama ve hizmetlere otomatik olarak bildirim gönderir. IoT Central uygulamanız, bir kural her tetiklendiğinde diğer uygulamanın HTTP uç noktasına bir POST isteği gönderir. Yük, cihaz ayrıntılarını ve kural tetikleyicisi ayrıntılarını içerir.

## <a name="set-up-the-webhook"></a>Web kancasını ayarlama

Bu örnekte, kurallar Web kancaları kullanılarak başlatıldığında bildirim almak için RequestBin 'e bağlanırsınız.

1. [Requestbin](https://requestbin.net/)'i açın.

1. Yeni bir RequestBin oluşturun ve **BIN URL 'sini**kopyalayın.

1. [Telemetri kuralı](howto-create-telemetry-rules.md) veya [olay kuralı](howto-create-event-rules.md)oluşturun. Kuralı kaydedin ve yeni bir eylem ekleyin.

    ![Web kancası oluşturma ekranı](media/howto-create-webhooks/webhookcreate.png)

1. Web kancası eylemini seçin ve bir görünen ad girin ve bin URL 'sini geri çağırma URL 'si olarak yapıştırın.

1. Kuralı kaydedin.

Artık kural tetiklendiğinde RequestBin ' de yeni bir istek göründüğünü görürsünüz.

## <a name="payload"></a>Te

Bir kural tetiklendiğinde, geri çağırma URL 'sine ölçümler, cihaz, kural ve uygulama ayrıntıları ile bir JSON yükü içeren bir HTTP POST isteği yapılır. Bir telemetri kuralı için yük aşağıdaki gibi görünür:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Bilinen sınırlamalar

Şu anda bir API aracılığıyla bu Web kancalarını abone yapmanın/aboneliğini kaldırın bir programlama yolu yoktur.

Bu özelliği geliştirme hakkında fikirler varsa, önerilerinizi [UserVoice forumumuza](https://feedback.azure.com/forums/911455-azure-iot-central)gönderin.

## <a name="next-steps"></a>Sonraki adımlar

Web kancalarını nasıl ayarlayıp kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [Microsoft Flow iş akışı oluşturmayı](howto-add-microsoft-flow.md)araştırmaya yönelik olur.
