---
title: Azure IoT Central uygulamasına gittiğinizde faturanızı görüntüleyin ve deneme sürenizi ödeyin. Microsoft Docs
description: Yönetici olarak, Azure IoT Central uygulamanızda Kullandıkça öde ve deneme sürümünü ücretsiz olarak nasıl görüntüleyeceğinizi öğrenin
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 16a58bfc3fa245ed1ede19b0439419ab4590234e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952582"
---
# <a name="view-your-bill-in-iot-central-application"></a>Faturanızı IoT Central uygulamanızı görüntüleme

Bu makalede, Yönetim bölümünde faturanızda Azure IoT Central uygulamanızı nasıl görüntüleyebileceğinizi ve ayrıca deneme sürümünüzü Kullandıkça Öde 'ye nasıl dönüştürebileceğinizi açıklar.

**Yönetim** bölümüne erişmek ve bunları kullanmak için, bir Azure IoT Central uygulaması için **yönetici** rolünde olmanız gerekir. Azure IoT Central uygulaması oluşturursanız, bu uygulama için otomatik olarak **yönetici** rolüne atanırsınız.

## <a name="view-your-bill"></a>Faturanızı görüntüleme

Faturanızı görüntülemek için, **Yönetim** bölümünde **faturalandırma** sayfasına gidin. Azure Faturalandırma sayfası, Azure IoT Central uygulamalarınızın her biri için faturanızı görebileceğiniz yeni bir sekmede açılır.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Deneme sürümünüzü Kullandıkça Öde için dönüştürün

- **Deneme** uygulamaları, süresi dolmadan yedi gün boyunca ücretsizdir. Süresi dolmadan önce herhangi bir anda Kullandıkça Öde uygulamasına dönüştürülebilir.
- **Kullandıkça Öde** uygulamaları, abonelik başına ilk beş cihaz ücretsiz olarak cihaz başına ücretlendirilir.

[Azure IoT Central fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-central/), fiyatlar hakkında daha fazla bilgi edinin.

Faturalandırma bölümünde, deneme uygulamalarınızı Kullandıkça Öde 'e dönüştürebilirsiniz.

Bu self servis işlemini gerçekleştirmek için şu adımları izleyin:

1. **Yönetim** bölümündeki **faturalandırma** sayfasına gidin.

    ![Deneme durumu](media/howto-administer/freetrialbilling.png)

1. **Kullandıkça Öde seçeneğine Dönüştür**' ü seçin.

    ![Deneme süresini Dönüştür](media/howto-administer/convert.png)

1. Uygun Azure Active Directory ve ardından Kullandıkça Öde uygulamanız için kullanılacak Azure aboneliğini seçin.

1. **Dönüştür**' i seçtikten sonra uygulamanız artık Kullandıkça Öde uygulaması olur ve faturalandırılmaya başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamasında faturanızı görüntüleme hakkında bilgi edindiğinize göre, önerilen sonraki adım Azure IoT Central [uygulama kullanıcı arabirimini özelleştirme](howto-customize-ui.md) hakkında bilgi almanız gerekir.