---
title: Kod değişikliği olmadan uygulamalarınızı izleyin-Azure Izleyici için otomatik izleme Application Insights | Microsoft Docs
description: Azure izleyici Application Insights-kodsuz kullanacaksınız uygulama performansı yönetimi için otomatik izleme 'ye Genel Bakış
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708501"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Otomatik izleme veya kodsuz kullanacaksınız Attach-Azure izleyici Application Insights nedir?

Otomatik izleme veya kodsuz kullanacaksınız iliştirme, kodunuzu değiştirmeden Application Insights ile uygulama izlemeyi etkinleştirmenizi sağlar.  

Application Insights çeşitli kaynak sağlayıcılarıyla tümleşiktir ve farklı ortamlarda çalışmaktadır. Temelde, tek yapmanız gereken, ve bazı durumlarda, Telemetriyi otomatik olarak toplayacak olan aracıyı yapılandırır. Zaman içinde, Application Insights kaynağınızda ölçümleri, verileri ve bağımlılıkları görürsünüz. Bu, olası sorunların kaynağını yapmadan önce bu kaynakları ortaya çıkarabilir ve bu durum, uçtan uca işlem görünümüyle ilgili asıl nedeni analiz etmenize olanak tanır.

## <a name="supported-environments-languages-and-resource-providers"></a>Desteklenen ortamlar, diller ve kaynak sağlayıcıları

Ek tümleştirmeler eklediğimiz için, otomatik izleme özelliği matrisi karmaşık hale gelir. Aşağıdaki tabloda, çeşitli kaynak sağlayıcılarının, dillerin ve ortamların destek alanının geçerli durumu gösterilmektedir.

|Ortam/kaynak sağlayıcısı          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Windows üzerinde Azure App Service           | GA, OnBD *       | GA, kabul etme      | Sürüyor     | Sürüyor     | Desteklenmez   |
|Linux üzerinde Azure App Service             | Yok             | Desteklenmez   | Sürüyor     | Genel Önizleme  | Desteklenmez   |
|AKS üzerinde Azure App Service               | Yok             | Tasarımda       | Tasarımda       | Tasarımda       | Desteklenmez   |
|Azure Işlevleri-temel                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Işlevleri Windows-bağımlılıklar | Desteklenmez   | Desteklenmez   | Genel Önizleme  | Desteklenmez   | Desteklenmez   |
|Azure Kubernetes Service               | Yok             | Tasarımda       | Aracı üzerinden   | Tasarımda       | Desteklenmez   |
|Azure VM 'Leri Windows                      | Genel Önizleme  | Desteklenmez   | Desteklenmez   | Desteklenmez   | Desteklenmez   |
|Şirket Içi VM 'Ler Windows                | GA, kabul etme      | Desteklenmez   | Aracı üzerinden   | Desteklenmez   | Desteklenmez   |
|Tek başına aracı-tüm env.            | Desteklenmez   | Desteklenmez   | GA              | Desteklenmez   | Desteklenmez   |

* OnBD varsayılan olarak açık için kısaysa, uygulamanızı desteklenen ortamlarda dağıttıktan sonra Application Insights otomatik olarak etkinleştirilir. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Windows üzerinde Azure App Service uygulama izleme, [.NET uygulamaları](./azure-web-apps.md?tabs=net) .NET için kullanılabilir ve varsayılan olarak etkindir.

#### <a name="netcore"></a>. NETCore
İçin izleme [. NETCore uygulamaları](./azure-web-apps.md?tabs=netcore) tek bir tıklama ile etkinleştirilebilir.

#### <a name="java"></a>Java
Java uygulamalarını Windows üzerinde App Service izlemeye yönelik portal tümleştirmesi Şu anda kullanılamıyor, ancak uygulamaları App Service dağıtım yapmadan önce herhangi bir kod değişikliği yapmadan uygulamanıza Application Insights [java 3,0 tek başına Aracısı](./java-in-process-agent.md) ekleyebilirsiniz. Application Insights Java 3,0 Aracısı genel kullanıma sunulmuştur.

#### <a name="nodejs"></a>Node.js
Windows üzerinde Node.js uygulamalar için izleme şu anda portaldan etkinleştirilemez. Node.js uygulamalarını izlemek için [SDK 'yı](./nodejs.md)kullanın.

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
İzleyin. Linux üzerinde çalışan NETCore uygulamaları [SDK](./asp-net-core.md)'yı kullanın.

#### <a name="java"></a>Java 
Portaldan Linux üzerinde App Service için Java uygulama izlemeyi etkinleştirmek kullanılamaz, ancak uygulamaları App Service dağıtım yapmadan önce uygulamanıza [Application Insights java 3,0 aracısını](./java-in-process-agent.md) ekleyebilirsiniz. Application Insights Java 3,0 Aracısı genel kullanıma sunulmuştur.

#### <a name="nodejs"></a>Node.js
[Linux üzerinde App Service Node.js uygulamalar](./azure-web-apps.md?tabs=nodejs) , genel önizlemede ve tüm bölgelerde kullanılabilen Azure Portal etkinleştirilebilir. 

#### <a name="python"></a>Python
[Python uygulamanızı izlemek](./opencensus-python.md) için SDK 'yı kullanma 

## <a name="azure-functions"></a>Azure İşlevleri

Azure Işlevleri için temel izleme, günlük, performans, hata verileri ve HTTP isteklerini toplamak üzere varsayılan olarak etkinleştirilmiştir. Java uygulamaları için, dağıtılmış izleme ile daha zengin izlemeyi etkinleştirebilir ve uçtan uca işlem ayrıntılarını alabilirsiniz. Java için bu işlev genel önizlemeye sunuldu ve [Azure Portal ' de etkinleştirebilirsiniz](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes hizmetinin ortak olmayan araçları, [tek başına aracı](./java-in-process-agent.md)aracılığıyla Java uygulamalarında kullanılabilir. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM 'Leri ve sanal makine ölçek kümesi

Azure VM 'Leri ve sanal makine ölçek kümesi için otomatik izleme, [.net](./azure-vm-vmss-apps.md) ve [Java](./java-in-process-agent.md)için kullanılabilir.  

## <a name="on-premises-servers"></a>Şirket içi sunucular
.NET uygulamaları ve [Java uygulamaları](./java-in-process-agent.md)için şirket [içi Windows sunucularınız](./status-monitor-v2-overview.md) için izlemeyi kolayca etkinleştirebilirsiniz.

## <a name="other-environments"></a>Diğer ortamlar
Çok yönlü Java tek başına Aracısı her türlü ortamda çalışarak, kodunuzu işaretlememeye gerek yoktur. Application Insights etkinleştirmek ve Java aracısının harika özellikleri hakkında bilgi edinmek için [kılavuzu izleyin](./java-in-process-agent.md) . Aracı genel önizlemede ve tüm bölgelerde kullanılabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Application Insights genel bakış](./app-insights-overview.md)
* [Uygulama eşlemesi](./app-map.md)
* [Uçtan uca performans izleme](../app/tutorial-performance.md)