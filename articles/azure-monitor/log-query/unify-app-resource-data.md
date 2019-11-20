---
title: Birden çok Azure Izleyici Application Insights kaynağını bütünleştirme | Microsoft Docs
description: Bu makalede, Azure Izleyici günlüklerinde bir işlevi kullanarak birden çok Application Insights kaynağını sorgulama ve bu verileri görselleştirme hakkında ayrıntılar verilmektedir.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650130"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Birden çok Azure Izleyici Application Insights kaynağını bütünleştirme 
Bu makalede, Application Insights Bağlayıcısı kullanım dışı bırakma işleminin yerine, farklı Azure aboneliklerinde olsalar bile, tüm Application Insights günlük verilerinizi tek bir yerde sorgulama ve görüntüleme açıklanmaktadır. Tek bir sorguya dahil edebilirsiniz Application Insights kaynak sayısı 100 ile sınırlıdır.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Birden çok Application Insights kaynağını sorgulamak için önerilen yaklaşım 
Bir sorgudaki birden çok Application Insights kaynağın listelenmesi, bakımını yapmak ve sürdürmek zor olabilir. Bunun yerine, uygulama kapsamı 'ndan sorgu mantığını ayırmak için işlevinden yararlanabilirsiniz.  

Bu örnek, birden çok Application Insights kaynağını nasıl izleyebileceğinizi ve başarısız isteklerin sayısını uygulama adına göre görselleştirmenizi gösterir. Başlamadan önce, bağlı uygulamaların listesini almak için bu sorguyu Application Insights kaynaklara bağlı çalışma alanında çalıştırın: 

```
ApplicationInsights
| summarize by ApplicationName
```

Uygulama listesiyle birlikte UNION işlecini kullanarak bir işlev oluşturun, sonra sorguyu çalışma alanınıza, *Applicationsscoping*diğer adı ile işlev olarak kaydedin. 

Çalışma alanınızda sorgu Gezgini ' ne gidip, Düzenle ve sonra kaydetme işlevini seçerek veya `SavedSearch` PowerShell cmdlet 'ini kullanarak, listelenen uygulamaları portalda dilediğiniz zaman değiştirebilirsiniz. 

>[!NOTE]
>Çalışma alanları ve uygulamalar dahil olmak üzere uyarı kuralı kaynaklarının erişim doğrulaması uyarı oluşturma sırasında gerçekleştirildiğinden, bu yöntem günlük uyarıları ile kullanılamaz. Uyarı oluşturulduktan sonra işleve yeni kaynaklar eklemek desteklenmez. Günlük uyarılarında kaynak kapsamı için işlev kullanmayı tercih ederseniz, kapsamdaki kaynakları güncelleştirmek için portalda veya bir Kaynak Yöneticisi şablonuyla uyarı kuralını düzenlemeniz gerekir. Alternatif olarak, günlük uyarısı sorgusuna kaynak listesini de ekleyebilirsiniz.

`withsource= SourceApp` Komut, günlüğü gönderen uygulamayı atayan sonuçlara bir sütun ekler. Parse işleci Bu örnekte isteğe bağlıdır ve SourceApp özelliğinden uygulama adını ayıklamak için kullanır. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Artık çapraz kaynak sorgusunda applicationsScoping işlevini kullanmaya hazırsınız:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Sorgu Application Insights şeması kullanır, ancak applicationsScoping işlevi Application Insights veri yapısını döndürdüğünden sorgu çalışma alanında yürütülür. İşlev diğer adı, tüm tanımlanmış uygulamalardan gelen isteklerin birleşimini döndürür. Sorgu daha sonra başarısız isteklere filtre uygular ve eğilimleri uygulamaya göre görselleştirir.

![Çapraz sorgu sonuçları örneği](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Application Insights kaynaklar ve çalışma alanı verileri genelinde sorgulama 
Bağlayıcıyı durdurduğunuzda ve Application Insights veri saklama (90 gün) tarafından kırpılan bir zaman aralığı üzerinde sorgular gerçekleştirmeniz gerektiğinde, çalışma alanında [çapraz kaynak sorgular](../../azure-monitor/log-query/cross-workspace-query.md) gerçekleştirmeniz ve bir ara için kaynakları Application Insights gerekir dönemini. Bu, yukarıda bahsedilen yeni Application Insights veri bekletme için uygulama verilerinize göre biriktirene kadar olur. Application Insights ve çalışma alanındaki şemalar farklı olduğundan sorgu bazı düzenlemeler gerektiriyor. Bu bölümün ilerleyen kısımlarında şema farklarını vurgulayan tabloya bakın. 

>[!NOTE]
>Log uyarılarındaki [çapraz kaynak sorgusu](../log-query/cross-workspace-query.md) , yeni [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)'sinde desteklenir. Azure Izleyici, [eski günlük uyarıları API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)'sinden geçiş yapmadığınız takdirde, varsayılan olarak, Azure Portal ' dan yeni günlük uyarı kuralları oluşturmak için [eskı Log Analytics uyarı API](../platform/api-alerts.md) 'sini kullanır. Anahtar sonrasında, yeni API Azure portal yeni uyarı kuralları için varsayılan olur ve çapraz kaynak sorgu günlüğü uyarı kuralları oluşturmanıza olanak sağlar. [Scheduledqueryrules API 'si Için ARM şablonunu](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) kullanarak anahtarı yapmadan [çapraz kaynak sorgu](../log-query/cross-workspace-query.md) günlüğü uyarı kuralları oluşturabilirsiniz, ancak bu uyarı kuralı Azure Portal değil, [scheduledqueryrules API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ile yönetilebilir.

Örneğin, bağlayıcı 2018-11-01 üzerinde çalışmayı durdurulmuşsa, çalışma alanındaki Application Insights kaynak ve uygulama verileri arasında Günlükler sorgulayıp, sorgunuz aşağıdaki örnekte olduğu gibi oluşturulur:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights ve Log Analytics çalışma alanı şema farklılıkları
Aşağıdaki tabloda Log Analytics ve Application Insights arasındaki şema farklılıkları gösterilmektedir.  

| Log Analytics çalışma alanı özellikleri| Application Insights kaynak özellikleri|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| Useraccountıd | user_AccountId |

## <a name="next-steps"></a>Sonraki adımlar

Kullanım [günlük araması](../../azure-monitor/log-query/log-query-overview.md) Application Insights uygulamalarınız için ayrıntılı bilgileri görüntülemek için.
