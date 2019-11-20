---
title: Azure IoT Central uygulamanızda olay kuralları oluşturma ve yönetme | Microsoft Docs
description: Azure IoT Central olay kuralları, cihazlarınızı neredeyse gerçek zamanlı olarak izlemenizi ve kural tetiklendiğinde e-posta gönderme gibi eylemleri otomatik olarak çağırmayı sağlar.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f932cb9621e4cfc62e2bb00d11030a7ff03450fc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954025"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızda bir olay kuralı oluşturma ve bildirim ayarlama

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bağlı cihazlarınızı uzaktan izlemek için Azure IoT Central kullanabilirsiniz. Azure IoT Central kuralları, cihazlarınızı neredeyse gerçek zamanlı olarak izlemenizi ve e-posta veya Microsoft Flow tetikleyici Gönder gibi eylemleri otomatik olarak çağırmayı sağlar. Yalnızca birkaç tıklamayla, cihaz verilerinizi izleyecek koşulu tanımlayabilir ve ilgili eylemi yapılandırabilirsiniz. Bu makalede, cihaz tarafından gönderilen olayları izlemek için kuralların nasıl oluşturulacağı açıklanmaktadır.

Cihazlar, önemli veya bilgilendirici cihaz olayları göndermek için olay ölçümünü kullanabilir. Bir olay kuralı, seçilen cihaz olayı cihaz tarafından bildirildiğinde tetiklenir.

## <a name="create-an-event-rule"></a>Olay kuralı oluşturma

Bir olay kuralı oluşturmak için, cihaz şablonunda en az bir olay ölçümü tanımlanmış olmalıdır. Bu örnek, bir fan motoru hata olayını raporlayan soğutma yapan bir havalandırma makinesi cihazını kullanır. Kural, cihaz tarafından bildirilen olayı izler ve olay bildirildiğinde bir e-posta gönderir.

1. **Cihaz şablonları** sayfasını kullanarak, kuralını eklemek istediğiniz cihaz şablonuna gidin.

1. Henüz bir kural oluşturmadıysanız aşağıdaki ekranı görürsünüz:

    ![Henüz kural yok](media/howto-create-event-rules/rules_landing_page1.png)

1. **Kurallar** sekmesinde, oluşturabileceğiniz kural türlerini görmek Için **+ Yeni kural** ' ı seçin.

1. Olay izleme kuralı oluşturmak için **olay** kutucuğunu seçin.

    ![Kural türleri](media/howto-create-event-rules/rule_types1.png)

1. Bu cihaz şablonunda kuralı tanımlamanızı sağlayan bir ad girin.

1. Bu şablondan oluşturulan tüm cihazların kuralını hemen etkinleştirmek için, **Bu şablonun tüm cihazları Için etkinleştir kuralını**açın.

    ![Kural ayrıntısı](media/howto-create-event-rules/rule_detail1.png)

    Kural, cihaz şablonu altındaki tüm cihazlara otomatik olarak uygulanır.

### <a name="configure-the-rule-conditions"></a>Kural koşullarını yapılandırma

Koşul, kural tarafından izlenen kriterleri tanımlar.

1. Yeni koşul eklemek için **koşullar** ' ın yanındaki **+** seçin.

1. Ölçüm açılan listesinden izlemek istediğiniz olayı seçin. Bu örnekte, **fan motoru hata** olayı seçilmiştir.

   ![Koşul](media/howto-create-event-rules/condition_filled_out1.png)

1. İsteğe bağlı olarak, **sayımı** **toplama** olarak ayarlayabilir ve karşılık gelen eşiği sağlayabilirsiniz.

   - Toplama olmadan kural, koşulu karşılayan her bir olay veri noktası için tetikler. Örneğin, bir **fan motoru hata** olayı gerçekleştiğinde kuralın koşulunu tetiklenecek şekilde yapılandırırsanız, cihaz bu olayı raporladığında kural neredeyse anında tetiklenir.
   - Count bir toplama işlevi olarak kullanılıyorsa, koşulun değerlendirilmesi gereken bir **eşik** ve bir **toplama zaman penceresi** sağlamanız gerekir. Bu durumda, olay sayısı toplanır ve kural yalnızca toplanmış olay sayısı eşikle eşleşiyorsa tetiklenir.

     Örneğin, 5 dakika içinde üçten fazla sayıda cihaz olayı olduğunda uyarı vermek istiyorsanız, olayı seçin ve toplama işlevini "say", işleç "büyüktür" ve "threshold" 3 olarak ayarlayın. "Toplama süresi dönemi" ni "5 dakika" olarak ayarlayın. Kural, 5 dakika içinde cihaz tarafından üçten fazla olay gönderildiğinde tetiklenir. Kural değerlendirme sıklığı **toplama zamanı penceresiyle**aynıdır, yani bu örnekte kural her 5 dakikada bir değerlendirilir.

     ![Olay koşulu Ekle](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >**Koşul**altında birden fazla olay ölçümü eklenebilir. Birden çok koşul belirtildiğinde kuralın tetiklenmesi için tüm koşulların karşılanması gerekir. Her koşul örtülü olarak bir ' ve ' yan tümcesi ile birleştirilir. Toplama kullanılırken, her ölçüm toplanmalıdır.

### <a name="configure-actions"></a>Eylemleri yapılandırma

Bu bölümde, kural tetiklendiğinde yapılacak eylemlerin nasıl ayarlanacağı gösterilmektedir. Kuralda belirtilen tüm koşulların doğru olarak değerlendirilmesi durumunda eylemler çağrılır.

1. **Eylemler**' in yanındaki **+** seçin. Burada, kullanılabilir eylemlerin listesini görürsünüz.

    ![Eylem Ekle](media/howto-create-event-rules/add_action1.png)

1. **E-posta** eylemini seçin, **to** alanına geçerli bir e-posta adresi girin ve kural tetiklendiğinde e-posta gövdesinde görüntülenecek bir notun sağlayın.

    > [!NOTE]
    > E-postalar yalnızca uygulamaya eklenmiş olan ve en az bir kez oturum açan kullanıcılara gönderilir. Azure IoT Central 'de [Kullanıcı yönetimi](howto-administer.md) hakkında daha fazla bilgi edinin.

   ![Eylemi Yapılandır](media/howto-create-event-rules/configure_action1.png)

1. Kuralı kaydetmek için **Kaydet**' i seçin. Kural birkaç dakika içinde canlı olarak geçer ve uygulamanıza gönderilen olayları izlemeye başlar. Kuralda belirtilen koşul eşleştiğinde kural, yapılandırılan e-posta eylemini tetikler.

Kurala Microsoft Flow ve Web kancaları gibi başka eylemler ekleyebilirsiniz. Kural başına en fazla 5 işlem ekleyebilirsiniz.

- Bir kural tetiklendiğinde Microsoft Flow iş akışını başlatma [eylemi Microsoft Flow](howto-add-microsoft-flow.md) 
- Bir kural tetiklendiğinde diğer hizmetlere bildirimde bulunan [Web kancası eylemi](howto-create-webhooks.md)

## <a name="parameterize-the-rule"></a>Kuralı Parametreleştirme

Eylemler, bir parametre olarak **Device özelliği** kullanılarak da yapılandırılabilir. Bir e-posta adresi bir cihaz özelliği olarak **depolanıyorsa,, adresini tanımlarken** kullanabilirsiniz.

## <a name="delete-a-rule"></a>Kuralı silme

Artık bir kurala ihtiyacınız yoksa, kuralı açıp **Sil**' i seçerek silin. Kural silindiğinde cihaz şablonundan ve ilişkili tüm cihazlardan kaldırılır.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Cihaz şablonu için bir kuralı etkinleştirme veya devre dışı bırakma

Cihaza gidin ve etkinleştirmek veya devre dışı bırakmak istediğiniz kuralı seçin. Cihaz şablonuyla ilişkili tüm cihazların kuralını etkinleştirmek veya devre dışı bırakmak için **Bu şablonun tüm cihazları Için etkinleştir kuralını** açın.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Cihaz için bir kuralı etkinleştirme veya devre dışı bırakma

Cihaza gidin ve etkinleştirmek veya devre dışı bırakmak istediğiniz kuralı seçin. Bu cihazın kuralını etkinleştirmek veya devre dışı bırakmak için **Bu cihaz için kuralı etkinleştir** düğmesini değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda kuralların nasıl oluşturulacağını öğrendiğinize göre, bir sonraki adım aşağıda verilmiştir:

- [Kurallara Microsoft Flow eylem ekleme](howto-add-microsoft-flow.md)
- [Kurallarda Web kancası eylemi ekleme](howto-create-webhooks.md)
- [Birden çok eylemi bir veya daha fazla kuralla çalışacak şekilde gruplandırma](howto-use-action-groups.md)
- [Cihazlarınızı yönetme](howto-manage-devices.md)
