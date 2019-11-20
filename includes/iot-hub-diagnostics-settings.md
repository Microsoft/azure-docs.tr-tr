---
title: include dosyası
description: include dosyası
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266832"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Tanılama ayarlarıyla günlüğü etkinleştir

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Oturum [Azure portalında](https://portal.azure.com) ve IOT hub'ınıza gidin.

2. **Tanılama ayarları**' nı seçin.

3. Seçin **tanılamayı Aç**.

   ![Tanılamayı aç](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Tanılama ayarlarına bir ad verin.

5. Günlükleri nereye göndermek istediğinizi seçin. Üç seçenekten herhangi bir birleşimini seçebilirsiniz:

   * Bir depolama hesabına arşivle
   * Bir olay hub'ına akış yap
   * Log Analytics'e gönder

6. Hangi işlemleri izlemek istediğinizi seçin ve bu işlemler için günlükleri etkinleştirin. Tanılama ayarlarının rapor aldığı işlemler şunlardır:

   * Bağlantılar
   * Cihaz telemetrisi
   * Bulut-cihaz iletilerini
   * Cihaz kimliği işlemleri
   * Dosya yüklemeleri
   * İleti yönlendirme
   * Buluttan cihaza ikizi işlemleri
   * Cihazdan buluta ikizi işlemleri
   * İkizi işlemleri
   * İş işlemleri
   * Doğrudan yöntemler  
   * Dağıtılmış izleme (Önizleme)
   * Yapılandırmaları
   * Cihaz akışları
   * Cihaz ölçümleri

6. Yeni ayarları kaydedin. 

Tanılama ayarlarını PowerShell ile açmak istiyorsanız aşağıdaki kodu kullanın:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Yeni ayarları yaklaşık 10 dakika içinde etkinleşir. Bundan sonra, Günlükler **Tanılama ayarları** dikey penceresinde yapılandırılan arşivleme hedefi ' nde görüntülenir. Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızdan günlük verilerini toplama ve](../articles/azure-monitor/platform/resource-logs-overview.md)kullanma.
