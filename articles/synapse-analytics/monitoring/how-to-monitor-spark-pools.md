---
title: SYNAPSE Studio 'da Apache Spark havuzlarını izleme
description: SYNAPSE Studio 'Yu kullanarak Apache Spark havuzlarınızı nasıl izleyeceğinizi öğrenin.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467240"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Apache Spark havuzlarınızı izlemek için SYNAPSE Studio 'Yu kullanma

Azure SYNAPSE Analytics ile, çalışma alanınızdaki Spark havuzlarında not defterlerini, işleri ve diğer uygulama türlerini çalıştırmak için Spark 'ı kullanabilirsiniz.

Bu makalede, farklı çalışma alanı kullanıcıları tarafından kaç sanal çekirdeğin kullanıldığı dahil olmak üzere havuzlarınızın durumunu göz önünde tutmanızı sağlayan Apache Spark havuzlarınızın nasıl izleneceği açıklanmaktadır.

## <a name="access-spark-pools-list"></a>Spark havuzları listesine eriş

Çalışma alanınızdaki Apache Spark havuzlarının listesini görmek için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin.

![Çalışma alanında oturum aç](./media/common/login-workspace.png)

Çalışma alanınızı açtıktan sonra, soldaki **izleyici** bölümünü seçin.

![Izleme hub 'ı seçin](./media/common/left-nav.png)

Apache Spark havuzlarının listesini görüntülemek için **Apache Spark havuzlarını** seçin.

 ![Apache Spark havuzları Seç](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Spark havuzlarınızı filtreleyin

Spark havuzlarının listesini ilgilendiğiniz olanlarla filtreleyebilirsiniz. Ekranın üst kısmındaki filtreler, filtrelemek istediğiniz bir alan belirtmenize izin verir.

Örneğin, yalnızca "dataprep" adını içeren Spark havuzlarını görmek için görünümü filtreleyebilirsiniz:

![Örnek filtre](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Belirli bir Spark havuzunun ayrıntılarını görüntüleme

Spark havuzlarından birinin ayrıntılarını görüntülemek için, ayrıntıları görüntülemek üzere Spark havuzunu seçin.

![Apache Spark havuzu ayrıntıları](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. [SYNAPSE Studio 'da işlem hattı çalıştırmaları](how-to-monitor-pipeline-runs.md) . 

Apache Spark uygulamalarını izleme hakkında daha fazla bilgi için, [SYNAPSE Studio 'Da izleme Apache Spark uygulamalar](how-to-monitor-spark-applications.md) makalesine bakın.
