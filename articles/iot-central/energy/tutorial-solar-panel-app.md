---
title: 'Öğretici: Azure IoT Central bir Solar paneli izleme uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak Solar Panel uygulaması oluşturmayı öğrenin.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831799"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Öğretici: güneş paneli izleme uygulama şablonu oluşturma ve araştırma 

Bu öğretici, sanal verileri içeren örnek bir cihaz modeli içeren bir güneş panel izleme uygulaması oluşturma sürecinde size rehberlik eder. Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:


> [!div class="checklist"]
> * Bir güneş panel uygulamasını ücretsiz oluşturun
> * Uygulamayı gözden geçir
> * Kaynakları temizleme


Aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için herhangi bir önkoşul yoktur. Azure aboneliği önerilir, ancak gerekli değildir.


## <a name="create-a-solar-panel-monitoring-app"></a>Güneş paneli izleme uygulaması oluşturma 

Bu uygulamayı üç basit adımda oluşturabilirsiniz:

1. [Azure IoT Central](https://apps.azureiotcentral.com)'ye gidin. Yeni bir uygulama oluşturmak **için Oluştur ' u seçin.** 

1. **Enerji** sekmesini seçin. **Güneş paneli izleme** altında **uygulama oluştur**' u seçin. 

    > [!div class="mx-imgBorder"]
    > ![Azure IoT Central derleme seçeneklerinin ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. **Yeni uygulama** iletişim kutusunda, istenen ayrıntıları girin ve ardından **Oluştur**' u seçin.
    * **Uygulama adı**: Azure IoT Central uygulamanız için bir ad seçin. 
    * **URL**: bir Azure IoT Central URL 'si seçin. Platform, benzersizliği doğrular.
    * **Fiyatlandırma planı**: zaten bir Azure aboneliğiniz varsa, varsayılan ayar önerilir. Azure aboneliğiniz yoksa ücretsiz deneme sürümü ile başlayın.
    * **Faturalandırma bilgisi**: uygulamanın kendisi ücretsizdir. Uygulamanıza yönelik kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
        ![Yeni uygulamanın ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Faturalama bilgilerinin ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Uygulamayı ve sanal verileri doğrulama

Yeni güneş paneli uygulamanızı dilediğiniz zaman değiştirebilirsiniz. Şimdilik, uygulamayı değiştirmeden önce uygulamanın dağıtıldığından ve beklendiği gibi çalıştığından emin olun.

Uygulama oluşturma ve veri benzetimini doğrulamak için **panoya** gidin. Kutucukları bazı verilerle birlikte görebiliyorsanız, uygulama dağıtımınız başarılı olmuştur. Veri simülasyonu, verileri oluşturmak birkaç dakika sürebilir. 

## <a name="application-walk-through"></a>Uygulama izlenecek yol
Uygulama şablonunu başarıyla dağıttıktan sonra, uygulamayı biraz daha incelemek isteyeceksiniz. Örnek akıllı ölçüm cihazı, cihaz modeli ve Pano ile birlikte geldiğinden emin olun.

Adatum, Solar panellerini izleyen ve yöneten kurgusal bir enerji şirketidir. Solar paneli izleme panosunda, güneş paneli özellikleri, verileri ve örnek komutları görürsünüz. Bu Pano, size veya destek ekibinize aşağıdaki etkinlikleri önceden gerçekleştirmenize olanak tanır ve herhangi bir sorun için ek destek gerekir:
* En son panel bilgilerini ve haritadaki yüklü konumunu gözden geçirin.
* Panel durumunu ve bağlantı durumunu kontrol edin.
* Anormal desenleri yakalamak için enerji oluşturma ve sıcaklık eğilimlerini gözden geçirin.
* Planlama ve faturalandırma amaçlarıyla toplam enerji üretimini izleyin.
* Bir paneli etkinleştirin ve gerekirse bellenim sürümünü güncelleştirin. Şablonda, komut düğmeleri olası işlevleri gösterir ve gerçek komutları göndermez.

> [!div class="mx-imgBorder"]
> ![Solar paneli Izleme şablonu panosunun ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Cihazlar
Uygulama bir örnek Solar Panel aygıtıyla birlikte gelir. Cihaz ayrıntılarını görmek için **cihazlar**' ı seçin.

> [!div class="mx-imgBorder"]
> ![Güneş paneli Izleme şablonu cihazlarının ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

**SP0123456789** örnek cihazını seçin. **Güncelleştirme özellikleri** sekmesinden, cihazın yazılabilir özelliklerini güncelleştirebilir ve panoda güncelleştirilmiş değerlerin görselini görebilirsiniz. 

> [!div class="mx-imgBorder"]
> ![Solar paneli Izleme şablonu güncelleştirme özellikleri sekmesinin ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Cihaz şablonu
Güneş paneli cihaz modelini görmek için **cihaz şablonları** sekmesini seçin. Modelde veriler, özellikler, komutlar ve görünümler için önceden tanımlanmış arabirimler vardır.

> [!div class="mx-imgBorder"]
> ![Solar paneli Izleme şablonu cihaz şablonlarının ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam etmeyi istemediğinize karar verirseniz, aşağıdaki adımlarla uygulamanızı silin:

1. Sol bölmeden **Yönetim**' i seçin.
1. **Uygulama ayarlarını**  >  **Sil**' i seçin. 

    > [!div class="mx-imgBorder"]
    > ![Güneş paneli Izleme şablonu yönetiminin ekran görüntüsü.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Sonraki adımlar
 
> [!div class="nextstepaction"]
> [Azure IoT Central-Solar paneli uygulama mimarisi](./concept-iot-central-solar-panel-app.md)

