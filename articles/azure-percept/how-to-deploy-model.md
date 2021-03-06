---
title: Azure Percept DK 'nize bir Vision AI modeli dağıtma
description: Azure Percept Studio 'dan Azure Percept DK 'ye bir Vision AI modeli dağıtmayı öğrenin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035492"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Azure Percept DK 'nize bir Vision AI modeli dağıtma

Azure Percept Studio içinden Azure Percept DK 'ye bir Vision AI modeli dağıtmak için bu kılavuzu izleyin.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): devkit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve devkit 'e IoT Hub

## <a name="model-deployment"></a>Model dağıtımı

1. Devkit üzerinde güç.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'ya gidin.

1. Genel Bakış sayfasının sol tarafında, **cihazlar**' a tıklayın.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Azure Percept Studio 'ya genel bakış ekranı." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Listeden devkit ' i seçin.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept cihazlar listesi.":::

1. Bir sonraki sayfada, önceden eğitilen örnek Vision modellerinden birini dağıtmak istiyorsanız **örnek modeli Dağıt ' a** tıklayın. Var olan bir [özel kod adım adım çözümü](./tutorial-nocode-vision.md)dağıtmak istiyorsanız **özel görüntü işleme projesi Dağıt ' a** tıklayın.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Dağıtım için model seçimleri.":::

1. Kod içermeyen bir Vision çözümü dağıtmayı seçtiyseniz, projenizi ve tercih ettiğiniz model yinelemi seçin ve **Dağıt**' a tıklayın.

1. Örnek bir model dağıtmayı tercih ettiyseniz, modeli seçin ve **cihaza dağıt**' a tıklayın.

1. Model dağıtımınız başarılı olduğunda, ekranınızın sağ üst köşesinde bir durum iletisi alırsınız. Modelinizi bir işlem sırasında görüntülemek için, devkit 'in Vision SoM 'dan RTSP video akışını görmek için durum iletisindeki **akışı görüntüle** bağlantısına tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure PERCEPT dk telemetrinizi](how-to-view-telemetry.md)görüntülemeyi öğrenin.