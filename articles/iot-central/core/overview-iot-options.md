---
title: Microsoft Azure IoT seçenekleri | Microsoft Docs
description: Azure IoT Central, IoT Çözüm Hızlandırıcıları veya IoT Hub kullanarak Azure IoT çözümünüzü nasıl uygulayacağınızı seçin.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 2ec6e1d00d331a7bb8dd7182dd4b0c91dd3d25a7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049084"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Azure IoT Central ile Azure IoT seçeneklerini karşılaştırma

Microsoft Azure IoT Central ve Azure IoT, IoT çözümü oluşturmak için çeşitli seçenekler sunar. Bu seçenekler, farklı müşteri gereksinimleri kümesi için uygundur:

* [Azure IoT Central](overview-iot-central.md) , bulut çözümü geliştirmede uzmanlığa gerek duymadan kurumsal düzeyde IoT çözümleri oluşturmanıza yardımcı olmak için model tabanlı bir yaklaşım kullanan IoT uygulama platformudur.

* [Azure IoT Çözüm Hızlandırıcıları](https://docs.microsoft.com/azure/iot-accelerators/) , özel IoT çözümlerinin geliştirilmesini hızlandırmanıza yardımcı olan bir hizmet olarak Azure platformu (PaaS) üzerinde oluşturulmuş [çözüm hızlandırıcılarına](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) yönelik kurumsal düzeyde bir koleksiyondur.

## <a name="azure-iot-hub"></a>Azure IoT Hub’ı

Azure IoT Hub, hem Azure IoT Central hem de Azure IoT çözüm hızlandırıcılarının kullandığı temel Azure PaaS çözümüdür. IoT Hub Milyonlarca IoT cihazı ve bir bulut çözümü arasında güvenilir ve güvenli çift yönlü iletişimleri destekler. IoT Hub, şunun gibi IoT uygulama sorunlarını karşılamanıza yardımcı olur:

* Yüksek hacimli cihaz bağlantısı ve yönetimi
* Yüksek hacimli telemetri alımı
* Cihazların komutu ve denetimi
* Cihaz güvenliği zorlaması

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Azure IoT Central ve Azure IoT çözüm hızlandırıcılarını karşılaştırma

Azure IoT ürününüzün seçilmesi, IoT çözümünüzü planlamanın önemli bir parçasıdır. IoT Hub, kendi başına uçtan uca bir IoT çözümü sağlamayan bir Azure hizmetidir. IoT Hub tüm IoT çözümleri için bir başlangıç noktası olarak kullanabilirsiniz. Ayrıca, IoT Hub kullanmak için Azure IoT Çözüm Hızlandırıcıları veya Azure IoT Central kullanmanıza gerek yoktur. IoT çözüm hızlandırıcıları ve Azure IoT Central’ın her ikisi de, diğer Azure hizmetleriyle birlikte IoT Hub’ı kullanır.

Aşağıdaki tabloda, gereksinimleriniz için doğru ürünü seçmenize yardımcı olmak üzere IoT çözüm hızlandırıcıları ile Azure IoT Central arasındaki temel farklılıklar özetlenmiştir:

|     | Azure IoT Central | Azure IoT çözüm hızlandırıcıları |
| --- | ----------- | --------- |
| Birincil kullanım                      | Ayrıntılı hizmet özelleştirmesi gerektirmeyen basit IoT çözümleri için pazarlama süresini kısaltma.                                                    | En üst düzeyde esneklik gerektiren özel bir IoT çözümünün geliştirilmesini hızlandırma.                                                                                                                             |
| Temel alınan PaaS hizmetlerine erişim | Tam olarak yönetilen bir çözüm olduğundan, temel alınan hizmetler gösterilmez.                                                                                            | Temel alınan Azure hizmetlerini yönetmek veya gerektiğinde değiştirmek için bu hizmetlere erişebilirsiniz.                                                                                                                    |
| Esneklik                        | Orta. Yerleşik tarayıcı tabanlı kullanıcı deneyimini kullanarak çözüm modelini ve kullanıcı arabirimini farklı yönlerini özelleştirebilirsiniz. Farklı bileşenler gösterilmediğinden altyapı özelleştirilemez. | Yüksek. Mikro hizmetlerin kodu açık kaynaktır ve uygun gördüğünüz herhangi bir şekilde değiştirebilirsiniz. Ayrıca, dağıtım altyapısını özelleştirebilirsiniz.                                               |
| Beceri düzeyi                        | Düşük. Çözümü özelleştirmek için modelleme becerileri gerekir. Hiçbir kodlama becerisi gerekli değildir.                                                                          | Orta yüksek. Çözümün arka ucunun özelleştirilmesi için Java veya .NET becerileri gerekir. Görselleştirmeyi özelleştirmek için JavaScript becerileri gerekir.                                                                       |
| Başlangıç deneyimi             | Uygulama şablonları ve cihaz şablonları önceden oluşturulmuş modeller sağlar. Birkaç dakika içinde dağıtılabilir.                                                                                                  | Önceden yapılandırılmış çözümler yaygın IOT senaryolarını uygular. Birkaç dakika içinde dağıtılabilir.                                                                                                                            |
| Fiyatlandırma                            | Basit, tahmin edilebilir fiyatlandırma yapısı.                                                                                                                           | Maliyet denetlemek için hizmetlerde ince ayar yapabilirsiniz.                                                                                                                                                            |

IoT çözümünüzü oluşturmak için kullanılacak ürün şunlara bağlıdır:

* İş gereksinimleriniz.
* Derlemek istediğiniz çözüm türü.
* Kuruluşunuzun çözüm oluşturma ve uzun vadede sürdürme becerileri.

## <a name="next-steps"></a>Sonraki adımlar

Seçtiğiniz ürün ve yaklaşıma bağlı olarak, önerilen sonraki adımlar şunlardır:

* **Azure IoT Central**: [Azure IoT Central nedir?](overview-iot-central.md)
* **IoT Çözüm Hızlandırıcıları**: [Azure IoT Çözüm Hızlandırıcıları nelerdir?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [Azure IoT Hub nedir?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)