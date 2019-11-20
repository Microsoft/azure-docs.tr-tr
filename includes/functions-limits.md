---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: ad87964216db72cfef447a18d4cd05b8a976491b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476932"
---
| Kaynak | [Tüketim planı](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium plan](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service planı](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Ölçeği genişletme | Olay odaklı | Olay odaklı | [El ile/otomatik ölçeklendirme](../articles/app-service/manage-scale-up.md) | 
| En fazla örnek sayısı | 200 | 20 | 10-20 |
|Varsayılan [zaman aşımı süresi](../articles/azure-functions/functions-scale.md#timeout) (dk) |5 | 30 |30<sup>2</sup> |
|Maksimum [zaman aşımı süresi](../articles/azure-functions/functions-scale.md#timeout) (dk) |10 | unbounded | sınırsız<sup>3</sup> |
| En fazla giden bağlantı (örnek başına) | 600 etkin (1200 toplam) | unbounded | unbounded |
| En fazla istek boyutu (MB)<sup>4</sup> | 100 | 100 | 100 |
| En fazla sorgu dizesi uzunluğu<sup>4</sup> | 4096 | 4096 | 4096 |
| En fazla istek URL uzunluğu<sup>4</sup> | 8192 | 8192 | 8192 |
| Örnek başına [acu](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 |
| Maksimum bellek (örnek başına GB) | 1,5 | 3,5-14 | 1,75-14 |
| Plan başına işlev uygulamaları |100 |100 |sınırsız<sup>5</sup> |
| [App Service planları](../articles/app-service/overview-hosting-plans.md) | [bölge](https://azure.microsoft.com/global-infrastructure/regions/) başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100 |
| Depolama<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Uygulama başına özel etki alanları</a> |500<sup>7</sup> |500 |500 |
| Özel etki alanı [SSL desteği](../articles/app-service/configure-ssl-bindings.md) |sınırsız SNI SSL bağlantısı dahildir | sınırsız SNI SSL ve 1 IP SSL bağlantı dahildir |sınırsız SNI SSL ve 1 IP SSL bağlantı dahildir | 

<sup>1</sup> çeşitli App Service planı seçeneklerine yönelik belirli sınırlar için, [App Service plan sınırlarına](../articles/azure-subscription-service-limits.md#app-service-limits)bakın.  
<sup>2</sup> varsayılan olarak, bir App Service planındaki işlev 1. x çalışma zamanının zaman aşımı değeri sınırsız olur.  
<sup>3</sup> App Service planının [her zaman açık](../articles/azure-functions/functions-scale.md#always-on)olarak ayarlanmasını gerektirir. Standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/)ödeyin.  
<sup>4</sup> bu sınırlar [konakta ayarlanır](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> barındırabilmeniz gereken işlev uygulamalarının gerçek sayısı, uygulamaların etkinliğine, makine örneklerinin boyutuna ve karşılık gelen kaynak kullanımına göre değişir.  
<sup>6</sup> depolama sınırı, aynı App Service planındaki tüm uygulamalarda geçici depolamadaki toplam içerik boyutudur. Tüketim planı, geçici depolama için Azure dosyalarını kullanır.  
<sup>7</sup> işlev uygulamanız bir [Tüketim PLANıNDA](../articles/azure-functions/functions-scale.md#consumption-plan)barındırılıyorsa yalnızca CNAME seçeneği desteklenir. Bir [Premium planındaki](../articles/azure-functions/functions-scale.md#premium-plan) veya bir [App Service planındaki](../articles/azure-functions/functions-scale.md#app-service-plan)işlev UYGULAMALARı için, bir CNAME veya a kaydı kullanarak özel bir etki alanını eşleyebilirsiniz.
