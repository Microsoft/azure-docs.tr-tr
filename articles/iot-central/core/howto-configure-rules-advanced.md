---
title: Azure IoT Central uygulamanızı diğer bulut hizmetleriyle bütünleştirmek için iş akışlarını kullanma | Microsoft Docs
description: Bu nasıl yapılır makalesi, bir Oluşturucu olarak, IoT Central uygulamanızı diğer bulut hizmetleriyle tümleştiren kuralların ve eylemlerin nasıl yapılandırılacağını gösterir. Gelişmiş bir kural oluşturmak için, güç otomatikleştirin veya Azure Logic Apps bir IoT Central Bağlayıcısı kullanırsınız.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: af6adcf408c9790b09830e986cd1cac00774a2ec
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491942"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Azure IoT Central uygulamanızı diğer bulut hizmetleriyle bütünleştirmek için iş akışlarını kullanma

*Bu makale, çözüm oluşturucuları için geçerlidir.*

Bir eşiği aşan cihaz sıcaklığı gibi telemetri tabanlı koşullara yanıt olarak, e-posta gönderme gibi eylemleri tetikleyen IoT Central kurallar oluşturabilirsiniz.

Power otomatikleştirmek ve Azure Logic Apps için Azure IoT Central v3 Bağlayıcısı, IoT Central işlemleri otomatik hale getirmek için daha gelişmiş kurallar oluşturmanıza olanak sağlar:

- Azure IoT Central uygulamanızda bir kural tetiklendiğinde, Power otomatikleştirin veya Azure Logic Apps içinde bir iş akışı tetiklenebilir. Bu iş akışları, Microsoft 365 veya üçüncü taraf bir hizmet gibi diğer bulut hizmetlerinde eylemler çalıştırabilir.
- Microsoft 365 gibi başka bir bulut hizmetindeki bir olay, Power otomatikleştirebileceğiniz veya Azure Logic Apps bir iş akışını tetikleyebilir. Bu iş akışları, IoT Central uygulamanızdaki eylemleri çalıştırabilir veya veri alabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Çözümü ayarlamak için sürüm 3 IoT Central uygulaması gerekir. Uygulamanızın sürümünü nasıl denetleyeceğinizi öğrenmek için, bkz. [uygulamanız hakkında](./howto-get-app-info.md). IoT Central uygulama oluşturmayı öğrenmek için bkz. [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md).

> [!NOTE]
> Sürüm 2 IoT Central uygulaması kullanıyorsanız, önceki sürümler belge sitesinde [Azure Logic Apps IoT Central Bağlayıcısı ile iş akışlarını derleme](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) ve Azure IoT Central v2 bağlayıcısını kullanma makalesine bakın.

## <a name="trigger-a-workflow-from-a-rule"></a>Bir kuraldan iş akışı tetikleyin

Power otomatikleştirin veya Azure Logic Apps bir iş akışını tetikleyebilmeniz için, IoT Central uygulamanızda bir kural gerekir. Daha fazla bilgi için bkz. [Azure IoT Central kuralları ve eylemleri yapılandırma](./howto-configure-rules.md).

**Azure IoT Central v3-önizleme** bağlayıcısını Power otomatikleştirmede bir tetikleyici olarak eklemek için:

1. Güç otomatikleştirme ' de **+ Oluştur**' u seçin, **özel** sekmesini seçin.
1. *IoT Central* arayın ve **Azure IoT Central v3-önizleme** bağlayıcısını seçin.
1. Tetikleyiciler listesinde **bir kural tetiklendiğinde (Önizleme)** öğesini seçin.
1. **Bir kural harekete geçirildiğinde** , IoT Central uygulamanızı ve kullanmakta olduğunuz kuralı seçin.

**Azure IoT Central v3-önizleme** bağlayıcısını Azure Logic Apps bir tetikleyici olarak eklemek için:

1. **Logic Apps tasarımcısında** **boş mantıksal uygulama** şablonunu seçin.
1. Tasarımcıda **özel** sekmesini seçin.
1. *IoT Central* arayın ve **Azure IoT Central v3-önizleme** bağlayıcısını seçin.
1. Tetikleyiciler listesinde **bir kural tetiklendiğinde (Önizleme)** öğesini seçin.
1. **Bir kural harekete geçirildiğinde** , IoT Central uygulamanızı ve kullanmakta olduğunuz kuralı seçin.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Azure IoT Central-Preview bağlayıcısını bulun ve tetikleyiciyi seçin":::

Şimdi, tümleştirme senaryonuzu oluşturmak için iş akışınıza daha fazla adım ekleyebilirsiniz.

## <a name="run-an-action"></a>Eylem çalıştırma

IoT Central bir uygulamada eylemleri, Power otomatikleştirin ve Azure Logic Apps iş akışlarıyla çalıştırabilirsiniz. İlk olarak, iş akışınızı oluşturun ve iş akışını başlatmak için bir tetikleyici tanımlamak üzere bir bağlayıcı kullanın. Ardından bir eylem olarak **Azure IoT Central v3-önizleme** bağlayıcısını kullanın.

**Azure IoT Central v3-önizleme** bağlayıcısını Power otomatikleştirmede bir eylem olarak eklemek için:

1. Güç otomatikleştirme ' de **Eylem Seç** panelinde **özel** sekmesini seçin.
1. *IoT Central* arayın ve **Azure IoT Central v3-önizleme** bağlayıcısını seçin.
1. Eylemler listesinde, kullanmak istediğiniz IoT Central eylemi seçin.
1. Eylem adımında, seçtiğiniz eylem için yapılandırmayı doldurun. Sonra **Kaydet**'i seçin.

**Azure IoT Central v3-önizleme** bağlayıcısını Azure Logic Apps bir eylem olarak eklemek için:

1. **Logic Apps tasarımcısında**, **Eylem Seç** panelinde **özel** sekmesini seçin.
1. *IoT Central* arayın ve **Azure IoT Central v3-önizleme** bağlayıcısını seçin.
1. Eylemler listesinde, kullanmak istediğiniz IoT Central eylemi seçin.
1. Eylem adımında, seçtiğiniz eylem için yapılandırmayı doldurun. Sonra **Kaydet**'i seçin.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Azure IoT Central v3 bağlayıcısını bulun ve bir eylem seçin":::

## <a name="list-of-actions"></a>Eylemler listesi

Aşağıdaki listede, **Azure IoT Central v3-önizleme** Bağlayıcısı ve yapılandırma seçeneklerinde bulunan tüm IoT Central eylemleri gösterilmektedir. Alanların birçoğu dinamik olarak oluşturulan içeriğe sahip olabilir. Örneğin, önceki bir adım geçerli adımın üzerinde hareket eden cihaz KIMLIĞINI belirleyebilir.

### <a name="create-or-update-a-device"></a>Cihaz oluşturma veya güncelleştirme

IoT Central uygulamanızda bir cihaz oluşturmak veya güncelleştirmek için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Oluşturulacak veya güncelleştirilecek cihazın benzersiz KIMLIĞI. |
| Onaylandı | Cihazın IoT Central bağlanmak için onaylanmış olup olmadığını seçin. |
| Aygıt Açıklaması | Cihazın ayrıntılı bir açıklaması. |
| Aygıt Adı | Cihazın görünen adı. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |
| Tir | Cihazın simülasyonu yapılıp yapılmayacağını seçin. |

### <a name="delete-a-device"></a>Cihazı silme

IoT Central uygulamanızdan bir cihazı silmek için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |

### <a name="execute-a-device-command"></a>Bir cihaz komutunu Yürüt

Cihaz arabirimlerinden birinde tanımlanan bir komutu yürütmek için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz bileşeni | Komut içeren cihaz şablonundaki arabirim. |
| Cihaz Komutu | Seçili arabirimdeki komutlardan birini seçin. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |
| Cihaz komut Isteği yükü | Komut için bir istek yükü gerekiyorsa buraya ekleyin.  |

> [!NOTE]
> Bir cihaz şablonu seçinceye kadar bir cihaz bileşeni seçemezsiniz.

### <a name="get-a-device-by-id"></a>Kimliğe göre cihaz alma

Cihazın ayrıntılarını almak için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |

Diğer eylemlerdeki dinamik ifadelerde döndürülen ayrıntıları kullanabilirsiniz. Döndürülen cihaz ayrıntıları şunlardır: **onaylanan**, **gövde**, **Cihaz açıklaması**, **Cihaz adı**, **cihaz şablonu**, **sağlandı** ve **benzetilen**.

### <a name="get-device-cloud-properties"></a>Cihaz bulutu özelliklerini al

Belirli bir cihazın bulut özelliği değerlerini almak için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |

Diğer eylemlerdeki dinamik ifadelerde döndürülen bulut özelliği değerlerini kullanabilirsiniz.

### <a name="get-device-properties"></a>Cihaz özelliklerini al

Belirli bir cihazın özellik değerlerini almak için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |

Diğer eylemlerdeki dinamik ifadelerde döndürülen özellik değerlerini kullanabilirsiniz.

### <a name="get-device-telemetry-value"></a>Cihaz telemetri değerini Al

Belirli bir cihazın telemetri değerlerini almak için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |

Diğer eylemlerdeki dinamik ifadelerde döndürülen telemetri değerlerini kullanabilirsiniz.

### <a name="update-device-cloud-properties"></a>Cihaz bulutu özelliklerini güncelleştirme

Belirli bir cihazın bulut özelliği değerlerini güncelleştirmek için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |
| Bulut özellikleri | Bir cihaz şablonu seçtikten sonra, şablonda tanımlanan her bir bulut özelliği için bir alan eklenir. |

### <a name="update-device-properties"></a>Cihaz özelliklerini güncelleştirme

Belirli bir cihazın yazılabilir özellik değerlerini güncelleştirmek için bu eylemi kullanın.

| Alan | Açıklama |
| ----- | ----------- |
| Uygulama | IoT Central uygulamalar listesinden seçin. |
| Cihaz | Silinecek cihazın benzersiz KIMLIĞI. |
| Cihaz şablonu | IoT Central uygulamanızdaki cihaz şablonları listesinden seçim yapın. |
| Yazılabilir Özellikler | Bir cihaz şablonu seçtikten sonra, şablonda tanımlanan her yazılabilir özellik için bir alan eklenir. |

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central uygulamanızda gelişmiş bir kural oluşturmayı öğrendiğinize göre, [azure IoT Central uygulamanızda cihaz verilerini çözümlemeyi](howto-create-analytics.md) öğrenebilirsiniz