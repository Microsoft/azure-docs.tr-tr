---
title: Azure Service Fabric performans Izleme
description: Azure Service Fabric kümelerini izleme ve tanılamaya yönelik performans sayaçları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: abe1b67bee863f287f148fdd9af13198b67db0f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628875"
---
# <a name="performance-metrics"></a>Performans ölçümleri

Kümenizin performansını ve üzerinde çalışan uygulamaları anlamak için ölçümlerin toplanması gerekir. Service Fabric kümeler için aşağıdaki performans sayaçlarını toplamayı öneririz.

## <a name="nodes"></a>Düğümler

Kümenizdeki makineler için, her makinede yükü daha iyi anlamak ve uygun küme ölçekleme kararları almak için aşağıdaki performans sayaçlarını toplamayı göz önünde bulundurun.

| Sayaç kategorisi | Sayaç Adı |
| --- | --- |
| Mantıksal disk | Mantıksal disk boş alanı |
| FizikselDisk (disk başına) | Ortalama Disk okuma sırası uzunluğu |
| FizikselDisk (disk başına) | Ortalama Disk yazma sırası uzunluğu |
| FizikselDisk (disk başına) | Ortalama Disk sn/okuma |
| FizikselDisk (disk başına) | Ortalama Disk sn/yazma |
| FizikselDisk (disk başına) | Disk Okuma/sn |
| FizikselDisk (disk başına) | Disk okuma bayt/sn |
| FizikselDisk (disk başına) | Disk yazma/sn |
| FizikselDisk (disk başına) | Disk yazma bayt/sn |
| Bellek | Kullanılabilir MBayt |
| PagingFile | Kullanım yüzdesi |
| İşlemci (Toplam) | % İşlemci zamanı |
| İşlem (hizmet başına) | % İşlemci zamanı |
| İşlem (hizmet başına) | KIMLIK Işlemi |
| İşlem (hizmet başına) | Özel baytlar |
| İşlem (hizmet başına) | İş parçacığı sayısı |
| İşlem (hizmet başına) | Sanal Bayt Sayısı |
| İşlem (hizmet başına) | Çalışma Kümesi |
| İşlem (hizmet başına) | Çalışma kümesi-özel |
| Ağ arabirimi (tüm örnekler) | Bayt recd |
| Ağ arabirimi (tüm örnekler) | Gönderilen bayt |
| Ağ arabirimi (tüm örnekler) | Toplam bayt |
| Ağ arabirimi (tüm örnekler) | Çıkış sırası uzunluğu |
| Ağ arabirimi (tüm örnekler) | Atılan giden paketler |
| Ağ arabirimi (tüm örnekler) | Atılan alınan paketler |
| Ağ arabirimi (tüm örnekler) | Giden paket hataları |
| Ağ arabirimi (tüm örnekler) | Alınan paket hataları |

## <a name="net-applications-and-services"></a>.NET uygulamaları ve Hizmetleri

Kümenize .NET Hizmetleri dağıtıyorsanız, aşağıdaki sayaçları toplayın. 

| Sayaç kategorisi | Sayaç Adı |
| --- | --- |
| .NET CLR belleği (hizmet başına) | İşlem Kimliği |
| .NET CLR belleği (hizmet başına) | Toplam kaydedilmiş bayt sayısı |
| .NET CLR belleği (hizmet başına) | Toplam ayrılan bayt sayısı |
| .NET CLR belleği (hizmet başına) | Tüm yığınlardaki bayt sayısı |
| .NET CLR belleği (hizmet başına) | Büyük nesne yığın boyutu |
| .NET CLR belleği (hizmet başına) | # GC tutamaçları |
| .NET CLR belleği (hizmet başına) | # Gen 0 toplamaları |
| .NET CLR belleği (hizmet başına) | # Gen 1 toplamaları |
| .NET CLR belleği (hizmet başına) | Gen 2 toplamaları sayısı |
| .NET CLR belleği (hizmet başına) | GC 'de% Time |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric özel performans sayaçları

Service Fabric, önemli miktarda özel performans sayacı üretir. SDK yüklü ise, performans Izleyicisi uygulamanızda Windows makinenizde kapsamlı listeyi görebilirsiniz (> performans Izleyicisini başlatın). 

Kümenize dağıttığınız uygulamalarda, Reliable Actors kullanıyorsanız, `Service Fabric Actor` ve kategorisinden sayaçlar ekleyin `Service Fabric Actor Method` (bkz. [Service Fabric Reliable Actors tanılama](service-fabric-reliable-actors-diagnostics.md)).

Reliable Services veya hizmet uzaktan Iletişim kullanıyorsanız, `Service Fabric Service` `Service Fabric Service Method` sayaçlarını toplamanız gereken ve sayaç kategorilerimiz, bkz. [hizmet uzaktan iletişim](service-fabric-reliable-serviceremoting-diagnostics.md) ve [güvenilir hizmetler performans sayaçları](service-fabric-reliable-services-diagnostics.md#performance-counters)ile izleme. 

Güvenilir koleksiyonlar kullanıyorsanız, `Avg. Transaction ms/Commit` `Service Fabric Transactional Replicator` işlem ölçümü başına ortalama işleme gecikme süresini toplamak için kaynağından ' dan eklenmesini öneririz.


## <a name="next-steps"></a>Sonraki adımlar

* Service Fabric içindeki [platform düzeyinde olay oluşturma](service-fabric-diagnostics-event-generation-infra.md) hakkında daha fazla bilgi edinin
* [Log Analytics Agent](service-fabric-diagnostics-oms-agent.md) aracılığıyla performans ölçümlerini toplayın
