---
title: Azure IoT Central mimari kavramları-enerji | Microsoft Docs
description: Bu makalede, Azure IoT Central enerji uygulama şablonu mimarisiyle ilgili temel kavramlar tanıtılmaktadır
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: cab593990369bb8a4135e82534a2eb409b574e96
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582755"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central-akıllı ölçüm uygulama mimarisi

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede, akıllı ölçüm izleme uygulama şablonu mimarisine genel bir bakış sunulmaktadır. Aşağıdaki diyagramda, Azure 'da IoT Central platformu kullanarak akıllı ölçüm uygulaması için yaygın olarak kullanılan bir mimari gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![akıllı ölçüm mimarisi](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Bu mimari aşağıdaki bileşenlerden oluşur. Bazı çözümler burada listelenen her bileşeni gerektirmeyebilir.

## <a name="smart-meters-and-connectivity"></a>Akıllı ölçümler ve bağlantı 

Akıllı ölçüm, tüm enerji varlıkları arasındaki en önemli cihazlardan biridir. Tüketim ve ödeme ve talep yanıtı gibi diğer kullanım durumları için enerji tüketimi verilerini kaydeder ve bu programlarla iletişim kurar. Ölçüm türüne göre, ağ geçitleri aracılığıyla veya diğer ara cihazlar ya da sistemler (örneğin, uç cihazlar ve baş uç sistemler) aracılığıyla IoT Central bağlanabilir. Doğrudan bağlanmayan cihazlara bağlanmak için IoT Central cihaz Köprüsü oluşturun. IoT Central cihaz Köprüsü açık kaynaklı bir çözümdür ve tüm ayrıntıları [burada](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)bulabilirsiniz. 


## <a name="iot-central-platform"></a>IoT Central platform

Azure IoT Central IoT çözümünüzü oluşturmayı kolaylaştıran ve IoT yönetimi, işlemler ve geliştirmenin yükünü ve maliyetlerini azaltmaya yardımcı olan bir platformdur. IoT Central, Nesnelerin İnterneti (IoT) varlıklarınızı ölçeklendirmek için kolayca bağlayabilirsiniz, izleyebilir ve yönetebilirsiniz. Akıllı ölçümlerinizi IoT Central 'e bağladığınızda, uygulama şablonu cihaz modelleri, komutlar ve panolar gibi yerleşik özellikleri kullanır. Uygulama şablonu Ayrıca, neredeyse gerçek zamanlı ölçüm verileri izleme, analiz, kurallar ve görselleştirme gibi sıcak yol senaryoları için IoT Central depolama alanını kullanır. 


## <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central ile derlemek için genişletilebilirlik seçenekleri
IoT Central platformu iki genişletilebilirlik seçeneği sağlar: sürekli veri dışa aktarma (CDE) ve API 'Ler. Müşteriler ve iş ortakları, çözümlerini belirli gereksinimlere göre özelleştirmek için bu seçenekler arasında seçim yapabilir. Örneğin, iş ortaklarımızın biri Azure Data Lake Storage (ADLS) ile CDE olarak yapılandırıldı. Bunlar, uzun süreli veri saklama ve diğer soğuk yol depolama senaryoları, örneğin, toplu işleme, denetim ve raporlama amaçları için ADLS 'yi kullanıyor. 

## <a name="next-steps"></a>Sonraki adımlar

* Artık mimari hakkında bilgi edindiğinize göre, [ücretsiz olarak akıllı ölçüm uygulaması oluşturun](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* IoT Central hakkında daha fazla bilgi edinmek için bkz. [IoT Central genel bakış](https://docs.microsoft.com/azure/iot-central/)
