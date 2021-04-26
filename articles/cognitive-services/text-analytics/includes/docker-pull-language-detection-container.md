---
title: Dil Algılama kapsayıcısı için Docker Pull
titleSuffix: Azure Cognitive Services
description: Dil Algılama kapsayıcısı için Docker Pull komutu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905997"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Dil Algılama kapsayıcısı için Docker Pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)Microsoft Container Registry bir kapsayıcı görüntüsünü indirmek için komutunu kullanın.

Metin Analizi kapsayıcıları için kullanılabilir etiketlerin tam açıklaması için, Docker Hub 'ındaki [dil algılama](https://go.microsoft.com/fwlink/?linkid=2018759) kapsayıcısına bakın.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
