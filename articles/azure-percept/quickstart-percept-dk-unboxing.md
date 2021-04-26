---
title: Azure Percept DK bileşenlerinizi kutudan kaldırma ve birleştirme
description: Azure Percept DK 'yi kutudan kaldırma, bağlama ve yeniden kullanma hakkında bilgi edinin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608189"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Hızlı başlangıç: Azure Percept DK bileşenlerinizi kutudan kaldırma ve birleştirme

Azure Percept DK 'nizi aldıktan sonra, bileşenleri bağlama ve cihazı kapatmaya ilişkin bilgiler için bu kılavuza başvurun.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- P7 screwdriver (isteğe bağlı, güç kablosu bağlayıcısını taşıyıcı panosuna güvenli hale getirmek için kullanılır)

## <a name="unbox-and-assemble-your-device"></a>Cihazınızı kutudan kaldırma ve birleştirme

1. Azure Percept DK bileşenlerinin işaretini kaldırın.

    Devkit, bir taşıyıcı panosu, Azure Percept Vision SoM, antenler ve kablolar içeren aksesuar kutusu ve onaltılı anahtarla bir karşılama kartı içerir.

1. Devkit bileşenlerini bağlayın.

    > [!NOTE]
    > Güç bağdaştırıcısı bağlantı noktası, taşıyıcı panosunun sağ tarafında bulunur. Kalan bağlantı noktaları (2x USB-A, 1x USB-C ve 1x Ethernet) ve güç düğmesi, taşıyıcı panosunun sol tarafında bulunur.

    1. Her ikisi de taşıyıcı panosuna Wi-Fi antenler.

    1. Vision SoM 'a, USB-c kablosuyla taşıyıcı panonun USB-C bağlantı noktasına bağlayın.

    1. Güç kablosunu güç bağdaştırıcısına bağlayın.

    1. Kalan tüm Plastik ambalajları cihazlardan kaldırın.

    1. Güç bağdaştırıcısını/kablosunu taşıyıcı panosuna ve bir duvar prizine bağlayın. Güç kablosu bağlayıcısını taşıyıcı panosuna tamamen güvenli hale getirmek için, bağlayıcı scresunu artırmak üzere bir P7 screwdriver (devkit 'e dahil değil) kullanın.

    1. Güç kablosunu bir duvar prizine takmadan sonra cihaz otomatik olarak açılır. Taşıyıcı panonun sol tarafındaki güç düğmesi de aydınlatılır. Lütfen cihazın önyüklemesi için bir süre bekleyin.

        > [!NOTE]
        > Güç düğmesi, bir güç prizine bağlıyken cihazı kapatma veya yeniden başlatma içindir. Güç kesintisi durumunda cihaz otomatik olarak yeniden başlatılır.

Devkit derlemesinin görsel bir gösterimi için lütfen şu videonun 0:00 ile 0:50 arası bölümüne bakın:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Sonraki adımlar

Artık devkit bağlı ve açık olduğuna göre, lütfen cihaz kurulumunu tamamlamak için [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md) Kılavuzu ' na bakın. Kurulum deneyimi, devkit 'i bir Wi-Fi ağına bağlamanıza, SSH oturum açma ayarlamanıza, bir IoT Hub oluşturmanıza ve devkit 'i Azure hesabınıza sağlamanıza olanak tanır. Cihaz kurulumunu tamamladıktan sonra, prototipsiz başlamaya başlayabilirsiniz.