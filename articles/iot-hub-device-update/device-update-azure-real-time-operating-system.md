---
title: Azure gerçek zamanlı işletim sistemi için cihaz güncelleştirmesi | Microsoft Docs
description: Azure gerçek zamanlı işletim sistemi için cihaz güncelleştirme ile çalışmaya başlama
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629062"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Azure gerçek zamanlı Işletim sistemi (RTOS) kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi

Bu öğreticide, Azure RTOS NetX Duo 'da IoT Hub aracısının cihaz güncelleştirmesini oluşturma adımları ele alınacaktır. Ayrıca, geliştiricilerin uygulamasındaki cihaz güncelleştirme yeteneklerini tümleştirmeleri için basit API 'Ler de sağlar. Cihazlarda AIR (OTA) güncelleştirmelerini yapılandırma, derleme ve dağıtma hakkında bilgi edinmek için Başlarken kılavuzlarını içeren anahtar yarı iletkeni değerlendirme panoları [örneklerini](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) bulun.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * başlarken
> * Cihazınızı etiketleme
> * Cihaz grubu oluşturma
> * Görüntü güncelleştirme dağıtımı
> * Güncelleştirme dağıtımını izleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
* IoT Hub erişim. Bir S1 (Standart) katmanını veya üstünü kullanmanız önerilir.
* IoT Hub bağlı bir cihaz güncelleştirme örneği ve hesabı. Daha önce yapmadıysanız bir cihaz güncelleştirme hesabı [oluşturmak ve bağlamak](http://create-device-update-account.md/) için Kılavuzu izleyin.

## <a name="get-started"></a>başlarken

Her panoya özgü örnek Azure RTOS projesi, IoT Hub için cihaz güncelleştirme kullanma hakkında kod ve belge içerir. 
1. Panoya özgü örnek dosyalarını [Azure RTOS ve cihaz güncelleştirme örneklerinden](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)indirin.
2. İndirilen örnekteki docs klasörünü bulun.
3. Belgelerinden Azure kaynaklarını hazırlama, hesap oluşturma ve IoT cihazlarını buna kaydetme adımlarını izleyin.
5. Ardından yeni bir bellenim görüntüsü oluşturmak ve panonuz için bildirim almak üzere belgeleri izleyin.
6. Daha sonra IoT Hub için üretici yazılımı görüntüsünü ve bildirimi cihaz güncelleştirmesine yayımlayın.
7. Son olarak, projeyi indirip cihazınızda çalıştırın.

[Azure RTOS](https://docs.microsoft.com/azure/rtos/)hakkında daha fazla bilgi edinin.  

## <a name="tag-your-device"></a>Cihazınızı etiketleme

1. Önceki adımda cihaz uygulamasını çalışır durumda tutun.
2. [Azure Portal](https://portal.azure.com) açın ve IoT Hub gidin.
3. Sol gezinti bölmesindeki ' IoT cihazları ' içinden IoT cihazınızı bulun ve cihaz Ikizi gidin.
4. Cihaz Ikizi, var olan tüm cihaz güncelleştirme etiketi değerlerini null olarak ayarlayarak silin.
5. Aşağıda gösterildiği gibi yeni bir cihaz güncelleştirme etiketi değeri ekleyin.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Güncelleştirme grubu oluştur

1. Daha önce cihaz güncelleştirme örneğinizi bağladığınız IoT Hub gidin.
2. Sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.
3. Sayfanın üst kısmındaki gruplar sekmesini seçin. 
4. Yeni bir grup oluşturmak için Ekle düğmesini seçin.
5. Önceki adımda oluşturduğunuz IoT Hub etiketini listeden seçin. Güncelleştirme grubu oluştur ' u seçin.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Etiket seçimini gösteren ekran görüntüsü." lightbox="media/create-update-group/select-tag.PNG":::

Etiket ekleme ve güncelleştirme grupları oluşturma hakkında [daha fazla bilgi edinin](create-update-group.md)

## <a name="deploy-new-firmware"></a>Yeni bellenim dağıtma

1. Grup oluşturulduktan sonra, bekleyen güncelleştirmeler altındaki güncelleştirmeye yönelik bir bağlantı ile cihaz grubunuz için kullanılabilen yeni bir güncelleştirme görmeniz gerekir. Bir kez yenilemeniz gerekebilir. 
2. Kullanılabilir güncelleştirmeye tıklayın.
3. Hedef grup olarak doğru grubun seçili olduğunu onaylayın. Dağıtımınızı zamanlayın ve ardından güncelleştirme Dağıt ' ı seçin.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Güncelleştirme seçin" lightbox="media/deploy-update/select-update.png":::

4. Uyumluluk grafiğini görüntüleyin. Güncelleştirmenin devam etmekte olduğunu görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Güncelleştirme devam ediyor" lightbox="media/deploy-update/update-in-progress.png":::

5. Cihazınız başarıyla güncelleştirildikten sonra, uyumluluk grafiği ve dağıtım ayrıntıları güncelinizi aynı şekilde yansıtacak şekilde görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Güncelleştirme başarılı oldu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Güncelleştirme dağıtımını izleme

1. Sayfanın üst kısmındaki dağıtımlar sekmesini seçin.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Dağıtımlar sekmesi" lightbox="media/deploy-update/deployments-tab.png":::

2. Dağıtım ayrıntılarını görüntülemek için oluşturduğunuz dağıtımı seçin.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dağıtım ayrıntıları" lightbox="media/deploy-update/deployment-details.png":::

3. En son durum ayrıntılarını görüntülemek için Yenile ' yi seçin. Durum başarılı olana kadar bu işleme devam edin.

Artık bir Raspberry Pi 3 B + cihazında IoT Hub için cihaz güncelleştirmesini kullanarak başarılı bir uçtan uca görüntü güncelleştirmesini tamamladınız. 

## <a name="cleanup-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında cihaz güncelleştirme hesabınızı, örneğinizi, IoT Hub ve IoT cihazını temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

Azure RTOS ve Azure IoT ile nasıl çalıştığı hakkında daha fazla bilgi için, bkz https://azure.com/rtos ..
