---
title: Öğretici-Azure IoT Central uygulamanızda kurallar oluşturma ve yönetme
description: Bu öğreticide, Azure IoT Central kurallarının cihazınızı neredeyse gerçek zamanlı olarak izlemenize ve kural tetiklendiğinde e-posta gönderme gibi eylemleri otomatik olarak nasıl çağıracağına ilişkin yönergeler gösterilmektedir.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b0b5aafd85fe6d992afa9d879f73ef0ec43e00d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834383"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Öğretici: Azure IoT Central uygulamanızda kural oluşturma ve bildirimleri ayarlama

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

Bağlı cihazlarınızı uzaktan izlemek için Azure IoT Central kullanabilirsiniz. Azure IoT Central kuralları cihazlarınızın neredeyse gerçek zamanlı olarak izlenmesini sağlar ve e-posta gönderme gibi eylemleri otomatik olarak çağırır. Bu makalede, cihazlarınızın gönderdikleri Telemetriyi izlemek için kuralların nasıl oluşturulacağı açıklanmaktadır.

Cihazlar, cihazdan sayısal veri göndermek için telemetri kullanır. Seçilen telemetri belirtilen eşiği aştığında bir kural tetiklenir.

Bu öğreticide, sanal bir algılayıcı cihazındaki sıcaklık 70 F 'yi aştığında bir e-posta göndermek için bir kural oluşturacaksınız &deg; .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Kural oluşturma
> * E-posta eylemi ekleme

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce [Azure IoT Central oluşturma uygulamasını](./quick-deploy-iot-central.md) tamamlayıp [IoT Central uygulamanıza sanal bir cihaz ekleyerek](./quick-create-simulated-device.md) , birlikte çalışmak üzere **algılayıcı denetleyicisi** cihaz şablonunu oluşturmak için hızlı başlangıçlara bir sanal cihaz ekleyin.

## <a name="create-a-rule"></a>Kural oluşturma

Bir telemetri kuralı oluşturmak için, cihaz şablonu en az bir telemetri değeri içermelidir. Bu öğretici, sıcaklık ve nem telemetrisi gönderen bir sanal **algılayıcı denetleyicisi** cihazını kullanır. Bu cihaz şablonunu eklediniz ve sanal cihaz oluşturma [IoT Central uygulama](./quick-create-simulated-device.md) hızlı başlangıç bölümünde sanal cihaz oluşturdunuz. Kural, cihaz tarafından bildirilen sıcaklığın izler ve 70 derecenin üzerinde kaldığında bir e-posta gönderir.

> [!NOTE]
> Uygulama başına 50 kural sınırı vardır.

1. Sol bölmede **kurallar**' ı seçin.

1. Henüz bir kural oluşturmadıysanız aşağıdaki ekranı görürsünüz:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Kuralların boş listesini gösteren ekran görüntüsü":::

1. Yeni bir kural eklemek için **+ Yeni** seçeneğini belirleyin.

1. Kuralı tanımlamak için _sıcaklık izleyicisini_ girin ve ENTER 'a basın.

1. **Algılayıcı denetleyicisi** cihaz şablonunu seçin. Varsayılan olarak, kural cihaz şablonuyla ilişkili tüm cihazlara otomatik olarak uygulanır. Cihazların bir alt kümesini filtrelemek için **+ filtre** ' yi seçin ve cihazları tanımlamak için cihaz özellikleri ' ni kullanın. Kuralı devre dışı bırakmak için, **etkin/devre dışı** düğmesini değiştirin:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Kural tanımındaki cihaz şablonunun seçimini gösteren ekran görüntüsü":::

### <a name="configure-the-rule-conditions"></a>Kural koşullarını yapılandırma

Koşullar kuralın izlediği kriterleri tanımlar. Bu öğreticide, sıcaklığın 70 F 'yi aşması durumunda tetiklemesi kuralını yapılandırırsınız &deg; .

1. **Telemetri** açılan menüsünde **sıcaklık** ' ı seçin.

1. Sonra, **işleç** **olarak büyüktür ' i seçin ve** **değer** olarak _70_ girin.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Kural için sıcaklık koşulunu gösteren ekran görüntüsü":::

1. İsteğe bağlı olarak, bir **zaman toplama** ayarlayabilirsiniz. Bir zaman toplamayı seçtiğinizde, toplama açılan listesinden ortalama veya toplam gibi bir toplama türü de seçmeniz gerekir.

    * Toplama olmadan kural, koşulu karşılayan her telemetri veri noktası için tetikler. Örneğin, sıcaklık 70 ' den fazla olduğunda tetiklenecek kuralı yapılandırırsanız, cihaz sıcaklığı bu değeri aştığında kural neredeyse anında tetiklenir.
    * Toplama ile kural, zaman penceresinde telemetri veri noktalarının toplam değeri koşulu karşılıyorsa tetiklenir. Örneğin, sıcaklık 70 ' den fazla olduğunda ve 10 dakikalık ortalama bir süre toplanmasına sahip olan kuralı tetiklemek üzere yapılandırırsanız kural, cihaz, 10 dakikalık bir aralıkta hesaplanan, 70 ' den büyük bir ortalama sıcaklık raporladığında tetiklenir.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Doldurulmuş toplam koşulu gösteren ekran görüntüsü":::

**+ Koşul**' i seçerek bir kurala birden çok koşul ekleyebilirsiniz. Birden çok koşul belirtildiğinde kuralın tetiklenmesi için tüm koşulların karşılanması gerekir. Her koşul örtük bir `AND` yan tümce ile birleştirilir. Birden çok koşuldan oluşan zaman toplamayı kullanıyorsanız, tüm telemetri değerlerinin toplanması gerekir.

### <a name="configure-actions"></a>Eylemleri yapılandırma

Koşulu tanımladıktan sonra, kural tetiklendiğinde gerçekleştirilecek eylemleri ayarlarsınız. Kuralda belirtilen tüm koşulların doğru olarak değerlendirilmesi durumunda eylemler çağrılır.

1. **Eylemler** bölümünde **+ e-posta** ' yı seçin.

1. Eylem  için görünen ad, **to** alanındaki e-posta adresiniz ve _cihazı denetlemeniz gerekir!_ e-postanın gövdesinde görünecek bir notun olması.

    > [!NOTE]
    > E-postalar yalnızca uygulamaya eklenmiş olan ve en az bir kez oturum açan kullanıcılara gönderilir. Azure IoT Central 'de [Kullanıcı yönetimi](howto-administer.md) hakkında daha fazla bilgi edinin.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Kural için e-posta eylemini gösteren ekran görüntüsü":::

1. Eylemi kaydetmek için **bitti**' yi seçin. Bir kurala birden çok eylem ekleyebilirsiniz.

1. Kuralı kaydetmek için **Kaydet**' i seçin. Kural birkaç dakika içinde canlı olarak geçer ve uygulamanıza gönderilen izleme Telemetriyi başlatır. Kuralda belirtilen koşul karşılandığında, kural yapılandırılan e-posta eylemini tetikler.

Bir süre sonra, kural tetiklendiğinde bir e-posta iletisi alırsınız:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Bildirim e-postasını gösteren ekran görüntüsü":::

## <a name="delete-a-rule"></a>Kuralı silme

Artık bir kurala ihtiyacınız yoksa, kuralı açıp **Sil**' i seçerek silin.

## <a name="enable-or-disable-a-rule"></a>Kuralı etkinleştirme veya devre dışı bırakma

Etkinleştirmek veya devre dışı bırakmak istediğiniz kuralı seçin. Kuralda kapsamı belirlenmiş tüm cihazların kuralını etkinleştirmek veya devre dışı bırakmak için kuraldaki **etkin/devre dışı** düğmesini değiştirin.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Belirli cihazlar için bir kuralı etkinleştirme veya devre dışı bırakma

Özelleştirmek istediğiniz kuralı seçin. Kuralın kapsamını izlemek istediğiniz cihazlara daraltmak için **hedef cihazlar** bölümünde bir veya daha fazla filtre kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Telemetri tabanlı kural oluşturma
* Eylem ekleme

Eşik tabanlı bir kural tanımladığınıza göre, önerilen sonraki adım şunları yapmayı öğrenmektedir:

> [!div class="nextstepaction"]
> [Kurallar üzerinde Web kancaları oluşturun](./howto-create-webhooks.md).
