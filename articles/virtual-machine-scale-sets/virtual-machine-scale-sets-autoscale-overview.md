---
title: Azure sanal makine ölçek kümeleriyle otomatik ölçeklendirmeye genel bakış
description: Bir Azure sanal makine ölçek kümesini performansa veya sabit zamanlamaya göre otomatik olarak ölçeklendirebileceğinizi gösteren farklı yollar hakkında bilgi edinin
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591402"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleriyle otomatik ölçeklendirmeye genel bakış
Bir Azure sanal makine ölçek kümesi, uygulamanızı çalıştıran VM örneği sayısını otomatik olarak artırabilir veya azaltabilir. Bu otomatik ve esnek davranış, uygulamanızın performansını izlemek ve iyileştirmek için yönetim ek yükünü azaltır. Olumlu bir müşteri deneyimi için kabul edilebilir performansı tanımlayan kurallar oluşturun. Bu tanımlı eşikler karşılandığında, otomatik ölçeklendirme kuralları, ölçek ayarlarınızın kapasitesini ayarlamak için işlem alır. Ayrıca, ölçek kümesi kapasitesini sabit saatlerde otomatik olarak artırmak veya azaltmak için de olayları zamanlayabilirsiniz. Bu makale, hangi performans ölçümlerinin kullanılabilir olduğunu ve hangi eylemlerin otomatik ölçeklendirmeyi gerçekleştirebileceklerini bir genel bakış sağlar.


## <a name="benefits-of-autoscale"></a>Otomatik ölçeklendirmeyi avantajları
Uygulamanızın talebi artarsa, ölçek kümenizdeki sanal makine örneklerinde üzerindeki yük de artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz.

> [!NOTE]
> Ölçek kümesi için otomatik örnek onarımları kullanırken, ölçek kümesindeki en fazla örnek sayısı 200 olabilir. [Otomatik örnek onarımları](./virtual-machine-scale-sets-automatic-instance-repairs.md)hakkında daha fazla bilgi edinin.

Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. CPU veya bellek gibi hangi ölçümlerin izleneceğini, uygulama yükünün belirli bir eşiği ne kadar süre karşılaması gerektiğini ve ölçek kümesine kaç tane sanal makine örneği ekleneceğini denetlersiniz.

Bir akşam veya hafta sonu uygulama talebiniz azalabilir. Yük belirli bir süreye yayılarak tutarlı şekilde azalıyorsa, ölçek kümesindeki sanal makine örneği sayısını azaltmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Mevcut talebi karşılamak için gerekli örnek sayısını yalnızca siz çalıştırdığınızdan, bu ölçeği daraltma eylemi ölçek kümenizi çalıştırma maliyetini azaltır.


## <a name="use-host-based-metrics"></a>Ana bilgisayar tabanlı ölçümleri kullanma
VM örneklerinizin kullanabildiği yerleşik ana bilgisayar ölçümlerinin bulunduğu otomatik ölçeklendirme kuralları oluşturabilirsiniz. Konak ölçümleri, ek aracılar ve veri koleksiyonları yüklemeye veya yapılandırmaya gerek kalmadan, bir ölçek kümesindeki sanal makine örneklerinin performansına ilişkin görünürlük sağlar. Bu ölçümleri kullanan otomatik ölçeklendirme kuralları, CPU kullanımına, bellek talebine veya disk erişimine yanıt olarak sanal makine örneklerinin sayısını veya ölçeğini değiştirebilir.

Konak tabanlı ölçümleri kullanan otomatik ölçeklendirme kuralları aşağıdaki araçlarla oluşturulabilir:

- [Azure portalındaki](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure şablonu](tutorial-autoscale-template.md)

Daha ayrıntılı performans ölçümleri kullanan otomatik ölçeklendirme kuralları oluşturmak için, sanal makine örneklerine [Azure tanılama uzantısını yükleyip yapılandırabilir](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) ya da [uygulamanızı Application Insights 'ı kullanacak şekilde yapılandırabilirsiniz](#application-level-metrics-with-app-insights).

Ana bilgisayar tabanlı ölçümler kullanan otomatik ölçeklendirme kuralları, Azure tanılama uzantısıyla Konuk içi VM ölçümleri ve App Insights aşağıdaki yapılandırma ayarlarını kullanabilir.

### <a name="metric-sources"></a>Ölçüm kaynakları
Otomatik ölçeklendirme kuralları aşağıdaki kaynaklardan birindeki ölçümleri kullanabilir:

| Ölçüm kaynağı        | Kullanım örneği                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Geçerli ölçek kümesi    | Ek aracıların yüklenmesini veya yapılandırılmasını gerektirmeyen ana bilgisayar tabanlı ölçümler için.                                  |
| Depolama hesabı      | Azure tanılama uzantısı, Azure depolama 'ya daha sonra otomatik ölçeklendirme kuralları tetiklemek için tüketilen performans ölçümlerini yazar. |
| Service Bus Kuyruğu    | Uygulamanız veya diğer bileşenleriniz, kuralları tetiklemek için Azure Service Bus kuyruğu üzerinde ileti aktarabilir.                   |
| Application Insights | Uygulamanıza, ölçümleri doğrudan uygulamadan akışı yapan bir izleme paketi.                         |


### <a name="autoscale-rule-criteria"></a>Otomatik ölçeklendirme kuralı ölçütleri
Aşağıdaki ana bilgisayar tabanlı ölçümler, otomatik ölçeklendirme kuralları oluştururken kullanılabilir. Azure tanılama uzantısı 'nı veya uygulama öngörülerini kullanıyorsanız, otomatik ölçeklendirme kurallarıyla hangi ölçümlerin izleneceğini ve kullanılacağını tanımlarsınız.

| Ölçüm adı               |
|---------------------------|
| CPU yüzdesi            |
| Ağ Girişi                |
| Ağ Çıkışı               |
| Disk okuma bayt sayısı           |
| Disk yazma baytları          |
| Disk okuma Işlemi/sn  |
| Disk yazma Işlemi/sn |
| Kalan CPU kredileri     |
| Tüketilen CPU kredileri      |

Belirli bir ölçümü izlemek için otomatik ölçeklendirme kuralları oluşturduğunuzda, kurallar aşağıdaki ölçüm toplama eylemlerinden birine bakar:

| Toplama türü |
|------------------|
| Ortalama          |
| Minimum          |
| Maksimum          |
| Toplam            |
| Son             |
| Count            |

Otomatik ölçeklendirme kuralları, ölçümler aşağıdaki işleçlerden biriyle tanımlanan eşiğe göre karşılaştırıldığında tetiklenir:

| İşleç                 |
|--------------------------|
| Büyüktür             |
| Büyük veya eşittir |
| Küçüktür                |
| Küçük veya eşittir    |
| Eşittir                 |
| Eşit değildir             |


### <a name="actions-when-rules-trigger"></a>Kuralların tetiklendiği eylemler
Bir otomatik ölçeklendirme kuralı tetiklendiğinde, ölçek kümenizde aşağıdaki yollarla otomatik olarak ölçek ayarlanabilir:

| Ölçeklendirme işlemi     | Kullanım örneği                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Sayıyı şu kadar artır   | Oluşturulacak sabit sayıda sanal makine örneği. Daha az sayıda VM 'ye sahip ölçek kümelerinde faydalıdır.                                           |
| Yüzdeyi yüzde artır | VM örneklerinin yüzde tabanlı artışı. Sabit bir artışın önemli ölçüde performansı artırabileceği büyük ölçekli kümeler için iyi bir seçimdir. |
| Sayıyı şu kadar artır   | İstenen maksimum miktara ulaşmak için çok sayıda sanal makine örneği gereklidir.                                                            |
| Sayıyı şu kadar azalt   | Kaldırılacak sanal makine örneği sayısı düzeltildi. Daha az sayıda VM 'ye sahip ölçek kümelerinde faydalıdır.                                           |
| Yüzdeyi azalt | VM örneklerinin yüzde tabanlı bir azalışını. Sabit bir artışın kaynak tüketimini ve maliyetlerini önemli ölçüde azaltabileceği büyük ölçekli kümeler için iyi bir seçimdir. |
| Sayıyı şu kadar azalt   | İstenen minimum tutara ulaşmak için çok sayıda sanal makine örneğinin gerekli olduğundan kaldırın.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Azure tanılama Uzantısı ile Konuk içi VM ölçümleri
Azure tanılama uzantısı, bir VM örneği içinde çalışan bir aracıdır. Aracı, performans ölçümlerini izler ve Azure depolama 'ya kaydeder. Bu performans ölçümleri, sanal makinenin durumu hakkında daha ayrıntılı bilgiler içerir (örneğin, diskler için *AverageReadTime* veya CPU için *PercentIdleTime* ). Yalnızca CPU kullanımının veya bellek tüketiminin yüzdesine göre değil, VM performansının daha ayrıntılı bir tanıma göre otomatik ölçeklendirme kuralları oluşturabilirsiniz.

Azure tanılama uzantısını kullanmak için, sanal makine örnekleriniz için Azure depolama hesapları oluşturmanız, Azure tanılama aracısını kurmanız ve ardından VM 'Leri belirli performans sayaçlarını depolama hesabına akışa almak üzere yapılandırmanız gerekir.

Daha fazla bilgi için Azure tanılama uzantısını [Linux VM](../virtual-machines/extensions/diagnostics-linux.md) veya [Windows VM](../virtual-machines/extensions/diagnostics-windows.md) için etkinleştirme makalelerini inceleyebilirsiniz.


## <a name="application-level-metrics-with-app-insights"></a>Uygulama öngörüleri ile uygulama düzeyi ölçümleri
Uygulamalarınızın performansına ilişkin daha fazla görünürlük elde etmek için Application Insights kullanabilirsiniz. Uygulamanıza uygulamayı izleyen ve Azure 'a telemetri gönderen küçük bir izleme paketi yüklersiniz. Uygulamanızın yanıt süreleri, sayfa yükü performansı ve oturum sayıları gibi ölçümleri izleyebilirsiniz. Bu uygulama ölçümleri, müşteri deneyimini etkileyebilecek eyleme dönüştürülebilir içgörüler temelinde kuralları tetikleyeceğinden, parçalı ve katıştırılmış düzeyde otomatik ölçeklendirme kuralları oluşturmak için kullanılabilir.

App Insights hakkında daha fazla bilgi için bkz. [Application Insights nedir?](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Zamanlanan otomatik ölçeklendirme
Ayrıca, zamanlamaları temel alan otomatik ölçeklendirme kuralları da oluşturabilirsiniz. Bu zamanlama tabanlı kurallar, sanal makine örneklerinin sayısını sabit saatlerde otomatik olarak ölçeklendirmenize olanak tanır. Performans tabanlı kurallarla, otomatik ölçeklendirme kuralları tetiklenecek ve yeni VM örnekleri sağlanmadan önce uygulama üzerinde bir performans etkisi olabilir. Bu talebi tahmin edebilirsiniz, ek VM örnekleri sağlanır ve ek müşteri kullanımı ve uygulama talebi için hazır hale gelir.

Aşağıdaki örnekler, zamanlama tabanlı otomatik ölçeklendirme kurallarının kullanımını avantajı olabilecek senaryolardır:

- Müşteri talebi arttıkça iş gününün başlangıcında sanal makine örneklerinin sayısını otomatik olarak ölçeklendirin. İş gününün sonunda, uygulama kullanımı düşük olduğunda kaynak maliyetlerini en aza indirmek için sanal makine örneklerinin sayısında otomatik olarak ölçeklendirin.
- Bir departman, ayın veya mali döngüsünün belirli bölümlerinde büyük ölçüde bir uygulama kullanıyorsa, sanal makine örneklerinin sayısını ek taleplerine uyacak şekilde otomatik olarak ölçeklendirin.
- Bir pazarlama olayı, promosyon veya tatil satışı olduğunda, beklenen müşteri talebinin önüne sanal makine örneklerinin sayısını otomatik olarak ölçeklendirebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki araçlardan biriyle ana bilgisayar tabanlı ölçümler kullanan otomatik ölçeklendirme kuralları oluşturabilirsiniz:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure şablonu](tutorial-autoscale-template.md)

Bu genel bakış, ölçek kümesindeki sanal makine örneklerinin *sayısını* yatay olarak ölçeklendirmek ve azaltmak için otomatik ölçeklendirme kurallarını kullanma hakkında ayrıntılı bilgi sağlar. Ayrıca, sanal makine örnek *boyutunu* artırmak veya azaltmak için dikey olarak ölçeklendirebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Ile dikey otomatik ölçeklendirme](virtual-machine-scale-sets-vertical-scale-reprovision.md).

VM örneklerinizi yönetme hakkında daha fazla bilgi için bkz. [Azure PowerShell ile sanal makine ölçek kümelerini yönetme](./virtual-machine-scale-sets-manage-powershell.md).

Otomatik ölçeklendirme kurallarınızın tetiklenmesi durumunda uyarı oluşturma hakkında bilgi edinmek için bkz. [Azure izleyici 'de e-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemlerini kullanma](../azure-monitor/autoscale/autoscale-webhook-email.md). [Azure izleyici 'de e-posta ve Web kancası uyarı bildirimleri göndermek için Denetim günlüklerini de kullanabilirsiniz](../azure-monitor/alerts/alerts-log-webhook.md).
