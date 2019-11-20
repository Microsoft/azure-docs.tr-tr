---
title: Yaklaşım Analizi kapsayıcı Docker örnekleri
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi kapsayıcı Docker örnekleri
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: c384d9d8af932db343aad106b6e89ea87821d900
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148507"
---
### <a name="sentiment-analysis-container-docker-examples"></a>Yaklaşım Analizi kapsayıcı Docker örnekleri

Aşağıdaki Docker örnekleri Yaklaşım Analizi kapsayıcısı içindir.

#### <a name="basic-example"></a>Temel örnek 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Günlüğe kaydetme örneği 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
