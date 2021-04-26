---
title: Özel uyarılar oluşturma
description: IoT güvenlik hizmeti için Azure Defender için özel cihaz uyarılarını anlayın, oluşturun ve atayın.
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 5d9bb7811396579ec9096715809a101aebbf36a3
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384553"
---
# <a name="create-custom-alerts"></a>Özel uyarılar oluşturma

Özel güvenlik grupları ve uyarılar kullanarak, IoT çözümünüz genelinde daha iyi güvenlik sağlamak için uçtan uca güvenlik bilgilerinin ve kategorik cihaz bilgilerinin tam avantajlarından yararlanır.

## <a name="why-use-custom-alerts"></a>Özel uyarılar neden kullanılmalıdır?

IoT cihazlarınızı en iyi şekilde anlarsınız.

Beklenen cihaz davranışlarını tam olarak anlayan müşteriler için, IoT için Defender, bu anlalamayı, beklenen, normal davranıştaki herhangi bir sapmayla ilgili bir cihaz davranış ilkesi ve uyarısına çevirmenizi sağlar.

## <a name="security-groups"></a>Güvenlik grupları

Güvenlik grupları, mantıksal cihaz gruplarını tanımlamanızı ve güvenlik durumlarını merkezi bir şekilde yönetmenizi sağlar.

Bu gruplar belirli donanımlar olan cihazları, belirli bir konumda dağıtılan cihazları veya özel gereksinimlerinize uygun diğer grupları temsil edebilir.

Güvenlik grupları, **SecurityGroup** adlı bir Device ikizi Tag özelliği tarafından tanımlanır. Varsayılan olarak, IoT Hub her bir IoT çözümünün **varsayılan** adlı bir güvenlik grubu vardır. Bir cihazın güvenlik grubunu değiştirmek için **SecurityGroup** özelliğinin değerini değiştirin.

Örnek:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Cihazlarınızı mantıksal kategoriler halinde gruplandırmak için güvenlik gruplarını kullanın. Grupları oluşturduktan sonra, en etkili uçtan uca IoT güvenlik çözümü için bunları tercih ettiğiniz özel uyarılara atayın.

## <a name="customize-an-alert"></a>Bir uyarıyı özelleştirme

1. IoT Hub açın ve **güvenlik** menüsünden **Ayarlar** ' ı seçin.

1. **Özel uyarılar**' ı seçin.

1. Özelleştirmeyi uygulamak istediğiniz güvenlik grubunu seçin.

1. **Özel uyarı Ekle**' yi seçin.

1. Açılan listeden özel bir uyarı seçin.

1. Gerekli özellikleri düzenleyin, **Tamam**' ı seçin.

1. **Kaydet**' i seçtiğinizden emin olun. Yeni uyarının kaydedilmeksizin, IoT Hub bir sonraki kapatıyorsunuz uyarı silinir.

## <a name="alerts-available-for-customization"></a>Özelleştirmeler için kullanılabilir uyarılar

IoT için Defender, özel gereksinimlerinize göre özelleştirilebilen çok sayıda uyarı sunmaktadır. Uyarı önem derecesi, veri kaynağı, açıklama ve her uyarının alındığı zaman ve önerdiğimiz düzeltme adımlarımızın [özelleştirilebilir uyarı tablosunu](concept-customizable-security-alerts.md) gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

Bir güvenlik aracısının nasıl dağıtılacağını öğrenmek için bir sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Güvenlik aracısı dağıtma](how-to-deploy-agent.md)
