---
title: Azure Izleyici uyarıları için eylem kuralları
description: Azure Izleyici 'deki eylem kurallarının ne olduğunu ve bunların nasıl yapılandırılacağını ve yönetileceğini anlamak.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 04/25/2019
ms.openlocfilehash: a858388a11cfdf36bacb1e5840f00fc6ef097867
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555711"
---
# <a name="action-rules-preview"></a>Eylem kuralları (Önizleme)

Eylem kuralları herhangi bir Azure Resource Manager kapsamında (Azure aboneliği, kaynak grubu veya hedef kaynak) eylemleri tanımlamanıza veya gösterkullanmanıza yardımcı olur. Üzerinde işlem yapmak istediğiniz uyarı örneklerinin belirli bir alt kümesini daraltmanıza yardımcı olan çeşitli filtreleri vardır.

## <a name="why-and-when-should-you-use-action-rules"></a>Eylem kurallarını neden ve ne zaman kullanmalısınız?

### <a name="suppression-of-alerts"></a>Uyarıların gizlemesi

Uyarıların oluşturduğu bildirimleri gizlemek için yararlı olduğu birçok senaryo vardır. Bu senaryolar, planlanan bir bakım penceresi sırasında, iş dışı saatlerde gizlemenin gizlenme sürecinde değişir. Örneğin, **contosovm** 'den sorumlu olan ekip, **contosovm** planlanmış bakımda olduğundan yaklaşan hafta sonu için uyarı bildirimlerini bastırmak istiyor. 

Ekip, **Contosovm** 'de yapılandırılmış olan her bir uyarı kuralını el ile devre dışı bırakabilse de (bakım sonrasında tekrar etkinleştirirseniz) basit bir işlem değildir. Eylem kuralları, göstermeme süresini esnek bir şekilde yapılandırma özelliği ile ölçeklendirerek uyarı gizleme tanımlamanıza yardımcı olur. Önceki örnekte, takım, **Contosovm** 'de, hafta sonu için tüm uyarı bildirimlerini gösteren bir eylem kuralı tanımlayabilir.


### <a name="actions-at-scale"></a>Ölçekteki eylemler

Uyarı kuralları, uyarı oluşturulduğunda tetiklenen eylem grubunu tanımlamanıza yardımcı olmakla birlikte, müşteriler genellikle işlem kapsamları genelinde ortak bir eylem grubuna sahiptir. Örneğin, **ContosoRG** kaynak grubundan sorumlu bir ekip, büyük olasılıkla **ContosoRG**içinde tanımlanan tüm uyarı kuralları için aynı eylem grubunu tanımlayacaktır. 

Eylem kuralları bu işlemi basitleştirmeye yardımcı olur. Eylemleri ölçekli olarak tanımlayarak, yapılandırılmış kapsamda oluşturulan herhangi bir uyarı için bir eylem grubu tetiklenebilir. Önceki örnekte, takım, içinde oluşturulan tüm uyarılar için aynı eylem grubunu tetikleyecek **ContosoRG** üzerinde bir eylem kuralı tanımlayabilir.

> [!NOTE]
> Eylem kuralları şu anda Azure hizmet durumu uyarıları için geçerli değildir.

## <a name="configuring-an-action-rule"></a>Eylem kuralını yapılandırma

Azure Izleyici 'de **Uyarılar** giriş sayfasından **eylemleri Yönet** ' i seçerek özelliğe erişebilirsiniz. Ardından **Eylem kuralları (Önizleme)** öğesini seçin. Uyarılar için giriş sayfasının panosundan **Eylem kuralları (Önizleme)** seçeneğini belirleyerek kurallara erişebilirsiniz.

![Azure Izleyici giriş sayfasından eylem kuralları](media/alerts-action-rules/action-rules-landing-page.png)

**+ Yeni eylem kuralı**' nı seçin. 

![Yeni eylem kuralı ekle](media/alerts-action-rules/action-rules-new-rule.png)

Alternatif olarak, bir uyarı kuralı yapılandırırken bir eylem kuralı oluşturabilirsiniz.

![Yeni eylem kuralı ekle](media/alerts-action-rules/action-rules-alert-rule.png)

Artık eylem kuralları oluşturmak için akış sayfasını görmeniz gerekir. Aşağıdaki öğeleri yapılandırın: 

![Yeni eylem kuralı oluşturma akışı](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Kapsam

Önce kapsamı (Azure aboneliği, kaynak grubu veya hedef kaynak) seçin. Ayrıca, tek bir abonelik içindeki kapsamların bir birleşimini seçebilirsiniz.

![Eylem kuralı kapsamı](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtre ölçütü

Ayrıca, uyarıların belirli bir alt kümesine daraltmak için filtreler tanımlayabilirsiniz. 

Kullanılabilir filtreler şunlardır: 

* **Önem derecesi**: bir veya daha fazla uyarı kümesi seçin. **Önem derecesi = Sev1** , eylem kuralının Sev1 olarak ayarlanan tüm uyarılar için geçerli olduğu anlamına gelir.
* **Izleme hizmeti**: kaynak izleme hizmetini temel alan bir filtre. Bu filtre Ayrıca birden çok seçim olur. Örneğin, **Monitor Service = "Application Insights"** , eylem kuralının tüm Application Insights tabanlı uyarılar için geçerli olduğu anlamına gelir.
* **Kaynak türü**: belirli bir kaynak türünü temel alan bir filtre. Bu filtre Ayrıca birden çok seçim olur. Örneğin, **kaynak türü = "sanal makineler"** , eylem kuralının tüm sanal makineler için geçerli olduğu anlamına gelir.
* **Uyarı KURALı kimliği**: uyarı KURALıNıN Kaynak Yöneticisi kimliğini kullanarak belirli uyarı kurallarını filtreleme seçeneği.
* **Izleme koşulu**: izleme koşulu olarak **tetiklenen** veya **çözümlenen** uyarı örnekleri için bir filtre.
* **Açıklama**: uyarı kuralının bir parçası olarak tanımlanan, açıklamaya karşı bir dize eşleşmesi tanımlayan bir Regex (normal ifade) eşleşir. Örneğin, **Açıklama ' prod ' içerir** açıklamalarındaki "üretim" dizesini içeren tüm uyarılarla eşleşir.
* **Uyarı bağlamı (yük)** : bir uyarı yükünün uyarı bağlam alanlarına karşı bir dize eşleşmesi tanımlayan bir Regex eşleşmesi. Örneğin, **Uyarı bağlamı (yük) ' bilgisayar-01 ' içeriyorsa** , yükleri "bilgisayar-01" dizesini içeren tüm uyarılarla eşleşir.

Bu filtreler bir diğeri ile birlikte uygulanır. Örneğin, **' = sanal makineler** ve **önem derecesi ' = Sev0**kaynak türünü ayarlarsanız, yalnızca sanal makinelerinizdeki tüm **Sev0** uyarılarını filtrelediyseniz. 

![Eylem kuralı filtreleri](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Gizleme veya eylem grubu yapılandırması

Sonra, eylem kuralını uyarı gizleme veya eylem grubu desteği için yapılandırın. Her ikisini de seçemezsiniz. Yapılandırma, daha önce tanımlanan kapsam ve filtrelerle eşleşen tüm uyarı örneklerinde çalışır.

#### <a name="suppression"></a>Gizleme

**Gizleme**' yi seçerseniz, eylemlerin ve bildirimlerin gizlemesi için süreyi yapılandırın. Aşağıdaki seçeneklerden birini belirleyin:
* **Şu andan itibaren (her zaman)** : tüm bildirimleri süresiz olarak bastırır.
* **Zamanlanan saatte**: bir sınırlanmış süre içinde bildirimleri bastırır.
* **Tekrarda**: yinelenen günlük, haftalık veya aylık zamanlamaya göre bildirimleri bastırır.

![Eylem kuralı gizleme](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Eylem grubu

Geçiş sırasında **eylem grubu** ' nu seçerseniz, var olan bir eylem grubu ekleyin ya da yeni bir tane oluşturun. 

> [!NOTE]
> Bir eylem kuralıyla yalnızca bir eylem grubunu ilişkilendirebilirsiniz.

![Eylem grubu seçerek yeni eylem kuralı Ekle veya oluştur](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Eylem kuralı ayrıntıları

Son olarak, eylem kuralı için aşağıdaki ayrıntıları yapılandırın:
* Adı
* Kaydedildiği kaynak grubu
* Açıklama 

## <a name="example-scenarios"></a>Örnek senaryolar

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Senaryo 1: önem derecesine göre uyarıların gizlemesi

Contoso **, her hafta sonu için tüm VM** 'Lerdeki tüm VM 'Lerde tüm Sev4 uyarılarını bildirimleri bastırmak istiyor.

**Çözüm:** İle bir eylem kuralı oluşturun:
* Kapsam = **Contososub**
* Filtreler
    * Önem derecesi = **Sev4**
    * Kaynak türü = **sanal makineler**
* Yinelenme ile haftalık olarak ayarlanan gizleme ve **Cumartesi** ve **Pazar** işaretli

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Senaryo 2: Uyarı bağlamına göre uyarıların gizlemesi (yük)

Contoso, **Contososub** 'da **bilgisayar-01** için oluşturulan tüm günlük uyarılarına ilişkin bildirimleri bakım sırasında olduğu kadar gizlemek istiyor.

**Çözüm:** İle bir eylem kuralı oluşturun:
* Kapsam = **Contososub**
* Filtreler
    * İzleme hizmeti = **Log Analytics**
    * Uyarı bağlamı (yük) **bilgisayar içeriyor-01**
* Gizleme **Şu andan itibaren olarak ayarlandı (her zaman)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Senaryo 3: bir kaynak grubunda tanımlanan eylem grubu

Contoso, [abonelik düzeyinde bir ölçüm uyarısı](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)tanımladı. Ancak, **ContosoRG**kaynak grubundan oluşturulan uyarılar için özel olarak tetiklenecek eylemleri tanımlamak istemektedir.

**Çözüm:** İle bir eylem kuralı oluşturun:
* Kapsam = **ContosoRG**
* Filtre yok
* Eylem grubu **Contosoactiongroup** olarak ayarlandı

> [!NOTE]
> *Eylem kuralları ve uyarı kuralları içinde tanımlanan eylem grupları, yinelenenleri kaldırma olmadan bağımsız olarak çalışır.* Daha önce açıklanan senaryoda, uyarı kuralı için bir eylem grubu tanımlanmışsa, eylem kuralında tanımlanan eylem grubuyla birlikte tetiklenir. 

## <a name="managing-your-action-rules"></a>Eylem kurallarınızı yönetme

Eylem kurallarınızı liste görünümünden görüntüleyebilir ve yönetebilirsiniz:

![Eylem kuralları liste görünümü](media/alerts-action-rules/action-rules-list-view.png)

Buradan eylem kurallarını etkinleştirebilir, devre dışı bırakabilir veya yanındaki onay kutusunu işaretleyerek bu kuralları ölçeklendirerek silebilirsiniz. Bir eylem kuralı seçtiğinizde, yapılandırma sayfası açılır. Sayfa, eylem kuralının tanımını güncelleştirmenize ve etkinleştirebilir veya devre dışı bırakmanızı sağlar.

## <a name="best-practices"></a>En iyi uygulamalar

[Sonuç sayısı](alerts-unified-log.md) seçeneği ile oluşturduğunuz günlük uyarıları, tüm arama sonucunu (birden çok bilgisayara yayılabilen) kullanarak tek bir uyarı örneği oluşturur. Bu senaryoda, bir eylem kuralı **Uyarı bağlamı (yük)** filtresini kullanıyorsa, eşleşme olduğu sürece uyarı örneği üzerinde işlem yapar. Daha önce açıklanan senaryo 2 ' de, oluşturulan günlük uyarısıyla ilgili arama sonuçları hem **bilgisayar-01** hem de **Bilgisayar-02**içeriyorsa, tüm bildirim bastırılır. **Bilgisayar-02** için hiçbir bildirim üretilmez.

![Eylem kuralları ve günlük uyarıları (sonuç sayısı)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Günlük uyarılarını eylem kuralları ile en iyi şekilde kullanmak için, [ölçüm ölçümü](alerts-unified-log.md) seçeneğiyle günlük uyarıları oluşturun. Ayrı uyarı örnekleri, tanımlı grup alanı temel alınarak bu seçenek tarafından oluşturulur. Ardından, Senaryo 2 ' de, **bilgisayar-01** ve **Bilgisayar-02**için ayrı uyarı örnekleri oluşturulur. Senaryoda açıklanan eylem kuralı nedeniyle yalnızca **bilgisayar-01** için bildirim bastırılır. **Bilgisayar-02** ' e yönelik bildirim normal olarak çalışmaya devam eder.

![Eylem kuralları ve günlük uyarıları (sonuç sayısı)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>SSS

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Bir eylem kuralı yapılandırmadım, ancak yinelenen bildirimlerin oluşmasını önlemek için tüm olası çakışan eylem kurallarını görmek istiyorum. Bunu yapmak mümkün midir?

Bir eylem kuralı yapılandırırken bir kapsam tanımladıktan sonra, aynı kapsamda (varsa) çakışan eylem kurallarının bir listesini görebilirsiniz. Bu örtüşme, aşağıdaki seçeneklerden biri olabilir:

* Tam eşleşme: Örneğin, tanımladığınız eylem kuralı ve çakışan eylem kuralı aynı abonelikte bulunur.
* Alt küme: Örneğin, tanımladığınız eylem kuralı bir abonelikte ve çakışan eylem kuralı, abonelik içindeki bir kaynak grubunda bulunur.
* Bir üst küme: Örneğin, tanımladığınız eylem kuralı bir kaynak grubunda ve çakışan eylem kuralı kaynak grubunu içeren abonelikte bulunur.
* Bir kesişim: Örneğin, tanımladığınız eylem kuralı **VM1** ve **VM2**ve çakışan eylem kuralı **VM2** ve **VM3**üzerinde.

![Çakışan eylem kuralları](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Bir uyarı kuralı yapılandırmadım, ancak tanımladığım uyarı kuralında işlem yapabilecek bir işlem kuralları olup olmadığını bilmelidir.

Uyarı kuralınız için hedef kaynağı tanımladıktan sonra, **Eylemler** bölümü altında **yapılandırılan eylemleri görüntüle** ' yi seçerek aynı kapsamda (varsa) davranan eylem kurallarının listesini görebilirsiniz. Bu liste, kapsam için aşağıdaki senaryolara göre doldurulur:

* Tam eşleşme: Örneğin, tanımladığınız uyarı kuralı ve eylem kuralı aynı abonelikte bulunur.
* Alt küme: Örneğin, tanımladığınız uyarı kuralı bir abonelikte ve eylem kuralı, abonelik içindeki bir kaynak grubunda yer alır.
* Bir üst küme: Örneğin, tanımladığınız uyarı kuralı bir kaynak grubunda ve eylem kuralı kaynak grubunu içeren abonelikte bulunur.
* Bir kesişim: Örneğin, tanımladığınız uyarı kuralı **VM1** ve **VM2**ve eylem kuralı **VM2** ve **VM3**üzerinde.
    
![Çakışan eylem kuralları](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Eylem kuralı tarafından gizlenen uyarıları görebilir miyim?

[Uyarılar listesi sayfasında](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), **gizleme durumu**adlı ek bir sütun seçebilirsiniz. Bir uyarı örneği için bildirim gizlenemedi, listede bu durum gösterilir.

![Gizlenen uyarı örnekleri](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Bir eylem grubu ve aynı kapsamda etkin gizleme içeren bir eylem kuralı varsa, ne olur?

Gizleme her zaman aynı kapsamda öncelik alır.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>İki ayrı eylem kurallarında izlenen bir kaynağınız varsa ne olur? Bir veya iki bildirim alıyorum? Örneğin, aşağıdaki senaryoda **VM2** :

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

VM1 ve VM3 üzerindeki her uyarı için, eylem grubu AG1 bir kez tetiklenir. **VM2**üzerindeki her uyarı için, eylem kuralları yinelenen işlemleri kaldırmadığı için eylem grubu AG1 iki kez tetiklenecektir. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>İki ayrı eylem kuralına göre izlenen bir kaynaktır ve diğeri de gizleme için bir eylem aradığında ne olur? Örneğin, aşağıdaki senaryoda **VM2** :

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

VM1 üzerindeki her uyarı için, eylem grubu AG1 bir kez tetiklenir. VM2 ve VM3 üzerindeki her uyarı için Eylemler ve bildirimler bastırılır. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Farklı eylem gruplarını çağıran aynı kaynak için bir uyarı kuralınız ve bir eylem kuralı tanımsam ne olur? Örneğin, aşağıdaki senaryoda **VM1** :

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
VM1 üzerindeki her uyarı için, eylem grubu AG1 bir kez tetiklenir. "Rule1" uyarı kuralı tetiklendiğinde, ayrıca AG2 de tetiklenecektir. Eylem kuralları ve uyarı kuralları içinde tanımlanan eylem grupları, yinelenenleri kaldırma olmadan bağımsız olarak çalışır. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da uyarılar hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
