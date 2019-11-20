---
title: Azure Izleyici ile kaynaklar arasında sorgu | Microsoft Docs
description: Bu makalede, aboneliğinizdeki birden çok çalışma alanı ve App Insights uygulamasının kaynaklarında nasıl sorgu yapılacağı açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/05/2019
ms.openlocfilehash: e74c81956ab0590b8b7237d3ecf60ae242a43b73
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894481"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Azure Izleyici 'de çapraz kaynak günlük sorguları gerçekleştirme  

Daha önce Azure Izleyici ile, verileri yalnızca geçerli çalışma alanından çözümleyebilirsiniz ve aboneliğinizde tanımlanan birden çok çalışma alanında sorgulama yeteneğinizi sınırlı olursunuz.  Ayrıca, yalnızca Application Insights veya Visual Studio 'dan Application Insights doğrudan Web tabanlı uygulamanızdan toplanan telemetri öğelerini arayabilirsiniz. Bu, işletimsel ve uygulama verilerinin birlikte yerel olarak analiz edilmesi için de bir zorluk yaptı.

Artık yalnızca birden fazla Log Analytics çalışma alanında değil, aynı zamanda aynı kaynak grubundaki belirli bir Application Insights uygulamasından, başka bir kaynak grubunda veya başka bir abonelikte da sorgulama yapabilirsiniz. Bu, verilerinizin sistem genelinde bir görünümünü sağlar. Bu tür sorguları yalnızca [Log Analytics](portals.md)için gerçekleştirebilirsiniz.

## <a name="cross-resource-query-limits"></a>Çapraz kaynak sorgu limitleri 

* Tek bir sorguya dahil edebilirsiniz Application Insights kaynak ve Log Analytics çalışma alanlarının sayısı 100 ile sınırlıdır.
* Görünüm tasarımcısında çapraz kaynak sorgusu desteklenmez. Log Analytics bir sorgu yazabilir ve [günlük sorgusunu görselleştirmek](../learn/tutorial-logs-dashboards.md)için Azure panosuna sabitleyebilirsiniz. 
* Log uyarılarındaki çapraz kaynak sorgusu, yeni [Scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)'sinde desteklenir. Azure Izleyici, [eski günlük uyarıları API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)'sinden geçiş yapmadığınız takdirde, varsayılan olarak, Azure Portal ' dan yeni günlük uyarı kuralları oluşturmak için [eskı Log Analytics uyarı API](../platform/api-alerts.md) 'sini kullanır. Anahtar sonrasında, yeni API Azure portal yeni uyarı kuralları için varsayılan olur ve çapraz kaynak sorgu günlüğü uyarı kuralları oluşturmanıza olanak sağlar. [Scheduledqueryrules API 'si için Azure Resource Manager şablonunu](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) kullanarak anahtarı yapmadan çapraz kaynak sorgu günlüğü uyarı kuralları oluşturabilirsiniz, ancak bu uyarı kuralı, [SCHEDULEDQUERYRULES API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ile değil, Azure Portal .


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Log Analytics çalışma alanlarında ve Application Insights sorgulama
Sorgunuzdaki başka bir çalışma alanına başvurmak için, [*çalışma alanı*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) tanımlayıcısını kullanın ve Application Insights bir uygulama için [*uygulama*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) tanımlayıcısı ' nı kullanın.  

### <a name="identifying-workspace-resources"></a>Çalışma alanı kaynaklarını tanımlama
Aşağıdaki örnekler, *ContosoRetail-It*adlı bir çalışma alanındaki Update tablosundan güncelleştirme tablosundan özetlenen günlük sayısını döndürmek için Log Analytics çalışma alanları genelinde sorguları gösterir. 

Bir çalışma alanının tanımlanması çeşitli yollarla gerçekleştirilebilir:

* Kaynak adı-çalışma alanının, bazen *bileşen adı*olarak adlandırılan okunabilir bir adıdır. 

    `workspace("contosoretail-it").Update | count`

* Nitelenmiş ad-Şu biçimdeki abonelik adı, kaynak grubu ve Bileşen adından oluşan, çalışma alanının "tam adı" dır: *subscriptionName/resourceGroup/ComponentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Azure abonelik adları benzersiz olmadığından, bu tanımlayıcı belirsiz olabilir. 
    >

* Çalışma alanı KIMLIĞI-bir çalışma alanı KIMLIĞI, bir genel benzersiz tanımlayıcı (GUID) olarak temsil edilen her çalışma alanına atanan benzersiz, sabit bir tanıtıcıdır.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Kaynak KIMLIĞI: çalışma alanının Azure tarafından tanımlanan benzersiz kimliği. Kaynak adı belirsiz olduğunda kaynak KIMLIĞI kullanılır.  Çalışma alanları için şu biçim: */Subscriptions/SubscriptionID/ResourceGroups/resourcegroup/Providers/Microsoft. Operationalınsights/çalışma alanları/componentName*.  

    Örnek:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Bir uygulamayı tanımlama
Aşağıdaki örneklerde, Application Insights 'de *fabrikamapp* adlı bir uygulamaya yönelik olarak yapılan bir Özet istek sayısı döndürülür. 

Application Insights ' de bir uygulamanın tanımlanması, *uygulama (tanımlayıcı)* ifadesiyle gerçekleştirilebilir.  *Tanımlayıcı* bağımsız değişkeni, aşağıdakilerden birini kullanarak uygulamayı belirtir:

* Kaynak adı-uygulamanın, bazen *bileşen adı*olarak da adlandırılan, okunabilir bir adıdır.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Bir uygulamanın adına göre tanımlanması, tüm erişilebilir aboneliklerde benzersizlik olduğunu varsayar. Belirtilen ada sahip birden çok uygulamanız varsa, belirsizlik nedeniyle sorgu başarısız olur. Bu durumda, diğer tanımlayıcılardan birini kullanmanız gerekir.

* Nitelikli ad-uygulamanın, abonelik adı, kaynak grubu ve Bileşen adından Şu biçimdeki "tam adı" vardır: *subscriptionName/resourceGroup/ComponentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Azure abonelik adları benzersiz olmadığından, bu tanımlayıcı belirsiz olabilir. 
    >

* ID-uygulamanın uygulama GUID 'SI.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Kaynak KIMLIĞI-uygulamanın Azure tarafından tanımlanan benzersiz kimliği. Kaynak adı belirsiz olduğunda kaynak KIMLIĞI kullanılır. Biçim: */Subscriptions/SubscriptionID/ResourceGroups/resourcegroup/Providers/Microsoft. Operationalınsights/bileşenler/componentName*.  

    Örnek:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Birden çok kaynak genelinde sorgu gerçekleştirme
Kaynak örneklerinizin herhangi birinden birden çok kaynağı sorgulayabilirsiniz, bunlar çalışma alanları ve uygulamalar birleştirilebilir.
    
İki çalışma alanı üzerinde sorgu örneği:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Birden çok kaynak için çapraz kaynak sorgusu kullanma
Çoklu Log Analytics çalışma alanları ve Application Insights kaynaklarından verileri ilişkilendirmek için çapraz kaynak sorguları kullanırken, sorgu karmaşık ve bakım açısından zor hale gelebilir. Sorgu mantığını sorgu yapısını kolaylaştıran sorgu kaynaklarının kapsamından ayırmak için [Azure izleyici günlük sorgularının işlevlerinden](functions.md) yararlanabilirsiniz. Aşağıdaki örnek, birden çok Application Insights kaynağını nasıl izleyebileceğinizi ve uygulama adına göre başarısız isteklerin sayısını görselleştirmenizi gösterir. 

Aşağıdaki gibi Application Insights kaynak kapsamına başvuran bir sorgu oluşturun. `withsource= SourceApp` komutu, günlüğü gönderen uygulama adını atayan bir sütun ekler. Sorguyu, _Applicationsscoping_diğer adı ile birlikte bir [işlev olarak kaydedin](functions.md#create-a-function) .

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Artık [Bu işlevi](../../azure-monitor/log-query/functions.md#use-a-function) aşağıdaki gibi bir çapraz kaynak sorgusunda kullanabilirsiniz. _Applicationsscoping_ işlev diğer adı, tüm tanımlanmış uygulamalardan gelen istekler tablosunun birleşimini döndürür. Sorgu daha sonra başarısız isteklere filtre uygular ve eğilimleri uygulamaya göre görselleştirir. Bu örnekte _Parse_ işleci isteğe bağlıdır. _Sourceapp_ özelliğinden uygulama adını ayıklar.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Çalışma alanları ve uygulamalar dahil olmak üzere uyarı kuralı kaynaklarının erişim doğrulaması uyarı oluşturma sırasında gerçekleştirildiğinden, bu yöntem günlük uyarıları ile kullanılamaz. Uyarı oluşturulduktan sonra işleve yeni kaynaklar eklemek desteklenmez. Günlük uyarılarında kaynak kapsamı için işlev kullanmayı tercih ederseniz, kapsamdaki kaynakları güncelleştirmek için portalda veya bir Kaynak Yöneticisi şablonuyla uyarı kuralını düzenlemeniz gerekir. Alternatif olarak, günlük uyarısı sorgusuna kaynak listesini de ekleyebilirsiniz.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Sonraki adımlar

- Günlük sorgularına genel bakış ve Azure Izleyici günlük verilerinin nasıl yapılandırıldığı hakkında bilgi için [Azure izleyici 'de günlük verilerini çözümleme '](log-query-overview.md) ye bakın.
- Azure izleyici günlük sorgularının tüm kaynaklarını görüntülemek için [Azure izleyici günlük sorgularını](query-language.md) gözden geçirin.
