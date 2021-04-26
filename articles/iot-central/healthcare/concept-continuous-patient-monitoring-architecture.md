---
title: Azure IoT Central sürekli hasta izleme mimarisi | Microsoft Docs
description: Öğretici-sürekli hasta izleme çözüm mimarisi hakkında bilgi edinin.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6f7359b2b2fb0a1ea6ce92ec52bba15fc74fc75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017163"
---
# <a name="continuous-patient-monitoring-architecture"></a>Sürekli hasta izleme mimarisi

Bu makalede, **sürekli hasta izleme** uygulaması şablonundan oluşturulan bir çözümün mimarisi açıklanmaktadır:

Sürekli hasta izleme çözümleri, sunulan uygulama şablonu kullanılarak oluşturulabilir ve aşağıdaki şekilde kılavuz olarak özetlenen mimari kullanılarak oluşturulabilir.

:::image type="content" source="media/cpm-architecture.png" alt-text="Sürekli hasta izleme mimarisi":::

## <a name="details"></a>Ayrıntılar

Bu bölüm, mimari diyagramın her bir parçasını daha ayrıntılı olarak özetler:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Bluetooth düşük enerji (BLE) tıbbi cihazlar

Sağlık IoT çözümlerinde kullanılan birçok tıp wearables, uyumlu olmayan cihazlardır. Bu cihazlar doğrudan buluta iletişim kuramaz ve Bulut çözümünüz ile veri alışverişi yapmak için bir ağ geçidi kullanmanız gerekir. Bu mimari, ağ geçidi olarak bir mobil telefon uygulaması kullanır.

### <a name="mobile-phone-gateway"></a>Cep telefonu ağ geçidi

Cep telefonu uygulamasının birincil işlevi, tıp cihazlarından AYRıLABILIR verileri toplayıp IoT Central ile iletişim kurmaktır. Uygulama Ayrıca cihaz kurulumu aracılığıyla hastalara kılavuzluk eder ve kendi kişisel sistem durumu verilerini görüntülemesine olanak tanır. Diğer çözümler bir tablet ağ geçidini veya bir saldırgan odasındaki statik ağ geçidini kullanabilir. Android ve iOS 'un uygulama geliştirme için başlangıç noktası olarak kullanması için açık kaynaklı örnek bir mobil uygulama kullanılabilir. Daha fazla bilgi edinmek için bkz. [IoT Central sürekli hasta izleme mobil uygulaması](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>FHıR için Azure API 'ye dışarı aktarma&reg;

Azure IoT Central HIPAA uyumludur ve ITRUST &reg; sertifikalı. Ayrıca, [FHıR Için Azure API](../../healthcare-apis/fhir/overview.md)kullanarak diğer hizmetlere hasta sistem durumu verileri gönderebilirsiniz. FHıR için Azure API, klinik sağlık verileri için standartlara dayalı bir API 'dir. [Fhır Için Azure IoT Bağlayıcısı](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) , IoT Central 'den sürekli veri dışa aktarma hedefi olarak fhır için Azure API 'si kullanmanıza olanak sağlar.

### <a name="machine-learning"></a>Makine öğrenimi

Bakım ekibinize yönelik Öngörüler ve destek kararı oluşturmak için FHıR verilerinize sahip makine öğrenimi modellerini kullanın. Daha fazla bilgi edinmek için bkz. [Azure Machine Learning belgeleri](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Sağlayıcı panosu

FHıR verileri için Azure API 'sini kullanarak bir hasta içgörüler panosu oluşturun veya bunu, ekip tarafından kullanılan bir elektronik tıbbi kayıtla doğrudan tümleştirin. Ekipler, hastalara yardımcı olmak ve daha erken uyarı işaretlerini belirlemek için panoyu kullanabilir. Daha fazla bilgi edinmek için [Power BI sağlayıcı oluşturma panosu](tutorial-health-data-triage.md) öğreticisine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Önerilen sonraki adım, [bir sürekli hasta izleme uygulama şablonunun nasıl dağıtılacağını öğrenmaktır](tutorial-continuous-patient-monitoring.md).