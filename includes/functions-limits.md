---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 91f72117fdbcdbeda1d906a9760243e66404920c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104106"
---
| Kaynak |[Tüketim planı](../articles/azure-functions/consumption-plan.md)|[Premium planı](../articles/azure-functions/functions-premium-plan.md)|[Ayrılmış plan](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Varsayılan [zaman aşımı süresi](../articles/azure-functions/functions-scale.md#timeout) (dk) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|En fazla [zaman aşımı süresi](../articles/azure-functions/functions-scale.md#timeout) (dk) |10 | sınırsız<sup>7</sup> | sınırsız<sup>2</sup> | unbounded | unbounded |
| En fazla giden bağlantı (örnek başına) | 600 etkin (1200 toplam) | unbounded | unbounded | unbounded | unbounded |
| En fazla istek boyutu (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Kümeye bağlıdır |
| En fazla sorgu dizesi uzunluğu<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Kümeye bağlıdır |
| En fazla istek URL uzunluğu<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Kümeye bağlıdır |
|Örnek başına [acu](../articles/virtual-machines/acu.md) | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS fiyatlandırması](https://azure.microsoft.com/pricing/details/container-service/) |
| Maksimum bellek (örnek başına GB) | 1,5 | 3,5-14 | 1,75-14 | 3,5-14 | Herhangi bir düğüm desteklenir |
| En fazla örnek sayısı | 200 | 100<sup>9</sup> | SKU<sup>10</sup> ' a göre değişir | 100<sup>10</sup> | Kümeye bağlıdır |   
| Plan başına işlev uygulamaları |100 |100 |sınırsız<sup>4</sup> | unbounded | unbounded |
| [App Service planları](../articles/app-service/overview-hosting-plans.md) | [bölge](https://azure.microsoft.com/global-infrastructure/regions/) başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100 | - | - |
| Depolama<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | yok |
| Uygulama başına özel etki alanları</a> |500<sup>6</sup> |500 |500 | 500 | yok |
| Özel etki alanı [SSL desteği](../articles/app-service/configure-ssl-bindings.md) |sınırsız SNI SSL bağlantısı dahildir | sınırsız SNI SSL ve 1 IP SSL bağlantı dahildir |sınırsız SNI SSL ve 1 IP SSL bağlantı dahildir | sınırsız SNI SSL ve 1 IP SSL bağlantı dahildir | yok |

<sup>1</sup> varsayılan olarak, bir App Service planındaki işlev 1. x çalışma zamanının zaman aşımı değeri sınırsız olur.  
<sup>2</sup> App Service planının [her zaman açık](../articles/azure-functions/dedicated-plan.md#always-on)olarak ayarlanmasını gerektirir. Standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/)ödeyin.  
<sup>3</sup> bu sınırlar [konakta ayarlanır](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> barındırabilmeniz gereken işlev uygulamalarının gerçek sayısı, uygulamaların etkinliğine, makine örneklerinin boyutuna ve karşılık gelen kaynak kullanımına göre değişir.  
<sup>5</sup> depolama sınırı, aynı App Service planındaki tüm uygulamalarda geçici depolamadaki toplam içerik boyutudur. Tüketim planı, geçici depolama için Azure dosyalarını kullanır.  
<sup>6</sup> işlev uygulamanız bir [Tüketim PLANıNDA](../articles/azure-functions/consumption-plan.md)barındırılıyorsa yalnızca CNAME seçeneği desteklenir. Bir [Premium planındaki](../articles/azure-functions/functions-premium-plan.md) veya bir [App Service planındaki](../articles/azure-functions/dedicated-plan.md)işlev UYGULAMALARı için, bir CNAME veya a kaydı kullanarak özel bir etki alanını eşleyebilirsiniz.  
<sup>7</sup> 60 dakikaya kadar garanti edilir.  
<sup>8</sup> çalışan, müşteri uygulamalarını barındıran rollerdir. Çalışanlar üç sabit boyutta kullanılabilir: bir vCPU/3,5 GB RAM; İki vCPU/7 GB RAM; Dört vCPU/14 GB RAM.   
<sup>9</sup> Premium bir planda Linux üzerinde çalışırken, şu anda 20 örnek ile sınırlı olursunuz.  
<sup>10</sup> ayrıntılar için [App Service sınırları](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) bölümüne bakın.