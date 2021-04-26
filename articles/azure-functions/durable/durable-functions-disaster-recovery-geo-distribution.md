---
title: Olağanüstü durum kurtarma ve coğrafi dağıtım Azure Dayanıklı İşlevler
description: Dayanıklı İşlevler ' de olağanüstü durum kurtarma ve coğrafi dağıtım hakkında bilgi edinin.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071219"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Azure Dayanıklı İşlevler olağanüstü durum kurtarma ve coğrafi dağıtım

Microsoft, Azure hizmetlerinin her zaman kullanılabilir olduğundan emin olmaya çalışır. Ancak, planlanmamış hizmet kesintileri meydana gelebilir. Uygulamanız dayanıklılık gerektiriyorsa, Microsoft uygulamanızı coğrafi yedeklilik için yapılandırmanızı önerir. Ayrıca, müşterilerin bölgesel hizmet kesintisi için bir olağanüstü durum kurtarma planı olması gerekir. Olağanüstü durum kurtarma planının önemli bir kısmı, birincil çoğaltmanın kullanılamaz hale geldiği olayda uygulamanızın ve depolamanın ikincil çoğaltmasını devretmek için hazırlanmadır.

Dayanıklı İşlevler, tüm durum Azure Storage 'da varsayılan olarak kalıcı hale getirilir. [Görev hub 'ı](durable-functions-task-hubs.md) , Azure depolama [kaynakları için, düzenleme ve](durable-functions-types-features-overview.md#orchestrator-functions) [varlıklar](durable-functions-types-features-overview.md#entity-functions)için kullanılan mantıksal bir kapsayıcıdır. Orchestrator, etkinlik ve varlık işlevleri aynı görev merkezine ait olduklarında yalnızca birbirleriyle etkileşim kurabilir. Bu belge, bu Azure depolama kaynaklarının yüksek oranda kullanılabilir tutulması için senaryolar açıklanırken görev hub 'larına başvuracaktır.

Düzenlemeler ve varlıklar, kendileri HTTP aracılığıyla veya desteklenen diğer Azure Işlevleri tetikleyici türlerinden biri ile tetiklenen [istemci işlevleri](durable-functions-types-features-overview.md#client-functions) kullanılarak tetiklenebilir. Bunlar, [YERLEŞIK HTTP API 'leri](durable-functions-http-features.md#built-in-http-apis)kullanılarak da tetiklenebilir. Kolaylık olması için bu makale, Azure depolama ve HTTP tabanlı işlev Tetikleyicileri ve kullanılabilirliği artırma ve olağanüstü durum kurtarma etkinlikleri sırasında kapalı kalma süresini en aza indirme seçeneklerini kapsayan senaryolara odaklanacaktır. Service Bus veya Cosmos DB Tetikleyicileri gibi diğer tetikleyici türleri açıkça kapsanmaz.

Aşağıdaki senaryolar, Azure Storage kullanımı tarafından sunulduğundan, Active-Passive yapılandırmalarına dayalıdır. Bu model, farklı bir bölgeye yedek (pasif) işlev uygulaması dağıtmaktan oluşur. Traffic Manager, HTTP kullanılabilirliği için birincil (etkin) işlev uygulamasını izler. Birincil işlem başarısız olursa yedekleme işlevi uygulamasına yük devreder. Daha fazla bilgi için bkz. [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) [Öncelik Traffic-Routing yöntemi.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

> [!NOTE]
> - Önerilen Active-Passive yapılandırması, bir istemcinin HTTP aracılığıyla her zaman yeni düzenlemeleri tetikleyebilmesini sağlar. Ancak, iki işlevli uygulamanın depolama alanında aynı görev merkezini paylaştığı bir sonucu olarak, bazı arka plan depolama işlemleri aralarında her ikisi arasında dağıtılır. Bu nedenle bu yapılandırma, ikincil işlev uygulaması için bazı ek çıkış maliyetleri doğurur.
> - Temel alınan depolama hesabı ve görev hub 'ı birincil bölgede oluşturulur ve her iki işlev uygulaması tarafından paylaşılır.
> - Redundantly dağıtılan tüm işlev uygulamaları, HTTP aracılığıyla etkinleştirilme durumunda aynı işlev erişim anahtarlarını paylaşmalıdır. Işlevler çalışma zamanı, tüketicilerin program aracılığıyla işlev anahtarlarını eklemesini, silmesini ve güncelleştirmesini sağlayan bir [Yönetim API 'si](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) sunar. Anahtar yönetimi, [Azure Resource Manager API 'leri](https://www.markheath.net/post/managing-azure-functions-keys-2)kullanılarak da mümkündür.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Senaryo 1-paylaşılan depolama ile yük dengeli işlem

Azure 'daki işlem altyapısı başarısız olursa, işlev uygulaması kullanılamaz hale gelebilir. Bu gibi kapalı kalma olasılığını en aza indirmek için, bu senaryo farklı bölgelere dağıtılan iki işlev uygulaması kullanır.
Traffic Manager, birincil işlev uygulamasındaki sorunları tespit etmek ve trafiği ikincil bölgedeki işlev uygulamasına otomatik olarak yeniden yönlendirmek üzere yapılandırılır. Bu işlev uygulaması aynı Azure Depolama hesabını ve görev hub 'ını paylaşır. Bu nedenle, işlev uygulamalarının durumu kaybedilmez ve iş normal şekilde sürdürülür. Sistem durumu birincil bölgeye geri yüklendikten sonra Azure Traffic Manager, istekleri bu işlev uygulamasına otomatik olarak yönlendirmeye başlar.

![Senaryo 1 ' i gösteren diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Bu dağıtım senaryosu kullanılırken birçok avantaj vardır:

- İşlem altyapısı başarısız olursa, çalışma yük devretme bölgesinde veri kaybı olmadan sürdürülür.
- Traffic Manager sağlıklı işlev uygulamasına otomatik olarak yük devretmeyi ele alır.
- Traffic Manager, kesinti düzeltildikten sonra birincil işlev uygulamasına trafiği otomatik olarak yeniden oluşturur.

Ancak, bu senaryonun kullanılması şunları göz önünde bulundurun:

- İşlev uygulaması adanmış bir App Service planı kullanılarak dağıtılırsa, yük devretme veri merkezinde işlem altyapısının çoğaltılması maliyetleri artırır.
- Bu senaryo, işlem altyapısındaki kesintileri ele alır, ancak depolama hesabı, işlev uygulaması için tek hata noktası olmaya devam eder. Bir depolama kesintisi oluşursa, uygulama kapalı kalma süresini de olumsuz şekilde azaltır.
- İşlev uygulaması yük devretmede başarısız olursa, bölgeler arasında depolama hesabına erişim olacağı için gecikme süresi artacaktır.
- Depolama hizmetine, bulunduğu farklı bir bölgeden, ağ çıkış trafiği nedeniyle daha yüksek maliyetli bir şekilde erişme.
- Bu senaryo Traffic Manager bağımlıdır. [Traffic Manager nasıl çalıştığını](../../traffic-manager/traffic-manager-how-it-works.md)göz önünde bulundurarak, dayanıklı bir işlevi tüketen bir istemci uygulamasının Traffic Manager işlev uygulama adresini yeniden sorgulayabilmesini gerektiren bir zaman olabilir.

> [!NOTE]
> Dayanıklı İşlevler uzantısının **v 2.3.0** 'den başlayarak, aynı depolama hesabı ve görev hub 'ı yapılandırmasıyla aynı anda iki işlev uygulaması güvenli bir şekilde çalıştırılabilir. Başlatılacak ilk uygulama, diğer uygulamaların görev hub 'ı kuyruklarından iletileri çalmasını önleyen uygulama düzeyinde bir blob kirası elde eder. Bu ilk uygulama çalışmayı durdurduktan sonra Kiralama süresinin dolması ve ikinci bir uygulama tarafından alınabilir ve böylece görev hub 'ı iletilerini işlemeye devam eder.
> 
> V 2.3.0 'den önce, aynı depolama hesabını kullanmak üzere yapılandırılan işlev uygulamaları, iletileri işleyecek ve depolama yapıtlarını eşzamanlı olarak güncelleyebilir, böylece genel gecikme süreleri ve çıkış maliyetleri çok daha yüksektir. Birincil ve çoğaltma uygulamalarına şimdiye kadar dağıtılan farklı kodlar varsa, bu durumda geçici olarak, iki uygulama genelinde Orchestrator işlev tutarsızlıkları nedeniyle düzenlemeler de başarısız olabilir. Bu nedenle, olağanüstü durum kurtarma amaçlarıyla coğrafi dağıtım gerektiren tüm uygulamaların, dayanıklı uzantının v 2.3.0 veya üstünü kullanması önerilir.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Senaryo 2-bölgesel depolama ile yük dengeli işlem

Yukarıdaki senaryo yalnızca işlem altyapısındaki hata durumunu ele alır. Depolama hizmeti başarısız olursa, işlev uygulamasının kesintiye neden olur.
Dayanıklı işlevlerin sürekli çalışmasını sağlamak için bu senaryo, işlev uygulamalarının dağıtıldığı her bölgede yerel bir depolama hesabı kullanır.

![Senaryo 2 ' nin gösterildiği diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Bu yaklaşım, önceki senaryoya yönelik iyileştirmeler ekler:

- İşlev uygulaması başarısız olursa, Traffic Manager ikincil bölgeye yük devretme işlemini gerçekleştirir. Ancak, işlev uygulaması kendi depolama hesabını temel aldığından, dayanıklı işlevler çalışmaya devam eder.
- Yük devretme sırasında, işlev uygulaması ve depolama hesabı birlikte bulunduğundan yük devretme bölgesinde ek bir gecikme olmaz.
- Depolama katmanının başarısızlığı dayanıklı işlevlerde hatalara neden olur, bu da yük devretme bölgesine yeniden yönlendirme tetikleyecektir. Aynı şekilde, işlev uygulaması ve depolama alanı her bölge için yalıtılmış olduğundan, dayanıklı işlevler çalışmaya devam edecektir.

Bu senaryoya ilişkin önemli noktalar:

- İşlev uygulaması adanmış bir App Service planı kullanılarak dağıtılırsa, yük devretme veri merkezinde işlem altyapısının çoğaltılması maliyetleri artırır.
- Mevcut durum yük devredilmez, bu, var olan düzenlemeler ve varlıkların, birincil bölge kurtarana kadar etkili bir şekilde duraklatılıp devre dışı olacağını gösterir.

Özetlemek gerekirse, ilk ve İkinci senaryo arasındaki zorunluluğunu getirir gecikme süresi korunur ve çıkış maliyetleri en aza indirilir ancak mevcut düzenlemeler ve varlıklar kapalı kalma süresi boyunca kullanılamaz. Bu dengelerin kabul edilebilir olup olmadığı, uygulamanın gereksinimlerine bağlıdır.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Senaryo 3-GRS paylaşılan depolama ile yük dengeli işlem

Bu senaryo, paylaşılan depolama hesabı uygulayan ilk senaryonun üzerinde yapılan bir değişikliktir. Temel fark, depolama hesabının coğrafi çoğaltma etkin olarak oluşturulduğu bir hesaptır.
İşlevsellik, bu senaryo Senaryo 1 ile aynı avantajları sağlar, ancak ek veri kurtarma avantajları sağlar:

- Coğrafi olarak yedekli depolama (GRS) ve Okuma Erişimli GRS (RA-GRS), depolama hesabınız için kullanılabilirliği en üst düzeye çıkarır.
- Depolama hizmetinde bölgesel bir kesinti varsa, [İkincil çoğaltmaya el ile yük devretme başlatabilirsiniz](../../storage/common/storage-initiate-account-failover.md). Önemli bir olağanüstü durum nedeniyle bölgenin kaybolması durumunda Microsoft, bölgesel bir yük devretme işlemi başlatabilir. Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez.
- Yük devretme gerçekleştiğinde, dayanıklı işlevlerin durumu, genellikle birkaç dakikada bir gerçekleşen depolama hesabının en son çoğaltmasına kadar korunur.

Diğer senaryolarda olduğu gibi önemli noktalar da vardır:

- Çoğaltmaya yük devretme biraz zaman alabilir. Yük devretme tamamlanana ve Azure Storage DNS kayıtları güncelleştirildikten sonra, işlev uygulaması bir kesinti olduğunu etkilemeyecektir.
- Coğrafi olarak çoğaltılan depolama hesaplarını kullanmanın maliyeti artar.
- GRS çoğaltma, verilerinizi zaman uyumsuz olarak kopyalar. Çoğaltma işleminin gecikmesi nedeniyle en son işlemlerden bazıları kaybolmuş olabilir.

![Senaryo 3 ' ü gösteren diyagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Senaryo 1 ' de açıklandığı gibi, bu stratejiyle dağıtılan işlev uygulamalarının Dayanıklı İşlevler uzantısının **v 2.3.0** veya üstünü kullanması önemle önerilir.

Daha fazla bilgi için bkz. [Azure depolama olağanüstü durum kurtarma ve depolama hesabı yük devretme](../../storage/common/storage-disaster-recovery-guidance.md) belgeleri.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama 'da yüksek oranda kullanılabilir uygulamalar tasarlama hakkında daha fazla bilgi edinin](../../storage/common/geo-redundant-design.md)
