---
title: Geçerli Azure Izleyici günlüğü uyarıları API 'sine yükselt
description: Günlük uyarıları ScheduledQueryRules API 'sine nasıl geçiş yapılacağını öğrenin
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 101a575d9acac5da4a8c0399f60e8f32e591d2cf
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285367"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Eski Log Analytics uyarı API 'sindeki geçerli günlük uyarıları API 'sine yükselt

> [!NOTE]
> Bu makale yalnızca Azure Kamu ile ilgilidir (Azure Kamu veya Azure Çin bulutuna **değil** ).

> [!NOTE]
> Kullanıcı tercihi geçerli [Scheduledqueryrules API](/rest/api/monitor/scheduledqueryrules) 'sine geçirmeyi seçerse, eski [eskı Log Analytics uyarı API](./api-alerts.md)'sine geri dönmek mümkün değildir.

Geçmişte, kullanıcılar günlük uyarı kurallarını yönetmek için [eski Log Analytics uyarı API](./api-alerts.md) 'sini kullandı. Geçerli çalışma alanları [Scheduledqueryrules API](/rest/api/monitor/scheduledqueryrules)kullanır. Bu makalede, eski API 'den geçerli API 'ye geçme avantajları ve işlemleri açıklanmaktadır.

## <a name="benefits"></a>Avantajlar

- Uyarı kurallarının oluşturulması için tek şablon (daha önce üç ayrı şablon gereklidir).
- Log Analytics çalışma alanları veya Application Insights kaynakları için tek API.
- [PowerShell cmdlet 'leri desteği](./alerts-log.md#managing-log-alerts-using-powershell).
- Diğer tüm uyarı türleriyle birlikte, diğer tüm uyarı türleriyle hizalama.
- Log Analytics çalışma alanları veya Application Insights kaynakları gibi çeşitli dış kaynakları kapsayan [çapraz çalışma alanı günlüğü uyarısı](../logs/cross-workspace-query.md) oluşturma yeteneği.
- Kullanıcılar, ' toplama açık ' parametresini kullanarak uyarıların bölüneceği boyutları belirtebilir.
- Günlük uyarıları, iki güne kadar veri (daha önce bir güne sınırlı) kadar uzatılmış bir süreye sahiptir.

## <a name="impact"></a>Etki

- Tüm yeni kurallar geçerli API ile oluşturulmalıdır/düzenlenmelidir. Bkz. [Azure kaynak şablonu aracılığıyla örnek kullanım](alerts-log-create-templates.md) ve [PowerShell aracılığıyla örnek kullanım](./alerts-log.md#managing-log-alerts-using-powershell).
- Kurallar geçerli API 'de izlenen kaynaklar Azure Resource Manager ve benzersiz olması gerekir, kurallar kaynak KIMLIĞI bu yapıya dönüşür: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Uyarı kuralının görünen adları değişmeden kalır.

## <a name="process"></a>İşleme

Geçiş işlemi etkileşimli değildir ve çoğu durumda el ile adımlar gerektirmez. Uyarı kurallarınız, anahtar sırasında veya sonrasında durdurulmaz veya durdurulmaz.
Bu çağrıyı, belirli bir Log Analytics çalışma alanıyla ilişkili tüm uyarı kurallarını değiştirmek için yapın:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Aşağıdaki JSON içeren istek gövdesiyle:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Aşağıda, yukarıdaki API çağrısını kolaylaştıran açık kaynaklı bir komut satırı aracı olan [Armclient](https://github.com/projectkudu/ARMClient)kullanımına ilişkin bir örnek verilmiştir:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": true}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Anahtar başarılı olursa, yanıt şu şekilde olur:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Çalışma alanının geçiş durumunu denetle

Bu API çağrısını, anahtar durumunu denetlemek için de kullanabilirsiniz:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[Armclient](https://github.com/projectkudu/ARMClient) aracını da kullanabilirsiniz:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Log Analytics çalışma alanı [Scheduledqueryrules API](/rest/api/monitor/scheduledqueryrules)'sine geçolduysa, yanıt şu şekilde olur:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Log Analytics çalışma alanı geçmediyse, yanıt şu şekilde olur:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici-günlük uyarıları](./alerts-unified-log.md)hakkında bilgi edinin.
- [API kullanarak günlük uyarılarınızı yönetmeyi](alerts-log-create-templates.md)öğrenin.
- [PowerShell kullanarak günlük uyarılarını yönetmeyi](./alerts-log.md#managing-log-alerts-using-powershell)öğrenin.
- [Azure uyarıları deneyimi](./alerts-overview.md)hakkında daha fazla bilgi edinin.
