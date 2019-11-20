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
ms.date: 05/30/2019
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580589"
---
**İşlem hedefleri bir eğitim işinden sonrakine**yeniden kullanılabilir. Örneğin, çalışma alanınıza uzak bir sanal makine iliştirdikten sonra, birden fazla iş için onu yeniden kullanabilirsiniz.

|Eğitim &nbsp;hedefleri| GPU desteği |[Otomatik ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML işlem hatları](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning Tasarımcısı](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Yerel bilgisayar](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Belki | evet | &nbsp; | &nbsp; |
|[Azure Machine Learning işlem kümesi](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| evet | Evet & <br/>Hiper parametre&nbsp;ayarlama | evet | evet |
|[Uzak VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |evet | Evet & <br/>Hiper parametre ayarlama | evet | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | evet | evet | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | evet | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | evet | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | evet | &nbsp; |
