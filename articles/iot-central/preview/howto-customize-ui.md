---
title: Azure IoT Central kullanıcı arabirimini özelleştirme | Microsoft Docs
description: Azure IoT Central uygulamanız için temayı özelleştirme ve yardım bağlantıları
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1c9f400c1712c4826044354ead27ecaf597ee311
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894948"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Azure IoT Central kullanıcı arabirimini özelleştirme (Önizleme özellikleri)

Bu makalede, bir yönetici olarak, özel temalar uygulayarak ve kendi özel yardım kaynaklarınıza işaret etmek üzere yardım bağlantılarını değiştirerek uygulamanızın kullanıcı arabirimini nasıl özelleştirebileceğinizi açıklar. 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Aşağıdaki ekran görüntüsünde, standart temanın kullanıldığı bir sayfa gösterilmektedir:

![Standart IoT Central teması](./media/howto-customize-ui/standard-ui.png)

Aşağıdaki ekran görüntüsünde özelleştirilmiş kullanıcı arabirimi öğeleri vurgulanmış bir özel ekran görüntüsü kullanan bir sayfa gösterilmektedir:

![Özel IoT Central teması](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Tema oluştur

Özel bir tema oluşturmak için **Yönetim** bölümünde **uygulamanızı özelleştirme** sayfasına gidin:

![IoT Central temaları](./media/howto-customize-ui/themes.png)

Bu sayfada, uygulamanızın aşağıdaki yönlerini özelleştirebilirsiniz:

### <a name="application-logo"></a>Uygulama logosu

Saydam bir arka planla 1 MB 'den büyük olmayan bir PNG görüntüsü. Bu logo, IoT Central uygulaması başlık çubuğunda sol tarafta görüntülenir.

Logo resminiz uygulamanızın adını içeriyorsa, uygulama adı metnini gizleyebilirsiniz. Daha fazla bilgi için bkz. [uygulamanızı yönetme](./howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Tarayıcı simgesi (öncelik simgesi)

Saydam bir arka planla 32 x 32 pikselden daha büyük olmayan bir PNG görüntüsü. Web tarayıcısı bu görüntüyü adres çubuğunda, geçmiş, yer işaretleri ve tarayıcı sekmesinde kullanabilir.

### <a name="browser-colors"></a>Tarayıcı renkleri

Sayfa üstbilgisinin rengini ve accenting düğmeleri ve diğer vurguları için kullanılan rengi değiştirebilirsiniz. `##ff6347`biçiminde altı karakterli bir onaltılık renk değeri kullanın. **Onaltılık değer** renk gösterimi hakkında daha fazla bilgi için bkz. [HTML renkleri](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> **Uygulamanızı özelleştirme** sayfanızda her zaman varsayılan seçeneklere geri dönebilirsiniz.

### <a name="changes-for-operators"></a>İşleçler için değişiklikler

Bir yönetici özel bir tema oluşturursa, operatörler ve uygulamanızın diğer kullanıcıları artık **ayarlarda**bir tema seçilebilirler.

## <a name="replace-help-links"></a>Yardım bağlantılarını Değiştir

Operatörlerinizi ve diğer kullanıcılarınıza özel yardım bilgileri sağlamak için, uygulama **Yardım** menüsünde bağlantıları değiştirebilirsiniz.

Yardım bağlantılarını değiştirmek için **Yönetim** bölümünde **Yardım Özelleştir** sayfasına gidin:

![Yardım bağlantılarını IoT Central özelleştirme](./media/howto-customize-ui/help-links.png)

Ayrıca, Yardım menüsüne yeni girişler ekleyebilir ve varsayılan girişleri kaldırabilirsiniz:

![Özelleştirilmiş IoT Central yardımı](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> **Yardım 'ı Özelleştir** sayfasındaki varsayılan Yardım bağlantılarına her zaman geri dönebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT Central uygulamanızda Kullanıcı arabirimini özelleştirmeyi öğrendiğinize göre, aşağıda önerilen bazı adımlar verilmiştir:

- [Uygulamanızı yönetme](./howto-administer.md)
- [Panonuza kutucuk ekleme](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)