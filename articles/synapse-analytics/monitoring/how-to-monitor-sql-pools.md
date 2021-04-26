---
title: SYNAPSE Studio 'da SQL havuzlarını izleme
description: SYNAPSE Studio 'Yu kullanarak SQL havuzlarınızı izlemeyi öğrenin.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467983"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>SQL havuzlarınızı izlemek için SYNAPSE Studio 'Yu kullanma

SYNAPSE Studio ile, çalışma alanınızdaki SQL havuzları üzerinde SQL betikleri çalıştırabilirsiniz.

Bu makalede, havuzlarınızın durumunu ve etkinliğini göz önünde tutmanız için SQL havuzlarınızın nasıl izleneceği açıklanır.

## <a name="access-sql-pools-list"></a>SQL havuzları listesine erişim

Çalışma alanınızdaki SQL havuzlarının listesini görmek için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin.

![Çalışma alanında oturum aç](./media/common/login-workspace.png)

Çalışma alanınızı açtıktan sonra, soldaki **izleyici** bölümünü seçin.

![Izleme hub 'ı seçin](./media/common/left-nav.png)

SQL havuzlarının listesini görüntülemek için **SQL havuzlarını** seçin.

 ![SQL havuzlarını seçin](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>SQL havuzlarınızı filtreleyin

SQL havuzlarının listesini ilgilendiğiniz olanlarla filtreleyebilirsiniz. Ekranın üst kısmındaki filtreler, filtrelemek istediğiniz bir alan belirtmenize izin verir.

Örneğin, yalnızca "salesrecords" adını içeren SQL havuzlarını görmek için görünümü filtreleyebilirsiniz:

![Örnek filtre](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Belirli bir SQL havuzunun ayrıntılarını görüntüleme

SQL havuzlarından birinin ayrıntılarını görüntülemek için, ayrıntıları görüntülemek üzere SQL havuzunu seçin.

![SQL havuzu ayrıntıları](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. [SYNAPSE Studio 'da işlem hattı çalıştırmaları](how-to-monitor-pipeline-runs.md) . 

SQL isteklerini izleme hakkında daha fazla bilgi için, [SYNAPSE Studio 'DA SQL Isteklerini izleme](how-to-monitor-sql-requests.md) makalesine bakın.