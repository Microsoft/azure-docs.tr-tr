---
title: Azure geçişi bulma ve değerlendirmede bağımlılık Analizi
description: Azure geçişi bulma ve değerlendirmesi kullanılarak değerlendirme için bağımlılık analizinin nasıl kullanılacağını açıklar.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778398"
---
# <a name="dependency-analysis"></a>Bağımlılık Analizi

Bu makalede Azure geçişi: bulma ve değerlendirme 'da bağımlılık Analizi açıklanmaktadır.

Bağımlılık Analizi keşfedilen şirket içi sunucular arasındaki bağımlılıkları tanımlar. Bu avantajları sağlar:

- Daha iyi güvenle, değerlendirme için sunucuları daha doğru bir şekilde gruplar halinde toplayabilirsiniz.
- Birlikte geçirilmesi gereken sunucuları belirleyebilirsiniz. Bu, özellikle hangi sunucuların Azure 'a geçirmek istediğiniz bir uygulama dağıtımının parçası olduğundan emin olmadığınız durumlarda faydalıdır.
- Sunucuların kullanımda olup olmadığını ve geçirilmesi yerine hangi sunucuların kullanımdan hangilerinin kullanılabilir olduğunu belirleyebilirsiniz.
- Bağımlılıkların çözümlenmesi, hiçbir şeyin geri ayrılmadığından ve geçişten sonra beklenmedik kesintilerden kaçınmasının sağlanmasına yardımcı olur.
- Bağımlılık analizi hakkında sık sorulan soruları [gözden geçirin](common-questions-discovery-assessment.md#what-is-dependency-visualization) .

## <a name="analysis-types"></a>Analiz türleri

Bağımlılık analizini dağıtmaya yönelik iki seçenek vardır

**Seçenek** | **Ayrıntılar** | **Genel bulut** | **Azure Devlet Kurumları**
----  |---- | ----
**Aracısız** | VSphere API 'Lerini kullanarak VMware 'deki sunuculardaki verileri yoklar.<br/><br/> Aracıları sunuculara yüklemeniz gerekmez.<br/><br/> Bu seçenek şu anda yalnızca VMware 'deki sunucular için önizleme aşamasındadır. | Destekleniyor. | Destekleniyor.
**Aracı tabanlı analiz** | , Bağımlılık görselleştirmesini ve analizini etkinleştirmek için Azure Izleyici 'de [hizmet eşlemesi çözümünü](../azure-monitor/vm/service-map.md) kullanır.<br/><br/> Çözümlemek istediğiniz her şirket içi sunucuya aracı yüklemeniz gerekir. | Desteklenir | Desteklenmez.

## <a name="agentless-analysis"></a>Aracısız analiz

Aracısız bağımlılık analizi, etkin olduğu sunuculardan gelen TCP bağlantı verilerini yakalayarak işe yarar. Sunuculara yüklü aracı yok. Aynı kaynak sunucu ve işlemle bağlantı, hedef sunucu, işlem ve bağlantı noktası, mantıksal olarak bir bağımlılık halinde gruplandırılır. Yakalanan bağımlılık verilerini bir harita görünümünde görselleştirebilir veya bir CSV olarak dışarı aktarabilirsiniz. Çözümlemek istediğiniz sunuculara yüklü aracı yok.

### <a name="dependency-data"></a>Bağımlılık verileri

Bağımlılık verilerinin bulunması başladıktan sonra yoklama başlar:

- Azure geçişi gereci, verileri toplamak için her beş dakikada bir sunuculardan gelen TCP bağlantı verilerini yoklar.
- Veriler, vSphere API 'Leri kullanılarak vCenter Server aracılığıyla konuk sunuculardan toplanır.
- Yoklama bu verileri toplar:

    - Etkin bağlantıları olan işlemlerin adı.
    - Etkin bağlantıları olan işlemlerin çalıştırıldığı uygulamanın adı.
    - Etkin bağlantılardaki hedef bağlantı noktası.

- Toplanan veriler, Azure geçişi gereci üzerinde işlenir ve kimlik bilgilerini, her altı saatte bir Azure geçişi 'ne gönderilir


## <a name="agent-based-analysis"></a>Aracı tabanlı analiz

Aracı tabanlı analizler için Azure geçişi: bulma ve değerlendirme Azure Izleyici 'de [hizmet eşlemesi](../azure-monitor/vm/service-map.md) çözümünü kullanır. Analiz etmek istediğiniz her sunucuya [Microsoft Monitoring Agent/Log Analytics aracısını](../azure-monitor/agents/agents-overview.md#log-analytics-agent) ve [bağımlılık aracısını](../azure-monitor/agents/agents-overview.md#dependency-agent)yüklersiniz.

### <a name="dependency-data"></a>Bağımlılık verileri

Aracı tabanlı analiz bu verileri sağlar:

- Kaynak sunucu adı, işlem, uygulama adı.
- Hedef sunucu adı, işlem, uygulama adı ve bağlantı noktası.
- Bağlantı sayısı, gecikme süresi ve veri aktarımı bilgilerinin toplanması ve Log Analytics sorguları için kullanılabilir olması.

## <a name="compare-agentless-and-agent-based"></a>Aracısız ve aracı tabanlı karşılaştırması karşılaştırın

Aracısız görselleştirme ve aracı tabanlı görselleştirme arasındaki farklılıklar tabloda özetlenmiştir.

**Gereksinim** | **Aracısız** | **Aracı tabanlı**
--- | --- | ---
**Destek** | Yalnızca VMware 'deki sunucular için önizleme aşamasında. Desteklenen işletim sistemlerini [gözden geçirin](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) . | Genel kullanılabilirlik (GA).
**Aracısı** | Çözümlemek istediğiniz sunucularda aracı gerekmez. | Çözümlemek istediğiniz her şirket içi sunucuda aracı gereklidir.
**Log Analytics** | Gerekli değildir. | Azure geçişi, bağımlılık analizi için [Azure izleyici günlüklerinde](../azure-monitor/logs/log-query-overview.md) [hizmet eşlemesi](../azure-monitor/vm/service-map.md) çözümünü kullanır.<br/><br/> Bir Log Analytics çalışma alanını projeyle ilişkilendirirsiniz. Çalışma alanı Doğu ABD, Güneydoğu Asya veya Batı Avrupa bölgelerinde bulunmalıdır. Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.
**İşleme** | TCP bağlantı verilerini yakalar. Bulmadan sonra, verileri beş dakikalık aralıklarla toplar. | Bir sunucuya yüklenen Hizmet Eşlemesi aracıları, her bir işlem için TCP işlemleri ve gelen/giden bağlantılar hakkında veri toplar.
**Veriler** | Kaynak sunucu adı, işlem, uygulama adı.<br/><br/> Hedef sunucu adı, işlem, uygulama adı ve bağlantı noktası. | Kaynak sunucu adı, işlem, uygulama adı.<br/><br/> Hedef sunucu adı, işlem, uygulama adı ve bağlantı noktası.<br/><br/> Bağlantı sayısı, gecikme süresi ve veri aktarımı bilgilerinin toplanması ve Log Analytics sorguları için kullanılabilir olması. 
**Görselleştirme** | Tek bir sunucunun bağımlılık eşlemesi, bir saat ile 30 güne kadar bir süre içinde görüntülenebilir. | Tek bir sunucunun bağımlılık eşlemesi.<br/><br/> Bir sunucu grubunun bağımlılık eşlemesi.<br/><br/>  Eşleme, yalnızca bir saat boyunca görüntülenebilir.<br/><br/> Harita görünümünden bir gruptaki sunucuları ekleyin ve kaldırın.
Veri dışarı aktarma | Son 30 günlük veri, CSV biçiminde indirilebilir. | Veriler, Log Analytics ile sorgulanabilir.



## <a name="next-steps"></a>Sonraki adımlar

- Aracı tabanlı bağımlılık görselleştirmesini [ayarlayın](how-to-create-group-machine-dependencies.md) .
- VMware 'deki sunucular için aracısız bağımlılık görselleştirmesini [deneyin](how-to-create-group-machine-dependencies-agentless.md) .
- Bağımlılık görselleştirmesine ilişkin [genel soruları](common-questions-discovery-assessment.md#what-is-dependency-visualization) gözden geçirin.
