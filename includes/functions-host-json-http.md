---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263340"
---
|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|routePrefix|'sindeki|Tüm yollar için geçerli olan rota öneki. Varsayılan ön eki kaldırmak için boş bir dize kullanın. |
|maxOutstandingRequests|200<sup>*</sup>|Belirli bir zamanda tutulan bekleyen istek sayısı üst sınırı. Bu sınır, kuyruğa alınmış ancak yürütmeyi başlatmayan isteklerin yanı sıra devam eden yürütmeler içerir. Bu sınırın üzerindeki tüm gelen istekler, 429 "çok meşgul" yanıtıyla reddedilir. Bu, çağıranların zamana dayalı yeniden deneme stratejileri kullanmasına izin verir ve ayrıca en fazla istek gecikme sürelerini denetlemenize yardımcı olur. Bu, yalnızca betik ana bilgisayar yürütme yolu içinde oluşan kuyruğu denetler. ASP.NET istek kuyruğu gibi diğer kuyruklar da etkin olmaya devam eder ve bu ayardan etkilenmez. <sup>*</sup> 1. x sürümü için varsayılan değer sınırsız (`-1`). Bir tüketim planında sürüm 2. x için varsayılan değer 200 ' dir. Adanmış bir plandaki 2. x sürümü için varsayılan değer sınırsız (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Paralel olarak yürütülecek http işlevlerinin maksimum sayısı. Bu, kaynak kullanımının yönetilmesine yardımcı olabilecek eşzamanlılık denetlemenize olanak tanır. Örneğin, eşzamanlılık çok yüksek olduğunda sorunlara yol açacağından, çok fazla sistem kaynağı (bellek/CPU/yuva) kullanan bir http işleviniz olabilir. Ya da bir üçüncü taraf hizmetine giden istekleri yapan bir işleviniz olabilir ve bu çağrıların hız sınırlı olması gerekir. Bu durumlarda, burada bir kısıtlama uygulanması yardımcı olabilir. <sup>*</sup> 1. x sürümü için varsayılan değer sınırsız (`-1`). Bir tüketim planında sürüm 2. x için varsayılan değer 100 ' dir. Adanmış bir plandaki 2. x sürümü için varsayılan değer sınırsız (`-1`).|
|dynamicThrottlesEnabled|true<sup>*</sup>|Bu ayar etkinleştirildiğinde, istek işleme işlem hattının bağlantılar/iş parçacıkları/işlemler/bellek/CPU/vb gibi sistem performans sayaçlarını düzenli olarak denetlemesini sağlar. bu sayaçlardan herhangi biri yerleşik yüksek bir eşiğin üzerindeyse (%80%), istek şöyle olur sayaç (ler) normal düzeylerine dönene kadar 429 "çok meşgul" yanıtıyla reddedildi. <sup>*</sup> 1. x sürümü için varsayılan değer false 'dur. Bir tüketim planında sürüm 2. x için varsayılan değer true 'dur. Adanmış bir planda sürüm 2. x için varsayılan değer false 'dur.|
