---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026924"
---
CRON ifadeleriyle kullanılan varsayılan saat dilimi Eşgüdümlü Evrensel Saat (UTC) ' dir. CRON ifadenizi başka bir saat dilimine göre oluşturmak için adlı işlev uygulamanız için bir uygulama ayarı oluşturun `WEBSITE_TIME_ZONE` . 

Bu ayarın değeri, işlev uygulamanızın çalıştırıldığı işletim sistemine ve plana bağlıdır.

|İşletim sistemi |Planlama |Değer |
|-|-|-|
| **Windows** |Tümü | Değeri, istenen saat diliminin, Windows komutu tarafından verilen her çiftin ikinci satırı tarafından verilen adı olarak ayarlayın `tzutil.exe /L` |
| **Linux** |Premium<br/>Ayrılmış |Değeri, [TZ veritabanında](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)gösterildiği gibi istenen saat diliminin adı olarak ayarlayın. |

> [!NOTE]
> `WEBSITE_TIME_ZONE` , Linux tüketim planında Şu anda desteklenmiyor.

Örneğin, ABD 'deki Doğu saati ( `Eastern Standard Time` (Windows) veya `America/New_York` (Linux)) Şu anda, standart saat boyunca utc-05:00, yaz saatı sırasında utc-04:00 kullanıyor. Her gün saat 10:00 ' de bir Zamanlayıcı tetikleyicisi tetiklenmesi için, işlev uygulamanız için adlı bir uygulama ayarı oluşturun `WEBSITE_TIME_ZONE` , değeri `Eastern Standard Time` (Windows) veya `America/New_York` (Linux) olarak ayarlayın ve ardından aşağıdaki ncrontab ifadesini kullanın: 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE`Saati kullandığınızda, gün ışığından yararlanma saati ve standart zamanda değişiklikler dahil olmak üzere belirli bir saat dilimindeki zaman değişikliklerine göre ayarlanır.
