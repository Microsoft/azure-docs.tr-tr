---
title: Azure IoT Central ile sağlık çözümleri oluşturun | Microsoft Docs
description: Azure IoT Central uygulama şablonlarını kullanarak sağlık çözümü oluşturmayı öğrenin.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: a5a8c8e52c4bebda4e22e592fefa3801449504e3
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027467"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Azure IoT Central ile sağlık çözümleri oluşturma 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Uygulama şablonları kullanarak Azure IoT Central ile sağlık çözümleri oluşturmayı öğrenin.

## <a name="what-is-continuous-patient-monitoring-template"></a>Sürekli hasta izleme şablonu nedir?

Sağlık IoT alanında sürekli hasta Izleme, readmissions riskini azaltma, zaman zaman daha etkili bir şekilde yönetim ve hasta sonuçlarını artırma önemli Etkinleştiricilerinde biridir. Sürekli hasta Izleme iki ana kategoriye ayrılabilir:

1. **Hasta izleme**: tıbbi wearables ve diğer cihazları barındırmastanda kullanarak, ekiplere bir günde birden çok kez göz atmak için bir Nurme göndermek zorunda kalmadan hasta öneme sahip belirtileri ve tıbbi koşulları izleyebilir. Ekipler, bir hastanın bildirimler aracılığıyla önemli bir dikkat gerektiren süreyi anlayabilmesi ve zaman süresini önceliklendirir.
1. **Uzaktan hasta izleme**: tıbbi wearables ve hasta ile bildirilen sonuçları (profesyonelleri) kullanarak, hastaların hostanın dışından izlenmesini sağlamak için yeniden giriş riski önemli ölçüde düşürülemez. Hastalar ve rehabilitasyon hastaları 'nın verileri, hastaların planlara uygunluğunu sağlamak için toplanabilir ve hasta olma uyarıları, önemli olmaya başlamadan önce ekiplere yönelik olarak ortaya çıkmış olabilir.

Bu uygulama şablonu, her iki sürekli hasta Izleme kategorisi için çözümler oluşturmak üzere kullanılabilir. Avantajları şunlardır:

* Çeşitli tıbbi wearables IoT Central bir örneğe sorunsuz bir şekilde bağlayın.
* Cihazların sağlıklı kalmasını sağlamak için cihazları izleyin ve yönetin.
* Uygun uyarıları tetiklemek için cihaz verileri etrafında özel kurallar oluşturun.
* Hasta sistem verilerinizi, uyumlu bir veri deposu olan FHıR için Azure API 'sine dışarı aktarın.
* Toplu öngörüleri, mevcut veya yeni iş uygulamalarına göre güçlendirin.

>[!div class="mx-imgBorder"] 
>![CPM-Pano](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

Sürekli hasta izleme çözümü oluşturmaya başlamak için:

* [Uygulama şablonunu dağıtma](tutorial-continuous-patient-monitoring.md)
* [Örnek bir mimariye bakın](concept-continuous-patient-monitoring-architecture.md)