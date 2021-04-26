---
title: Azure 'da dağıtılmış Izleme Application Insights | Microsoft Docs
description: Microsoft 'un, OpenCensus projesindeki ortaklarımız aracılığıyla dağıtılmış izleme desteği hakkında bilgi sağlar
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579511"
---
# <a name="what-is-distributed-tracing"></a>Dağıtılmış Izleme nedir?

Modern bulut ve [mikro hizmet](https://azure.com/microservices) mimarilerinin bir yolu, kullanılabilirliği ve üretilen işi artırırken maliyetleri azaltmaya yardımcı olabilecek basit ve bağımsız olarak dağıtılabilir hizmetlerden oluşan bir artış verdi. Ancak, bu taşımalar tek tek Hizmetleri daha kolay bir şekilde öğrenirken, genel sistemleri bir veya hata ayıklama nedenine daha zor hale getirir.

Tek parçalı mimarilerde, çağrı yığınlarıyla hata ayıklamak için kullanılır. Çağrı yığınları, yürütme akışını göstermek için harika araçlardır (Yöntem C olarak adlandırılan Yöntem B), bu çağrıların her biri hakkındaki ayrıntılar ve parametrelerle birlikte. Bu, tek bir işlemde çalışan tek bir veya daha fazla hizmet için harika, ancak çağrı yalnızca yerel yığında bir başvuruya değil bir işlem sınırında olduğunda hata ayıkladık mi? 

Bu, dağıtılmış izlemenin içinde geldiği yerdir.  

Dağıtılmış izleme, içinde oluşturulan bir uyarlaması performans profil oluşturucusu eklenmesiyle modern bulut ve mikro hizmet mimarileri için çağrı yığınlarının eşdeğeridir. Azure Izleyici 'de, dağıtılmış izleme verilerini tüketmeye yönelik iki deneyim sağlıyoruz. Birincisi, bir zaman boyutu eklenmiş bir çağrı yığını gibi olan [işlem tanılama](./transaction-diagnostics.md) görünümümüzü. İşlem tanılama görünümü tek bir işlem/istek için görünürlük sağlar ve her istek için güvenilirlik sorunlarının ve performans sorunlarının kök nedenini bulmak için yararlıdır.

Azure Izleyici Ayrıca, sistemlerin nasıl etkileşime gireceğini ve ortalama performans ve hata tarifelerinin ne olduğunu gösteren bir topik görünümünü göstermek için birçok işlem toplayan bir [uygulama Haritası](./app-map.md) görünümü sunar. 

## <a name="how-to-enable-distributed-tracing"></a>Dağıtılmış Izlemeyi etkinleştirme

Bir uygulamadaki hizmetler genelinde dağıtılmış izlemeyi etkinleştirmek, hizmetin uygulandığı dile göre her bir hizmete uygun aracı, SDK veya kitaplık eklemek kadar basittir.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Otomatik izleme veya SDK 'lar aracılığıyla Application Insights aracılığıyla etkinleştirme

.NET, .NET Core, Java, Node.js ve JavaScript 'In tüm Application Insights aracıları ve/veya SDK 'Ları, dağıtılmış izlemeyi yerel olarak destekler. Her Application Insights SDK 'Yı yükleme ve yapılandırmaya yönelik yönergeler aşağıda verilmiştir:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Uygun Application Insights SDK yüklü ve yapılandırılmış olarak, popüler çerçeveler, kitaplıklar ve teknolojiler için izleme bilgileri SDK bağımlılığı otomatik toplayıcılarına göre otomatik olarak toplanır. Desteklenen teknolojilerin tam listesi [bağımlılık otomatik toplama belgelerinde](./auto-collect-dependencies.md)bulunabilir.

 Ayrıca, tüm teknolojiler [TelemetryClient](./api-custom-events-metrics.md)üzerinde [trackdependency](./api-custom-events-metrics.md) çağrısı ile el ile izlenebilir.

## <a name="enable-via-opencensus"></a>OpenCensus aracılığıyla etkinleştir

Application Insights SDK 'lara ek olarak, Application Insights [Opencensus](https://opencensus.io/)aracılığıyla dağıtılmış izlemeyi da destekler. OpenCensus, hizmetler için ölçüm koleksiyonu ve dağıtılmış izleme sağlamak üzere açık kaynaklı, satıcının bağımsız, tek kitaplıkların tek bir dağıtımı olur. Ayrıca, açık kaynak topluluğunun redin, Memcache veya MongoDB gibi popüler teknolojilerle dağıtılmış izlemeyi etkinleştirmesini sağlar. [Microsoft, diğer birçok izleme ve bulut iş ortaklarıyla OpenCensus üzerinde işbirliği](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)yapıyor.

[Python](opencensus-python.md) 

OpenCensus Web sitesi, [Python](https://opencensus.io/api/python/trace/usage.html) ve [Go](https://godoc.org/go.opencensus.io)için API başvuru belgelerini ve ayrıca opencensus kullanmaya yönelik çeşitli farklı kılavuzların bakımını yapar. 

## <a name="next-steps"></a>Sonraki adımlar

* [OpenCensus Python Kullanım Kılavuzu](https://opencensus.io/api/python/trace/usage.html)
* [Uygulama eşlemesi](./app-map.md)
* [Uçtan uca performans izleme](../app/tutorial-performance.md)

