---
title: Azure etkinlik günlüğü 'ne genel bakış
description: Azure Etkinlik günlüğünün ne olduğunu ve Azure aboneliğinizde gerçekleşen olayları anlamak için nasıl kullanabileceğinizi öğrenin.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262121"
---
# <a name="overview-of-azure-activity-log"></a>Azure etkinlik günlüğü 'ne genel bakış

**Azure etkinlik günlüğü** , Azure 'da oluşan abonelik düzeyi olaylar hakkında öngörüler sağlar. Bu, Azure Resource Manager işlemsel verilerden hizmet durumu olaylarında güncelleştirmelere kadar olan bir veri aralığı içerir. Yönetim kategorisi abonelikleriniz için denetim düzlemi olaylarını raporladığında, etkinlik günlüğü daha önce _Denetim günlükleri_ veya _işletimsel Günlükler_olarak bilinirdi. 

Aboneliğinizdeki kaynaklarda _hangi_yazma IŞLEMLERININ (put, Post _, silme_) alındığını _belirlemek için etkinlik_ günlüğünü kullanın. Ayrıca, işlemi ve ilgili diğer özellikleri durumunu anlayabilirsiniz. 

Etkinlik günlüğü, klasik/RDFE modelini kullanan kaynaklara yönelik okuma (GET) işlemlerini veya işlemlerini içermez.

## <a name="comparison-to-resource-logs"></a>Kaynak Günlükleriyle karşılaştırma
Her bir Azure aboneliği için tek bir etkinlik günlüğü vardır. Dışarıdaki ("Denetim düzlemi") bir kaynaktaki işlemler hakkında veri sağlar. [Kaynak günlükleri](resource-logs-overview.md) bir kaynak tarafından dağıtılır ve bu kaynağın ("veri düzlemi") işlemi hakkında bilgiler sağlar. Kaynak günlüklerini toplamak için her kaynak için bir tanılama ayarı oluşturmanız gerekir.

![Kaynak Günlükleriyle karşılaştırılan etkinlik günlükleri](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure etkinlik günlüğü öncelikle Azure Resource Manager oluşan etkinliklere yöneliktir. Klasik/RDFE modelini kullanan kaynakları izlemez. Bazı klasik kaynak türleri Azure Resource Manager bir proxy kaynak sağlayıcısına sahiptir (örneğin, Microsoft. ClassicCompute). Bu proxy kaynak sağlayıcılarını kullanarak Azure Resource Manager aracılığıyla klasik bir kaynak türüyle etkileşim ediyorsanız, işlemler etkinlik günlüğünde görüntülenir. Azure Resource Manager proxy 'leri dışında klasik bir kaynak türüyle etkileşime sahipseniz, eylemleriniz yalnızca Işlem günlüğüne kaydedilir. Işlem günlüğü, portalın ayrı bir bölümünde gözatılabilir.

## <a name="activity-log-retention"></a>Etkinlik günlüğü saklama
Oluşturulduktan sonra, etkinlik günlüğü girdileri sistem tarafından değiştirilmez veya silinmez. Ayrıca, bunları arabiriminden veya programlı olarak değiştiremezsiniz. Etkinlik günlüğü olayları 90 gün süreyle depolanır. Bu verileri uzun süreler boyunca depolamak için [Azure izleyici 'de toplayın](activity-log-collect.md) veya [depolama veya Event Hubs dışa aktarın](activity-log-export.md).

## <a name="view-the-activity-log"></a>Etkinlik günlüğünü görüntüleme
Tüm kaynaklar için etkinlik günlüğünü Azure portal **izleme** menüsünden görüntüleyin. Söz konusu kaynak menüsündeki **etkinlik günlüğü** seçeneğinden belirli bir kaynağın etkinlik günlüğünü görüntüleyin. Ayrıca, PowerShell, CLı veya REST API etkinlik günlüğü kayıtlarını da alabilirsiniz.  Bkz. [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](activity-log-view.md).

![Etkinlik günlüğünü görüntüle](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Azure Izleyici 'de etkinlik günlüğü toplama
Etkinlik günlüğünü diğer izleme verileriyle analiz etmek ve verileri 90 günden uzun süre bekletmek üzere Azure Izleyici 'de bir Log Analytics çalışma alanında toplayın. Bkz. Azure [izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplayın ve çözümleyin](activity-log-collect.md).

![Sorgu etkinlik günlüğü](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Etkinlik Günlüğünü Dışarı Aktar
Arşivleme veya bir üçüncü taraf hizmeti veya özel analiz çözümünün alımı için bir olay hub 'ına akışa almak üzere etkinlik günlüğünü Azure depolama 'ya aktarın. Bkz. [Azure etkinlik günlüğünü dışarı aktarma](activity-log-export.md). Ayrıca, [**Power BI içerik paketini**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)kullanarak Power BI etkinlik günlüğü olaylarını analiz edebilirsiniz.

## <a name="alert-on-activity-log"></a>Etkinlik günlüğünde uyar
Etkinlik günlüğünde [etkinlik günlüğü uyarısıyla](activity-log-alerts.md)belirli olaylar oluşturulduğunda bir uyarı oluşturabilirsiniz. Etkinlik günlüğleriniz bir Log Analytics çalışma alanına bağlıyken [günlük sorgusu](alerts-log-query.md) kullanarak da bir uyarı oluşturabilirsiniz, ancak sorgu uyarılarını günlüğe kaydetmek için bir maliyet vardır. Etkinlik günlüğü uyarıları için bir maliyet yoktur.

## <a name="categories-in-the-activity-log"></a>Etkinlik günlüğündeki Kategoriler
Etkinlik günlüğündeki her olayın, aşağıdaki tabloda açıklanan belirli bir kategorisi vardır. Bu kategorilerin şemaların hakkında tam Ayrıntılar için bkz: [Azure etkinlik günlüğü olay şeması](activity-log-schema.md). 

| Category | Açıklama |
|:---|:---|
| Yönetim | Kaynak Yöneticisi aracılığıyla gerçekleştirilen tüm oluşturma, güncelleştirme, silme ve eylem işlemlerinin kaydını içerir. Yönetim olayları örnekleri arasında _sanal makine oluşturma_ ve _ağ güvenlik grubu silme_sayılabilir.<br><br>Kaynak Yöneticisi kullanarak bir kullanıcı veya uygulama tarafından gerçekleştirilen her eylem, belirli bir kaynak türündeki işlem olarak modellenir. İşlem türü _yazma_, _silme_veya _eylem_ise, bu işlemin hem başlangıç hem de başarı veya başarısızlık kayıtları yönetim kategorisine kaydedilir. Yönetim olayları, bir abonelikte rol tabanlı erişim denetimine yapılan tüm değişiklikleri de içerir. |
| Hizmet Durumu | Azure 'da oluşan hizmet durumu olaylarının kaydını içerir. Doğu ABD bir hizmet sistem durumu olay SQL Azure bir örnek _kapalı kalma süresi yaşıyor_. <br><br>Hizmet durumu olayları altı değişen elikler halinde gelir: _Eylem gerekli_, _yardımlı kurtarma_, _olay_, _bakım_, _bilgi_veya _güvenlik_. Bu olaylar yalnızca abonelikte olaydan etkilenecek bir kaynağınız varsa oluşturulur.
| Kaynak Durumu | Azure kaynaklarınızda oluşan herhangi bir kaynak sistem durumu olayının kaydını içerir. Kaynak Durumu olayına bir örnek, _sanal makine sistem durumu kullanılamaz olarak değişir_.<br><br>Kaynak Durumu olaylar, dört sistem durumu durumlarını temsil edebilir: _Kullanılabilir_, _kullanılamaz_, _düşürülmüş_ve _bilinmiyor_. Ayrıca, Kaynak Durumu olaylar _platform tarafından başlatılmış_ veya _Kullanıcı tarafından başlatılmış_olarak kategorize edilebilir. |
| Uyarı | Azure uyarıları için etkinleştirme kaydını içerir. _Son 5 dakika boyunca, myVM 'deki% CPU 'su% 80_üzerinde bir uyarı olayına bir örnektir.|
| Otomatik Ölçeklendirme | Aboneliğinizde tanımladığınız otomatik ölçeklendirme ayarlarına bağlı olarak, otomatik ölçeklendirme altyapısının işlemiyle ilgili olayların kaydını içerir. Otomatik ölçeklendirme olayına bir örnek, _Otomatik ölçeklendirme ölçeği artırma eylemi başarısız oldu_. |
| Öneri | Azure Advisor 'ın öneri olaylarını içerir. |
| Güvenlik | Azure Güvenlik Merkezi tarafından oluşturulan uyarıların kaydını içerir. Bir güvenlik olayına örnek olarak, _şüpheli çift uzantı dosyası yürütülür_. |
| İlke | Azure Ilkesi tarafından gerçekleştirilen tüm etki eylemi işlemlerinin kayıtlarını içerir. Ilke olayları örnekleri _Denetim_ ve _reddetme_içerir. Ilke tarafından gerçekleştirilen her eylem, bir kaynaktaki işlem olarak modellenir. |


## <a name="next-steps"></a>Sonraki Adımlar

* [Azure etkinlik günlüğünü dışarı aktarmak için bir günlük profili oluşturma](activity-log-export.md)
* [Azure etkinlik günlüğünü Event Hubs akışa almak](activity-logs-stream-event-hubs.md)
* [Azure etkinlik günlüğünü depolama alanına Arşivle](archive-activity-log.md)

