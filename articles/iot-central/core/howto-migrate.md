---
title: V2 Azure IoT Central uygulamasını v3 'e geçirme | Microsoft Docs
description: Yönetici olarak, v2 Azure IoT Central uygulamanızı v3 'e geçirmeyi öğrenin
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702734"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>V2 IoT Central uygulamanızı v3 'e geçirin

*Bu makale Yöneticiler için geçerlidir.*

Şu anda yeni bir IoT Central uygulaması oluşturduğunuzda, bu bir v3 uygulamasıdır. Daha önce bir uygulama oluşturduysanız, ne zaman oluşturduğunuza bağlı olarak, v2 olabilir. Bu makalede, en yeni IoT Central özelliklerini kullandığınızdan emin olmak için bir v2 'yi bir v3 uygulamasına nasıl geçirebileceğiniz açıklanır.

IoT Central uygulamasının sürümünü nasıl tanımlayacağınızı öğrenmek için, bkz. [uygulamanız hakkında](howto-get-app-info.md).

Bir uygulamayı v2 'den v3 'e geçirme adımları şunlardır:

1. V2 uygulamasından yeni bir v3 uygulaması oluşturun.
1. V3 uygulamasını yapılandırın.
1. V2 uygulamasına silin.

## <a name="create-a-new-v3-application"></a>Yeni bir v3 uygulaması oluşturun

Yeni bir v3 uygulaması oluşturmak için Geçiş Sihirbazı 'nı kullanın.

IoT Central, mevcut bir v3 uygulamasına geçirmeyi desteklemiyor. Mevcut cihazları otomatik olarak taşımak için, v3 uygulamanızı oluşturmak üzere Geçiş Sihirbazı 'nı kullanın.

Geçiş Sihirbazı:

- Yeni bir v3 uygulaması oluşturur.
- V3 ile uyumluluk için cihaz şablonlarınızı denetler.
- Tüm cihaz şablonlarınızı Yeni v3 uygulamasına kopyalar.
- Tüm kullanıcıları ve rol atamalarını yeni v3 uygulamasına kopyalar.

> [!NOTE]
> V3 ile cihaz uyumluluğunu sağlamak için, cihaz şablonundaki temel türler nesne özellikleri haline gelir. Cihaz kodunuzda herhangi bir değişiklik yapmanız gerekmez.

Bir uygulamayı v3 'e geçirmek için yönetici olmanız gerekir.

1. **Yönetim** menüsünde **bir v3 uygulamasına geçir**' i seçin:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Uygulama Geçiş Sihirbazı 'nı gösteren ekran görüntüsü":::

1. Yeni bir **uygulama adı** girin ve isteğe bağlı olarak otomatik olarak oluşturulan  **URL 'yi** değiştirin. URL, geçerli v2 uygulamanızın URL 'siyle aynı olamaz. Ancak, v2 uygulamanızı sildikten sonra URL 'YI daha sonra değiştirebilirsiniz.

1. **Yeni bir v3 uygulaması oluştur**' u seçin.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Uygulama Geçiş Sihirbazı 'ndaki seçenekleri gösteren ekran görüntüsü":::

    Cihaz şablonlarınızın sayısına ve karmaşıklığına bağlı olarak, bu işlemin tamamlanması birkaç dakika sürebilir.

    > [!Warning]
    > Herhangi bir cihaz şablonunda bir sayıyla başlayan veya şu karakterlerden herhangi birini (,,,,,,,,,,,,,,,,,,,,,,,,,) içeriyorsa v3 uygulamanızın oluşturulması başarısız olur `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` . V3 uygulamalarının kullandığı DTDL cihaz şablonu şeması, bu karakterlere izin vermez. V3 'e geçmeden önce bu sorunu gidermek için cihaz şablonunuzu güncelleştirin.

1. Yeni v3 uygulamanız hazırsanız, açmak için **Yeni uygulamanızı aç** ' ı seçin.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Uygulama geçişten sonra uygulama geçiş Sihirbazı 'nı gösteren ekran görüntüsü":::

## <a name="configure-the-v3-application"></a>V3 uygulamasını yapılandırma

Yeni v3 uygulamanız oluşturulduktan sonra, araçları v2 uygulamasından v3 uygulamasına taşımadan önce herhangi bir yapılandırma değişikliği yapın.

> [!TIP]
> Önceki sürümden bazı farklılıklar olduğu için [v3 hakkında bilgi edinmek](overview-iot-central-tour.md#navigate-your-application) için bir dakikanızı ayırın.

Göz önünde bulundurulması gereken bazı yapılandırma adımları aşağıda verilmiştir:

- [Panoları yapılandırma](howto-add-tiles-to-your-dashboard.md)
- [Veri vermeyi yapılandırma](howto-export-data.md)
- [Kuralları ve eylemleri yapılandırma](quick-configure-rules.md)
- [Uygulama kullanıcı arabirimini özelleştirme](howto-customize-ui.md)

V3 uygulamanız gereksinimlerinizi karşılayacak şekilde yapılandırıldığında, cihazlarınızı v2 uygulamanızdan v3 uygulamanıza taşımaya hazırsınız demektir.

## <a name="move-your-devices-to-the-v3-application"></a>Cihazlarınızı v3 uygulamasına taşıyın

Bu adım tamamlandığında, tüm cihazlarınız yalnızca yeni v3 uygulamanızla iletişim kurar.

> [!IMPORTANT]
> Cihazlarınızı v3 uygulamanıza taşımadan önce, v3 uygulamasında oluşturduğunuz tüm cihazları silin.

Bu adım, tüm mevcut cihazlarınızı Yeni v3 uygulamanıza taşılardır. Cihaz verileriniz kopyalanmaz.

Cihazlarınızı taşımaya başlamak için **tüm cihazları taşı** ' yı seçin. Bu adımın tamamlanması birkaç dakika sürebilir.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Cihazları taşı seçeneğiyle uygulama geçiş Sihirbazı 'nı gösteren ekran görüntüsü":::

Taşıma tamamlandıktan sonra, Yeni v3 uygulamasına bağlandıklarından emin olmak için tüm cihazlarınızı yeniden başlatın.

> [!WARNING]
> V2 uygulamanız artık çalışamaz olduğundan v3 uygulamanızı silmeyin.

## <a name="delete-your-old-v2-application"></a>Eski v2 uygulamanızı silme

Yeni v3 uygulamanızda her şeyin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra, eski v2 uygulamanızı silin. Bu adım, artık kullandığınız bir uygulama için faturalandırılmamasını sağlar.

> [!Note]
> Bir uygulamayı silmek için, uygulamayı oluştururken seçtiğiniz Azure aboneliğindeki kaynakları silme izninizin olması gerekir. Daha fazla bilgi edinmek için bkz. [Azure abonelik kaynaklarınıza erişimi yönetmek için rol tabanlı erişim denetimi kullanma](../../role-based-access-control/role-assignments-portal.md).

1. V2 uygulamanızda, menüdeki **Yönetim** sekmesini seçin
2. IoT Central uygulamanızı kalıcı olarak silmek için **Sil** ' i seçin. Bu seçenek, bu uygulamayla ilişkili tüm verileri kalıcı olarak siler.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı nasıl geçireceğinizi öğrendiğinize göre, önerilen sonraki adım v3 'de nelerin değiştiğini görmek için [Azure IoT Central kullanıcı arabirimini](overview-iot-central-tour.md) gözden geçirmeli.