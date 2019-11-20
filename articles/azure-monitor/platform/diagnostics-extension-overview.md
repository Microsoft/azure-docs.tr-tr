---
title: Azure Tanılama uzantısına genel bakış
description: Azure tanılama 'yı kullanarak hata ayıklama, performans, izleme, bulut hizmetlerinde trafik analizi, sanal makineler ve Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 02/13/2019
ms.openlocfilehash: d1721411b57fc3542af48fc5f48eca7e4a2d06c8
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552154"
---
# <a name="what-is-azure-diagnostics-extension"></a>Azure Tanılama uzantısı nedir?
Azure Tanılama uzantısı, dağıtılan bir uygulamadaki tanılama verilerinin toplanmasını sağlayan Azure 'daki bir aracıdır. Tanılama uzantısını bir dizi farklı kaynaktan kullanabilirsiniz. Şu anda desteklenen Azure bulut hizmeti (klasik) Web ve çalışan rolleri, sanal makineler, sanal makine ölçek kümeleri ve Service Fabric. Diğer Azure hizmetlerinde farklı tanılama yöntemleri vardır. Bkz. [Azure 'da Izlemeye genel bakış](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux Aracısı
Linux çalıştıran sanal makinelerde [uzantının Linux sürümü](../../virtual-machines/extensions/diagnostics-linux.md) kullanılabilir. Toplanan istatistikler ve davranış Windows sürümünden farklıdır.

## <a name="data-you-can-collect"></a>Toplayacağınız veriler
Azure Tanılama uzantısı aşağıdaki veri türlerini toplayabilirler:

| Veri kaynağı | Açıklama |
| --- | --- |
| Performans sayacı ölçümleri |İşletim sistemi ve özel performans sayaçları |
| Uygulama günlükleri |Uygulamanız tarafından yazılan izleme iletileri |
| Windows olay günlükleri |Windows olay günlüğü sistemine gönderilen bilgiler |
| .NET EventSource günlükleri |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) sınıfını kullanarak kod yazma olayları |
| IIS Günlükleri |IIS Web siteleri hakkında bilgi |
| [Bildirim tabanlı ETW günlükleri](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Herhangi bir işlem tarafından oluşturulan Windows olayları için olay Izleme. (1 |
| Kilitlenme dökümü (Günlükler) |Uygulamanın kilitlenirse işlemin durumu hakkında bilgi |
| Özel hata günlükleri |Uygulamanız veya hizmetiniz tarafından oluşturulan Günlükler |
| Azure tanılama altyapı günlükleri |Azure Tanılama hakkında bilgi |

(1) ETW sağlayıcılarının bir listesini almak için `c:\Windows\System32\logman.exe query providers`, bilgi toplamak istediğiniz makinede bir konsol penceresinde çalıştırın.

## <a name="data-storage"></a>Veri depolama
Uzantı, verilerini belirttiğiniz bir [Azure depolama hesabında](diagnostics-extension-to-storage.md) depolar.

Ayrıca, [Application Insights](../../azure-monitor/app/cloudservices.md)gönderebilirsiniz. 

Diğer bir seçenek de [Olay Hub 'ına](../../event-hubs/event-hubs-about.md)akışını, daha sonra Azure olmayan izleme hizmetlerine göndermenizi sağlar.

Verilerinizi Azure Izleyici ölçümleri zaman serisi veritabanına gönderme seçeneğiniz de vardır. Şu anda, bu havuz yalnızca performans sayaçları için geçerlidir. Performans sayaçlarını özel ölçümler olarak göndermenizi sağlar. Bu özellik önizleme aşamasındadır. Azure Izleyici havuzu şunları destekler:
* Azure izleyici [ölçümleri API 'leri](https://docs.microsoft.com/rest/api/monitor/) aracılığıyla Azure izleyici 'ye gönderilen tüm performans sayaçlarını alma.
* Azure izleyici 'de [ölçüm uyarıları](../../azure-monitor/platform/alerts-overview.md) aracılığıyla Azure izleyici 'ye gönderilen tüm performans sayaçlarında uyarı verme
* Performans sayaçlarındaki joker karakter operatörü, ölçümünüzün "örnek" boyutu olarak değerlendiriliyor.  Örneğin, "MantıksalDisk (\*)/DiskWrites/sec" sayacını topladıysanız, sanal makine üzerindeki her bir mantıksal disk için (örneğin, C:) disk yazma/sn üzerinde disk yazma/sn üzerinde uyarı oluşturmak veya uyarı vermek üzere "örnek" boyutunda filtre uygulayabilir ve bölebilirsiniz.

Bu havuzun nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için [Azure tanılama şeması belgelerine bakın.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Maliyetler
Yukarıdaki seçeneklerin her biri maliyette bulunabilir. Beklenmedik faturaları önlemek için bunları araştırdığınızdan emin olun.  Application Insights, Olay Hub 'ı ve Azure Storage, giriş ve depolama ile ilişkili ayrı maliyetlere sahiptir. Azure depolama, belirli bir zaman süresinden sonra daha eski verileri temizlemek istiyorsanız, bazı verileri süresiz olarak tutar.    

## <a name="versioning-and-configuration-schema"></a>Sürüm oluşturma ve yapılandırma şeması
Bkz. [Azure tanılama sürümü geçmişi ve şeması](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Sonraki adımlar
Tanılama 'yı toplamaya çalıştığınız hizmeti seçin ve başlamak için aşağıdaki makaleleri kullanın. Belirli görevler için başvuru için genel Azure tanılama bağlantılarını kullanın.

## <a name="cloud-services-using-azure-diagnostics"></a>Azure Tanılama kullanarak Cloud Services
* Visual Studio kullanıyorsanız bkz. başlamak için [Visual Studio 'yu kullanarak Cloud Services uygulamayı izleme](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) . Aksi takdirde, bkz.
* [Azure Tanılama kullanarak bulut hizmetlerini izleme](../../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services uygulamasında Azure Tanılama ayarlama](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Daha gelişmiş konular için bkz.

* [Cloud Services için Application Insights ile Azure Tanılama kullanma](../../azure-monitor/app/cloudservices.md)
* [Azure Tanılama bir Cloud Services uygulamasının akışını izleme](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Cloud Services tanılamayı ayarlamak için PowerShell 'i kullanma](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines (Sanal Makineler)
* Visual Studio kullanıyorsanız bkz. başlamak için [Azure sanal makinelerini izlemek üzere Visual Studio 'Yu kullanma](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) . Aksi takdirde, bkz.
* [Azure sanal makinesinde Azure Tanılama ayarlama](/azure/virtual-machines/extensions/diagnostics-windows)

Daha gelişmiş konular için bkz.

* [Azure sanal makinelerinde tanılamayı ayarlamak için PowerShell 'i kullanma](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager şablonu kullanarak izleme ve Tanılama ile Windows sanal makinesi oluşturma](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
[Service Fabric uygulamayı izlemeye](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)başlayın. Diğer pek çok Service Fabric tanılama makalesi, bu makaleye ulaştıktan sonra sol taraftaki Gezinti ağacında bulunur.

## <a name="general-articles"></a>Genel makaleler
* [Azure tanılama 'de performans sayaçlarını kullanmayı](../../cloud-services/diagnostics-performance-counters.md)öğrenin.
* Azure depolama tablolarında verilerinizi başlatma veya bulma ile ilgili sorun yaşıyorsanız, bkz. [sorun giderme Azure tanılama](diagnostics-extension-troubleshooting.md)

