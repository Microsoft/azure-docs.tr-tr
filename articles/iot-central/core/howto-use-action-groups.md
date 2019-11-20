---
title: Azure IoT Central kuralından birden çok eylem çalıştırma | Microsoft Docs
description: Tek bir IoT Central kuralından birden çok eylem çalıştırın ve birden çok kuraldan çalıştırabileceğiniz yeniden kullanılabilir eylem grupları oluşturun.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: cc89033cedcdf185ce7e18c3981045e2a20569f5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952179"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Birden çok eylemi bir veya daha fazla kuralla çalışacak şekilde gruplandırma

*Bu makale, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central, bir koşul karşılandığında eylemleri çalıştırmak için kurallar oluşturursunuz. Kurallar cihaz telemetrisini veya olaylarını temel alır. Örneğin, bir cihazdaki sıcaklık bir eşiği aştığında bir işlece bildirimde bulunabilir. Bu makalede, bir IoT Central kuralına birden çok eylem eklemek için [Azure izleyici](../../azure-monitor/overview.md) *eylem gruplarının* nasıl kullanılacağı açıklanır. Birden çok kurala bir eylem grubu ekleyebilirsiniz. Bir [eylem grubu](../../azure-monitor/platform/action-groups.md) , bir Azure aboneliğinin sahibi tarafından tanımlanan bildirim tercihleri koleksiyonudur.

## <a name="prerequisites"></a>Önkoşullar

- Kullandıkça Öde uygulaması
- Azure Izleyici eylem grupları oluşturmak ve yönetmek için bir Azure hesabı ve aboneliği

## <a name="create-action-groups"></a>Eylem grubu oluşturma

Azure portal veya [Azure Resource Manager şablonuyla](../../azure-monitor/platform/action-groups-create-resource-manager-template.md) [eylem grupları oluşturabilir ve yönetebilirsiniz](../../azure-monitor/platform/action-groups.md) .

Bir eylem grubu şunları yapabilir:

- E-posta, SMS veya sesli çağrı oluşturma gibi bildirimleri gönderin.
- Web kancası çağırma gibi bir eylem çalıştırın.

Aşağıdaki ekran görüntüsünde, e-posta ve SMS bildirimleri gönderen ve bir Web kancası çağıran bir eylem grubu gösterilmektedir:

![Eylem grubu](media/howto-use-action-groups/actiongroup.png)

Bir IoT Central kuralında bir eylem grubunu kullanmak için, eylem grubunun IoT Central uygulamayla aynı Azure aboneliğinde olması gerekir.

## <a name="use-an-action-group"></a>Bir eylem grubu kullanma

IoT Central uygulamanızda bir eylem grubu kullanmak için önce bir telemetri veya olay kuralı oluşturun. Kurala bir eylem eklediğinizde **Azure Izleyici eylem grupları**' nı seçin:

![Eylem seçin](media/howto-use-action-groups/chooseaction.png)

Azure aboneliğinizden bir eylem grubu seçin:

![Eylem grubu seçin](media/howto-use-action-groups/chooseactiongroup.png)

**Kaydet**’i seçin. Eylem grubu artık kural tetiklendiğinde çalıştırılacak Eylemler listesinde görüntülenir:

![Eylem grubu kaydedildi](media/howto-use-action-groups/savedactiongroup.png)

Aşağıdaki tabloda, desteklenen eylem türlerine gönderilen bilgiler özetlenmektedir:

| Eylem türü | Çıkış biçimi |
| ----------- | -------------- |
| E-posta       | Standart IoT Central e-posta şablonu |
| SMS         | Azure IoT Central uyarısı: $ {applicationName}-"$ {ruleName}", $ {triggerDate} $ {triggerTime} konumunda "$ {aygıtadı}" üzerinde tetiklendi |
| Ses       | Azure ı. O. T orta uyarı: "$ {ruleName}" kuralı $ {triggerDate} $ {triggerTime} konumunda "$ {aygıtadı}" cihazında tetiklendi; uygulama $ {applicationName} |
| Web Kancası     | {"SchemaId": "AzureIoTCentralRuleWebhook", "Data": {[Regular Web kancası yükü](#payload)}} |

Aşağıdaki metin bir eylem grubundan örnek bir SMS iletisidir:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>Aşağıdaki JSON örnek bir Web kancası eylem yükünü göstermektedir:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık eylem gruplarını kurallarla nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [cihazlarınızın nasıl yönetileceğini](howto-manage-devices.md)öğrenirsiniz.
