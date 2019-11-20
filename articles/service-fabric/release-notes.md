---
title: Azure Service Fabric yayınları
description: Service Fabric 'teki en son özellikler ve geliştirmeler için sürüm notları.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 4a681b3a09def3a7b27b603cf5201aebdbf2e4bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72386221"
---
# <a name="service-fabric-releases"></a>Service Fabric yayınları

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">sorun giderme kılavuzu</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">sorun izleme</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Destek seçenekleri</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Desteklenen sürümler</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kod örnekleri</a>

Bu makalede, Service Fabric çalışma zamanına ve SDK 'lara yönelik en son yayınlar ve güncelleştirmeler hakkında daha fazla bilgi sağlanmaktadır.

## <a name="whats-new-in-service-fabric"></a>Service Fabric yenilikleri

### <a name="service-fabric-65"></a>Service Fabric 6,5

En son Service Fabric sürümü, küme ve uygulama yaşam döngüsü yönetimini kolaylaştırmak için desteklenebilirlik, güvenilirlik ve performans iyileştirmeleri, yeni özellikler, hata düzeltmeleri ve geliştirmeler içerir.

> [!IMPORTANT]
> Service Fabric 6,5, Visual Studio 2015 ' de Service Fabric araçları desteğiyle son sürümdür. Müşterilerin ileri giderek [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ' e taşınması önerilir.

Service Fabric 6,5 ' deki yenilikler aşağıda verilmiştir:

- Service Fabric Explorer, görüntü deposuna yüklediğiniz uygulamaları incelemek için bir [görüntü deposu Görüntüleyici](service-fabric-visualizing-your-cluster.md#image-store-viewer) içerir.

- [Düzeltme Eki düzenleme uygulaması (POA)](service-fabric-patch-orchestration-application.md) sürüm [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) , birçok kendi kendine tanılama geliştirmesi içerir. POA müşterilerinin bu sürüme taşınması önerilir.

- [Eventstore hizmeti,](service-fabric-visualizing-your-cluster.md#event-store) siz kabul etmediğiniz müddetçe Service Fabric 6,5 kümeleri için varsayılan olarak etkinleştirilmiştir.

- Durum bilgisi olan hizmetler için [çoğaltma yaşam döngüsü olayları](service-fabric-diagnostics-event-generation-operational.md#replica-events) eklendi.

- Çekirdek düğümünün sağlıksız olması durumunda (*aşağı*, *kaldırılmış* veya *bilinmiyor*) küme düzeyinde uyarılar da dahil olmak üzere [çekirdek düğüm durumunun daha iyi görünebilirliği](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status).

- [Service Fabric uygulama olağanüstü durum kurtarma aracı](https://github.com/Microsoft/Service-Fabric-AppDRTool) , birincil küme bir olağanüstü durum ile karşılaştığında, durum bilgisi olan hizmetlerin hızla kurtarılmasını Service Fabric sağlar. Birincil kümeden alınan veriler, düzenli yedekleme ve geri yükleme kullanılarak ikincil bekleme uygulamasıyla sürekli olarak eşitlenir.

- [.NET Core uygulamalarını Linux tabanlı kümelere yayımlamak](service-fabric-how-to-publish-linux-app-vs.md)Için Visual Studio desteği.

- Azure 'da yeni bir Linux kümesi yükselttiğinizde veya oluşturduğunuzda, [azure SERVICE fabrıc CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) , Service Fabric 6,5 (ve sonraki sürümler) için otomatik olarak yüklenir.

- [Sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) , MacOS/Linux Onebox kümelerinde varsayılan olarak yüklenir.

Daha fazla ayrıntı için [Service Fabric 6,5 sürüm notlarına](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)bakın.

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 yayınları

| Sürüm tarihi | Yayınlayın | Daha fazla bilgi |
|---|---|---|
| 11 Haziran 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Sürüm notları](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 Temmuz 2019 | [Azure Service Fabric 6,5 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Sürüm notları](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 Temmuz 2019 | [Azure Service Fabric 6,5 yenileme sürümü](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Sürüm notları](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 Ağu 2019 | [Azure Service Fabric 6,5 yenileme sürümü](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Sürüm notları](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 Eki, 2019 | [Azure Service Fabric 6,5 yenileme sürümü](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Sürüm notları](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Önceki sürümler

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 yayınları

| Sürüm tarihi | Yayınlayın | Daha fazla bilgi |
|---|---|---|
| 30 Kasım 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 Aralık 2018 | [Windows kümeleri için Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 Şubat 2019 | [Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 Mart 2019 | [Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 Nisan 2019 | [Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 Mayıs 2019 | [Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 Mayıs 2019 | [Azure Service Fabric 6,4 yenileme sürümü](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Sürüm notları](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
