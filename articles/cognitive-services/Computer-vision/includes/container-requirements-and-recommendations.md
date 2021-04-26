---
title: Kapsayıcı gereksinimleri ve önerileri
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308788"
---
> [!NOTE]
> Gereksinimler ve öneriler, 29 satır ve toplam 803 karakter içeren taranmış bir iş harfinin 8 MB 'lik bir görüntüsünü kullanarak saniyede tek bir istek ile kıyaslamayı temel alır.

Aşağıdaki tabloda her bir okuma OCR kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Okuma 2,0-Önizleme | 1 çekirdek, 8 GB bellek |    8 çekirdek, 16 GB bellek |
| 3,2 okuyun | 8 çekirdek, 16 GB bellek | 8 çekirdek, 24 GB bellek |

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.

Çekirdek ve bellek, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .
