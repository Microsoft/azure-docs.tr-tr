---
title: 'Öğretici: IoT Central ile akıllı ölçüm analizi uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak akıllı ölçüm izleme uygulaması oluşturmayı öğrenin.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a73f4d75811a384eb822d1f8594a22506509d560
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112545"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Öğretici: akıllı ölçüm izleme uygulaması şablonunu oluşturma ve adım adım izleme 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğretici, benzetimli verilerle örnek bir cihaz modeli içeren akıllı ölçüm izleme uygulaması oluşturma sürecinde size rehberlik eder. Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Akıllı ölçüm uygulamasını ücretsiz oluşturun
> * Uygulama izlenecek yol
> * Kaynakları temizleme


Aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Önkoşullar
- None
- Azure aboneliği önerilir, ancak denemek için gerekli değildir

## <a name="create-a-smart-meter-monitoring-app"></a>Akıllı sayaç izleme uygulaması oluşturma 

Bu uygulamayı üç basit adımda oluşturabilirsiniz:

1. [Azure IoT Central giriş sayfasını](https://apps.azureiotcentral.com) açın ve yeni bir uygulama oluşturmak için **Oluştur** ' a tıklayın. 

2. **Enerji** sekmesini seçin ve **akıllı ölçüm izleme** uygulaması kutucuğunda uygulama **Oluştur** ' a tıklayın.

    > [!div class="mx-imgBorder"]
    > ![Build App](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **Uygulama oluştur** **Yeni uygulama** formunu açar. İstenen ayrıntıları aşağıdaki şekilde gösterildiği gibi girin:
    * **Uygulama adı**: IoT Central uygulamanız için bir ad seçin. 
    * **URL**: bir IoT Central URL 'si seçin, platform benzersiz olduğunu doğrular.
    * **7 günlük ücretsiz deneme**: zaten bir Azure aboneliğiniz varsa varsayılan ayar önerilir. Azure aboneliğiniz yoksa ücretsiz deneme sürümü ile başlayın.
    * **Faturalandırma bilgisi**: uygulamanın kendisi ücretsizdir. Uygulamanıza yönelik kaynakları sağlamak için dizin, Azure aboneliği ve bölge ayrıntıları gereklidir.
    * Sayfanın alt kısmındaki **Oluştur** düğmesine tıklayın, uygulamanız bir dakika içinde oluşturulur.     
        > [!div class="mx-imgBorder"]
        > Yeni ![uygulama formu](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Uygulamayı ve sanal verileri doğrulama

Yeni oluşturulan akıllı ölçüm uygulaması, uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Bu uygulamayı değiştirmeden önce uygulamanın dağıtıldığından ve beklendiği gibi çalıştığından emin olalım.

Uygulama oluşturma ve veri benzetimini doğrulamak için **panoya**gidin. Kutucukları bazı verilerle birlikte görebiliyorsanız, uygulama dağıtımınız başarılı olmuştur. Veri simülasyonu, verileri oluşturmak birkaç dakika sürebilir, bu nedenle 1-2 dakika bekleyin. 

## <a name="application-walk-through"></a>Uygulama izlenecek yol
Uygulama şablonunu başarıyla dağıttıktan sonra, örnek akıllı ölçüm cihazı, cihaz modeli ve bir pano ile birlikte gelir. 

Adatum, akıllı ölçümleri izleyen ve yöneten kurgusal bir enerji şirketidir. Akıllı ölçüm izleme panosunda akıllı ölçüm özellikleri, veriler ve örnek komutlar görürsünüz. Operatörlerin ve destek ekiplerinin destek olaylarına geçmeden önce aşağıdaki etkinlikleri proaktif olarak gerçekleştirmesini sağlar: 
* Haritada en son ölçüm bilgilerini ve yüklü konumunu gözden geçirin
* Ölçüm ağını ve bağlantı durumunu proaktif olarak denetleme 
* Ağ durumu için min ve Max voltaj ayarlarını izleme 
* Anormal desenleri yakalamak için enerji, güç ve voltaj eğilimlerini gözden geçirin 
* Planlama ve faturalandırma amaçlarıyla toplam enerji tüketimini izleyin
* Yeniden bağlama ölçer ve üretici yazılımı sürümünü güncelleştir gibi komut ve denetim işlemleri. Şablonda, komut düğmeleri olası işlevleri gösterir ve gerçek komutları göndermez. 

> [!div class="mx-imgBorder"]
> Akıllı ölçüm izleme panosu ![](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Cihazlar
Uygulama, örnek bir akıllı ölçüm cihazından gelir. **Cihazlar** sekmesine tıklayarak cihaz ayrıntılarını görebilirsiniz.

> [!div class="mx-imgBorder"]
> ![akıllı ölçüm cihazları](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Cihaz ayrıntılarını görmek için örnek cihaz **SM0123456789** bağlantısına tıklayın. Aygıtın yazılabilir özelliklerini **güncelleştirme özellikleri** sayfasında güncelleştirebilir ve panoda güncelleştirilmiş değerleri görselleştirebilirsiniz.

> [!div class="mx-imgBorder"]
> Akıllı Ölçüm özellikleri ![](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Cihaz şablonu
Akıllı ölçüm cihaz modelini görmek için **cihaz şablonları** sekmesine tıklayın. Modelde veriler, özellikler, komutlar ve görünümler için önceden tanımlama arabirimi bulunur.

> [!div class="mx-imgBorder"]
> Akıllı ölçüm cihaz şablonlarını ![](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam etmeyi istemediğinize karar verirseniz, aşağıdaki adımları izleyerek uygulamanızı silin:

1. Sol bölmeden Yönetim sekmesini açın
2. Uygulama ayarları ' nı seçin ve sayfanın altındaki Sil düğmesine tıklayın. 

    > [!div class="mx-imgBorder"]
    > ![uygulama](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png) Sil


## <a name="next-steps"></a>Sonraki adımlar
* Akıllı ölçüm uygulama mimarisi hakkında bilgi edinin [kavram makalesine](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app) bakın
* Ücretsiz olarak akıllı ölçüm uygulama şablonları oluşturun: [akıllı ölçüm](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) uygulaması
* IoT Central hakkında daha fazla bilgi için bkz. [IoT Central genel bakış](https://docs.microsoft.com/azure/iot-central/)
