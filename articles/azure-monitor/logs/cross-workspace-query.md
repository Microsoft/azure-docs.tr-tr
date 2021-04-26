---
title: Azure Izleyici ile kaynaklar arasında sorgu | Microsoft Docs
description: Bu makalede, aboneliğinizdeki birden çok çalışma alanı ve App Insights uygulamasının kaynaklarında nasıl sorgu yapılacağı açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379562"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Azure Izleyici 'de çalışma alanları ve uygulamalar arasında yayılan günlük sorgusu gerçekleştirme

Azure Izleyici günlükleri aynı kaynak grubunda, başka bir kaynak grubunda veya başka bir abonelikte birden çok Log Analytics çalışma alanı ve Application Insights uygulaması arasında sorguyu destekler. Bu, verilerinizin sistem genelinde bir görünümünü sağlar.

Birden çok çalışma alanında ve uygulamalarda depolanan verileri sorgulamak için iki yöntem vardır:
1. Çalışma alanını ve uygulama ayrıntılarını belirterek açık olarak. Bu teknik, bu makalede ayrıntılı olarak açıklanmıştır.
2. [Kaynak bağlamı sorgularını](./design-logs-deployment.md#access-mode)örtük olarak kullanma. Belirli bir kaynak, kaynak grubu veya abonelik bağlamında sorgulama yaptığınızda ilgili veriler, bu kaynaklarla ilgili verileri içeren tüm çalışma alanlarından alınacaktır. Uygulamalarda depolanan Application Insights verileri getirilmeyecektir.

> [!IMPORTANT]
> [Çalışma alanı tabanlı Application Insights kaynak](../app/create-workspace-resource.md) telemetrisi kullanıyorsanız, diğer tüm günlük verileriyle birlikte bir Log Analytics çalışma alanında depolanır. Birden çok çalışma alanındaki uygulamayı içeren bir sorgu yazmak için Workspace () ifadesini kullanın. Aynı çalışma alanındaki birden çok uygulama için, bir çapraz çalışma alanı sorgusuna gerek yoktur.


## <a name="cross-resource-query-limits"></a>Çapraz kaynak sorgu limitleri 

* Tek bir sorguya dahil edebilirsiniz Application Insights kaynak ve Log Analytics çalışma alanlarının sayısı 100 ile sınırlıdır.
* Görünüm tasarımcısında çapraz kaynak sorgusu desteklenmez. Log Analytics bir sorgu yazabilir ve [bir günlük sorgusunu görselleştirmek](../visualize/tutorial-logs-dashboards.md) veya [çalışma kitaplarına](../visualize/workbooks-overview.md)eklemek için Azure panosuna sabitleyebilirsiniz.
* Günlük uyarılarındaki çapraz kaynak sorguları yalnızca geçerli [Scheduledqueryrules API](/rest/api/monitor/scheduledqueryrules)'sinde desteklenir. Eski Log Analytics Uyarıları API 'sini kullanıyorsanız [GEÇERLI API 'ye geçmeniz](../alerts/alerts-log-api-switch.md)gerekir.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Log Analytics çalışma alanlarında ve Application Insights sorgulama
Sorgunuzdaki başka bir çalışma alanına başvurmak için, [*çalışma alanı*](../logs/workspace-expression.md) tanımlayıcısını kullanın ve Application Insights bir uygulama için [*uygulama*](./app-expression.md) tanımlayıcısı ' nı kullanın.  

### <a name="identifying-workspace-resources"></a>Çalışma alanı kaynaklarını tanımlama
Aşağıdaki örnekler, *ContosoRetail-It* adlı bir çalışma alanındaki Update tablosundan güncelleştirme tablosundan özetlenen günlük sayısını döndürmek için Log Analytics çalışma alanları genelinde sorguları gösterir. 

Bir çalışma alanının tanımlanması çeşitli yollarla gerçekleştirilebilir:

* Kaynak adı-çalışma alanının, bazen *bileşen adı* olarak adlandırılan okunabilir bir adıdır. 

    >[!IMPORTANT]
    >Uygulama ve çalışma alanı adları benzersiz olmadığından, bu tanımlayıcı belirsiz olabilir. Başvurunun nitelenmiş ad, çalışma alanı KIMLIĞI veya Azure Kaynak KIMLIĞI tarafından olması önerilir.

    `workspace("contosoretail-it").Update | count`

* Nitelenmiş ad-Şu biçimdeki abonelik adı, kaynak grubu ve Bileşen adından oluşan, çalışma alanının "tam adı" dır: *subscriptionName/resourceGroup/ComponentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Azure abonelik adları benzersiz olmadığından, bu tanımlayıcı belirsiz olabilir.

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

* Kaynak adı-uygulamanın, bazen *bileşen adı* olarak da adlandırılan, okunabilir bir adıdır.  

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
Çoklu Log Analytics çalışma alanları ve Application Insights kaynaklarından verileri ilişkilendirmek için çapraz kaynak sorguları kullanırken, sorgu karmaşık ve bakım açısından zor hale gelebilir. Sorgu mantığını sorgu yapısını kolaylaştıran sorgu kaynaklarının kapsamından ayırmak için [Azure izleyici günlük sorgularının işlevlerinden](./functions.md) yararlanabilirsiniz. Aşağıdaki örnek, birden çok Application Insights kaynağını nasıl izleyebileceğinizi ve uygulama adına göre başarısız isteklerin sayısını görselleştirmenizi gösterir. 

Aşağıdaki gibi Application Insights kaynak kapsamına başvuran bir sorgu oluşturun. `withsource= SourceApp`Komut, günlüğü gönderen uygulama adını atayan bir sütun ekler. Sorguyu, _Applicationsscoping_ diğer adı ile birlikte bir [işlev olarak kaydedin](./functions.md#create-a-function) .

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Artık [Bu işlevi](./functions.md#use-a-function) aşağıdaki gibi bir çapraz kaynak sorgusunda kullanabilirsiniz. _Applicationsscoping_ işlev diğer adı, tüm tanımlanmış uygulamalardan gelen istekler tablosunun birleşimini döndürür. Sorgu daha sonra başarısız isteklere filtre uygular ve eğilimleri uygulamaya göre görselleştirir. Bu örnekte _Parse_ işleci isteğe bağlıdır. _Sourceapp_ özelliğinden uygulama adını ayıklar.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Çalışma alanları ve uygulamalar dahil olmak üzere uyarı kuralı kaynaklarının erişim doğrulaması uyarı oluşturma sırasında gerçekleştirildiğinden, bu yöntem günlük uyarıları ile kullanılamaz. Uyarı oluşturulduktan sonra işleve yeni kaynaklar eklemek desteklenmez. Günlük uyarılarında kaynak kapsamı için işlev kullanmayı tercih ederseniz, kapsamdaki kaynakları güncelleştirmek için portalda veya bir Kaynak Yöneticisi şablonuyla uyarı kuralını düzenlemeniz gerekir. Alternatif olarak, günlük uyarısı sorgusuna kaynak listesini de ekleyebilirsiniz.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Sonraki adımlar

- Günlük sorgularına genel bakış ve Azure Izleyici günlük verilerinin nasıl yapılandırıldığı hakkında bilgi için [Azure izleyici 'de günlük verilerini çözümleme '](./log-query-overview.md) ye bakın.