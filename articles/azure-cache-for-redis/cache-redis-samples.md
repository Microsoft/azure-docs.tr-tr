---
title: Redis için Azure Önbelleği örnekleri
description: Redis için Azure Cache’i kullanmayı öğrenin
author: yegu-ms
ms.service: cache
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 9097a25c0223faebd91fe3c01ff3edea6c4e6f9f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122607"
---
# <a name="azure-cache-for-redis-samples"></a>Redis için Azure Önbelleği örnekleri
Bu konu, Redsıs örnekleri için Azure önbelleğinin bir listesini, bir önbelleğe bağlanma, önbelleğe veri okuma ve önbellekten veri yazma ve Redsıs sağlayıcıları için ASP.NET Azure önbelleğini kullanma gibi senaryoları kapsayan bir liste sağlar. Bazı örnekler indirilebilir projelerdir ve bazıları adım adım rehberlik sağlar ve kod parçacıkları içerir ancak indirilebilir bir projeye bağlantı vermez.

## <a name="hello-world-samples"></a>Merhaba dünya örnekleri
Bu bölümdeki örneklerde, Redsıs örneği için bir Azure önbelleğine bağlanma ve çeşitli diller ve Redo istemcileri kullanılarak önbelleğe veri okuma ve yazma temelleri gösterilmektedir.

[Merhaba Dünya](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) örneği, [StackExchange. redsıs](https://github.com/StackExchange/StackExchange.Redis) .NET istemcisi kullanılarak çeşitli önbellek işlemlerinin nasıl gerçekleştirileceğini gösterir.

Bu örnekte nasıl yapılacağı gösterilmektedir:

* Çeşitli bağlantı seçeneklerini kullanın
* Zaman uyumlu ve zaman uyumsuz işlemler kullanarak önbelleğe ve önbellekten nesneleri okuma ve yazma
* Belirtilen anahtarların değerlerini döndürmek için Redsıs MGET/MSET komutlarını kullanın
* Redsıs işlem işlemlerini gerçekleştirme
* Redi listeleriyle ve sıralanmış kümeleriyle çalışma
* JsonConvert serileştiricileri kullanarak .NET nesnelerini depolayın
* Etiketleme uygulamak için Redsıs kümelerini kullanma
* Redsıs kümesiyle çalışma

Daha fazla bilgi için GitHub 'daki [StackExchange. redthe](https://github.com/StackExchange/StackExchange.Redis) belgelerine bakın ve daha fazla kullanım senaryosunda, [StackExchange. redsıs. Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) birim testlerini inceleyin.

[Azure önbelleğini redsıs için kullanma Python ile](cache-python-get-started.md) , Python ve [redin-Kopyala](https://github.com/andymccurdy/redis-py) istemcisi kullanılarak redin için Azure önbelleği kullanmaya nasıl başlacağınız gösterilmektedir.

[Önbellekte .NET nesneleriyle çalışma](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) , .net nesnelerini yazmanız ve bunları redsıs örneği Için bir Azure önbelleğinden okuyabilmeniz için bir yol gösterir. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Red, ASP.NET SignalR için genişleme geri düzlemi olarak redsıs için Azure önbelleğini kullanma
[Redin Için Azure önbelleğini, ASP.NET SignalR örneği için bir genişleme geri düzlemi olarak kullanın](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) örnek Için Azure önbelleğini bir SignalR geri düzlemi olarak nasıl kullanabileceğinizi gösterir. Geri düzlemi hakkında daha fazla bilgi için bkz. [redsıs Ile SignalR ölçeği](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Redsıs müşterisi için Azure önbelleği sorgu örneği
Bu örnek, bir önbellekteki verilere erişme ve kalıcılık depolamadaki verilere erişme arasındaki performansı karşılaştırır. Bu örnekte iki proje vardır.

* [Redin için Azure önbelleğinin, verileri önbelleğe alarak performansı nasıl iyileştirebileceğimizi gösteren tanıtım](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Demo için veritabanını ve önbelleği çekirdek yapın](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET oturum durumu ve çıktı önbelleği
[Redsıs Için Azure önbelleği kullan ASP.net sessionState ve OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) örneği, Redsıs için sessionState ve OutputCache sağlayıcıları kullanılarak ASP.NET oturumu ve çıktı önbelleğini depolamak üzere Redsıs Için Azure önbelleğini nasıl kullanacağınızı gösterir.

## <a name="manage-azure-cache-for-redis-with-maml"></a>MAML ile Redsıs için Azure önbelleğini yönetme
Azure [Yönetim kitaplıkları kullanılarak redsıs Için Azure önbelleğini yönetme](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) örneği, önbelleğinizi yönetmek Için Azure Yönetim kitaplıklarını nasıl kullanabileceğinizi gösterir (oluşturma/güncelleştirme/silme). 

## <a name="custom-monitoring-sample"></a>Özel izleme örneği
[Redsıs izleme verileri Için Azure önbellek erişimi](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) örneği, Azure önbelleğiniz için Izleme verilerine Azure portalının dışından nasıl erişebileceğinizi gösterir.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>PHP ve Redsıs kullanılarak yazılan Twitter stili bir kopya
[Retwa](https://github.com/SyntaxC4-MSFT/retwis) örneği, reddir Merhaba dünya. Bu, [Predıs](https://github.com/nrk/predis) Istemcisi kullanılarak REDSıS ve php kullanılarak yazılmış, en az bir Twitter stili sosyal ağ kopyalamadır. Kaynak kodu, farklı redin veri yapılarını göstermek için çok basittir ve aynı anda tasarlanmıştır.

## <a name="bandwidth-monitor"></a>Bant genişliği İzleyicisi
[Bant genişliği izleyici](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) örneği, istemcide kullanılan bant genişliğini izlemenize olanak sağlar. Bant genişliğini ölçmek için, örneği önbellek istemci makinesinde çalıştırın, önbelleğe çağrılar yapın ve bant genişliği izleyici örneği tarafından raporlanan bant genişliğini gözlemleyin.

