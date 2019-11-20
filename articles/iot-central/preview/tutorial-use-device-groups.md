---
title: Azure IoT Central uygulamanızda cihaz gruplarını kullanma | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central uygulamanızdaki cihazlardan telemetri çözümlemek için cihaz gruplarını nasıl kullanacağınızı öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 281806999b08c3babbb753459835850ad9d733eb
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895442"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Öğretici: cihaz telemetrisini analiz etmek için cihaz gruplarını kullanma (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede, Azure IoT Central uygulamanızda cihaz telemetrisini çözümlemek üzere cihaz gruplarını kullanarak nasıl bir operatör olarak kullanılacağı açıklanmaktadır.

Bir cihaz grubu, belirtilen bazı ölçütlerle eşleştiğinden, birlikte gruplanmış cihazların bir listesidir. Cihaz grupları cihazları daha küçük ve mantıksal gruplara gruplandırarak, cihazları ölçekli olarak yönetmenize, görselleştirmenize ve çözümlemenize yardımcı olur. Örneğin, bir teknisyenin sorumlu oldukları cihazları bulmasını sağlamak için Seattle 'daki tüm AIR klimaları cihazlarını listelemek üzere bir cihaz grubu oluşturabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir cihaz grubu oluşturma
> * Cihaz telemetrisini çözümlemek için bir cihaz grubu kullanma

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce, [Azure IoT Central oluşturma uygulamasını](./quick-deploy-iot-central.md) tamamlayıp [IoT Central uygulamanıza sanal bir cihaz ekleyerek](./quick-create-pnp-device.md) , birlikte çalışmak üzere **ortam algılayıcı** cihaz şablonu oluşturmanız gerekir.

## <a name="create-simulated-devices"></a>Sanal cihazlar oluşturma

Bir cihaz grubu oluşturmadan önce, bu öğreticide kullanmak üzere **ortam algılayıcısı** cihaz şablonundan en az beş sanal cihaz ekleyin:

![Beş sanal çevresel algılayıcı cihazı](./media/tutorial-use-device-groups/simulated-devices.png)

Çevresel algılayıcı aygıtlarından oluşan dört için, müşteri adını **contoso**olarak ayarlamak üzere **çevresel algılayıcı özellikleri** görünümünü kullanın:

![Müşteri adını contoso olarak ayarla](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Bir cihaz grubu oluşturma

Bir cihaz grubu oluşturmak için:

1. Sol bölmedeki **cihaz grupları** ' nı seçin.

1. **+ Yeni**seçeneğini belirleyin.

    ![Yeni cihaz grubu](media/tutorial-use-device-groups/image1.png)

1. Cihaz grubunuza **contoso cihazları**gibi bir ad verin. Ayrıca, bir açıklama ekleyebilirsiniz. Bir cihaz grubu yalnızca tek bir cihaz şablonundan cihaz içerebilir. Bu grup için kullanılacak **çevresel algılayıcı** cihaz şablonunu seçin.

1. **Müşteri adı** özelliğini, **eşittir** karşılaştırma işlecini ve **contoso** 'yu değer olarak seçerek cihaz grubu için **contoso** 'ya ait olan cihazları tanımlamak üzere sorgu oluşturun. Birden çok sorgu ekleyebilirsiniz ve **Tüm** ölçütlere uyan cihazlar cihaz grubuna yerleştirilir. Oluşturduğunuz cihaz grubuna, uygulamaya erişimi olan herkes tarafından erişilebilir, böylece herkes cihaz grubunu görüntüleyebilir, değiştirebilir veya silebilir.

    ![Cihaz grubu sorgusu](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > Cihaz grubu dinamik bir sorgudur. Cihazların listesini her görüntülediğinizde, listede farklı cihazlar olabilir. Liste, şu anda sorgu ölçütlerine uyan cihazlara bağlıdır.

1. **Kaydet**'i seçin.

> [!NOTE]
> Azure IoT Edge cihazlar için bir cihaz grubu oluşturmak üzere Azure IoT Edge Şablonlar ' ı seçin.

## <a name="analytics"></a>Analiz

Gruptaki cihazlardan telemetri çözümlemek için bir cihaz grubuyla **Analytics** kullanabilirsiniz. Örneğin, tüm contoso ortam sensörleri tarafından raporlanan ortalama sıcaklığın çizebilirsiniz.

Bir cihaz grubunun telemetrisini çözümlemek için:

1. Sol bölmedeki **analiz** ' ı seçin.

1. Oluşturduğunuz **contoso cihazları** cihaz grubunu seçin. Ardından hem **sıcaklık** hem de **nem** telemetrisi türlerini ekleyin:

    ![Analiz oluştur](./media/tutorial-use-device-groups/create-analysis.png)

    Bir toplama türü seçmek için telemetri türlerinin yanındaki dişli tekerleği simgesini kullanın. Varsayılan değer **Average**' dir. Toplama verilerinin nasıl gösterileceğini değiştirmek için **Böl** ' ü kullanın. Örneğin, cihaz KIMLIĞINE göre böldüğünüz takdirde, **Çözümle**' yi seçtiğinizde her bir cihaz için bir çizim görürsünüz.

1. Ortalama telemetri değerlerini görüntülemek için **Çözümle** ' yi seçin:

    ![Analizi görüntüle](./media/tutorial-use-device-groups/view-analysis.png)

    Görünümü özelleştirebilir, gösterilen zaman dilimini değiştirebilir ve verileri dışarı aktarabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda cihaz gruplarını nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Telemetri kuralları oluşturma](tutorial-create-telemetry-rules.md)
