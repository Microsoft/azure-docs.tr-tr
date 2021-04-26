---
title: Azure IoT Central kuralından birden çok eylem çalıştırma | Microsoft Docs
description: Tek bir IoT Central kuralından birden çok eylem çalıştırın ve birden çok kuraldan çalıştırabileceğiniz yeniden kullanılabilir eylem grupları oluşturun.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 84a723023bf8614b837ba9783bae987403921c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585490"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Birden çok eylemi bir veya daha fazla kuralla çalışacak şekilde gruplandırma

*Bu makale, oluşturucular ve yöneticiler için geçerlidir.*

Azure IoT Central, bir koşul karşılandığında eylemleri çalıştırmak için kurallar oluşturursunuz. Kurallar cihaz telemetrisini veya olaylarını temel alır. Örneğin, bir cihazın sıcaklığı bir eşiği aştığında bir işlece bildirimde bulunabilir. Bu makalede, bir IoT Central kuralına birden çok eylem eklemek için [Azure izleyici](../../azure-monitor/overview.md) *eylem gruplarının* nasıl kullanılacağı açıklanır. Birden çok kurala bir eylem grubu ekleyebilirsiniz. Bir [eylem grubu](../../azure-monitor/alerts/action-groups.md) , bir Azure aboneliğinin sahibi tarafından tanımlanan bildirim tercihleri koleksiyonudur.

## <a name="prerequisites"></a>Önkoşullar

- Standart fiyatlandırma planı kullanılarak oluşturulan bir uygulama
- Azure Izleyici eylem grupları oluşturmak ve yönetmek için bir Azure hesabı ve aboneliği

## <a name="create-action-groups"></a>Eylem grubu oluşturma

Azure portal veya [Azure Resource Manager şablonuyla](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md) [eylem grupları oluşturabilir ve yönetebilirsiniz](../../azure-monitor/alerts/action-groups.md) .

Bir eylem grubu şunları yapabilir:

- E-posta, SMS veya sesli çağrı oluşturma gibi bildirimleri gönderin.
- Web kancası çağırma gibi bir eylem çalıştırın.

Aşağıdaki ekran görüntüsünde, e-posta ve SMS bildirimleri gönderen ve bir Web kancası çağıran bir eylem grubu gösterilmektedir:

![Eylem grubu](media/howto-use-action-groups/actiongroup.png)

Bir IoT Central kuralında bir eylem grubunu kullanmak için, eylem grubunun IoT Central uygulamayla aynı Azure aboneliğinde olması gerekir.

## <a name="use-an-action-group"></a>Bir eylem grubu kullanma

IoT Central uygulamanızda bir eylem grubu kullanmak için önce bir kural oluşturun. Kurala bir eylem eklediğinizde **Azure Izleyici eylem grupları**' nı seçin:

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
| Web Kancası     | {"SchemaId": "AzureIoTCentralRuleWebhook", "Data": {[Regular Web kancası yükü](howto-create-webhooks.md#payload)}} |

Aşağıdaki metin bir eylem grubundan örnek bir SMS iletisidir:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Sonraki adımlar

Artık eylem gruplarını kurallarla nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [cihazlarınızın nasıl yönetileceğini](howto-manage-devices.md)öğrenirsiniz.
