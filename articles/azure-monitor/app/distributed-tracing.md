---
title: Azure 'da dağıtılmış Izleme Application Insights | Microsoft Docs
description: Microsoft 'un, OpenCensus projesindeki yerel ileticimiz ve ortaklarımız aracılığıyla dağıtılmış izleme desteği hakkında bilgi sağlar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 5c07fac0590b833aa19909849271c59d65d42ad6
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242448"
---
# <a name="what-is-distributed-tracing"></a>Dağıtılmış Izleme nedir?

Modern bulut ve [mikro hizmet](https://azure.com/microservices) mimarilerinin bir yolu, kullanılabilirliği ve üretilen işi artırırken maliyetleri azaltmaya yardımcı olabilecek basit ve bağımsız olarak dağıtılabilir hizmetlerden oluşan bir artış verdi. Ancak, bu taşımalar tek tek Hizmetleri daha kolay bir şekilde öğrenirken, genel sistemleri bir veya hata ayıklama nedenine daha zor hale getirir.

Tek parçalı mimarilerde, çağrı yığınlarıyla hata ayıklamak için kullanılır. Çağrı yığınları, yürütme akışını göstermek için harika araçlardır (Yöntem C olarak adlandırılan Yöntem B), bu çağrıların her biri hakkındaki ayrıntılar ve parametrelerle birlikte. Bu, tek bir işlemde çalışan tek bir veya daha fazla hizmet için harika, ancak çağrı yalnızca yerel yığında bir başvuruya değil bir işlem sınırında olduğunda hata ayıkladık mi? 

Bu, dağıtılmış izlemenin içinde geldiği yerdir.  

Dağıtılmış izleme, içinde oluşturulan bir uyarlaması performans profil oluşturucusu eklenmesiyle modern bulut ve mikro hizmet mimarileri için çağrı yığınlarının eşdeğeridir. Azure Izleyici 'de, dağıtılmış izleme verilerini tüketmeye yönelik iki deneyim sağlıyoruz. Birincisi, bir zaman boyutu eklenmiş bir çağrı yığını gibi olan [işlem tanılama](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) görünümümüzü. İşlem tanılama görünümü tek bir işlem/istek için görünürlük sağlar ve her istek için güvenilirlik sorunlarının ve performans sorunlarının kök nedenini bulmak için yararlıdır.

Azure Izleyici Ayrıca, sistemlerin nasıl etkileşime gireceğini ve ortalama performans ve hata tarifelerinin ne olduğunu gösteren bir topik görünümünü göstermek için birçok işlem toplayan bir [uygulama Haritası](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) görünümü sunar. 

## <a name="how-to-enable-distributed-tracing"></a>Dağıtılmış Izlemeyi etkinleştirme

Bir uygulamadaki hizmetler genelinde dağıtılmış izlemeyi etkinleştirmek, hizmetin uygulandığı dile bağlı olarak her bir hizmete uygun SDK veya kitaplık eklemek kadar basittir.

## <a name="enabling-via-application-insights-sdks"></a>Application Insights SDK 'Ları aracılığıyla etkinleştirme

.NET, .NET Core, Java, Node. js ve JavaScript 'In tüm Application Insights SDK 'Ları, dağıtılmış izlemeyi yerel olarak destekler. Her Application Insights SDK 'Yı yükleme ve yapılandırmaya yönelik yönergeler aşağıda verilmiştir:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python (Önizleme)](opencensus-python.md)

Uygun Application Insights SDK yüklü ve yapılandırılmış olarak, popüler çerçeveler, kitaplıklar ve teknolojiler için izleme bilgileri SDK bağımlılığı otomatik toplayıcılarına göre otomatik olarak toplanır. Desteklenen teknolojilerin tam listesi [bağımlılık otomatik toplama belgelerinde](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)bulunabilir.

 Ayrıca, tüm teknolojiler [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)üzerinde [trackdependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) çağrısı ile el ile izlenebilir.

## <a name="enable-via-opencensus"></a>OpenCensus aracılığıyla etkinleştir

Application Insights SDK 'lara ek olarak, Application Insights [Opencensus](https://opencensus.io/)aracılığıyla dağıtılmış izlemeyi da destekler. OpenCensus, hizmetler için ölçüm koleksiyonu ve dağıtılmış izleme sağlamak üzere açık kaynaklı, satıcının bağımsız, tek kitaplıkların tek bir dağıtımı olur. Ayrıca, açık kaynak topluluğunun redin, Memcache veya MongoDB gibi popüler teknolojilerle dağıtılmış izlemeyi etkinleştirmesini sağlar. [Microsoft, diğer birçok izleme ve bulut iş ortaklarıyla OpenCensus üzerinde işbirliği](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)yapıyor.

[Python (Önizleme)](opencensus-python.md) 

OpenCensus Web sitesi, [Python](https://opencensus.io/api/python/trace/usage.html) ve [Go](https://godoc.org/go.opencensus.io)için API başvuru belgelerini ve ayrıca opencensus kullanmaya yönelik çeşitli farklı kılavuzların bakımını yapar. 

## <a name="next-steps"></a>Sonraki adımlar

* [OpenCensus Python Kullanım Kılavuzu](https://opencensus.io/api/python/trace/usage.html)
* [Uygulama eşlemesi](./../../azure-monitor/app/app-map.md)
* [Uçtan uca performans izleme](./../../azure-monitor/learn/tutorial-performance.md)
