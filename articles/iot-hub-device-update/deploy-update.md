---
title: Azure IoT Hub cihaz güncelleştirmesini kullanarak bir güncelleştirme dağıtma | Microsoft Docs
description: Azure IoT Hub yönelik cihaz güncelleştirmesini kullanarak bir güncelleştirme dağıtın.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680025"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>IoT Hub için cihaz güncelleştirmesini kullanarak bir güncelleştirme dağıtın

IoT Hub için cihaz güncelleştirmesini kullanarak bir IoT cihazına güncelleştirme dağıtmayı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* [IoT Hub etkinleştirilmiş cihaz güncelleştirmesiyle bir IoT Hub erişim](create-device-update-account.md). IoT Hub için bir S1 (Standart) katmanı veya üstünü kullanmanız önerilir. 
* [Sağlanan cihaz için en az bir güncelleştirme başarıyla içeri aktarıldı.](import-update.md) 
* IoT Hub içinde cihaz güncelleştirmesi için sağlanan bir IoT cihazı (veya simülatör).
* [Güncelleştirmeye çalıştığınız IoT cihazına bir etiket atandı. Cihaz en az bir güncelleştirme grubunun parçasıdır.](create-update-group.md)
* Desteklenen tarayıcılar:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Güncelleştirme dağıtma

1. [Azure Portal](https://portal.azure.com) git

2. IoT Hub cihaz güncelleştirme dikey penceresine gidin.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub’ı" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Sayfanın üst kısmındaki gruplar sekmesini seçin. Cihaz grupları hakkında [daha fazla bilgi edinin](device-update-groups.md) . 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Gruplar sekmesi" lightbox="media/deploy-update/updated-view.png":::

4. Güncelleştirme uyumluluğu grafiği ve grupları listesini görüntüleyin. Cihaz grubunuz için, bekleyen güncelleştirmeler altındaki güncelleştirmeye yönelik bir bağlantı ile yeni bir güncelleştirme görmeniz gerekir (bir kez yenilemeniz gerekebilir). [Güncelleştirme uyumluluğu hakkında daha fazla bilgi edinin.](device-update-compliance.md) 

5. Kullanılabilir güncelleştirmeyi seçin.

6. Hedef grup olarak doğru grubun seçili olduğunu onaylayın. Dağıtımınızı zamanlayın ve ardından güncelleştirme Dağıt ' ı seçin.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Güncelleştirme seçin" lightbox="media/deploy-update/select-update.png":::

7. Uyumluluk grafiğini görüntüleyin. Güncelleştirmenin devam etmekte olduğunu görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Güncelleştirme devam ediyor" lightbox="media/deploy-update/update-in-progress.png":::

8. Cihazınız başarıyla güncelleştirildikten sonra, uyumluluk grafiği ve dağıtım ayrıntıları güncelinizi aynı şekilde yansıtacak şekilde görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Güncelleştirme başarılı oldu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Güncelleştirme dağıtımını izleme

1. Sayfanın üst kısmındaki dağıtımlar sekmesini seçin.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Dağıtımlar sekmesi" lightbox="media/deploy-update/deployments-tab.png":::

2. Dağıtım ayrıntılarını görüntülemek için oluşturduğunuz dağıtımı seçin.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dağıtım ayrıntıları" lightbox="media/deploy-update/deployment-details.png":::

3. En son durum ayrıntılarını görüntülemek için Yenile ' yi seçin. Durum başarılı olana kadar bu işleme devam edin.


## <a name="retry-an-update-deployment"></a>Güncelleştirme dağıtımını yeniden dene

Dağıtımınız bazı nedenlerle başarısız olursa, başarısız cihazlar için dağıtımı yeniden deneyebilirsiniz. 

1. Dağıtımlar sekmesine gidin ve başarısız olan dağıtımı seçin. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dağıtım ayrıntıları" lightbox="media/deploy-update/deployment-details.png":::

2. Ayrıntılı dağıtım bilgileri bölmesinde "başarısız" Cihaz durumuna tıklayın.

3. "Başarısız cihazları yeniden dene" düğmesine tıklayın ve onay bildirimini onaylayın. 

## <a name="next-steps"></a>Sonraki adımlar

[Yaygın sorunları giderme](troubleshoot-device-update.md)
