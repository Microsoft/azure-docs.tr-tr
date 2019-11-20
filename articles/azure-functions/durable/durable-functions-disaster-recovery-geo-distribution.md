---
title: Dayanıklı İşlevler-Azure 'da olağanüstü durum kurtarma ve coğrafi dağıtım
description: Dayanıklı İşlevler ' de olağanüstü durum kurtarma ve coğrafi dağıtım hakkında bilgi edinin.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: f242a16bc89e6b229efa42b88ebd20ca174e2516
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098063"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Olağanüstü durum kurtarma ve coğrafi dağıtım

## <a name="overview"></a>Genel Bakış

Dayanıklı İşlevler, Azure depolama 'da tüm durum kalıcı hale getirilir. [Görev hub 'ı](durable-functions-task-hubs.md) , Azure depolama kaynakları için, düzenleme için kullanılan mantıksal bir kapsayıcıdır. Orchestrator ve Activity işlevleri aynı görev merkezine ait olduklarında yalnızca birbirleriyle etkileşim kurabilir.
Açıklanan senaryolar kullanılabilirliği artırmak ve olağanüstü durum kurtarma etkinlikleri sırasında kapalı kalma süresini en aza indirmek için dağıtım seçenekleri önerin.

Bu senaryoların, Azure Storage kullanımı tarafından sunulduğundan, etkin-Pasif yapılandırmalara dayalı olduğunu fark etmek önemlidir. Bu model, farklı bir bölgeye yedek (pasif) işlev uygulaması dağıtmaktan oluşur. Traffic Manager, birincil (etkin) işlev uygulamasını kullanılabilirlik için izler. Birincil işlem başarısız olursa yedekleme işlevi uygulamasına yük devreder. Daha fazla bilgi için bkz [](https://azure.microsoft.com/services/traffic-manager/). Traffic Manager [Öncelik trafiği-yönlendirme yöntemi.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Önerilen etkin-Pasif yapılandırma, bir istemcinin HTTP aracılığıyla her zaman yeni düzenlemeleri tetikleyebilmesini sağlar. Ancak, iki işlev uygulamasının aynı depolamayı paylaştığı bir sonucu olarak, arka plan işleme her ikisi arasında dağıtılır ve aynı kuyruklarda iletiler için rekabet edilir. Bu yapılandırma, ikincil işlev uygulaması için eklenen çıkış maliyetlerinde yer doğurur.
> - Temel alınan depolama hesabı ve görev hub 'ı birincil bölgede oluşturulur ve her iki işlev uygulaması tarafından paylaşılır.
> - Redundantly dağıtılan tüm işlev uygulamaları, HTTP aracılığıyla etkinleştirilme durumunda aynı işlev erişim anahtarlarını paylaşmalıdır. Işlevler çalışma zamanı, tüketicilerin program aracılığıyla işlev anahtarlarını eklemesini, silmesini ve güncelleştirmesini sağlayan bir [Yönetim API 'si](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) sunar.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Senaryo 1-paylaşılan depolama ile yük dengeli işlem

Azure 'daki işlem altyapısı başarısız olursa, işlev uygulaması kullanılamaz hale gelebilir. Bu gibi kapalı kalma olasılığını en aza indirmek için, bu senaryo farklı bölgelere dağıtılan iki işlev uygulaması kullanır.
Traffic Manager, birincil işlev uygulamasındaki sorunları tespit etmek ve trafiği ikincil bölgedeki işlev uygulamasına otomatik olarak yeniden yönlendirmek üzere yapılandırılır. Bu işlev uygulaması aynı Azure Depolama hesabını ve görev hub 'ını paylaşır. Bu nedenle, işlev uygulamalarının durumu kaybedilmez ve iş normal şekilde sürdürülür. Sistem durumu birincil bölgeye geri yüklendikten sonra Azure Traffic Manager, istekleri bu işlev uygulamasına otomatik olarak yönlendirmeye başlar.

![Senaryo 1 ' i gösteren diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Bu dağıtım senaryosu kullanılırken birçok avantaj vardır:

- İşlem altyapısı başarısız olursa, iş, yük devretme bölgesinde durum kaybı olmadan sürdürülür.
- Traffic Manager, otomatik olarak sağlıklı işlev uygulamasına otomatik yük devretme işlemini gerçekleştirir.
- Traffic Manager, kesinti düzeltildikten sonra birincil işlev uygulamasına trafiği otomatik olarak yeniden oluşturur.

Ancak, bu senaryonun kullanılması şunları göz önünde bulundurun:

- İşlev uygulaması ayrılmış bir App Service planı kullanılarak dağıtılırsa, işlem altyapısını yük devretmek için çoğaltma maliyetlerini artırır.
- Bu senaryo, işlem altyapısındaki kesintileri ele alır, ancak depolama hesabı, işlev uygulaması için tek hata noktası olmaya devam eder. Depolama kesintisi varsa, uygulama kesinti süresi yaşar.
- İşlev uygulaması yük devretmede başarısız olursa, bölgeler arasında depolama hesabına erişim olacağı için gecikme süresi artacaktır.
- Depolama hizmetine, bulunduğu farklı bir bölgeden, ağ çıkış trafiği nedeniyle daha yüksek maliyetli bir şekilde erişme.
- Bu senaryo Traffic Manager bağımlıdır. [Traffic Manager nasıl çalıştığını](../../traffic-manager/traffic-manager-how-it-works.md)göz önünde bulundurarak, dayanıklı bir işlevi tüketen bir istemci uygulamasının Traffic Manager işlev uygulama adresini yeniden sorgulayabilmesini gerektiren bir zaman olabilir.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Senaryo 2-bölgesel depolama ile yük dengeli işlem

Yukarıdaki senaryo yalnızca işlem altyapısındaki hata durumunu ele alır. Depolama hizmeti başarısız olursa, işlev uygulamasının kesintiye neden olur.
Dayanıklı işlevlerin sürekli çalışmasını sağlamak için bu senaryo, işlev uygulamalarının dağıtıldığı her bölgede yerel bir depolama hesabı kullanır.

![Senaryo 2 ' nin gösterildiği diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Bu yaklaşım, önceki senaryoya yönelik iyileştirmeler ekler:

- İşlev uygulaması başarısız olursa, Traffic Manager ikincil bölgeye yük devretme işlemini gerçekleştirir. Ancak, işlev uygulaması kendi depolama hesabını temel aldığından, dayanıklı işlevler çalışmaya devam eder.
- Yük devretme sırasında, işlev uygulaması ve depolama hesabı birlikte bulunduğundan, yük devretme bölgesinde ek bir gecikme yoktur.
- Depolama katmanının başarısızlığı, dayanıklı işlevlerde hatalara neden olur. Bu, sırasıyla yük devretme bölgesine yeniden yönlendirme tetikleyecektir. Aynı şekilde, işlev uygulaması ve depolama alanı her bölge için yalıtılmış olduğundan, dayanıklı işlevler çalışmaya devam edecektir.

Bu senaryoya ilişkin önemli noktalar:

- İşlev uygulaması ayrılmış bir AppService planı kullanılarak dağıtılırsa, işlem altyapısını yük devretmek için çoğaltma maliyetlerini artırır.
- Geçerli durum yük devretmez, bu da yürütmenin yürütülmesi ve CheckIn işlevinin başarısız olacağını gösterir. İşi yeniden denemek/yeniden başlatmak için istemci uygulamasına kadar.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Senaryo 3-GRS paylaşılan depolama ile yük dengeli işlem

Bu senaryo, paylaşılan depolama hesabı uygulayan ilk senaryonun üzerinde yapılan bir değişikliktir. Depolama hesabının coğrafi çoğaltma etkin olarak oluşturulduğu temel fark.
İşlevsellik, bu senaryo Senaryo 1 ile aynı avantajları sağlar, ancak ek veri kurtarma avantajları sağlar:

- Coğrafi olarak yedekli depolama (GRS) ve Okuma Erişimli GRS (RA-GRS), depolama hesabınız için kullanılabilirliği en üst düzeye çıkarır.
- Depolama hizmeti 'nin bir bölge kesintisi varsa, olasılıklardan biri, veri merkezi işlemlerinin, depolama alanının ikincil bölgeye yük devretmeli olduğunu belirlemesidir. Bu durumda, depolama hesabı erişimi, Kullanıcı müdahalesi olmadan depolama hesabının coğrafi olarak çoğaltılan kopyasına saydam olarak yönlendirilir.
- Bu durumda, dayanıklı işlevlerin durumu depolama hesabının en son çoğaltmasına kadar saklanır ve bu işlem birkaç dakikada bir gerçekleşir.

Diğer senaryolarda olduğu gibi önemli noktalar da vardır:

- Çoğaltmaya yük devretme, veri merkezi işleçleri tarafından yapılır ve bu işlem biraz zaman alabilir. Bu saate kadar, işlev uygulaması bir kesinti olduğunu etkilemeyecektir.
- Coğrafi olarak çoğaltılan depolama hesaplarını kullanmanın maliyeti artar.
- GRS zaman uyumsuz olarak gerçekleştirilir. Çoğaltma işleminin gecikmesi nedeniyle en son işlemlerden bazıları kaybolmuş olabilir.

![Senaryo 3 ' ü gösteren diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Sonraki adımlar

[RA-GRS kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](../../storage/common/storage-designing-ha-apps-with-ragrs.md) hakkında daha fazla bilgi edinebilirsiniz
