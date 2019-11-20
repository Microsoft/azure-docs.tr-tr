---
title: Azure IoT Central sürekli hasta izleme mimarisi | Microsoft Docs
description: Sürekli hasta izleme çözüm mimarisi hakkında bilgi edinin.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027483"
---
# <a name="continuous-patient-monitoring-architecture"></a>Sürekli hasta izleme mimarisi

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Sürekli hasta izleme çözümleri, sunulan uygulama şablonuyla ve kılavuz olarak aşağıda özetlenen mimari kullanılarak oluşturulabilir.

>[!div class="mx-imgBorder"] 
>![CPM mimarisi](media/cpm-architecture.png)

1. Bluetooth düşük enerji (BLE) aracılığıyla iletişim kuran tıbbi cihazlar
1. Mobil telefon ağ geçidi, verileri alan ve IoT Central gönderme
1. Azure API 'sine FHıR için sürekli veri aktarma&reg;
1. Birlikte çalışabilen verileri temel alan makine öğrenimi
1. FHıR verileri üzerinde oluşturulmuş ekip panosu

## <a name="details"></a>Ayrıntılar
Bu bölümde, mimari diyagramın her bir bölümü daha ayrıntılı olarak özetlenmektedir.

### <a name="ble-medical-devices"></a>BLEME cihazları
Sağlık IoT alanında kullanılan birçok tıp wearables, Bluetooth düşük enerji cihazlarıdır, bu da doğrudan buluta konuşamazlar ve bir ağ geçidinden geçiş yapması gerekecektir. Bu mimaride, bu ağ geçidi olarak bir mobil telefon uygulaması kullanılması önerilir.

### <a name="mobile-phone-gateway"></a>Cep telefonu ağ geçidi
Cep telefonu uygulamasının birincil işlevi, tıbbi cihazlardan verileri almak ve Azure IoT Central ile iletişim kurmak için kullanılır. Ayrıca, uygulama, bir cihaz kurulumu ve sağlama akışından hastalara rehberlik etmenize yardımcı olabilir ve kişisel sistem durumu verilerinin bir görünümünü görebilirler. Aynı iletişim akışına ulaşmak için bir hastanoda içindeyse, diğer çözümler bir tablet ağ geçidini veya statik ağ geçidini kullanabilir.

### <a name="export-to-azure-api-for-fhirreg"></a>FHıR için Azure API 'ye dışarı aktarma&reg;
Azure IoT Central, HIPAA uyumludur ve ITRUST&reg; sertifikalı, ancak sistem durumu ile ilgili verileri FHIR için Azure API 'sine göndermek de isteyebilirsiniz. [FHıR Için Azure API](../../healthcare-apis/overview.md) 'si, sistem durumu verilerinize yönelik yeni bir katılım sistemi oluşturmanıza olanak sağlayan, klinik sağlık verilerine yönelik tam olarak yönetilen, standartlara dayalı ve uyumlu bir API 'dir. Bulut ortamında yönetilen bir hizmet olarak platform (PaaS) tarafından desteklenen FHıR API 'Leri aracılığıyla verilerin hızlı bir şekilde değiş tokuşuna izin vermez. IoT Central sürekli veri dışa aktarma işlevini kullanarak FHIR için Azure API 'sine veri gönderebilirsiniz.

### <a name="machine-learning"></a>Makine öğrenme
Verilerinizi toplayarak ve FHıR biçimine çevirerek, öngörüleri zenginleştirmek ve bakım ekibiniz için daha akıllı karar verme olanağı sağlayan makine öğrenimi modelleri oluşturabilirsiniz. Makine öğrenimi modellerini derlemek, eğitebilmek ve dağıtmak için kullanılabilecek çeşitli hizmetler vardır. Azure 'un makine öğrenimi tekliflerini kullanma hakkında daha fazla bilgi için [makine öğrenimi belgelerimizde](../../machine-learning/index.yml)bulabilirsiniz.

### <a name="provider-dashboard"></a>Sağlayıcı panosu
FHıR için Azure API 'sinde bulunan veriler bir hasta içgörüler panosu oluşturmak için veya ekiplerin hasta durumunu görselleştirmesine yardımcı olmak için doğrudan bir EMR ile tümleştirilebilecek şekilde kullanılabilir. Ekipler bu panoyu, yardım gerektiren hastalara yönelik işlem yapmak ve daha erken uyarı işaretleri belirlemek için kullanabilir. Power BI gerçek zamanlı bir sağlayıcı panosu oluşturmayı öğrenmek için [nasıl yapılır kılavuzumuzu](howto-health-data-triage.md)izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Sürekli hasta izleme uygulaması şablonunu dağıtmayı öğrenin](tutorial-continuous-patient-monitoring.md)