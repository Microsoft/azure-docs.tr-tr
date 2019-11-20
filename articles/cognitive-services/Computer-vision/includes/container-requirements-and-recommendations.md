---
title: Kapsayıcı gereksinimleri ve önerileri
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481744"
---
> [!NOTE]
> Gereksinimler ve öneriler, 29 satır ve toplam 803 karakter içeren taranmış bir iş harfinin 8 MB 'lik bir görüntüsünü kullanarak saniyede tek bir istek ile kıyaslamayı temel alır.

Aşağıdaki tabloda, her bir okuma kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

| Kapsayıcı | Minimum | Önerilen |TPS<br>(En düşük, en yüksek)|
|-----------|---------|-------------|--|
| Okuma | 1 çekirdek, 8 GB bellek, 0,24 TPS | 8 çekirdek, 16 GB bellek, 1,17 TPS | 0,24, 1,17 |

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.
* TPS-saniye başına işlem.

Çekirdek ve bellek, `docker run` komutunun bir parçası olarak kullanılan `--cpus` ve `--memory` ayarlarına karşılık gelir.
