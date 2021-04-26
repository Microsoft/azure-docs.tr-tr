---
title: Azure Percept ses ayarlama
description: Azure Percept ses cihazınızı Azure Percept DK 'nize bağlamayı öğrenin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605799"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept ses kurulumu

Azure Percept Audio, Azure Percept DK ile kullanıma hazır. Benzersiz kurulum gerekmez.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- Azure Percept sesi
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): devkit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve devkit 'e IoT Hub
- 3,5 mm ses jakına bağlanabilecek konuşmacı veya kulaklıklar (isteğe bağlı)

## <a name="connecting-your-devices"></a>Cihazlarınızı bağlama

1. Azure Percept Audio cihazını, dahil edilen mikro USB ile USB türü-bir kabloyla Azure Percept DK taşıyıcı panosuna bağlayın. Kablonun mikro USB sonunu ses bağlama (Geliştirici) panosuna ve tür-A sonunu Percept DK taşıyıcısı panosuna bağlayın.

1. (İsteğe bağlı) hoparlör veya kulaklığınızdan "çıkış" etiketli ses jakı aracılığıyla Azure Percept ses cihazınıza bağlanın. Bu, ses yanıtlarını duymanızı sağlar.

1. Devkit üzerinde güç. Ses örgü panoda LED L02, cihazın açık olduğunu ve ses SoM 'un kimlik doğrulaması olduğunu göstermek için yanıp sönen beyaz olarak değişir.

1. Kimlik doğrulama işleminin tamamlanmasını bekleyin--bu, 3 dakikaya kadar sürebilir.

1. Aşağıdakilerden birini gördüğünüzde prototipsiz başlamaya hazırsınız:

    - LED L02, düz beyaz olarak değiştirilecek: Bu, kimlik doğrulamanın tamamlandığını ve devkit 'in henüz bir anahtar sözcükle yapılandırılmadığını gösterir.
    - Üç LED 'in tümü mavi: Bu, kimlik doğrulamanın tamamlandığını ve devkit 'in bir anahtar sözcükle yapılandırıldığını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'da [kod içermeyen bir konuşma çözümü](./tutorial-no-code-speech.md) oluşturun.
