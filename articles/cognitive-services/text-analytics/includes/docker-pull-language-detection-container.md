---
title: Dil Algılama kapsayıcısı için Docker Pull
titleSuffix: Azure Cognitive Services
description: Dil Algılama kapsayıcısı için Docker Pull komutu
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966735"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Dil Algılama kapsayıcısı için Docker Pull

Microsoft Container Registry bir kapsayıcı görüntüsünü indirmek için [komutunukullanın.`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)

Metin Analizi kapsayıcıları için kullanılabilir etiketlerin tam açıklaması için, Docker Hub 'ındaki [dil algılama](https://go.microsoft.com/fwlink/?linkid=2018759) kapsayıcısına bakın.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
