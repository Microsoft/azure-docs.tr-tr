---
title: 'Öğretici: Azure IoT Central kuralları ve eylemleri yapılandırma'
description: Bu öğreticide, bir oluşturucu olarak Azure IoT Central uygulamanızda telemetri tabanlı kural ve eylemleri nasıl yapılandıracağınız gösterilir.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9c2c8c1bacf4abfa775747a03d2a4a6121b67714
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106584"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Öğretici: Azure IoT Central’da cihazınız için kurallar ve eylemler yapılandırma

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu öğreticide, bağlı bir klima cihazı 90&deg; F sıcaklığı aştığında e-posta gönderen bir kural oluşturacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Telemetri tabanlı kural oluşturma
> * Eylem ekleme

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, [Uygulamanızda yeni bir cihaz türü tanımlama](tutorial-define-device-type.md) öğreticisini tamamlayarak birlikte çalışacağınız **Bağlı Klima** cihaz şablonunu oluşturmanız gerekir.

## <a name="create-a-telemetry-based-rule"></a>Telemetri tabanlı kural oluşturma

1. Uygulamanıza yeni bir telemetri tabanlı kural eklemek için, sol bölmede **cihaz şablonları**' nı seçin:

    ![Cihaz şablonları sayfası](media/tutorial-configure-rules/templatespage1.png)

    Önceki öğreticide oluşturduğunuz **bağlı AIR conditioner (1.0.0)** cihaz şablonunu görürsünüz.

2. Cihaz şablonunuzu özelleştirmek için, önceki öğreticide oluşturduğunuz **bağlı AIR conditioner** şablonunu seçin.

3. **Kurallar** görünümünde telemetri temelli bir kural eklemek için **kurallar**' ı seçin ve **+ Yeni kural**' ı seçin ve **telemetri**' i seçin:

    ![Kurallar görünümü](media/tutorial-configure-rules/newrule.png)

5. Kuralınızı tanımlamak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Ayar                                      | Değer                             |
    | -------------------------------------------- | ------------------------------    |
    | Ad                                         | Klima sıcaklığı uyarısı |
    | Bu şablonun tüm cihazları için kuralı etkinleştir | Açık                                |
    | Koşul                                    | Sıcaklık 90’dan fazla    |
    | Toplama                                  | None                              |

    ![Sıcaklık kural koşulu](media/tutorial-configure-rules/temperaturerule.png)

    Daha sonra **Kaydet**’e tıklayın.

## <a name="add-an-action"></a>Eylem ekleme

Bir kural tanımladığınızda, kural koşulları yerine getirildiği zaman çalıştırılacak bir eylem de tanımlarsınız. Bu öğreticide, bir e-posta bildirimi gönderen bir eylem ile bir kural oluşturacaksınız.

1. Bir **eylem**eklemek için önce kuralı **kaydedin** ve ardından **Telemetriyi Yapılandır** paneli ' ne gidin. **Eylemler**' in yanındaki **+** seçin ve ardından **e-posta**' yı seçin:

    ![Sıcaklık kuralı eylemi](media/tutorial-configure-rules/addaction.png)

2. Eyleminizi tanımlamak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Ayar   | Değer                          |
    | --------- | ------------------------------ |
    | Alıcı        | E-posta adresiniz             |
    | Notlar     | Klima sıcaklığı eşiği aştı. |

    > [!NOTE]
    > Bir e-posta bildirimi almak için e-posta adresinin [uygulamadaki bir kullanıcı kimliği](howto-administer.md) olması ve bu kullanıcının uygulamada en az bir kez oturum açmış olması gerekir.

    ![Sıcaklık eylemi](media/tutorial-configure-rules/temperatureaction.png)

3. **Kaydet**’i seçin. Kuralınız **kurallar** sayfasında listelenir.

## <a name="test-the-rule"></a>Kuralı test etme

Kural kaydedildikten kısa bir süre sonra dinamik olur. Kuralda tanımlanan koşullar karşılandığında, uygulamanız eylemde belirtilen e-posta adresine bir ileti gönderir.

> [!NOTE]
> Testiniz tamamlandıktan sonra, gelen kutunuzda uyarı almayı durdurmak için kuralı kapatın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Telemetri tabanlı kural oluşturma
> * Eylem ekleme

Eşik tabanlı bir kural tanımladığınıza göre, bir sonraki adım, [işlecin görünümlerini özelleştirecek](tutorial-customize-operator.md).

Azure IoT Central’daki farklı kural türleri ve kural tanımının nasıl parametre haline getirileceği hakkında daha fazla bilgi için bkz.:
* [Bir telemetri kuralı oluşturun ve bildirimleri ayarlayın](howto-create-telemetry-rules.md).
* [Bir olay kuralı oluşturun ve bildirimleri ayarlayın](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->