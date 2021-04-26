---
title: Tümleşik çözümler oluşturun
description: Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) ile tümleştirilen çözüm araçları ve iş ortakları.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3e55ef054d5c305937f88d6ec5b2b4453cac6792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117767"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Diğer hizmetleri Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) ile tümleştirin.

Azure SYNAPSE Analytics içindeki adanmış SQL Havuzu (eski adıyla SQL DW) özelliği, kullanıcıların Azure 'daki diğer birçok hizmet ile tümleştirilmesine olanak sağlar. Adanmış bir SQL havuzu bazı ek hizmetlerden yararlanabilir, bazıları şunları içerir:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Azure genelinde Tümleştirme Hizmetleri hakkında daha fazla bilgi için, [Tümleştirme iş ortakları](sql-data-warehouse-partner-data-integration.md)makalesini gözden geçirin.

## <a name="power-bi"></a>Power BI

Power BI tümleştirme, bir veri ambarının işlem gücünü Power BI dinamik raporlama ve görselleştirmesiyle birleştirmenizi sağlar. Power BI tümleştirmesi şu anda aşağıdakileri içermektedir:

* **Doğrudan Bağlan**: adanmış SQL Havuzu (eskı ADıYLA SQL DW) kullanılarak sağlanan bir veri ambarına karşı mantıksal olarak aşağı doğru bir şekilde daha gelişmiş bir bağlantı. İtme, geniş ölçekli analiz işlemlerinin daha hızlı tamamlanmasını sağlar.
* **Power BI açın**: ' Power BI aç ' düğmesi, bağlanmak için basitleştirilmiş bir yöntem için örnek bilgilerini Power BI geçirir.

Daha fazla bilgi için bkz. [Power BI Ile tümleştirme](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)veya [Power BI belgeleri](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory, kullanıcılara karmaşık ayıklama ve yükleme işlem hatları oluşturmak için yönetilen bir platform sağlar. Azure Data Factory ile adanmış SQL Havuzu (eski adıyla SQL DW) Tümleştirmesi şunları içerir:

* **Saklı yordamlar**: saklı yordamların yürütülmesini düzenleyin.
* **Kopyala**: VERILERI adanmış SQL havuzuna (eskı ADıYLA SQL DW) TAŞıMAK için ADF 'yi kullanın. Bu işlem, tüm kapak altındaki ADF 'nin standart veri taşıma mekanizmasını veya PolyBase 'i kullanabilir.

Daha fazla bilgi için bkz. [Azure Data Factory tümleştirme](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning, büyük bir tahmine dayalı araç kümesi kullanarak karmaşık modeller oluşturmanıza olanak sağlayan, tam olarak yönetilen bir analiz hizmetidir. Adanmış SQL Havuzu (eski adıyla SQL DW), bu modeller için hem kaynak hem de hedef olarak desteklenir ve aşağıdaki işlevlere sahiptir:

* **Verileri oku:** Özel bir SQL havuzunda (eski adıyla SQL DW), T-SQL kullanarak modelleri ölçeklendirin.
* **Veri yaz:** Herhangi bir modelden değişiklikleri ayrılmış bir SQL havuzuna (eski adıyla SQL DW) uygulayın.

Daha fazla bilgi için bkz. [Azure Machine Learning tümleştirme](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics, Azure Olay Hub 'ından oluşturulan olay verilerinin işlenmesi ve kullanılması için karmaşık, tam olarak yönetilen bir altyapısıdır.  Adanmış SQL Havuzu (eski adıyla SQL DW) ile tümleştirme, akış verilerinin etkili bir şekilde işlenmesini ve ilişkisel verilerle birlikte depolanmasını sağlar ve daha ayrıntılı analizler.  

* **Iş çıkışı:** Stream Analytics işlerden çıktıyı doğrudan adanmış bir SQL havuzuna (eski adıyla SQL DW) gönderin.

Daha fazla bilgi için bkz. [Azure Stream Analytics tümleştirme](sql-data-warehouse-integrate-azure-stream-analytics.md).