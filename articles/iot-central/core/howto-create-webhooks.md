---
title: Azure IoT Central kurallar üzerinde Web kancaları oluşturun | Microsoft Docs
description: Kurallar başlatıldığında diğer uygulamalara otomatik olarak bildirimde bulunan Azure IoT Central Web kancaları oluşturun.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87337237"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central'da kurallar üzerinde web kancası eylemleri oluşturma

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

Web kancaları, uzaktan izleme ve bildirimler için IoT Central uygulamanızı diğer uygulama ve hizmetlere bağlamanıza olanak tanır. Web kancaları, IoT Central uygulamanızda her bir kural tetiklendiğinde bağlandığınız diğer uygulama ve hizmetlere otomatik olarak bildirim gönderir. IoT Central uygulamanız, bir kural her tetiklendiğinde diğer uygulamanın HTTP uç noktasına bir POST isteği gönderir. Yük, cihaz ayrıntılarını ve kural tetikleyicisi ayrıntılarını içerir.

## <a name="set-up-the-webhook"></a>Web kancasını ayarlama

Bu örnekte, kurallar Web kancaları kullanılarak başlatıldığında bildirim almak için RequestBin 'e bağlanırsınız.

1. [Requestbin](https://requestbin.net/)'i açın.

1. Yeni bir RequestBin oluşturun ve **BIN URL 'sini** kopyalayın.

1. [Telemetri kuralı](tutorial-create-telemetry-rules.md)oluşturun. Kuralı kaydedin ve yeni bir eylem ekleyin.

    ![Web kancası oluşturma ekranı](media/howto-create-webhooks/webhookcreate.png)

1. Web kancası eylemini seçin ve bir görünen ad girin ve bin URL 'sini geri çağırma URL 'si olarak yapıştırın.

1. Kuralı kaydedin.

Artık kural tetiklendiğinde RequestBin ' de yeni bir istek göründüğünü görürsünüz.

## <a name="payload"></a>Te

Bir kural tetiklendiğinde Telemetriyi, cihazı, kuralı ve uygulama ayrıntılarını içeren bir JSON yükü içeren geri çağırma URL 'SI için HTTP POST isteği yapılır. Yük aşağıdaki gibi görünebilir:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Kural, toplanan telemetrileri bir süre içinde iz, yük farklı bir telemetri bölümü içerecektir.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Veri biçimi değişiklik bildirimi

**3 nisan 2020**' den önce oluşturulmuş ve kaydedilmiş bir veya daha fazla Web kancası varsa, Web kancasını silmeniz ve yeni bir Web kancası oluşturmanız gerekir. Bunun nedeni, eski Web kancalarının gelecekte kullanım dışı bırakılacak eski bir yük biçimi kullanması nedeniyle oluşur.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Web kancası yükü (biçim 3 Nisan 2020 itibariyle kullanım dışı)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Bilinen sınırlamalar

Şu anda bir API aracılığıyla bu Web kancalarını abone yapmanın/aboneliğini kaldırın bir programlama yolu yoktur.

Bu özelliği geliştirme hakkında fikirler varsa, önerilerinizi [Kullanıcı sesli forumumuza](https://feedback.azure.com/forums/911455-azure-iot-central)gönderin.

## <a name="next-steps"></a>Sonraki adımlar

Web kancalarını nasıl ayarlayıp kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [Azure Izleyici eylem gruplarını yapılandırmayı](howto-use-action-groups.md)araştırmaya yönelik olur.
