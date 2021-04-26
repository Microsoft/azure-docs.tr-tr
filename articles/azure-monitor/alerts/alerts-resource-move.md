---
title: Hedef kaynakları farklı bir Azure bölgesine hareket ettirildiğinde uyarı kurallarını veya eylem kurallarını güncelleştirme
description: Hedef kaynakları farklı bir Azure bölgesine hareket ettirildiğinde uyarı kurallarının veya eylem kurallarının nasıl güncelleştirilmesi için arka plan ve yönergeler.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: d21ee7a60d11a154737c5380ec20d3e9c4490962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786072"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Hedef kaynakları farklı bir Azure bölgesine hareket ettirildiğinde uyarı kurallarını veya eylem kurallarını güncelleştirme

Bu makalede, diğer Azure kaynaklarını bölgeler arasında taşıdığınızda mevcut [uyarı kurallarının](./alerts-overview.md) ve [eylem kurallarının](./alerts-action-rules.md) neden etkilenmiş olabileceği ve bu sorunları nasıl tanımlayacağınız ve giderecağınız açıklanır. Kaynak taşıma hakkında daha fazla bilgi için ana [kaynak taşıma belgelerini](../../azure-resource-manager/management/move-resources-overview.md) kontrol edin ve bir taşıma işlemi tasarlama denetim listesi

## <a name="why-the-problem-exists"></a>Sorun neden var?

Uyarı kuralları ve eylem kuralları diğer Azure kaynaklarına başvurur. [Azure VM 'leri](../../site-recovery/azure-to-azure-tutorial-migrate.md), [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)ve [Azure depolama](../../storage/common/storage-account-move.md)örnekleri aşağıda verilmiştir. Bu kuralların başvurduğu kaynakları taşıdığınızda, başvurdukları kaynakları bulamadığı için kuralların doğru çalışmayı durdurması olasıdır.

Hedef kaynakları taşıdıktan sonra kurallarınızın çalışmayı durdurmasına neden olabilecek iki temel neden vardır:

- Kuralınızın kapsamı, eski kaynağa açıkça başvuruyor.
- Uyarı kuralınız ölçümleri temel alır.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Kural kapsamı, açıkça eski kaynağa başvurur

Bir kaynağı taşıdığınızda kaynak KIMLIĞI çoğu durumda değişir. Arka planda, sistem eski bölgeden silinmeden önce kaynağı yeni bölgeye çoğaltır. Bu işlem için iki kaynak ve bu nedenle iki farklı kaynak kimliği aynı anda küçük bir süre için mevcut olmalıdır. Kaynak kimliklerinin benzersiz olması gerektiğinden, işlem sırasında yeni bir KIMLIK oluşturulması gerekir. 

**Kaynağı taşıma, mevcut kuralları nasıl etkiler?**

Uyarı kuralları ve eylem kuralları, uygulanan kaynakların kapsamına sahiptir. Kapsam, tüm abonelik, kaynak grubu veya bir veya daha fazla belirli kaynak olabilir.
Örneğin, iki kaynağa sahip bir kapsama sahip bir kural (iki sanal makine) aşağıda verilmiştir:

![Çoklu kaynak uyarı kuralı](media/alerts-resource-move/multi-resource-alert-rule.png)

Kural kapsamı açıkça bir kaynağın bahsetme ve kaynak KIMLIĞINI taşımışsa ve değiştiyse, bu kural yanlış veya mevcut olmayan bir kaynağı arar ve bu nedenle başarısız olur.

**Sorun nasıl düzeltilir?**

Etkilenen kuralı yeni kaynağa işaret etmek için güncelleştirin veya yeniden oluşturun. Kapsamı güncelleştirme işlemi bu makalenin ilerleyen kısımlarında bulunur.

Sorun bu kural türleri için geçerlidir:

- Etkinlik günlüğü uyarı kuralları
- Eylem kuralları
- Ölçüm uyarıları – daha fazla bilgi Için ölçümlere göre sonraki bölüm [uyarısı kuralları](#alert-rules-based-on-metrics)bölümüne bakın.

> [!NOTE]
> Günlük arama uyarı kuralları ve akıllı algılayıcı uyarı kuralları etkilenmez çünkü kapsamları bir çalışma alanı veya Application Insights. Bu kapsamların hiçbiri şu anda bölge taşımalarını desteklemez.

## <a name="alert-rules-based-on-metrics"></a>Ölçümleri temel alan uyarı kuralları

Azure kaynakları 'nın yayan ölçümler bölgesel olarak bulunur. Bir kaynak yeni bir bölgeye her taşınırsa, bu yeni bölgede ölçümlerini yaymaya başlar. Sonuç olarak, ölçümleri temel alan herhangi bir uyarı kuralı, doğru bölgedeki geçerli ölçüm akışına işaret etmek için güncelleştirilmeleri veya yeniden oluşturulması gerekir.

Bu açıklama hem [ölçüm uyarı kuralları](alerts-metric-overview.md) hem de [Kullanılabilirlik testi uyarı kuralları](../app/monitor-web-app-availability.md)için geçerlidir.

Kapsamdaki **Tüm** kaynaklar taşınmışsa, kuralı yeniden oluşturmanız gerekmez. Uyarı kuralının, uyarı kuralı açıklaması gibi herhangi bir alanını güncelleştirebilir ve kaydedebilirsiniz.
Kapsamdaki kaynakların **yalnızca bir kısmı** taşınırsa, taşınan kaynakları mevcut kuraldan kaldırmanız ve yalnızca taşınan kaynakları içeren yeni bir kural oluşturmanız gerekir.

## <a name="procedures-to-fix-problems"></a>Sorunları gidermeye yönelik yordamlar

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Azure portal taşınan kaynakla ilişkili kuralları tanımlama

- **Uyarı kuralları için** -uyarı kurallarını yönetmek > Uyarılar ' a gidin ve kapsayan aboneliğe ve taşınan kaynağa göre filtre uygulayın >.
> [!NOTE]
> Etkinlik günlüğü uyarı kuralları bu işlemi desteklemiyor. Bir etkinlik günlüğü uyarı kuralının kapsamını güncelleştirmek mümkün değildir ve başka bir abonelikteki bir kaynağı işaret ediyor olabilir. Bunun yerine, eskisini değiştirecek yeni bir kural oluşturabilirsiniz.

- **Eylem kuralları için** -uyarılara gidin > eylemleri yönetme > eylem kuralları (önizleme) > kapsayan aboneliğe ve taşınan kaynağa göre filtreleyin.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Azure portal bir kuralın kapsamını değiştirme

1. Önceki adımda belirlediğiniz kuralı, üzerine tıklayarak açın.
2. **Kaynak** altında **Düzenle** ' ye tıklayın ve gerektiğinde kapsamı ayarlayın.
3. Kuralın diğer özelliklerini gerektiği gibi ayarlayın.
4. **Kaydet**’e tıklayın.

![Uyarı kuralı kapsamını Değiştir](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak bir kuralın kapsamını değiştirme

1. Kuralın Azure Resource Manager şablonunu alın.   Azure portal bir kuralın şablonunu dışarı aktarmak için:
   1. Portalın kaynak grupları bölümüne gidin ve kuralı içeren kaynak grubunu açın.
   2. Genel bakış bölümünde **gizli türü göster** onay kutusunu işaretleyin ve kuralın ilgili türüne göre filtreleyin.
   3. Ayrıntılarını görüntülemek için ilgili kuralı seçin.
   4. **Ayarlar** altında, **şablonu dışarı aktar**' ı seçin.
2. Şablonu değiştirin. Gerekirse, iki kurala ayırın (yukarıda belirtilen bazı ölçüm uyarıları durumları için geçerlidir).
3. Şablonu yeniden dağıtın.

### <a name="change-scope-of-a-rule-using-rest-api"></a>REST API kullanarak bir kuralın kapsamını değiştirme

1. Mevcut kuralı al ([ölçüm uyarıları](/rest/api/monitor/metricalerts/get), [etkinlik günlüğü uyarıları](/rest/api/monitor/activitylogalerts/get))
2. Kapsamı değiştirme ([etkinlik günlüğü uyarıları](/rest/api/monitor/activitylogalerts/update))
3. Kuralı yeniden Dağıt ([ölçüm uyarıları](/rest/api/monitor/metricalerts/createorupdate), [etkinlik günlüğü uyarıları](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>PowerShell kullanarak bir kuralın kapsamını değiştirme

1. Mevcut kuralı ([ölçüm uyarıları](/powershell/module/az.monitor/get-azmetricalertrulev2), [etkinlik günlüğü uyarıları](/powershell/module/az.monitor/get-azactivitylogalert), [Eylem kuralları](/powershell/module/az.alertsmanagement/get-azactionrule)) alın.
2. Kapsamı değiştirin. Gerekirse, iki kurala ayırın (yukarıda belirtilen bazı ölçüm uyarıları durumları için geçerlidir).
3. Kuralı yeniden dağıtın ([ölçüm uyarıları](/powershell/module/az.monitor/add-azmetricalertrulev2), [etkinlik günlüğü uyarıları](/powershell/module/az.monitor/enable-azactivitylogalert), [Eylem kuralları](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Azure CLı kullanarak bir kuralın kapsamını değiştirme

1.  Mevcut kuralı al ([ölçüm uyarıları](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_show), [etkinlik günlüğü uyarıları](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log-alert_list)).
2.  Kural kapsamını doğrudan güncelleştirme ([ölçüm uyarıları](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_update), [etkinlik günlüğü uyarıları](/cli/azure/monitor/activity-log/alert/scope))
3.  Gerekirse, iki kurala ayırın (yukarıda belirtilen bazı ölçüm uyarıları durumları için geçerlidir).

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bildirimleri](alerts-troubleshoot.md), [ölçüm uyarıları](alerts-troubleshoot-metric.md)ve [günlük uyarıları](alerts-troubleshoot-log.md)ile ilgili diğer sorunları düzeltme hakkında bilgi edinin.