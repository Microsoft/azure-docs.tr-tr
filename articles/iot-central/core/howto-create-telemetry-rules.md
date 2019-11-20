---
title: Azure IoT Central uygulamanızda telemetri kuralları kullanma | Microsoft Docs
description: Azure IoT Central telemetri kuralları, cihazlarınızı neredeyse gerçek zamanlı olarak izlemenizi ve kural tetiklendiğinde e-posta gönderme gibi eylemleri otomatik olarak çağırmayı sağlar.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3b2fff84b70c5c5e37d14faa87143e5dacc82bce
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930196"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızda bir telemetri kuralı oluşturma ve bildirim ayarlama

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bağlı cihazlarınızı uzaktan izlemek için Azure IoT Central kullanabilirsiniz. Azure IoT Central kuralları, cihazlarınızı neredeyse gerçek zamanlı olarak izlemenizi ve e-posta veya Microsoft Flow tetikleyici Gönder gibi eylemleri otomatik olarak çağırmayı sağlar. Yalnızca birkaç tıklamayla, cihaz verilerinizi izleyecek koşulu tanımlayabilir ve ilgili eylemi yapılandırabilirsiniz. Bu makalede, cihaz tarafından gönderilen Telemetriyi izlemek için kuralların nasıl oluşturulacağı açıklanmaktadır.

Cihazlar, cihazdan sayısal veri göndermek için telemetri ölçümü kullanabilir. Bir telemetri kuralı, seçilen cihaz telemetrisi belirtilen eşiği aştığında tetikler.

## <a name="create-a-telemetry-rule"></a>Telemetri kuralı oluşturma

Bir telemetri kuralı oluşturmak için, cihaz şablonunda en az bir telemetri ölçümü tanımlanmış olmalıdır. Bu örnek, sıcaklık ve nem telemetrisi gönderen bir soğutma makinesi cihazı kullanır. Kural, cihaz tarafından bildirilen sıcaklığın izler ve 80 derecenin üzerinde kaldığında bir e-posta gönderir.

1. **Cihaz şablonları** sayfasını kullanarak, kuralını eklemek istediğiniz cihaz şablonuna gidin.

1. Henüz bir kural oluşturmadıysanız aşağıdaki ekranı görürsünüz:

    ![Henüz kural yok](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. **Kurallar** sekmesinde, oluşturabileceğiniz kural türlerini görmek Için **+ Yeni kural** ' ı seçin.

1. Cihaz telemetrisini izlemek için bir kural oluşturmak üzere **telemetri** ' i seçin.

    ![Kural türleri](media/howto-create-telemetry-rules/rule_types1.png)

1. Bu cihaz şablonunda kuralı tanımlamanızı sağlayan bir ad girin.

1. Bu şablon için oluşturulan tüm cihazların kuralını hemen etkinleştirmek için, **Bu şablon için tüm cihazlar kuralını etkinleştir**seçeneğini değiştirin.

   ![Kural ayrıntısı](media/howto-create-telemetry-rules/rule_detail1.png)

    Kural, cihaz şablonu altındaki tüm cihazlara otomatik olarak uygulanır.

### <a name="configure-the-rule-conditions"></a>Kural koşullarını yapılandırma

Koşul, kural tarafından izlenen kriterleri tanımlar.

1. **Koşullar** ' ın yanındaki **+** ' u seçerek yeni bir koşul ekleyin.

1. **Ölçüm** açılan listesinden izlemek istediğiniz Telemetriyi seçin.

1. Sonra **toplama**, **işleç**' i seçin ve bir **eşik** değeri sağlayın.
   - Toplama isteğe bağlıdır. Toplama olmadan kural, koşulu karşılayan her telemetri veri noktası için tetikler. Örneğin, kural sıcaklık 80 üzerinde olduğunda kural tetiklenecek şekilde yapılandırıldıysa, cihaz sıcaklık > 80 ' i raporladığında kural neredeyse anında tetiklenir.
   - Ortalama, min, Max, Count gibi bir toplama işlevi seçilirse, kullanıcının koşulun değerlendirilmesi gereken bir **toplama zaman penceresi** sağlaması gerekir. Örneğin, dönemi "5 dakika" olarak ayarlarsanız ve kuralınız 80 üzerinde ortalama sıcaklık alıyorsa, kural ortalama sıcaklık en az 5 dakika boyunca 80 ' in üzerinde olduğunda tetiklenir. Kural değerlendirme sıklığı **toplama zamanı penceresiyle**aynıdır, yani bu örnekte kural her 5 dakikada bir değerlendirilir.

     ![Koşul](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >**Koşul**altında birden fazla telemetri ölçümü eklenebilir. Birden çok koşul belirtildiğinde kuralın tetiklenmesi için tüm koşulların karşılanması gerekir. Her bir koşullu ' ve ' yan tümcesiyle dolaylı olarak birleştirilir. Toplama kullanılırken, her ölçüm toplanmalıdır.

### <a name="configure-actions"></a>Eylemleri yapılandırma

Bu bölümde, kural tetiklendiğinde yapılacak eylemlerin nasıl ayarlanacağı gösterilmektedir. Kuralda belirtilen tüm koşulların doğru olarak değerlendirilmesi durumunda eylemler çağrılır.

1. **Eylemler**' in yanındaki **+** seçin. Burada, kullanılabilir eylemlerin listesini görürsünüz.  

    ![Eylem Ekle](media/howto-create-telemetry-rules/add_action1.png)

1. **E-posta** eylemini seçin, **to** alanına geçerli bir e-posta adresi girin ve kural tetiklendiğinde e-posta gövdesinde görüntülenecek bir notun sağlayın.

    > [!NOTE]
    > E-postalar yalnızca uygulamaya eklenmiş olan ve en az bir kez oturum açan kullanıcılara gönderilir. Azure IoT Central 'de [Kullanıcı yönetimi](howto-administer.md) hakkında daha fazla bilgi edinin.

   ![Eylemi Yapılandır](media/howto-create-telemetry-rules/configure_action1.png)

1. Kuralı kaydetmek için **Kaydet**' i seçin. Kural birkaç dakika içinde canlı olarak geçer ve uygulamanıza gönderilen izleme Telemetriyi başlatır. Kuralda belirtilen koşul karşılandığında, kural yapılandırılan e-posta eylemini tetikler.

Kurala Microsoft Flow ve Web kancaları gibi başka eylemler ekleyebilirsiniz. Kural başına en fazla 5 işlem ekleyebilirsiniz.

- Bir kural tetiklendiğinde Microsoft Flow iş akışını başlatma [eylemi Microsoft Flow](howto-add-microsoft-flow.md) 
- Bir kural tetiklendiğinde diğer hizmetlere bildirimde bulunan [Web kancası eylemi](howto-create-webhooks.md)

## <a name="parameterize-the-rule"></a>Kuralı Parametreleştirme

Kurallar, **cihaz özelliklerinden** belirli özellikleri parametre olarak türetebilir. Farklı cihazlarda telemetri eşiklerinin değişebileceği senaryolarda, parametreleri kullanmak faydalıdır. Kuralı oluşturduğunuzda, 80 derece gibi mutlak bir değer sağlamak yerine, eşiği belirten, **en yüksek Ideal eşik**gibi bir cihaz özelliği seçin. Kural yürütüldüğünde, Device özelliğinde ayarlanan değer ile cihaz telemetrisiyle eşleşir.

Parametreleri kullanmak cihaz şablonu başına yönetilecek kural sayısını azaltmak için etkili bir yoldur.

Eylemler, bir parametre olarak **Device özelliği** kullanılarak da yapılandırılabilir. Bir e-posta adresi özellik olarak **depolanıyorsa,, adresini tanımlarken** kullanabilirsiniz.

## <a name="delete-a-rule"></a>Kuralı silme

Artık bir kurala ihtiyacınız yoksa, kuralı açıp **Sil**' i seçerek silin. Kural silindiğinde cihaz şablonundan ve ilişkili tüm cihazlardan kaldırılır.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Cihaz şablonu için bir kuralı etkinleştirme veya devre dışı bırakma

Cihaza gidin ve etkinleştirmek veya devre dışı bırakmak istediğiniz kuralı seçin. Cihaz şablonuyla ilişkili tüm cihazların kuralını etkinleştirmek veya devre dışı bırakmak için kuraldaki **Bu şablonun tüm cihazları Için etkinleştir kuralını** açın.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Cihaz için bir kuralı etkinleştirme veya devre dışı bırakma

Cihaza gidin ve etkinleştirmek veya devre dışı bırakmak istediğiniz kuralı seçin. Bu cihazın kuralını etkinleştirmek veya devre dışı bırakmak için **Bu cihaz için kuralı etkinleştir** düğmesini değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda kuralların nasıl oluşturulacağını öğrendiğinize göre, bir sonraki adım aşağıda verilmiştir:

- [Kurallara Microsoft Flow eylem ekleme](howto-add-microsoft-flow.md)
- [Kurallarda Web kancası eylemi ekleme](howto-create-webhooks.md)
- [Birden çok eylemi bir veya daha fazla kuralla çalışacak şekilde gruplandırma](howto-use-action-groups.md)
- [Cihazlarınızı yönetme](howto-manage-devices.md)
