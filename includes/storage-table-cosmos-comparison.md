---
title: include dosyası
description: include dosyası
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 698219a446240950b9a661f923109fbb2f863556
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178452"
---
Şu anda Azure Tablo Depolama hizmetini kullanıyorsanız, Azure Cosmos DB Tablo API’sine geçerek aşağıdaki avantajlara sahip olabilirsiniz:

|Özellik | Azure Tablo depolama | Azure Cosmos DB Tablo API’si |
| --- | --- | --- |
| Gecikme süresi | Hızlıdır, ancak gecikme süresi için üst sınır yoktur. | Herhangi bir ölçekte, dünyanın her yerinde okuma ve yazma işlemleri için tek haneli milisaniyelik gecikme (99. yüzdebirlik dilimde okumalar için 10 ms'den az, yazma için 15 ms'den az gecikme süresiyle desteklenir). |
| Aktarım hızı | Değişken aktarım hızı modeli. Tabloların 20.000 işlem/sn'lik bir ölçeklenebilirlik sınırı vardır. | SLA'lar ile desteklenen [tablo başına adanmış, ayrılmış aktarım hızı](../articles/cosmos-db/request-units.md) ile yüksek düzeyde ölçeklenebilir. Hesapların aktarım hızı üst sınırı yoktur ve tablo başına 10.000.000 işlem/s >desteklenmez (sağlanan aktarım hızı modunda). |
| Genel dağıtım | Otomatik ve el ile hesap yük devretmesini destekleyen yüksek kullanılabilirlik için isteğe bağlı okunabilir bir ikincil okuma bölgesi olan tek bölge. | 30'dan fazla bölgenin birinden [anahtar teslimi genel dağıtım](../articles/cosmos-db/distribute-data-globally.md). Her zaman, dünyanın her yerinde [otomatik ve el ile yük devretme](../articles/cosmos-db/high-availability.md) desteği. |
| Dizinleme | Yalnızca PartitionKey ve RowKey’de birincil dizin. İkincil dizin yok. | Tüm özelliklerde otomatik ve eksiksiz dizin oluşturma, dizin yönetimi yok. |
| Sorgu | Sorgu yürütme birincil anahtar için dizini kullanır, aksi durumda tarar. | Sorgular, hızlı sorgu süreleri için özelliklerde otomatik dizin oluşturma avantajından yararlanabilir. |
| Tutarlılık | Birincil bölge içinde güçlü. İkincil bölge içinde nihai. | Uygulama gereksinimlerinize bağlı olarak kullanılabilirlik, gecikme süresi, aktarım hızı ve tutarlılığı karşılamak için [iyi tanımlanmış beş tutarlılık düzeyi](../articles/cosmos-db/consistency-levels.md) . |
| Fiyatlandırma | Tüketim tabanlı. | Hem [tüketim tabanlı](../articles/cosmos-db/serverless.md) hem de [sağlanan kapasite](../articles/cosmos-db/set-throughput.md) modlarında kullanılabilir. |
| SLA’lar | %99,99 kullanılabilirlik. | Rahat bir tutarlılıkla tek tek tüm bölge hesapları ve çok bölgeli tüm hesaplar için %99,99 kullanılabilirlik SLA'sı ve çok bölgeli tüm veritabanı hesaplarında %99,999 okunabilirlik Genel kullanıma sunulma aşamasında [endüstri lideri kapsamlı SLA'lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/). |
