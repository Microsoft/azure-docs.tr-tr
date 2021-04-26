---
title: Azure IoT Hub işlemleri izlemeyi Azure Izleyici 'de kaynak günlüklerine IoT Hub geçirme | Microsoft Docs
description: Azure IoT Hub 'yi, IoT Hub 'ınızdaki işlemlerin durumunu gerçek zamanlı olarak izlemek için işlem izleme yerine Azure Izleyici 'yi kullanacak şekilde güncelleştirme.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591850"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>IoT Hub işlemler izlemeden Azure Izleyici kaynak günlüklerine geçirme

IoT Hub içindeki işlemlerin durumunu izlemek için [İşlem izlemeyi](iot-hub-operations-monitoring.md) kullanan müşteriler, Azure izleyici 'nin bir özelliği olan [Azure izleyici kaynak günlüklerine](../azure-monitor/essentials/platform-logs-overview.md)bu iş akışını geçirebilir. Kaynak günlükleri birçok Azure hizmeti için kaynak düzeyinde tanılama bilgilerini sağlar.

**IoT Hub işlemler izleme işlevi kullanım dışıdır** ve portaldan kaldırılmıştır. Bu makalede, iş yüklerinizi işlemler izlemeden Azure Izleyici kaynak günlüklerine taşımaya yönelik adımlar sağlanmaktadır. Kullanımdan kaldırma zaman çizelgesi hakkında daha fazla bilgi için bkz. Azure [izleme Ile Azure IoT çözümlerinizi izleme ve Azure Kaynak durumu](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Güncelleştirme IoT Hub

Azure portal IoT Hub güncelleştirmek için, önce bir tanılama ayarı oluşturun, sonra işlem izlemeyi kapatın.  

### <a name="create-a--diagnostic-setting"></a>Tanılama ayarı oluştur

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Sol bölmedeki **izleme** altında **Tanılama ayarları**' nı seçin. Ardından **Tanılama ayarı Ekle**' yi seçin.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Izleme bölümündeki tanılama ayarlarını vurgulayan ekran görüntüsü.":::

1. **Tanılama ayarı** bölmesinde, tanılama ayarına bir ad verin.

1. **Kategori ayrıntıları**' nın altında, izlemek istediğiniz işlemlerin kategorilerini seçin. IoT Hub bulunan işlemlerin kategorileri hakkında daha fazla bilgi için bkz. [kaynak günlükleri](monitor-iot-hub-reference.md#resource-logs).

1. **Hedef ayrıntıları**' nın altında, günlükleri nereye göndermek istediğinizi seçin. Bu hedeflerin herhangi bir birleşimini seçebilirsiniz:

   * Bir depolama hesabına arşivle
   * Bir olay hub'ına akış yap
   * Log Analytics çalışma alanı aracılığıyla Azure Izleyici günlüklerine gönder

   Aşağıdaki ekran görüntüsünde, bağlantı ve cihaz telemetri kategorilerindeki işlemleri bir Log Analytics çalışma alanına yönlendiren bir tanılama ayarı gösterilmektedir:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Tamamlanmış bir tanılama ayarını gösteren ekran görüntüsü.":::

1. Ayarları kaydetmek için **Kaydet**’i seçin.

Yeni ayarlar yaklaşık 10 dakika içinde etkili olur. Bundan sonra, Günlükler yapılandırılmış hedefte görüntülenir. Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızdan günlük verilerini toplama ve](../azure-monitor/essentials/platform-logs-overview.md)kullanma.

PowerShell ve Azure CLı dahil olmak üzere tanılama ayarlarını oluşturma hakkında daha ayrıntılı bilgi için bkz. Azure Izleyici belgelerindeki [Tanılama ayarları](../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="turn-off-operations-monitoring"></a>İşlem izlemeyi kapat

> [!NOTE]
> 11 Mart 2019 itibariyle, işlemler izleme özelliği IoT Hub Azure portal arabiriminden kaldırılmıştır. Aşağıdaki adımlar artık uygulanmaz. Geçiş yapmak için, yukarıdaki Azure Izleyici tanılama ayarıyla doğru kategorilerin bir hedefe yönlendirildiğinden emin olun.

Yeni tanılama ayarlarını iş akışınızda test etmeniz durumunda, işlemler izleme özelliğini kapatabilirsiniz. 

1. IoT Hub menüsünde, **işlem izleme**' yi seçin.

2. Her izleme kategorisinin altında **hiçbiri**' ni seçin.

3. İşlemleri izleme değişikliklerini kaydedin.

## <a name="update-applications-that-use-operations-monitoring"></a>İşlem izlemeyi kullanan uygulamaları güncelleştirme

İşlem izleme ve kaynak günlüklerinin şemaları biraz farklılık gösterir. Kaynak günlükleri tarafından kullanılan şemayla eşlenecek işlemleri izlemeyi bugün kullanan uygulamaları güncelleştirmeniz önemlidir.

Ayrıca, IoT Hub kaynak günlükleri izleme için beş yeni kategori sunar. Mevcut şema için uygulamaları güncelleştirdikten sonra yeni kategorileri de ekleyin:

* Buluttan cihaza ikizi işlemleri
* Cihazdan buluta ikizi işlemleri
* İkizi sorguları
* İş işlemleri
* Doğrudan Yöntemler

Belirli şema yapıları için bkz. [kaynak günlükleri](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Düşük gecikme süresi ile cihaz bağlama ve bağlantı kesme olaylarını izleme

Cihazdaki cihaz bağlantısını ve bağlantı kesmeyi izlemek için, uyarıları almak ve cihaz bağlantı durumunu izlemek üzere Event Grid üzerindeki [ **cihaz bağlantısı kesilen** olaya](iot-hub-event-grid.md#event-types) abone olmayı öneririz. Bu [öğreticiyi](iot-hub-how-to-order-connection-state-events.md) kullanarak cihaz bağlantılı ve cihaz bağlantısı kesilen olayları ıot çözümünüzdeki IoT Hub tümleştirme hakkında bilgi edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Hub’ı izleme](monitor-iot-hub.md)