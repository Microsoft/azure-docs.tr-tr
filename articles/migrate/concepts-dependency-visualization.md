---
title: Azure Geçişi'nde bağımlılık görselleştirme
description: Azure geçişi 'nde sunucu değerlendirmesi hizmeti 'nde değerlendirme hesaplamalarına genel bakış sağlar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 17ba06d6ac09f220b4343092292275a1cc315377
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489202"
---
# <a name="dependency-visualization"></a>Bağımlılık görselleştirme

Bu makalede, Azure geçişi: Sunucu değerlendirmesi ' nde bağımlılık görselleştirme özelliği açıklanır.

Bağımlılık görselleştirme, değerlendirmek ve geçirmek istediğiniz makineler arasındaki bağımlılıkları anlamanıza yardımcı olur. Daha yüksek düzeyde güvenle makineler değerlendirmek istediğinizde genellikle bağımlılık eşlemesini kullanırsınız.

- Azure geçişi: Sunucu değerlendirmesi ' nde, makineleri değerlendirme için gruplar halinde toplayın. Gruplar genellikle birlikte geçirmek istediğiniz makinelerden oluşur ve bağımlılık görselleştirmeleri, makineleri doğru bir şekilde gruplandırabilmeniz için makine bağımlılıklarını çapraz denetlemenize yardımcı olur.
- Görselleştirmeyi kullanarak, birlikte geçirilmesi gereken bağımlı sistemleri bulabilirsiniz. Çalışan sistemlerin hala kullanımda olup olmadığını veya geçiş yerine sistemlerin kullanımdan oluşturulup alınmayacağını belirleyebilirsiniz.
- Bağımlılıkların görselleştirilmesi, hiçbir şeyin geri ayrılmaması ve geçiş sırasında beklenmedik kesintilerden kaçınılması sağlar.
- Bu özellik özellikle uygulamaların parçası olan makineleri tamamen bilmiyorsanız yararlıdır ve bu nedenle Azure 'a birlikte geçirilmesi gerekir.


> [!NOTE]
> Bağımlılık görselleştirme işlevselliği Azure Kamu 'da kullanılamaz.

## <a name="agent-based-and-agentless"></a>Aracı tabanlı ve aracısız

Bağımlılık görselleştirmesini dağıtmaya yönelik iki seçenek vardır:

- **Aracısız bağımlılık görselleştirmesi**: Bu seçenek şu anda önizleme aşamasındadır. Makinelere herhangi bir aracı yüklemenizi gerektirmez. 
    - Etkin olduğu makinelerden gelen TCP bağlantısı verilerini yakalayıp işe yarar. [Daha fazla bilgi edinin](how-to-create-group-machine-dependencies-agentless.md).
Bağımlılık keşfi başlatıldıktan sonra, Gereç, beş dakikalık yoklama aralığındaki makinelerden veri toplar.
    - Aşağıdaki veriler toplanır:
        - TCP bağlantıları
        - Etkin bağlantıları olan işlemlerin adları
        - Yukarıdaki işlemlerin çalıştırıldığı yüklü uygulamaların adları
        - Hayır. her yoklama aralığında algılanan bağlantıların sayısı
- **Aracı tabanlı bağımlılık görselleştirmesi**: aracı tabanlı bağımlılık görselleştirmesini kullanmak için, çözümlemek istediğiniz her şirket içi makineye aşağıdaki aracıları indirmeniz ve yüklemeniz gerekir.  
    - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) her makineye yüklenmelidir. MMA aracısının nasıl yükleneceği hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) .
    - [Bağımlılık aracısının](../azure-monitor/platform/agents-overview.md#dependency-agent) her makinede yüklü olması gerekir. Bağımlılık aracısının nasıl yükleneceği hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) .
    - Ayrıca, İnternet bağlantısı olmayan makineleriniz varsa, bu makinelere Log Analytics ağ geçidini indirip yüklemeniz gerekir.

## <a name="agent-based-requirements"></a>Aracı tabanlı gereksinimler

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Bağımlılık görselleştirmesini dağıtmaya ne yapmam gerekir?

Bağımlılık görselleştirmesini dağıtmadan önce, Azure geçişi: Sunucu değerlendirmesi aracı projeye eklenmiş bir Azure geçişi projesine sahip olmanız gerekir. Şirket içi makinelerinizi bulmaya yönelik bir Azure geçiş gereci ayarladıktan sonra bağımlılık görselleştirmesini dağıtırsınız.

Aracı ekleme ve [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)veya fiziksel sunucular için bir gereç dağıtma hakkında [daha fazla bilgi edinin](how-to-assess.md) .


### <a name="how-does-it-work"></a>Nasıl çalışır?

Azure geçişi, bağımlılık görselleştirmesi için [Azure izleyici günlüklerinde](../log-analytics/log-analytics-overview.md) [hizmet eşlemesi](../operations-management-suite/operations-management-suite-service-map.md) çözümünü kullanır.

- Bağımlılık görselleştirmesini yükseltmek için, bir Log Analytics çalışma alanını (yeni veya var olan) Azure geçişi projesiyle ilişkilendirmeniz gerekir.
- Çalışma alanı, Azure geçişi projesini oluşturduğunuz abonelikle aynı abonelikte olmalıdır.
- Azure geçişi Doğu ABD, Güneydoğu Asya ve Batı Avrupa bölgelerinde bulunan çalışma alanlarını destekler. Diğer bölgelerdeki çalışma alanları bir projeyle ilişkilendirilemez. Ayrıca, çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)bir bölgede olması gerektiğini unutmayın.
- Bir Azure geçişi projesi çalışma alanı eklendikten sonra değiştirilemez.
- Log Analytics, Azure geçişi ile ilişkili çalışma alanı, geçiş projesi anahtarıyla ve proje adıyla etiketlenir.

    ![Log Analytics çalışma alanında gezin](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Bunun için ödeme yapmam gerekiyor mu?

Bağımlılık görselleştirmesi Hizmet Eşlemesi ve ilişkili bir Log Analytics çalışma alanı gerektirir. 

- Hizmet Eşlemesi çözümü ilk 180 gün boyunca ücret ödemez. Bu, Log Analytics çalışma alanını Azure geçişi projesiyle ilişkilendirdiğiniz günden itibaren yapılır.
- 180 gün sonra standart Log Analytics ücretleri uygulanır.
- İlişkili Log Analytics çalışma alanında Hizmet Eşlemesi dışında herhangi bir çözümün kullanılması [standart Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) ücretlendirmeye tabi olacaktır.
- Azure geçişi projesi silindiğinde, çalışma alanı onunla birlikte silinmez. Projeyi sildikten sonra Hizmet Eşlemesi kullanımı ücretsizdir ve her düğüm, Log Analytics çalışma alanının ücretli katmanına göre ücretlendirilecektir.

Azure Geçişi fiyatlandırması hakkında daha fazla bilgiyi [burada](https://azure.microsoft.com/pricing/details/azure-migrate/) bulabilirsiniz.

> [!NOTE]
> Azure 'un genel kullanıma sunulmadan önce oluşturduğunuz projeleriniz 28 Şubat 2018 ' de varsa, ek Hizmet Eşlemesi ücretleri tahakkuk etmeyebilirsiniz. Yalnızca 180 günden sonra ödediğinizden emin olmak için, genel kullanılabilirliğe ait mevcut çalışma alanları ücrete tabi olmaya devam ettiğinden yeni bir proje oluşturmanızı öneririz.



### <a name="how-do-i-manage-the-workspace"></a>Çalışma alanını yönetmek Nasıl yaparım? mı?

- Aracıları çalışma alanına kaydettiğinizde, Azure geçişi projesi tarafından sunulan KIMLIĞI ve anahtarı kullanırsınız.
- Log Analytics çalışma alanını Azure geçişi dışında kullanabilirsiniz.
- İlişkili Azure geçişi projesini silerseniz, çalışma alanı otomatik olarak silinmez. [El ile silmeniz](../azure-monitor/platform/manage-access.md)gerekir.
- Azure geçişi projesini silmediğiniz takdirde Azure geçişi tarafından oluşturulan çalışma alanını silmeyin. Bunu yaparsanız, bağımlılık görselleştirme işlevselliği beklendiği gibi çalışmaz.

## <a name="next-steps"></a>Sonraki adımlar
- [Makine bağımlılıklarını kullanan makineleri gruplandırın](how-to-create-group-machine-dependencies.md)
- Bağımlılık görselleştirmesinde SSS hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) .


