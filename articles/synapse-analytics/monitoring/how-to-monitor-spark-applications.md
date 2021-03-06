---
title: SYNAPSE Studio 'da Apache Spark uygulamalarını izleme
description: SYNAPSE Studio 'Yu kullanarak Apache Spark uygulamalarınızı nasıl izleyeceğinizi öğrenin.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586049"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Apache Spark uygulamalarınızı izlemek için SYNAPSE Studio 'Yu kullanma

Azure SYNAPSE Analytics ile, çalışma alanınızdaki Spark Havuzlarınızda not defterlerini, işleri ve diğer uygulama türlerini çalıştırmak için Spark 'ı kullanabilirsiniz.

Bu makalede, Apache Spark uygulamalarınızın nasıl izleneceği açıklanmaktadır. Bu, en son durum, sorun ve ilerlemeyi göz önünde tutmanıza olanak sağlar.

## <a name="access-apache-spark-applications-list"></a>Apache Spark uygulamalar listesine erişin

Çalışma alanınızdaki Apache Spark uygulamalarının listesini görmek için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin.

![Çalışma alanında oturum aç](./media/common/login-workspace.png)

Çalışma alanınızı açtıktan sonra, soldaki **izleyici** bölümünü seçin.

![Izleme hub 'ı seçin](./media/common/left-nav.png)

Apache Spark uygulamalarının listesini görüntülemek için **Apache Spark uygulamalar** ' ı seçin.

 ![Spark uygulamalarını seçin](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Apache Spark uygulamalarınızı filtreleyin

Apache Spark uygulamalarının listesini ilgilendiğiniz olanlarla filtreleyebilirsiniz. Ekranın üst kısmındaki filtreler, filtrelemek istediğiniz bir alan belirtmenize izin verir.

Örneğin, yalnızca "Sales" adını içeren Apache Spark uygulamalarını görmek için görünümü filtreleyebilirsiniz:

![Örnek filtre](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Belirli bir Apache Spark uygulamayla ilgili ayrıntıları görüntüleme

Apache Spark uygulamalarınızdan biri hakkındaki ayrıntıları görüntülemek için Apache Spark uygulamasını seçin ve ayrıntıları görüntüleyin. Apache Spark uygulama hala çalışıyorsa, ilerlemeyi izleyebilirsiniz. [Daha fazla bilgi edinin](apache-spark-applications.md).

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. işlem [hattı çalıştırmalarını Izleme SYNAPSE Studio](how-to-monitor-pipeline-runs.md) makalesi. 

Apache Spark uygulamada hata ayıklama hakkında daha fazla bilgi için bkz. [SYNAPSE Studio 'da uygulamaları izleme Apache Spark](apache-spark-applications.md) .