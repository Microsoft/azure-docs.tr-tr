---
title: Azure IoT Central mimari kavramları-Solar paneli | Microsoft Docs
description: Bu makalede, Azure IoT Central Solar paneli izleme uygulamasının mimarisiyle ilgili temel kavramlar tanıtılmaktadır.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1efb693e1ad9b9e9bfdbeafa27e9cc75c645baf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834298"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central-Solar paneli uygulama mimarisi

Bu makalede, güneş paneli izleme uygulama şablonu mimarisine genel bir bakış sunulmaktadır. Aşağıdaki diyagramda, Azure 'da IoT Central platformu kullanarak bir Solar Panel uygulaması için yaygın olarak kullanılan bir mimari gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Akıllı ölçüm mimarisi](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Bu mimari aşağıdaki bileşenlerden oluşur. Bazı uygulamalara burada listelenen bileşenlerin tümü gerekmeyebilir.

## <a name="solar-panels-and-connectivity"></a>Güneş bölmeleri ve bağlantısı

Güneş bölmeleri, yenilenebilir enerji açısından önemli kaynaklardan biridir. Solar paneli türüne ve ayarına bağlı olarak, ağ geçitlerini veya diğer ara cihazları ve özel sistemleri kullanarak da bağlanabilirsiniz. Cihazları bağlamak için doğrudan bağlanamamış IoT Central cihaz Köprüsü oluşturmanız gerekebilir. IoT Central cihaz Köprüsü açık kaynaklı bir çözümdür ve tüm ayrıntıları [burada](../core/howto-build-iotc-device-bridge.md)bulabilirsiniz. 

## <a name="iot-central-platform"></a>IoT Central platform
Azure IoT Central IoT çözümünüzü oluşturmayı kolaylaştıran ve IoT yönetimi, işlemler ve geliştirmenin yükünü ve maliyetlerini azaltmaya yardımcı olan bir platformdur. IoT Central, Nesnelerin İnterneti (IoT) varlıklarınızı ölçeklendirmek için kolayca bağlayabilirsiniz, izleyebilir ve yönetebilirsiniz. Solar panellerinizi IoT Central 'e bağladığınızda, uygulama şablonu cihaz modelleri, komutlar ve panolar gibi yerleşik özellikleri kullanır. Uygulama şablonu Ayrıca, neredeyse gerçek zamanlı ölçüm verileri izleme, analiz, kurallar ve görselleştirme gibi sıcak yol senaryoları için IoT Central depolama alanını kullanır.


## <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central ile derlemek için genişletilebilirlik seçenekleri
IoT Central platformu iki genişletilebilirlik seçeneği sağlar: sürekli veri dışa aktarma (CDE) ve API 'Ler. Müşteriler ve iş ortakları, çözümlerini belirli gereksinimlere göre özelleştirmek için bu seçenekler arasında seçim yapabilir. Örneğin, iş ortaklarımızın biri Azure Data Lake Storage (ADLS) ile CDE olarak yapılandırıldı. Bunlar, uzun süreli veri saklama ve diğer soğuk yol depolama senaryoları (örneğin, toplu işleme, denetim ve raporlama amaçları) için ADLS kullanıyor. 

## <a name="next-steps"></a>Sonraki adımlar

* Artık mimari hakkında bilgi edindiğinize göre, [güneş panel uygulamasını ücretsiz oluşturun](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* IoT Central hakkında daha fazla bilgi edinmek için bkz. [IoT Central genel bakış](../index.yml)
