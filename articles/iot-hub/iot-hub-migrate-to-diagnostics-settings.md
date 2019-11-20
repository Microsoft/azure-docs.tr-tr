---
title: Azure IoT Hub tanılama ayarlarına geçiş | Microsoft Docs
description: Azure IoT Hub 'yi, IoT Hub 'ınızdaki işlemlerin durumunu gerçek zamanlı olarak izlemek üzere Operations Monitoring yerine Azure tanılama ayarlarını kullanacak şekilde güncelleştirme.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 0feca8b477a1d76eae9e3f41de0026a33cc3249d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259346"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>IoT Hub işlemler izlemeden tanılama ayarlarına geçirin

IoT Hub içindeki işlemlerin durumunu izlemek için [İşlem izlemeyi](iot-hub-operations-monitoring.md) kullanan müşteriler, bu Iş akışını Azure izleyici 'nin bir özelliği olan [Azure tanılama ayarlarına](../azure-monitor/platform/resource-logs-overview.md)geçirebilir. Tanılama ayarları birçok Azure hizmeti için kaynak düzeyinde tanılama bilgilerini sağlar.

**IoT Hub işlemler izleme işlevi kullanım dışıdır**ve portaldan kaldırılmıştır. Bu makalede, iş yüklerinizi işlemler izlemeden tanılama ayarlarına taşıma adımları sağlanır. Kullanımdan kaldırma zaman çizelgesi hakkında daha fazla bilgi için bkz. Azure [izleme Ile Azure IoT çözümlerinizi izleme ve Azure Kaynak durumu](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Güncelleştirme IoT Hub

Azure portal IoT Hub güncelleştirmek için, önce tanılama ayarlarını açın, sonra işlem izlemeyi devre dışı bırakın.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>İşlem izlemeyi kapat

> [!NOTE]
> 11 Mart 2019 itibariyle, işlemler izleme özelliği IoT Hub Azure portal arabiriminden kaldırılmıştır. Aşağıdaki adımlar artık uygulanmaz. Geçiş yapmak için yukarıdaki Azure Izleyici tanılama ayarlarında doğru kategorilerin açık olduğundan emin olun.

Yeni tanılama ayarlarını iş akışınızda test etmeniz durumunda, işlemler izleme özelliğini kapatabilirsiniz. 

1. IoT Hub menüsünde, **işlem izleme**' yi seçin.

2. Her izleme kategorisinin altında **hiçbiri**' ni seçin.

3. İşlemleri izleme değişikliklerini kaydedin.

## <a name="update-applications-that-use-operations-monitoring"></a>İşlem izlemeyi kullanan uygulamaları güncelleştirme

İşlem izleme ve tanılama ayarlarının şemaları biraz farklılık gösterir. Tanılama ayarları tarafından kullanılan şemaya eşlemek için, günümüzde işlemleri izleme kullanan uygulamaları güncelleştirmeniz önemlidir. 

Ayrıca, Tanılama ayarları izleme için beş yeni kategori sunar. Mevcut şema için uygulamaları güncelleştirdikten sonra yeni kategorileri de ekleyin:

* Buluttan cihaza ikizi işlemleri
* Cihazdan buluta ikizi işlemleri
* İkizi sorguları
* İş işlemleri
* Doğrudan Yöntemler

Belirli şema yapıları için bkz. [Tanılama ayarları şemasını anlama](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Düşük gecikme süresi ile cihaz bağlama ve bağlantı kesme olaylarını izleme

Cihazdaki cihaz bağlantısını ve bağlantı kesmeyi izlemek için, uyarıları almak ve cihaz bağlantı durumunu izlemek üzere Event Grid üzerindeki [ **cihaz bağlantısı kesilen** olaya](iot-hub-event-grid.md#event-types) abone olmayı öneririz. Bu [öğreticiyi](iot-hub-how-to-order-connection-state-events.md) kullanarak cihaz bağlantılı ve cihaz bağlantısı kesilen olayları ıot çözümünüzdeki IoT Hub tümleştirme hakkında bilgi edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure IoT Hub durumunu izleyin ve sorunları hızla tanılayın](iot-hub-monitor-resource-health.md)
