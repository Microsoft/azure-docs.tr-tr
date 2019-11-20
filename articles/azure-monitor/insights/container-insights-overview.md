---
title: Kapsayıcılar için Azure Izleyicisine genel bakış | Microsoft Docs
description: Bu makalede AKS kapsayıcı öngörüleri çözümünü izleyen kapsayıcılar için Azure Izleyici ve AKS kümelerinizin sistem durumunu ve Azure 'daki Container Instances izleyerek sunduğu değer açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 906ae92b0018430bdda02639642dd66ae2231dce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477053"
---
# <a name="azure-monitor-for-containers-overview"></a>Kapsayıcılar için Azure Izleyici 'ye Genel Bakış

Kapsayıcılar için Azure Izleyici, Azure Container Instances, Azure Kubernetes hizmeti (AKS) üzerinde barındırılan yönetilen Kubernetes kümelerine veya barındırılan otomatik olarak yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izlemek için tasarlanmış bir özelliktir Azure Stack. Kapsayıcılarınızın izlenmesi, özellikle de bir üretim kümesini birden çok uygulamayla birlikte çalışırken kritik öneme sahiptir.

Kapsayıcılar için Azure Izleyici, ölçüm API 'SI aracılığıyla Kubernetes 'te bulunan denetleyicilerden, düğümlerden ve kapsayıcılardan bellek ve işlemci ölçümleri toplayarak performans görünürlüğüne sahip olmanızı sağlar. Kapsayıcı günlükleri de toplanır.  Kubernetes kümelerinden izlemeyi etkinleştirdikten sonra, ölçümler ve Günlükler, Linux için Log Analytics aracısının kapsayıcılı bir sürümü aracılığıyla sizin için otomatik olarak toplanır. Ölçümler ölçüm deposuna yazılır ve günlük verileri [Log Analytics](../log-query/log-query-overview.md) çalışma alanıyla ilişkili Günlükler deposuna yazılır. 

![Kapsayıcılar için Azure Izleyici mimarisi](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Kapsayıcılar için Azure Izleyici ne sağlar?

Kapsayıcılar için Azure Izleyici, Azure Izleyici 'nin farklı özelliklerini kullanarak kapsamlı bir izleme deneyimi sunarak, Kubernetes kümenizin ve kapsayıcı iş yüklerinizin performansını ve durumunu anlamanıza olanak sağlar. Kapsayıcılar için Azure Izleyici ile şunları yapabilirsiniz:

* Düğüm üzerinde çalışan AKS kapsayıcılarını ve bunların ortalama işlemci ve bellek kullanımını belirler. Bu bilgi, kaynak performans sorunlarını belirlemenize yardımcı olabilir.
* Kapsayıcı gruplarının ve Azure Container Instances barındırılan kapsayıcılarının işlemci ve bellek kullanımını belirler.  
* Kapsayıcının bir denetleyicide veya Pod içinde nerede olduğunu belirler. Bu bilgi, denetleyicinin veya Pod 'un genel performansını görüntülemenize yardımcı olabilir. 
* Pod 'u destekleyen standart süreçlerle ilgisi olmayan konakta çalışan iş yüklerinin kaynak kullanımını gözden geçirin.
* Kümenin davranışını ortalama ve en ağır yüklerle anlayın. Bu bilgi, kapasite gereksinimlerini belirlemenize ve kümenin hangi yük için uygun olduğunu belirlemenize yardımcı olabilir. 
* Düğümleri veya kapsayıcılardaki CPU ve bellek kullanımı eşiklerinizi aştığında ya da altyapıda, düğümlerde veya iş yükü sistem durumu toplamakta olan kümede bir sistem durumu değişikliği gerçekleştiğinde sizi önceden bilgilendirmesi veya kaydetmeniz için uyarıları yapılandırın.
* Özel uyarılar, panolar oluşturmak ve ayrıntılı analiz gerçekleştirmek için [sorguları](container-insights-log-search.md) kullanarak düğümlerden ve Kubernetes tarafından toplanan uygulama ve iş yükü ölçümlerini görüntülemek Için [Prometheus](https://prometheus.io/docs/introduction/overview/) ile tümleştirin.

    >[!NOTE]
    >Prometheus desteği şu anda genel önizlemede bir özelliktir.
    >

* Şirket içi ve [aks altyapısına Azure Stack üzerinde](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) [dağıtılan](https://github.com/microsoft/OMS-docker/tree/aks-engine) kapsayıcı iş yüklerini izleyin.

AKS kümenizi kapsayıcılar için Azure Izleyici ile izleme hakkında bilgi edinmenize yardımcı olmak için, aşağıdaki videoya göz atın.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Bu özelliğe erişmek Nasıl yaparım??

Azure izleyici 'yi Azure Izleyici 'den veya doğrudan seçilen AKS kümesinden iki yolla kapsayıcılar için Azure Izleyicisine erişebilirsiniz. Azure Izleyici 'den, izlenen ve olmayan tüm kapsayıcılardan oluşan küresel bir perspektife sahipsiniz, bu, abonelikleriniz ve kaynak gruplarınız üzerinde arama ve filtreleme olanağı sağlar ve ardından Azure Izleyici 'de kapsayıcılar için Seçili kapsayıcı.  Aksi takdirde, özelliğe doğrudan, AKS sayfasından seçilen bir AKS kapsayıcısından erişebilirsiniz.  

![Kapsayıcılar için Azure Izleyicisine erişme yöntemlerine genel bakış](./media/container-insights-overview/azmon-containers-experience.png)

Yapılandırma, denetim ve kaynak kullanımını görüntülemek için AKS dışında çalışan Docker ve Windows kapsayıcı konaklarınızı izlemeye ve yönetmeye ilgileniyorsanız, bkz. [kapsayıcı izleme çözümü](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Sonraki adımlar

- AKS kümenizi izlemeye başlamak için, izlemeyi etkinleştirmek üzere gereksinimleri ve kullanılabilir yöntemleri anlamak üzere [kapsayıcılar Için Azure Izleyicisini nasıl etkinleştireceğinizi](container-insights-onboard.md) gözden geçirin. 

- Şirket içinde dağıtılan Azure Stack veya Kubernetes üzerinde AKS altyapısını izlemeye başlamak için, [kapsayıcılar Için Azure izleyici Ile karma Kubernetes kümelerini yapılandırma](container-insights-hybrid-setup.md)konusunu gözden geçirin.  
