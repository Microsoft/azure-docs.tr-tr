---
title: include dosyası
description: include dosyası
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080918"
---
| **Kaynak** | **Varsayılan sınır** | **Üst sınırı** |
| --- | --- | --- |
| Abonelik başına bölge başına Azure Batch hesapları | 1-3 |50 |
| Batch hesabı başına adanmış çekirdekler | 90-900 | Desteğe başvurun |
| Batch hesabı başına düşük öncelikli çekirdekler | 10-100 | Desteğe başvurun |
| Batch hesabı başına **[etkin](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** işler ve iş zamanlamaları (**Tamamlanan** işlerin sınırı yoktur) | 100-300 | 1\.000<sup>1</sup> |
| Batch hesabı başına havuz sayısı | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Varsayılan sınırlar, bir Batch hesabı oluşturmak için kullandığınız abonelik türüne bağlı olarak farklılık gösterir. Toplu hizmet modundaki Batch hesapları için gösterilen çekirdekler kotaları. [Batch hesabınızdaki kotaları görüntüleyin](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Bu sınırın ötesinde bir artış istemek için Azure desteği 'ne başvurun.
