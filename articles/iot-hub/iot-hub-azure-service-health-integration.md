---
title: Azure IoT Hub hizmetini ve kaynak sistem durumunu denetleme | Microsoft Docs
description: IoT Hub izlemek için Azure hizmet durumu ve Azure Kaynak Durumu kullanma
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548584"
---
# <a name="check-iot-hub-service-and-resource-health"></a>IoT Hub hizmeti ve kaynak durumunu denetleme

Azure IoT Hub, IoT Hub hizmetinin ve tek tek IoT Hub 'larının hizmet düzeyi sistem durumunu izlemenize olanak sağlamak için [Azure hizmet durumu hizmeti](../service-health/overview.md) ile tümleşir. Ayrıca, IoT Hub hizmetin veya bir IoT Hub 'ının durumu değiştiğinde bildirim almak için de uyarı kurabilirsiniz. Azure hizmet sistem durumu hizmeti, Azure Kaynak Durumu, Azure hizmet durumu ve Azure durum sayfası olmak üzere üç daha küçük hizmetin bir birleşimidir. Bu makaledeki bölümler, her bir hizmeti ve IoT Hub ilişkilerini daha ayrıntılı olarak anlatmaktadır.

Azure hizmet durumu hizmeti, IoT Hub hizmetinin ve bireysel IoT Hub 'larınızın kullanılabilirliğini etkileyebilecek kesintiler ve yükseltmeler gibi hizmet düzeyi olaylarını izlemenize yardımcı olur. IoT Hub Ayrıca, belirli bir IoT Hub 'ında gerçekleşen işletimsel hata ve koşulları izlemek için kullanabileceğiniz IoT Hub platform ölçümleri ve IoT Hub kaynak günlükleri sağlamak üzere Azure Izleyici ile tümleşir. Daha fazla bilgi için bkz. [izleyici IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Azure Kaynak Durumu bir IoT Hub 'ının durumunu denetleme

Azure Kaynak Durumu, Azure Service Health hizmetinin tek tek kaynakların sistem durumunu izleyen bir parçasıdır. IoT Hub 'ınızın sistem durumunu doğrudan portaldan kontrol edebilirsiniz.

Portalı kullanarak IoT Hub 'ınızın durum ve durum geçmişini görmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), Azure Portal IoT Hub 'ınıza gidin.

1. Sol bölmede, **destek + sorun giderme** altında **kaynak durumu**' yi seçin.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT Hub 'ı için kaynak durumu sayfası":::

Azure Kaynak Durumu ve sistem durumu verilerini yorumlama hakkında daha fazla bilgi edinmek için bkz. Azure hizmet durumu belgelerinde [kaynak durumu genel bakış](../service-health/resource-health-overview.md) .

IoT Hub 'ınızın sağlık durumu değiştiğinde tetiklenecek uyarıları ayarlamak için **kaynak sistem durumu uyarısı Ekle** ' yi de seçebilirsiniz. Daha fazla bilgi edinmek için bkz. Azure hizmet durumu belgelerindeki [Uyarıları Yapılandırma hizmeti sistem durumu olayları](../service-health/alerts-activity-log-service-notifications-portal.md) ve ilgili konular.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Azure hizmet durumu ile aboneliğinizdeki IoT Hub 'larının sistem durumunu denetleme

Azure hizmet durumu ile aboneliğinizdeki tüm IoT Hub 'larının sistem durumunu kontrol edebilirsiniz.

IoT Hub 'larınızın sistem durumunu denetlemek için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Hizmet sistem durumu**  >  **kaynak sistem durumu**' na gidin.

3. Aşağı açılan kutularda, aboneliğinizi seçin ve kaynak türü olarak **IoT Hub** ' yi seçin.

Azure hizmet durumu hakkında daha fazla bilgi edinmek ve sistem durumu verilerini yorumlama hakkında daha fazla bilgi için bkz. Azure hizmet durumu belgelerindeki [hizmet durumu genel bakış](../service-health/service-health-overview.md) .

Azure hizmet durumu ile uyarıları ayarlama hakkında bilgi edinmek için bkz. Azure hizmet durumu belgelerindeki [Uyarıları yapılandırma, hizmet durumu olayları](../service-health/alerts-activity-log-service-notifications-portal.md) ve ilgili konular.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Azure durum sayfasında bölgeye göre IoT Hub hizmetinin sistem durumunu denetle

IoT Hub ve diğer hizmetlerin dünya genelinde bölge durumunu görmek için [Azure durum sayfasını](https://status.azure.com/status)kullanabilirsiniz. Azure durum sayfası hakkında daha fazla bilgi için bkz. Azure hizmet durumu belgelerindeki [Azure durumuna genel bakış](../service-health/azure-status-overview.md) .

## <a name="next-steps"></a>Sonraki adımlar

* Azure hizmet durumu, Azure Kaynak Durumu ve Azure durum sayfası hakkındaki ayrıntılar için bkz. [Azure hizmet durumu hizmeti](../service-health/overview.md) .
* Bkz. Azure IoT Hub izleme açıklaması için bkz. [azure IoT Hub](monitor-iot-hub.md) izleme.
