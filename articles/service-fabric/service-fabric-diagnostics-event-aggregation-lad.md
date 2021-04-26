---
title: Linux Azure Tanılama ile olay toplama
description: Azure Service Fabric kümelerini izleme ve Tanılama için LAD 'yi kullanarak olayları toplama ve toplama hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628943"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Linux Azure Tanılama kullanarak olay toplama ve toplama
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric kümesi çalıştırırken, günlükleri merkezi konumdaki tüm düğümlerden toplamak iyi bir fikirdir. Günlüklerin merkezi bir konumda olması, kümenizdeki sorunları veya bu kümede çalışan uygulama ve hizmetlerde sorunları analiz etmenize ve gidermenize yardımcı olur.

Günlükleri karşıya yükleme ve toplamanın bir yolu, Azure depolama 'ya günlükleri yükleyen ve ayrıca Azure Application Insights veya Event Hubs günlükleri gönderme seçeneğine sahip Linux Azure Tanılama (LAD) uzantısını kullanmaktır. Ayrıca, olayları depolama alanından okumak ve [Azure izleyici günlükleri](./service-fabric-diagnostics-oms-setup.md) ya da başka bir günlük ayrıştırma çözümü gibi bir çözümleme platformu ürününe yerleştirmek için bir dış işlem de kullanabilirsiniz.

## <a name="log-and-event-sources"></a>Günlük ve olay kaynakları

### <a name="service-fabric-platform-events"></a>Service Fabric platform olayları
Service Fabric, işletimsel olaylar veya çalışma zamanı olayları dahil olmak üzere [Lttng](https://lttng.org)aracılığıyla kullanıma hazır birkaç günlüğü yayar. Bu Günlükler, kümenin Kaynak Yöneticisi şablonunun belirttiği konumda depolanır. Depolama hesabı ayrıntılarını almak veya ayarlamak için **AzureTableWinFabETWQueryable** etiketini arayın ve **storeconnectionstring** öğesine bakın.

### <a name="application-events"></a>Uygulama olayları
 Yazılımlarınızdan yararlanarak sizin tarafınızdan belirtilen şekilde, uygulamalarınızın ve hizmetlerinizin kodundan yayılan olaylar. Metin tabanlı günlük dosyaları yazan herhangi bir günlük çözümünü kullanabilirsiniz; Örneğin, LTTng. Daha fazla bilgi için, uygulamanızı izlemeye yönelik LTTng belgelerine bakın.

[Yerel makine geliştirme kurulumunda Hizmetleri izleyin ve tanılayın](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Tanılama uzantısını dağıtma
Günlükleri toplamanın ilk adımı, Service Fabric kümesindeki her bir sanal makineye tanılama uzantısını dağıtmaktır. Tanılama uzantısı her bir VM 'de günlükleri toplar ve bunları belirttiğiniz depolama hesabına yükler. 

Tanılama uzantısını küme oluşturmanın bir parçası olarak kümedeki VM 'lere dağıtmak için, **tanılamayı** **Açık** olarak ayarlayın. Kümeyi oluşturduktan sonra, portalı kullanarak bu ayarı değiştiremezsiniz, bu nedenle Kaynak Yöneticisi şablonunda uygun değişiklikleri yapmanız gerekir.

Bu, LAD aracısını belirtilen günlük dosyalarını izleyecek şekilde yapılandırır. Dosyaya yeni bir satır eklendiğinde, belirttiğiniz depolama alanına (tablo) gönderilen bir Syslog girişi oluşturur.


## <a name="next-steps"></a>Sonraki adımlar

1. Sorunları giderirken hangi olayları incelemeniz gerektiğini daha ayrıntılı olarak anlamak için bkz. [Lttng belgeleri](https://lttng.org/docs) ve [lad kullanma](../virtual-machines/extensions/diagnostics-linux.md).
2. Ölçümleri toplamaya, kümenizde dağıtılan kapsayıcıları izlemeye ve günlüklerinizi görselleştirmenize yardımcı olmak için [Log Analytics aracısını ayarlayın](service-fabric-diagnostics-event-analysis-oms.md) 
