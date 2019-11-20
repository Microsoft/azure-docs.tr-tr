---
title: Azure otomatik ölçeklendirme sorunlarını giderme
description: Service Fabric, sanal makineler, Web Apps ve bulut hizmetlerinde kullanılan Azure otomatik ölçeklendirmeyle ilgili sorunları izleme.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 14e2f79383aa45125ce6955f3611a75ece1df10d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152950"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure otomatik ölçeklendirme sorunlarını giderme
 
Azure Izleyici otomatik ölçeklendirme, uygulamanızdaki yükü işlemek için çalışan doğru kaynak miktarına sahip olmanıza yardımcı olur. Yük arttıkça işlemek için kaynaklar eklemenize ve ayrıca boştaki kaynakları kaldırarak para tasarrufu sağlamanıza olanak sağlar. Seçtiğiniz zamanlamaya göre, sabit tarih-saat veya kaynak ölçüsüne göre ölçeklendirebilirsiniz. Daha fazla bilgi için bkz. [Otomatik ölçeklendirme genel bakış](autoscale-overview.md).

Otomatik Ölçeklendirme hizmeti, hangi ölçeklendirme eylemlerinin oluştuğunu ve bu eylemlere yol açan koşulların değerlendirilmesini anlamak için ölçüm ve Günlükler sağlar. Şu soruların yanıtlarını bulabilirsiniz:

- Neden hizmetten mi ölçeklendirmeliyim?
- Hizmetiniz neden ölçeklendirmedi?
- Otomatik ölçeklendirme eylemi neden başarısız oldu?
- Otomatik ölçeklendirme eylemi neden ölçeklendirmek için zaman alıyorsunuz?
  
## <a name="autoscale-metrics"></a>Otomatik ölçeklendirme ölçümleri

Otomatik ölçeklendirme, işlemini anlamak için size [dört ölçüm](metrics-supported.md#microsoftinsightsautoscalesettings) sağlar. 

- **Gözlemlenen ölçüm değeri** -otomatik ölçeklendirme altyapısı tarafından görüldüğü veya hesaplanarak ölçekleme eylemini gerçekleştirmek için seçtiğiniz ölçümün değeri. Tek bir otomatik ölçeklendirme ayarı birden çok kurala ve bu nedenle birden çok ölçüm kaynağına sahip olabileceğinden, bir boyut olarak "ölçüm kaynağı" kullanarak filtre uygulayabilirsiniz.
- **Ölçüm eşiği** -ölçekleme eylemini gerçekleştirmek için ayarladığınız eşik. Tek bir otomatik ölçeklendirme ayarı birden çok kurala ve bu nedenle birden çok ölçüm kaynağına sahip olabileceğinden, bir boyut olarak "ölçüm kuralı" kullanarak filtre uygulayabilirsiniz.
- **Gözlemlenen kapasite** -otomatik ölçeklendirme motoru tarafından görülen hedef kaynağın etkin örnek sayısı.
- **Ölçek eylemleri başlatıldı** -otomatik ölçeklendirme motoru tarafından başlatılan genişleme ve ölçek genişletme eylemlerinin sayısı. Ölçek Genişletme ile, eylemlerde ölçeğe göre filtreleyebilirsiniz.

Yukarıdaki ölçümleri tek bir yerde grafik olarak eklemek için [Ölçüm Gezgini](metrics-getting-started.md) kullanabilirsiniz. Grafik şunları göstermelidir:

  - gerçek ölçüm
  - Otomatik ölçeklendirme motoru tarafından görülen/hesaplanan ölçüm
  - ölçek eyleminin eşiği
  - kapasitede değişiklik 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Örnek 1-basit bir otomatik ölçeklendirme kuralını çözümleme 

Bir sanal makine ölçek kümesi için şu şekilde basit bir otomatik ölçeklendirme ayarı sunuyoruz:

- bir küme ortalama CPU yüzdesi 10 dakika boyunca %70 ' den büyükse ölçeği ölçeklendirir 
- küme CPU yüzdesi 10 dakikadan uzun süre boyunca %5 ' ten az olduğunda ölçeklendirme yapar. 

Otomatik ölçeklendirme hizmetindeki ölçümleri gözden geçirelim.
 
![Sanal makine ölçek kümesi yüzde CPU örneği](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Sanal makine ölçek kümesi yüzde CPU örneği](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Şekil 1a-sanal makine ölçek kümesi için CPU Ölçümü yüzdesi ve otomatik ölçeklendirme ayarı için gözlemlenen ölçüm değeri ölçümü***

![Ölçüm eşiği ve gözlemlenen kapasite](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Şekil 1B-metrik eşiği ve gözlemlenen kapasite***

Şekil 1B, ölçek genişletme kuralı için **ölçüm eşiği** (açık mavi çizgi) 70 ' dir.  **Gözlenen kapasite** (koyu mavi çizgi), şu anda 3 olan etkin örnek sayısını gösterir. 

> [!NOTE]
> Ölçek Genişletme eşiğini ve kural içindeki ölçeği (azalmayı) görmek için ölçüm tetikleme kuralı boyut ölçeği genişletme (artış) kuralına göre **ölçüm eşiğini** filtrelemeniz gerekir. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Örnek 2-bir sanal makine ölçek kümesi için gelişmiş otomatik ölçeklendirme

Bir sanal makine ölçek kümesi kaynağının kendi ölçüm **giden akışlarına**göre ölçeklendirilmesine izin veren bir otomatik ölçeklendirme ayarı vardır. Ölçüm eşiğinin **örnek sayısına göre ölçüyü Böl** seçeneğinin işaretli olduğuna dikkat edin. 

Ölçek eylemi kuralı: 

**Örnek başına giden akış** değeri 10 ' dan büyükse otomatik ölçeklendirme hizmeti 1 örneğe göre ölçeklendirmelidir. 

Bu durumda, otomatik ölçeklendirme motorunun gözlemlenen ölçüm değeri, gerçek ölçüm değeri olarak örnek sayısına bölünür. Gözlemlenen ölçüm değeri eşikten küçükse, ölçek genişletme eylemi başlatılmaz. 
 
![Sanal makine ölçek kümesi otomatik ölçeklendirme ölçümleri grafikleri örneği](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Sanal makine ölçek kümesi otomatik ölçeklendirme ölçümleri grafikleri örneği](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Şekil 2-sanal makine ölçek kümesi otomatik ölçeklendirme ölçümleri grafikleri örneği***

Şekil 2 ' de iki ölçüm grafiği görebilirsiniz. 

Üstteki grafik, **giden akışlar** ölçüsünün gerçek değerini gösterir. Gerçek değer 6 ' dır. 

En alttaki grafik birkaç değeri gösterir. 
 - **Gözlenen ölçüm değeri** (açık mavi) 3 ' tür ve 2 etkin örnek ve 6 ' a bölünmüş 2 3 ' tür. 
 - **Gözlenen kapasite** (mor), otomatik ölçeklendirme motoru tarafından görülen örnek sayısını gösterir. 
 - **Ölçüm eşiği** (açık yeşil) 10 olarak ayarlanır. 

Birden fazla ölçeklendirme eylemi kuralı varsa, belirli bir kaynak veya kurala göre ölçüm bölümüne bakmak için ölçüm Gezgini grafiğinde bölme veya **Filtre Ekle** seçeneğini kullanabilirsiniz. Ölçüm grafiğinin bölünmesi hakkında daha fazla bilgi için bkz. [ölçüm grafiklerinin gelişmiş özellikleri-bölme](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Örnek 3-otomatik ölçeklendirme olaylarını anlama

Otomatik ölçeklendirme ayarı ekranında, en son ölçeklendirme eylemlerini görmek için **çalışma geçmişi** sekmesine gidin. Bu sekmede zaman içinde **gözlenen kapasite** değişikliği de gösterilmektedir. Otomatik ölçeklendirme ayarlarını güncelleştirme/silme gibi işlemler de dahil olmak üzere tüm otomatik ölçeklendirme eylemleri hakkında daha fazla ayrıntı bulmak için etkinlik günlüğünü görüntüleyin ve otomatik ölçeklendirme işlemlerine göre filtreleyin.

![Otomatik ölçeklendirme ayarları çalıştırma geçmişi](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Kaynak günlüklerini otomatik ölçeklendirme

Otomatik Ölçeklendirme hizmeti, diğer Azure kaynakları ile aynı şekilde [kaynak günlükleri](resource-logs-overview.md)sağlar. İki günlük kategorisi vardır.

- **Otomatik ölçeklendirme değerlendirmeleri** -otomatik ölçeklendirme altyapısı her bir denetim her seferinde her bir koşul değerlendirmesi için günlük girişlerini kaydeder.  Giriş, ölçümlerin gözlemlenen değerleriyle ilgili ayrıntıları, değerlendirilen kuralları ve değerlendirme bir ölçeklendirme eylemiyle sonuçlanmış olduğunu içerir.

- **Otomatik ölçeklendirme ölçeklendirme eylemleri** -motor, otomatik ölçeklendirme hizmeti tarafından başlatılan ölçeklendirme eylemi olaylarını ve bu ölçeklendirme eylemlerinin sonuçlarını (başarılı, başarısız ve otomatik ölçeklendirme hizmeti tarafından görüldüğü için ne kadar ölçeklendirme oluştuğunu) kaydeder.

Azure Izleyici desteklenen herhangi bir hizmette olduğu gibi, bu günlükleri yönlendirmek için [tanılama ayarlarını](diagnostic-settings.md) kullanabilirsiniz:

- ayrıntılı analiz için Log Analytics çalışma alanınıza
- Azure dışı araçlara Event Hubs ve ardından
- Azure depolama hesabınıza arşivleme için  

![Otomatik ölçeklendirme Tanılama ayarları](media/autoscale-troubleshoot/diagnostic-settings.png)

Önceki resimde Azure portal otomatik ölçeklendirme Tanılama ayarları gösterilmektedir. Tanılama günlükleri sekmesini seçebilir ve günlük toplamayı ve yönlendirmeyi etkinleştirebilirsiniz. Ayrıca, *Microsoft. Insights/oto scalesettings*olarak kaynak türünü seçerek Tanılama ayarları için REST API, CLI, PowerShell ve Kaynak Yöneticisi şablonlarını kullanarak aynı eylemi gerçekleştirebilirsiniz. 

## <a name="troubleshooting-using-autoscale-logs"></a>Otomatik ölçeklendirme günlüklerini kullanarak sorun giderme 

En iyi sorun giderme deneyimi için, otomatik ölçeklendirme ayarını oluşturduğunuzda bir çalışma alanı aracılığıyla günlüklerinizi Azure Izleyici günlüklerine (Log Analytics) yönlendirmenizi öneririz. Bu işlem, önceki bölümde bulunan resimde gösterilir. Log Analytics kullanarak değerlendirmeleri doğrulayabilir ve eylemleri daha iyi ölçeklendirebilirsiniz.

Otomatik ölçeklendirme günlüklerinizi Log Analytics çalışma alanına gönderilmek üzere yapılandırdıktan sonra, günlükleri denetlemek için aşağıdaki sorguları çalıştırabilirsiniz. 

Başlamak için, en son otomatik ölçeklendirme değerlendirme günlüklerini görüntülemek üzere bu sorguyu deneyin:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Veya en son ölçeklendirme eylemi günlüklerini görüntülemek için aşağıdaki sorguyu deneyin:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Bu sorulara aşağıdaki bölümleri kullanın. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Beklemediğim bir ölçeklendirme eylemi oluştu

İlk olarak, ilgilendiğiniz ölçek eylemini bulmak için, ölçek eylemini sorgu olarak yürütün. En son ölçeklendirme eylemli ise, aşağıdaki sorguyu kullanın:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Ölçek eylemleri günlüğünden CorrelationId alanını seçin. Doğru değerlendirme günlüğünü bulmak için Bağıntıkimliği 'ı kullanın. Aşağıdaki sorgunun yürütülmesi, bu ölçeklendirme eylemine göre değerlendirilen tüm kuralları ve koşulları görüntüler.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Hangi profil bir ölçeklendirme eylemine neden oldu?

Ölçeklenen bir eylem oluştu, ancak çakışan kurallar ve profilleriniz var ve bu eyleme neden olan bir izleme yapmanız gerekiyor. 

Ölçek eyleminin (örnek 1 ' de açıklandığı gibi) CorrelationId 'sini bulun ve ardından profil hakkında daha fazla bilgi edinmek için değerlendirme günlüklerinde sorguyu yürütün.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Tüm profil değerlendirmesi, aşağıdaki sorgu kullanılarak da daha iyi anlaşılabilirler

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Bir ölçeklendirme eylemi gerçekleşmedi

Bir ölçeklendirme eylemi bekledim ve gerçekleşmedi. Hiçbir ölçeklendirme eylemi olayı veya günlüğü olmayabilir.

Ölçüm tabanlı ölçek kuralı kullanıyorsanız otomatik ölçeklendirme ölçümlerini gözden geçirin. **Gözlemlenen ölçüm değeri** veya **gözlemlenen kapasite** , bunların sizin beklediğiniz şekilde olmaması ve bu nedenle ölçek kuralının tetiklenmesi mümkündür. Bir ölçek genişletme kuralı değil, değerlendirmeleri görmeye devam edersiniz. Sık erişimli sürenin bir ölçeklendirme eylemi meydana geldiğini de mümkün hale gelir. 

 Ölçek eyleminin gerçekleşmesini beklediğiniz süre boyunca otomatik ölçeklendirme değerlendirme günlüklerini gözden geçirin. Yaptığı tüm değerlendirmeleri ve neden bir ölçeklendirme eylemi tetikleyemediğini gözden geçirin.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Ölçeklendirme eylemi başarısız oldu

Otomatik Ölçeklendirme hizmeti 'nin ölçeklendirme eylemini aldığı, ancak sistemin ölçeklenmeye karar verdiği veya ölçek eyleminin tamamlanmadığında veya başarısız olduğu bir durum olabilir. Başarısız ölçeklendirme eylemlerini bulmak için bu sorguyu kullanın.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Otomatik ölçeklendirme eylemleri veya arızaları hakkında bildirim almak için uyarı kuralları oluşturun. Otomatik ölçeklendirme olayları hakkında bildirim almak için uyarı kuralları da oluşturabilirsiniz.

## <a name="schema-of-autoscale-resource-logs"></a>Otomatik ölçeklendirme kaynak günlüklerinin şeması

Daha fazla bilgi için bkz. [Otomatik ölçeklendirme kaynak günlükleri](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Sonraki adımlar
[Otomatik ölçeklendirme en iyi yöntemleri](autoscale-best-practices.md)hakkında bilgi edinin. 