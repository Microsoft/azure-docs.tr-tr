---
title: SYNAPSE Studio 'da SQL isteklerini izleme
description: SYNAPSE Studio 'Yu kullanarak SQL isteklerinizi nasıl izleyeceğinizi öğrenin.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467980"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>SQL isteklerinizi izlemek için SYNAPSE Studio 'Yu kullanma

SYNAPSE Studio ile, çalışma alanınızdaki SQL havuzları üzerinde SQL betikleri çalıştırabilirsiniz.

Bu makalede, SQL isteklerinizi nasıl izlediğiniz, çalışan isteklerin durumunu göz önünde tutmanız ve geçmiş isteklerin ayrıntılarını bulmanızı sağlar.

## <a name="access-sql-requests-list"></a>SQL istekleri listesine erişin

Çalışma alanınızdaki SQL isteklerinin listesini görmek için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin.

![Çalışma alanında oturum aç](./media/common/login-workspace.png)

Çalışma alanınızı açtıktan sonra, soldaki **izleyici** bölümünü seçin.

![Izleme hub 'ı seçin](./media/common/left-nav.png)

SQL isteklerinin listesini görüntülemek için **SQL istekleri** ' ni seçin.

 ![SQL isteklerini seçin](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Bir SQL havuzu seçin

Varsayılan olarak, yerleşik sunucusuz SQL havuzu için SQL istek geçmişi bu görünümde gösterilmektedir. Bu havuzun SQL istek geçmişini görmek için adanmış SQL havuzlarınızın birini seçebilirsiniz.

![SQL havuzu seçin](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>SQL isteklerinizi filtreleyin

SQL isteklerinin listesini ilgilendiğiniz olanlara göre filtreleyebilirsiniz. Ekranın üst kısmındaki filtreler, filtrelemek istediğiniz bir alan belirtmenize izin verir.

Örneğin, görünümü yalnızca tarafından gönderilen SQL isteklerini görmek için filtreleyebilirsiniz `maria@contoso.com` :

![Örnek filtre](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Belirli bir SQL isteği hakkındaki ayrıntıları görüntüleme

SQL isteklerinizin biriyle ilgili ayrıntıları görüntülemek için, Ayrıntılar görünümüne gitmek üzere SQL isteğini açın. Adanmış SQL havuzları üzerinde çalışan karmaşık istekler için ilerlemeyi izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. [SYNAPSE Studio 'da işlem hattı çalıştırmaları](how-to-monitor-pipeline-runs.md) . 

Apache Spark uygulamalarını izleme hakkında daha fazla bilgi için, [SYNAPSE Studio 'Da izleme Apache Spark uygulamalar](how-to-monitor-spark-applications.md) makalesine bakın.