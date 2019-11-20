---
title: Eski Log Analytics Uyarıları API 'sinden yeni Azure uyarıları API 'sine geçiş
description: Eski savedSearch tabanlı Log Analytics uyarı API 'sine ve işlem, uyarı kurallarını yeni ScheduledQueryRules API 'sine geçirmek için genel müşteri sorunlarını gidermeye yönelik bilgiler.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 7b3a09c9227110d6dba205987903a2c97dccf1b8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677798"
---
# <a name="switch-api-preference-for-log-alerts"></a>Günlük uyarıları için anahtar API tercihi

> [!NOTE]
> Azure Kamu veya Azure Çin bulutu için **değil** yalnızca Azure genel bulutu kullanıcılarına yönelik içerik.  

Son olarak, Microsoft Operations Management Suite portalında uyarı kuralları yönetilecektir. Yeni uyarılar deneyimi Log Analytics dahil Microsoft Azure çeşitli hizmetlerle tümleştirildi ve [Uyarı KURALLARıNıZı OMS portalından Azure 'a genişletmemiz](alerts-extend.md)istendi. Ancak, müşteriler için minimum kesintiden emin olmak için, işlem, SavedSearch 'e göre tüketim [Log Analytics uyarı API 'si](api-alerts.md) için programlı arabirimi değiştirmedi.

Ancak artık, [Azure Faturalandırma-for log uyarılarında](alerts-unified-log.md#pricing-and-billing-of-log-alerts)de yansıtıcı olan doğru bir Azure programlı alternatifi olan [Azure Izleyici-SCHEDULEDQUERYRULES API 'sini](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)Log Analytics uyarma konusunda duyuruyoruz. API kullanarak günlük uyarılarınızı yönetme hakkında daha fazla bilgi edinmek için bkz. [Azure kaynak şablonu kullanarak günlük uyarılarını yönetme](alerts-log.md#managing-log-alerts-using-azure-resource-template) ve [PowerShell kullanarak günlük uyarılarını yönetme](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Yeni Azure API 'sine geçiş avantajları

[Eski Log Analytics uyarı API](api-alerts.md) ['Si üzerinden scheduledqueryrules API 'sini](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanarak uyarı oluşturmanın ve yönetmenin çeşitli avantajları vardır. Aşağıdaki önemli olanlardan bazılarını listeliyoruz:

- [Çalışma alanı günlük aramasını](../log-query/cross-workspace-query.md) uyarı kurallarında çapraz olarak ve Log Analytics çalışma alanları gibi dış kaynakları veya hatta Application Insights uygulamaları yaymaya yönelik bir özellik
- Sorguda gruplamak için birden çok alan kullanıldığında, [Scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanıcısı kullanmak Azure Portal içinde hangi alanın toplanacağını belirtebilir
- [Scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanılarak oluşturulan günlük uyarıları, 48 saate kadar bir süre tanımlanmış ve verileri daha uzun bir süre önce getirecek şekilde alabilir
- [Eski Log Analytics uyarı API 'si](api-alerts.md) ile birlikte üç farklı kaynak düzeyi oluşturma gereksinimi olmadan tek bir çekde uyarı kuralları oluşturun
- Azure 'da sorgu tabanlı günlük uyarılarının tüm türevleri için tek bir programlama arabirimi-yeni [Scheduledqueryrules API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , Log Analytics kuralları yönetmek için kullanılabilir Application Insights
- [PowerShell cmdlet 'lerini](alerts-log.md#managing-log-alerts-using-powershell) kullanarak günlük uyarılarınızı yönetme
- Tüm yeni günlük uyarı işlevselliği ve gelecekteki geliştirmeler yalnızca yeni [Scheduledqueryrules API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) aracılığıyla kullanılabilir

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Eski günlük uyarıları API 'sinden geçiş işlemi

Kullanıcılar [eski Log Analytics uyarı API](api-alerts.md) 'sini veya yeni [scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)'sini kullanabilir. Her iki API tarafından oluşturulan uyarı kuralları, *aynı API ile aynı zamanda ve Azure Portal ile yönetilebilir* . Varsayılan olarak, Azure Izleyici, mevcut Log Analytics çalışma alanları için Azure portal yeni bir uyarı kuralı oluşturmak üzere [eski Log Analytics uyarı API 'sini](api-alerts.md) kullanmaya devam edecektir. [1 haziran 2019 ' de veya sonrasında oluşturulan yeni günlük çalışma alanı,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) Azure Portal de dahil olmak üzere varsayılan olarak otomatik olarak yeni [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanır.

Aşağıdaki bir tercihe yönelik anahtar kümesi, scheduledQueryRules API 'SI ile aşağıda derlenir:

- Programlı arabirimler aracılığıyla günlük uyarılarını yönetmek için yapılan tüm etkileşimler artık bunun yerine [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanılarak yapılmalıdır. Daha fazla bilgi için bkz. [Azure kaynak şablonu aracılığıyla örnek kullanım](alerts-log.md#managing-log-alerts-using-azure-resource-template) ve [PowerShell aracılığıyla örnek kullanım](alerts-log.md#managing-log-alerts-using-powershell)
- Azure portal ' de oluşturulan tüm yeni günlük uyarısı kuralları yalnızca [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanılarak oluşturulur ve KULLANıCıLARıN [yeni API 'nin ek işlevlerini](#benefits-of-switching-to-new-azure-api) Azure Portal aracılığıyla kullanmasına izin verir
- Günlük uyarı kuralları için önem derecesi: *kritik, uyarı & bilgilendirici*, *0, 1 & 2 ' nin önem derecesine*sahip olur. Ayrıca, önem derecesi 3 ve 4 ile uyarı kuralları oluşturma/güncelleştirme seçeneği ile birlikte.

Uyarı kurallarının [eski Log Analytics uyarı API](api-alerts.md) 'sinden taşınması işlemi, uyarı tanımınızın, sorgunuzun veya yapılandırmanızın herhangi bir şekilde değiştirilmesini kapsamaz. Uyarı kurallarınızın ve izlemenin etkilenmemiştir ve uyarılar, anahtar sırasında veya sonrasında durdurulmaz veya durdurulmaz. Tek değişiklik, API tercihi ve yeni bir API aracılığıyla kurallarınızın erişimine yönelik bir değişiklik olur.

> [!NOTE]
> Bir Kullanıcı yeni [Scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)'sine tercih ettikten sonra eski [eskı Log Analytics uyarı API](api-alerts.md)'sini kullanmaya geri çevirebilir veya geri dönemezsiniz.

Gönüllü olarak yeni [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 'a geçiş yapan ve [eskı Log Analytics uyarı API](api-alerts.md)'sinden kullanımı engelleyen müşteriler; Bu işlemi, belirli bir Log Analytics çalışma alanıyla ilişkili tüm uyarı kurallarını değiştirmek için aşağıdaki API üzerinde bir PUT çağrısı gerçekleştirerek yapabilirsiniz.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Aşağıdaki JSON içeren istek gövdesiyle.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

API 'ye, Azure Resource Manager API 'sini çağırmayı kolaylaştıran açık kaynaklı bir komut satırı aracı olan [Armclient](https://github.com/projectkudu/ARMClient)kullanarak bir PowerShell komut satırından da erişilebilir. Aşağıda gösterildiği gibi, özel Log Analytics çalışma alanıyla ilişkili tüm uyarı kurallarını değiştirmek için ARMclient aracını kullanan örnek PUT çağrısı bölümünde verilmiştir.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Yeni [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanmak için Log Analytics çalışma alanındaki tüm uyarı kurallarının anahtarı başarılı olursa, aşağıdaki yanıt sağlanacaktır.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Kullanıcılar ayrıca, Log Analytics çalışma alanınızın geçerli durumunu denetleyebilir ve yalnızca [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanmak üzere geçiş olup olmadığını görebilir. Kullanıcıların, aşağıdaki API üzerinde bir GET çağrısı gerçekleştirmesini sağlayabilirsiniz.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[Armclient](https://github.com/projectkudu/ARMClient) aracını kullanarak PowerShell komut satırını kullanarak yukarıdaki ' u çalıştırmak için aşağıdaki örneğe bakın.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Belirtilen Log Analytics çalışma alanı yalnızca [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 'ı kullanmak üzere geçolduysa; ardından, yanıt JSON 'SI aşağıda listelendiği gibi olacaktır.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Aksi takdirde, belirtilen log analitik çalışma alanı henüz bir [Scheduledqueryrules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanmaya geçmediyse; ardından, yanıt JSON 'SI aşağıda listelendiği gibi olacaktır.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici-günlük uyarıları](alerts-unified-log.md)hakkında bilgi edinin.
- [Azure uyarıları 'nda günlük uyarıları](alerts-log.md)oluşturmayı öğrenin.
- [Azure uyarıları deneyimi](../../azure-monitor/platform/alerts-overview.md)hakkında daha fazla bilgi edinin.
