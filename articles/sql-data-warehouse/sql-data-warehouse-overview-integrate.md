---
title: Tümleşik çözümler oluşturun
description: Azure SQL veri ambarı ile tümleştirilen çözümler içeren araçlar ve iş ortakları.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685632"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Diğer hizmetleri SQL veri ambarı ile tümleştirme
Temel işlevlerine ek olarak, SQL veri ambarı, kullanıcıların Azure 'daki diğer birçok hizmet ile tümleşmesini sağlar. Bu hizmetlerden bazıları şunlardır:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL veri ambarı, Azure üzerinde daha fazla hizmet ve daha fazla [Tümleştirme iş ortağı](sql-data-warehouse-partner-data-integration.md)ile tümleşmeye devam eder.

## <a name="power-bi"></a>Power BI
Power BI tümleştirme, SQL veri ambarı 'nın işlem gücünü Power BI dinamik raporlama ve görselleştirmesiyle birleştirmenizi sağlar. Power BI tümleştirme Şu anda şunları içerir:

* **Doğrudan bağlantı**: SQL veri ambarı 'nda mantıksal olarak aşağı itme ile daha gelişmiş bir bağlantı. Aşağı itme daha büyük bir ölçekte daha hızlı analiz sağlar.
* **Power BI açın**: ' Power BI aç ' düğmesi, bağlanmak için basitleştirilmiş bir yöntem için örnek bilgilerini Power BI geçirir.

Daha fazla bilgi için bkz. [Power BI Ile tümleştirme](sql-data-warehouse-get-started-visualize-with-power-bi.md)veya [Power BI belgeleri](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, kullanıcılara karmaşık ayıklama ve yükleme işlem hatları oluşturmak için yönetilen bir platform sağlar. SQL veri ambarı 'nın Azure Data Factory tümleştirmesi şunları içerir:

* **Saklı yordamlar**: SQL veri ambarı 'nda saklı yordamların yürütülmesini düzenleyin.
* **Kopyala**: SQL veri ambarı 'na veri TAŞıMAK için ADF kullanın. Bu işlem, tüm kapak altındaki ADF 'nin standart veri taşıma mekanizmasını veya PolyBase 'i kullanabilir. 

Daha fazla bilgi için bkz. [Azure Data Factory tümleştirme](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning, büyük bir tahmine dayalı araç kümesi kullanarak karmaşık modeller oluşturmanıza olanak sağlayan, tam olarak yönetilen bir analiz hizmetidir. SQL veri ambarı, aşağıdaki işlevlere sahip bu modeller için hem kaynak hem de hedef olarak desteklenir:

* **Verileri oku:** SQL veri ambarı ile T-SQL kullanarak modelleri ölçeklendirin.
* **Veri yaz:** Tüm modellerden SQL veri ambarı 'na geri değişiklikleri uygulayın.

Daha fazla bilgi için bkz. [Azure Machine Learning tümleştirme](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics, Azure Olay Hub 'ından oluşturulan olay verilerinin işlenmesi ve kullanılması için karmaşık, tam olarak yönetilen bir altyapısıdır.  SQL veri ambarı ile tümleştirme, akış verilerinin, daha derin ve daha gelişmiş analizler sağlayan ilişkisel verilerle birlikte etkili bir şekilde işlenmesini ve depolanmasını sağlar.  

* **Iş çıkışı:** Stream Analytics işlerden çıktıyı doğrudan SQL veri ambarı 'na gönderin.

Daha fazla bilgi için bkz. [Azure Stream Analytics tümleştirme](sql-data-warehouse-integrate-azure-stream-analytics.md).


