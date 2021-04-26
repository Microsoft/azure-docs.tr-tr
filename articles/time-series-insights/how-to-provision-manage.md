---
title: Gen 2 ortamını yönetme-Azure zaman serisi | Microsoft Docs
description: Azure Time Series Insights Gen 2 ortamının nasıl yönetileceğini öğrenin.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461904"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 yönetme

[Azure CLI](./how-to-create-environment-using-cli.md) veya [Azure Portal](./how-to-create-environment-using-portal.md)kullanarak Azure Time Series Insights Gen2 ortamınızı oluşturduktan sonra, erişim ilkelerinizi ve diğer ortam özniteliklerini iş gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

## <a name="manage-the-environment"></a>Ortamı yönetme

[Azure Portal](https://portal.azure.com/)kullanarak Azure Time Series Insights Gen2 ortamınızı yönetebilirsiniz. Ortamınızı Azure portal aracılığıyla yönetirken göz önünde bulundurmanız için Gen2 ortamı ve Gen1 S1 veya Gen1 S2 ortamları arasında birkaç temel fark vardır:

* Azure portal Gen2 **genel bakış** bölmesi aşağıdaki değişiklikleri içerir:

  * Kapasite, Gen2 ortamları için uygulanmadığından kaldırılır.
  * **Zaman SERISI kimliği** özelliği eklendi. Verilerinizin bölümlenme şeklini belirler.
  * Başvuru veri kümeleri kaldırılır.
  * Görüntülenmiş URL sizi [Azure Time Series Insights Gezgini](./concepts-ux-panels.md)'ne yönlendirir.
  * Azure depolama hesabınızın adı listelenir.

* Ölçek birimleri Azure Time Series Insights Gen2 ortamlarına uygulanmadığından Azure portal **yapılandırma** bölmesi kaldırılır. Ancak, Yeni tanıtılan yarı depoyu yapılandırmak için **depolama yapılandırması** 'nı kullanabilirsiniz.

* Başvuru verileri kavramı, [zaman serisi modeliyle (TSD)](./concepts-model-overview.md)değiştirildiğinden, Azure Portal **başvuru verileri** bölmesi Azure Time Series Insights Gen2 ' de kaldırılır.

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure portal Gen2 ortamı Azure Time Series Insights":::

## <a name="next-steps"></a>Sonraki adımlar

* [Akış alma en iyi yöntemleri](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) listesini gözden geçirin
* [Tanılama ve sorun gidermeyi](./how-to-diagnose-troubleshoot.md) öğrenin
