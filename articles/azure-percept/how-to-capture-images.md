---
title: Azure Percept Studio 'da kod içermeyen bir Vision çözümü için görüntüleri yakalama
description: Kod içermeyen bir vizyon çözümü için Azure Percept Studio 'da Azure Percept DK ile görüntüleri yakalama hakkında bilgi edinin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d6cece6ee3079ba9f400f40026ca26ea36668710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024651"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Azure Percept Studio 'da bir Vision projesi için görüntüleri yakalama

Azure Percept Studio 'daki mevcut bir Vision projesi için Azure Percept DK 'nin vizyonunu kullanarak görüntüleri yakalamak için bu kılavuzu izleyin. Henüz bir Vision projesi oluşturmadıysanız, lütfen [kod yok Vision öğreticisine](./tutorial-nocode-vision.md)bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): devkit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve devkit 'e IoT Hub
- [Kod içermeyen Vision projesi](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Görüntüleri yakala

1. Devkit üzerinde güç.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'ya gidin.

1. Genel Bakış sayfasının sol tarafında, **cihazlar**' a tıklayın.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio 'ya genel bakış ekranı." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Listeden devkit ' i seçin.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept cihazlar listesi.":::

1. Cihaz sayfanızda **bir proje Için yakalama görüntüleri**' ne tıklayın.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Percept cihazlar sayfası, kullanılabilir eylemleri listelendi.":::

1. **Görüntü yakalama** penceresinde şunları yapın:

    1. **Proje** açılan menüsünde, görüntülerini toplamak istediğiniz vizyon projesini seçin.

    1. **Görüntü akışını görüntüle** ' ye tıklayarak, görme som 'un kameranın doğru yerleştirildiğinden emin olun.

    1. Görüntü yakalamak için **Fotoğraf Al** ' ı tıklatın.

    1. Alternatif olarak, görüntü yakalama için bir zamanlayıcı ayarlamak üzere **Otomatik görüntü yakalama** seçeneğinin yanındaki kutuyu işaretleyin:

        1. **Yakalama hızı** altında tercih ettiğiniz görüntüleme hızını seçin.
        1. **Hedef** altında toplamak istediğiniz toplam görüntü sayısını seçin.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Görüntü yakalama ekranı.":::

[Özel görüntü işleme](https://www.customvision.ai/)tüm görüntülere erişilebilecektir.

## <a name="next-steps"></a>Sonraki adımlar

[Kod içermeyen Vision modelinize test edin ve yeniden eğitme](../cognitive-services/custom-vision-service/test-your-model.md).