---
title: 'Öğretici: IoT Central bir Solar penal izleme uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak Solar Panel uygulaması oluşturmayı öğrenin.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 5560cb31e31f7cf33ac8ae71d649e88be3dbb6e6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112514"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Öğretici: güneş paneli izleme uygulama şablonunu oluşturun ve ilerleyin 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğretici, sanal verileri içeren örnek bir cihaz modeli içeren Solar paneli izleme uygulaması oluşturma sürecinde size rehberlik eder. Bu öğreticide şunları öğreneceksiniz:


> [!div class="checklist"]
> * Güneş paneli uygulamasını ücretsiz oluşturun
> * Uygulama izlenecek yol
> * Kaynakları temizleme


Aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Önkoşullar
- None
- Azure aboneliği önerilir, ancak denemek için gerekli değildir


## <a name="create-a-solar-panel-monitoring-app"></a>Güneş paneli izleme uygulaması oluşturma 

Bu uygulamayı üç basit adımda oluşturabilirsiniz:

1. [Azure IoT Central giriş sayfasını](https://apps.azureiotcentral.com) açın ve yeni bir uygulama oluşturmak için **Oluştur** ' a tıklayın. 

2. **Enerji** sekmesini seçin ve sonra da **güneş paneli izleme** uygulaması kutucuğunda **uygulama oluştur** ' a tıklayın. 

    > [!div class="mx-imgBorder"]
    > ![Build App](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Uygulama oluştur** **Yeni uygulama** formunu açar. İstenen ayrıntıları aşağıdaki şekilde gösterildiği gibi girin:
    * **Uygulama adı**: IoT Central uygulamanız için bir ad seçin. 
    * **URL**: bir IoT Central URL 'si seçin, platform benzersiz olduğunu doğrular.
    * **7 günlük ücretsiz deneme**: zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. Azure aboneliğiniz yoksa ücretsiz deneme sürümü ile başlayın.
    * **Faturalandırma bilgisi**: uygulamanın kendisi ücretsizdir. Uygulamanıza yönelik kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
    * Sayfanın alt kısmındaki **Oluştur** düğmesine tıklayın, uygulamanız bir dakika içinde oluşturulur.
        > [!div class="mx-imgBorder"]
        > Yeni ![uygulama formu](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Uygulamayı ve sanal verileri doğrulama

Yeni oluşturulan Solar paneli uygulaması, uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Bu uygulamayı değiştirmeden önce uygulamanın dağıtıldığından ve beklendiği gibi çalıştığından emin olalım.

Uygulama oluşturma ve veri benzetimini doğrulamak için **panoya**gidin. Kutucukları bazı verilerle birlikte görebiliyorsanız, uygulama dağıtımınız başarılı olmuştur. Veri simülasyonu, verileri oluşturmak birkaç dakika sürebilir, bu nedenle 1-2 dakika bekleyin. 

## <a name="application-walk-through"></a>Uygulama izlenecek yol
Uygulama şablonunu başarıyla dağıttıktan sonra, örnek akıllı ölçüm cihazı, cihaz modeli ve Pano ile birlikte gelir.

Adatum, Solar bölmelerini izleyen ve yöneten kurgusal bir enerji şirketidir. Solar paneli izleme panosunda, güneş paneli özellikleri, verileri ve örnek komutları görürsünüz. Operatörlerin ve destek ekiplerinin destek olaylarına geçmeden önce aşağıdaki etkinlikleri proaktif olarak gerçekleştirmesini sağlar:
* En son panel bilgilerini ve haritadaki yüklü konumunu gözden geçirin
* Panel durumunu ve bağlantı durumunu proaktif olarak denetleme
* Anormal desenleri yakalamak için enerji oluşturma ve sıcaklık eğilimlerini gözden geçirin
* Planlama ve faturalandırma amaçlarıyla toplam enerji üretimini izleyin
* Etkinleştirme paneli ve bellenim sürümünü güncelleştirme gibi komut ve denetim işlemleri. Şablonda, komut düğmeleri olası işlevleri gösterir ve gerçek komutları göndermez.

> [!div class="mx-imgBorder"]
> ![Solar paneli izleme panosu](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Cihazlar
Uygulama bir örnek Solar Panel aygıtıyla birlikte gelir. **Cihazlar** sekmesine tıklayarak cihaz ayrıntılarını görebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Solar Panel cihazları](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Cihaz ayrıntılarını görmek için örnek cihaz **SP0123456789** bağlantısına tıklayın. **Güncelleştirme özellikleri** sayfasında, cihazın yazılabilir özelliklerini güncelleştirebilir ve panoda güncelleştirilmiş değerleri görselleştirebilirsiniz. 

> [!div class="mx-imgBorder"]
> ![Solar paneli özellikleri](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Cihaz şablonu
Güneş paneli cihaz modelini görmek için **cihaz şablonları** sekmesine tıklayın. Modelde veriler, özellikler, komutlar ve görünümler için önceden tanımlama arabirimi bulunur.

> [!div class="mx-imgBorder"]
> ![Solar paneli cihaz şablonu](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam etmeyi istemediğinize karar verirseniz, aşağıdaki adımları izleyerek uygulamanızı silin:

1. Sol bölmeden Yönetim sekmesini açın
2. Uygulama ayarları ' nı seçin ve sayfanın altındaki Sil düğmesine tıklayın. 

    > [!div class="mx-imgBorder"]
    > ![uygulama](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png) Sil


## <a name="next-steps"></a>Sonraki adımlar
* Güneş paneli uygulama mimarisi hakkında bilgi edinin [kavram makalesine](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app) bakın
* Ücretsiz paneli için uygulama şablonları oluşturun: [güneş paneli](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) uygulaması
* IoT Central hakkında daha fazla bilgi için bkz. [IoT Central genel bakış](https://docs.microsoft.com/azure/iot-central/)

