---
title: include dosyası
description: include dosyası
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178393"
---
- 512 GiB 'den az olan bir Premium SSD üzerinde isteğe bağlı bir patlama etkinleştirilemez. 512 GiB 'den küçük Premium SSD 'Ler her zaman kredi tabanlı patlama kullanır.
- İsteğe bağlı burdıya yalnızca Premium SSD 'lerde desteklenir. İsteğe bağlı patlama özelliği etkinleştirilmiş bir Premium SSD başka bir disk türüne geçiş yapsa, disk patlaması devre dışı bırakılır.
- İsteğe bağlı burte, performans katmanı değiştirildiğinde kendisini otomatik olarak devre dışı bırakır. Performans katmanınızı değiştirmek istiyorsanız ancak diski yeniden almaya devam etmek istemiyorsanız, bunu devre dışı bırakmanız gerekir.
- İsteğe bağlı patlama özelliği, yalnızca disk bir VM 'den ayrıldığında veya VM durdurulduğunda etkinleştirilebilir. İsteğe bağlı burdıya, etkinleştirildikten sonra 12 saat devre dışı bırakılabilir.