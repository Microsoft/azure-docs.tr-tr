---
title: include dosyası
description: include dosyası
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629916"
---
**İşlem hedefleri bir eğitim işinden sonrakine yeniden kullanılabilir.** Örneğin, çalışma alanınıza uzak bir VM iliştirdikten sonra, birden fazla iş için onu yeniden kullanabilirsiniz. Makine öğrenimi ardışık düzenleri için, her işlem hedefi için uygun işlem [hattı adımını](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) kullanın.

Çoğu iş için eğitim işlem hedefi için aşağıdaki kaynaklardan herhangi birini kullanabilirsiniz. Her kaynak otomatik makine öğrenimi, makine öğrenimi ardışık düzenleri veya tasarımcı için kullanılamaz.

|Eğitim &nbsp; hedefleri|[Otomatik makine öğrenimi](../articles/machine-learning/concept-automated-ml.md) | [Makine öğrenmesi işlem hatları](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning tasarımcısı](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Yerel bilgisayar](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Yes | &nbsp; | &nbsp; |
|[Azure Machine Learning işlem kümesi](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Yes | Yes | Yes |
|[Azure Machine Learning işlem örneği](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Evet (SDK aracılığıyla)  | Yes |  |
|[Uzak VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Yes  | Yes | &nbsp; |
|[Azure &nbsp; databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Evet (yalnızca SDK yerel modu) | Yes | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Yes | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Yes | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Yes | &nbsp; |

> [!TIP]
> İşlem örneğinde 12GB işletim sistemi diski vardır. Disk alanı tükenseniz, işlem örneğini [durdurmadan veya yeniden başlatmadan](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage) önce en az 1-2 GB 'yi temizlemek için [terminali kullanın](../articles/machine-learning/how-to-access-terminal.md) .
