---
title: Yaklaşım Analizi kapsayıcısı için Docker Pull
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi kapsayıcısı için Docker Pull komutu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564464"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Yaklaşım Analizi v3 kapsayıcısı için Docker Pull

Yaklaşım Analizi kapsayıcısı v3 kapsayıcısı, çeşitli dillerde kullanılabilir. Ingilizce kapsayıcısının kapsayıcısını indirmek için aşağıdaki komutu kullanın. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Kapsayıcıyı başka bir dile indirmek için `en` aşağıdaki dil kodlarından biriyle değiştirin. 

| Metin Analizi kapsayıcı | Dil kodu |
|--|--|
| Basitleştirilmiş Çince    |   `zh-hans`   |
| Geleneksel Çince   |   `zh-hant`   |
| Felemenkçe                 |     `nl`      |
| İngilizce               |     `en`      |
| Fransızca                |     `fr`      |
| Almanca                |     `de`      |
| Hintçe                 |    `hi`       |
| İtalyanca               |     `it`      |
| Japonca              |     `ja`      |
| Korece                |     `ko`      |
| Norveççe (Bokmål)   |     `no`      |
| Portekizce (Brezilya)   |    `pt-BR`    |
| Portekizce (Portekiz) |    `pt-PT`    |
| İspanyolca               |     `es`      |
| Türkçe               |     `tr`      |

Metin Analizi kapsayıcıları için kullanılabilir etiketlerin tam açıklaması için bkz. [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
