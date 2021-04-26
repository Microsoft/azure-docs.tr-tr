---
title: Azure için cihaz güncelleştirmesi IoT Hub cihaz grubu oluştur | Microsoft Docs
description: Azure IoT Hub cihaz güncelleştirmesinde bir cihaz grubu oluşturma
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680032"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>IoT Hub cihaz güncelleştirmesinde cihaz grupları oluşturma
IoT Hub cihaz güncelleştirmesi bir IoT cihazları grubuna güncelleştirme dağıtmaya izin verir.

## <a name="prerequisites"></a>Önkoşullar

* [IoT Hub etkinleştirilmiş cihaz güncelleştirmesiyle bir IoT Hub erişim](create-device-update-account.md). IoT Hub için bir S1 (Standart) katmanı veya üstünü kullanmanız önerilir. 
* IoT Hub içinde cihaz güncelleştirmesi için sağlanan bir IoT cihazı (veya simülatör).
* [Sağlanan cihaz için en az bir güncelleştirme başarıyla içeri aktarıldı.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Cihazlarınıza bir etiket ekleyin  

IoT Hub cihaz güncelleştirmesi bir IoT cihazları grubuna güncelleştirme dağıtmaya izin verir. Bir grup oluşturmak için, ilk adım IoT Hub içindeki hedef cihaz kümesine bir etiket eklemektir. Etiketler, cihaz güncelleştirmesine bağlandıktan sonra cihazınıza yalnızca başarıyla eklenebilir.

Aşağıdaki belgeler bir etiketin nasıl ekleneceğini ve güncelleştirileceğini açıklar.

### <a name="programmatically-update-device-twin"></a>Cihaz Ikizi program aracılığıyla güncelleştirme

Cihazı cihaz güncelleştirmesiyle kaydettikten sonra RegistryManager kullanarak cihaz Ikizi 'yi uygun etiketle güncelleştirebilirsiniz. 
[Örnek bir .NET uygulaması kullanarak nasıl etiket ekleneceğini öğrenin.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Etiket özellikleri hakkında bilgi edinin](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Cihaz güncelleştirme etiketi biçimi

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Işleri kullanma

[Bu](../iot-hub/iot-hub-devguide-jobs.md) örneklerin ardından bir cihaz güncelleştirme etiketi eklemek veya güncelleştirmek için birden çok cihazda bir işi zamanlamak mümkündür. [Daha fazla bilgi edinin](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Bu eylem, geçerli ıOT hub iletilerinizin kotasına karşı gider ve tek seferde yalnızca 50.000 cihaz ikizi etiketi değiştirmeniz önerilir, aksi takdirde günlük IoT Hub ileti kotasını aşarsanız daha fazla IoT Hub birimi satın almanız gerekebilir. Ayrıntılar, [Kotalar ve azaltma](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)konusunda bulunabilir.

### <a name="direct-twin-updates"></a>Doğrudan Ikizi güncelleştirmeleri

Etiketler doğrudan Device ikizi 'ta da eklenebilir veya güncelleştirilir.

1. [Azure Portal](https://portal.azure.com) açın ve IoT Hub gidin.

2. Sol gezinti bölmesindeki ' IoT cihazları ' veya ' IoT Edge ' içinden IoT cihazınızı bulun ve cihaz Ikizi gidin.

3. Cihaz Ikizi, var olan tüm cihaz güncelleştirme etiketi değerlerini null olarak ayarlayarak silin.

4. Aşağıda gösterildiği gibi yeni bir cihaz güncelleştirme etiketi değeri ekleyin. [Etiketler içeren JSON belgesi ikizi örnek cihaz.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Sınırlamalar

* Etiketinize, ayrılmış bir değer olan ' kategorilere ayrılmamış ' dışında herhangi bir değer ekleyebilirsiniz.
* Etiket değeri 255 karakteri aşamaz.
* Etiket değeri, alfasayısal karakterler ve şu özel karakterleri içerebilir ".", "-", "_", "~".
* Etiket ve grup adları büyük/küçük harfe duyarlıdır.
* Bir cihazda ADUGroup adlı tek bir etiket bulunabilir, bu ada sahip bir etiketin sonraki eklemeleri, ADUGroup etiket adı için varolan değeri geçersiz kılar.
* Bir cihaz yalnızca bir gruba ait olabilir.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Mevcut bir IoT Hub etiketi seçerek bir cihaz grubu oluşturun

1. [Azure Portal](https://portal.azure.com) gidin.

2. Daha önce cihaz güncelleştirme örneğinizi bağladığınız IoT Hub seçin.

3. Sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.

4. Sayfanın üst kısmındaki gruplar sekmesini seçin. Henüz Gruplanmayan cihaz güncelleştirmesine bağlı cihazların sayısını görebileceksiniz.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Gruplandırılmamış cihazların ekran görüntüsü." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Yeni bir grup oluşturmak için Ekle düğmesini seçin.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Cihaz grubu ekleme ekranının ekran görüntüsü." lightbox="media/create-update-group/add-group.png":::

6. Listeden bir IoT Hub etiketi seçin ve ardından güncelleştirme grubu oluştur ' u seçin.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Etiket seçiminin ekran görüntüsü." lightbox="media/create-update-group/select-tag.png":::

7. Grup oluşturulduktan sonra, güncelleştirme uyumluluğu grafiği ve gruplar listesinin güncelleştirildiğini görürsünüz.  Güncelleştirme uyumluluğu grafiği, farklı uyumluluk durumlarındaki cihaz sayısını gösterir: en son güncelleştirmede, yeni güncelleştirmeler var, devam eden güncelleştirmeler ve cihazlarda henüz gruplandırılmaz. [Güncelleştirme uyumluluğu hakkında bilgi edinin.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Güncelleştirme uyumluluğu görünümünün ekran görüntüsü." lightbox="media/create-update-group/updated-view.png":::

8. Yeni oluşturduğunuz grubu ve yeni gruptaki cihazlar için kullanılabilir güncelleştirmeleri görmeniz gerekir. Güncelleştirme adına tıklayarak bu görünümden yeni gruba güncelleştirme dağıtabilirsiniz. Daha fazla ayrıntı için bkz. sonraki adım: dağıtımı güncelleştirme.

## <a name="view-device-details-for-the-group-you-created"></a>Oluşturduğunuz grup için cihaz ayrıntılarını görüntüleme

1. Yeni oluşturduğunuz gruba gidin ve Grup adına tıklayın.

2. Grubun parçası olan cihazların listesi, cihaz güncelleştirme özellikleriyle birlikte gösterilir. Bu görünümde, grubun üyesi olan tüm cihazlar için güncelleştirme uyumluluk bilgilerini de görebilirsiniz. Uyumluluk grafiği güncelleştirme, çeşitli uyumluluk durumlarında cihaz sayısını gösterir: en son güncelleştirmede, yeni güncelleştirmeler kullanılabilir ve güncelleştirmeler devam ediyor.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Cihaz grubu ayrıntıları görünümünün ekran görüntüsü." lightbox="media/create-update-group/group-details.png":::

3. Ayrıca, IoT Hub ' deki cihaz ayrıntıları sayfasına yönlendirilmek için bir grup içindeki her bir cihaza tıklayabilirsiniz.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Cihaz ayrıntıları görünümünün ekran görüntüsü." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Sonraki Adımlar 

[Güncelleştirme dağıt](deploy-update.md)

[Cihaz grupları hakkında daha fazla bilgi edinin](device-update-groups.md)

[Güncelleştirme uyumluluğu hakkında bilgi edinin.](device-update-compliance.md)
