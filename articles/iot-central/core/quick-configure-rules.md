---
title: Hızlı başlangıç-Azure IoT Central kuralları ve eylemleri yapılandırma
description: Bu hızlı başlangıçta, Azure IoT Central uygulamanızda telemetri tabanlı kuralların ve eylemlerin nasıl yapılandırılacağı hakkında bir Oluşturucu olarak gösterilir.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589015"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Hızlı Başlangıç: Azure IoT Central’da cihazınız için kurallar ve eylemler yapılandırma

Bu hızlı başlangıçta, bir cihaz algılayıcısı tarafından bildirilen nem %55 ' ü aştığında bir e-posta gönderen bir kural oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bir önceki hızlı başlangıç [oluşturma Azure IoT Central uygulaması oluşturmanız](./quick-deploy-iot-central.md) ve birlikte çalışmak üzere **algılayıcı denetleyicisi** cihaz şablonunu oluşturmak için [IoT Central uygulamanıza sanal bir cihaz eklemeniz](./quick-create-simulated-device.md) gerekir.

## <a name="create-a-telemetry-based-rule"></a>Telemetri tabanlı kural oluşturma

1. Uygulamanıza yeni bir telemetri tabanlı kural eklemek için, sol bölmede **kurallar**' ı seçin.

1. Yeni bir kural oluşturmak için **+ Yeni**' yi seçin.

1. Kural adı olarak **çevresel nem** girin.

1. **Hedef cihazlar** bölümünde, cihaz şablonu olarak **algılayıcı denetleyicisi** ' ni seçin. Bu seçenek, kural için geçerli olan cihazları cihaz şablonu türüne göre filtreler. **+ Filter** öğesini seçerek daha fazla filtre ölçütü ekleyebilirsiniz.

1. **Koşullar** bölümünde, kuralınızı neyin tetikleyeceğini tanımlarsınız. Sıcaklık telemetrisine dayalı bir koşul tanımlamak için aşağıdaki bilgileri kullanın:

    | Alan        | Değer            |
    | ------------ | ---------------- |
    | Ölçüm  | Sensorhumıd      |
    | Operatör     | büyüktür  |
    | Değer        | 55               |

    Daha fazla koşul eklemek için **+ koşul**' ı seçin.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Kural koşulunu gösteren ekran görüntüsü":::

1. Kural tetiklendiğinde çalıştırılacak bir e-posta eylemi eklemek için **+ e-posta**' yı seçin.

1. Eyleminizi tanımlamak için aşağıdaki tablodaki bilgileri kullanın ve ardından **bitti**' yi seçin:

    | Ayar   | Değer                                             |
    | --------- | ------------------------------------------------- |
    | Görünen ad | İşleç e-posta eylemi                          |
    | Amaç        | E-posta adresiniz                                |
    | Notlar     | Çevresel nem eşiği aştı. |

    > [!NOTE]
    > Bir e-posta bildirimi almak için e-posta adresinin [uygulamadaki bir kullanıcı kimliği](howto-administer.md) olması ve bu kullanıcının uygulamada en az bir kez oturum açmış olması gerekir.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Kurala eklenen bir e-posta eylemini gösteren ekran görüntüsü":::

1. **Kaydet**’i seçin. Kuralınız **Kurallar** sayfasında listelenir.

## <a name="test-the-rule"></a>Kuralı test etme

Kural kaydedildikten kısa bir süre sonra dinamik olur. Kuralda tanımlanan koşullar karşılandığında, uygulamanız eylemde belirttiğiniz adrese bir e-posta gönderir.

> [!NOTE]
> Testiniz tamamlandıktan sonra, gelen kutunuzda uyarı almayı durdurmak için kuralı kapatın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Telemetri tabanlı kural oluşturma
* Eylem ekleme

Uygulamanıza bağlı olan cihazları izleme hakkında daha fazla bilgi edinmek için hızlı başlangıç ile devam edin:

> [!div class="nextstepaction"]
> [Cihazlarınızı izlemek Için Azure IoT Central kullanın](quick-monitor-devices.md).
