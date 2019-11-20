---
title: Azure Analysis Services için tanılama günlüğü | Microsoft Docs
description: Azure Analysis Services sunucunuzu izlemek için Azure Kaynak tanılama günlüğü 'nün nasıl ayarlanacağını açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b8ae2c529bebebae4ebc2d7b0b8a7e420fe9bcc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572785"
---
# <a name="setup-diagnostic-logging"></a>Tanılama günlüğüne kaydetmeyi ayarlama

Herhangi bir Analysis Services çözümünün önemli bir kısmı, sunucularınızın nasıl çalıştığını izlerdir. [Azure Kaynak tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md)Ile [Azure depolama](https://azure.microsoft.com/services/storage/)'ya günlükleri izleyip gönderebilir, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)akışa alabilir ve [Azure izleyici günlüklerine](../azure-monitor/azure-monitor-log-hub.md)aktarabilirsiniz.

![Depolama, Event Hubs veya Azure Izleyici günlüklerine tanılama günlüğü](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Günlüğe kaydedilen nedir?

**Motor**, **hizmet**ve **ölçüm** kategorilerini seçebilirsiniz.

### <a name="engine"></a>Altyapısına

**Motor** seçildiğinde tüm [XEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)günlüğe kaydedilir. Ayrı olayları seçemezsiniz. 

|XEvent kategorileri |Olay adı  |
|---------|---------|
|Güvenlik Denetimi    |   Oturum açma denetimi      |
|Güvenlik Denetimi    |   Oturumu kapatma denetimi      |
|Güvenlik Denetimi    |   Denetim sunucusu başlatılır ve duraklar      |
|İlerleme raporları     |   İlerleme raporu başlangıç      |
|İlerleme raporları     |   İlerleme raporu sonu      |
|İlerleme raporları     |   İlerleme raporu geçerli      |
|Sorgular     |  Sorgu başlangıcı       |
|Sorgular     |   Sorgu bitişi      |
|Komutlar     |  Komut başlangıcı       |
|Komutlar     |  Komut sonu       |
|Hatalar & uyarıları     |   Hata      |
|Keşif     |   Bulma bitişi      |
|Bildirim     |    Bildirim     |
|Oturum     |  Oturum başlatma       |
|Kilitler    |  Çözül       |
|Sorgu Işleme     |   VertiPaq, sorgu başlangıcı      |
|Sorgu Işleme     |   VertiPaq bir sorgu bitişi      |
|Sorgu Işleme     |   VertiPaq & sorgu önbelleği eşleşmesi      |
|Sorgu Işleme     |   Doğrudan sorgu başlangıcı      |
|Sorgu Işleme     |  Doğrudan sorgu bitişi       |

### <a name="service"></a>Hizmet

|İşlem adı  |Olduğunda gerçekleşir  |
|---------|---------|
|ResumeServer     |    Bir sunucuyu sürdürür     |
|SuspendServer    |   Bir sunucuyu duraklatma      |
|DeleteServer     |    Sunucu silme     |
|RestartServer    |     Kullanıcı SSMS veya PowerShell aracılığıyla bir sunucuyu yeniden başlatır    |
|GetServerLogFiles    |    Kullanıcı, PowerShell aracılığıyla sunucu günlüğünü dışarı aktarır     |
|Dışarı ExportModel     |   Kullanıcı, Visual Studio 'da aç kullanarak portalda bir modeli dışarı aktarır     |

### <a name="all-metrics"></a>Tüm ölçümler

Ölçüm kategorisi, AzureMetrics tablosuna aynı [Sunucu ölçümlerini](analysis-services-monitor.md#server-metrics) günlüğe kaydeder. Sorgu [ölçeği](analysis-services-scale-out.md) genişletme kullanıyorsanız ve her bir okuma çoğaltması için ölçümleri ayırmanız gerekiyorsa, bunun yerine AzureDiagnostics tablosunu kullanın; burada, **OperationName** , **logmetric**öğesine eşittir.

## <a name="setup-diagnostics-logging"></a>Tanılama günlüğünü ayarlama

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) > sunucusu ' nda, sol gezinti bölmesinde **tanılama günlükleri** ' ne ve ardından **tanılamayı aç**' a tıklayın.

    ![Azure portal Azure Cosmos DB için tanılama günlüğünü açın](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **Tanılama ayarları**' nda, aşağıdaki seçenekleri belirtin: 

    * **Ad**. Oluşturmak günlükleri için bir ad girin.

    * **Bir depolama hesabına arşivleme**. Bu seçeneği kullanmak için, bağlanmak üzere var olan bir depolama hesabının olması gerekir. Bkz. [depolama hesabı oluşturma](../storage/common/storage-create-storage-account.md). Yönergeleri izleyerek Kaynak Yöneticisi, genel amaçlı bir hesap oluşturun ve ardından portalda bu sayfaya dönüp depolama hesabınızı seçin. Yeni oluşturulan depolama hesaplarının açılan menüde görünmesi birkaç dakika sürebilir.
    * **Bir olay hub 'ına akış**. Bu seçeneği kullanmak için, bağlanmak için mevcut bir olay hub 'ı ad alanı ve Olay Hub 'ı gerekir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Event Hubs ad alanı ve Olay Hub 'ı oluşturma](../event-hubs/event-hubs-create.md). Ardından, Olay Hub 'ı ad alanını ve ilke adını seçmek için portalda bu sayfaya geri dönün.
    * **Azure izleyici 'ye (Log Analytics çalışma alanı) gönderin**. Bu seçeneği kullanmak için, var olan bir çalışma alanını kullanın ya da portalda [Yeni bir çalışma alanı kaynağı oluşturun](../azure-monitor/learn/quick-create-workspace.md) . Günlüklerinizi görüntüleme hakkında daha fazla bilgi için bu makaledeki [Log Analytics çalışma alanındaki günlükleri görüntüle](#view-logs-in-log-analytics-workspace) bölümüne bakın.

    * **Altyapı**. XEvents 'i günlüğe kaydetmek için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, tanılama günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.
    * **Hizmeti**. Hizmet düzeyi olaylarını günlüğe kaydetmek için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, tanılama günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.
    * **Ölçümler**. Ayrıntılı verileri [ölçümlerde](analysis-services-monitor.md#server-metrics)depolamak için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, tanılama günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.

3. **Kaydet** düğmesine tıklayın.

    "\<çalışma alanı adı için tanılama güncellenemedi > bildiren bir hata alırsanız. Abonelik \<abonelik kimliği > Microsoft. Insights 'ı kullanmak için kayıtlı değil. " hesabı kaydetmek için [sorun giderme Azure tanılama](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) yönergeleri izleyin ve bu yordamı yeniden deneyin.

    Tanılama günlüklerinizin gelecekte herhangi bir noktada nasıl kaydedildiğini değiştirmek istiyorsanız, ayarları değiştirmek için bu sayfaya geri dönebilirsiniz.

### <a name="powershell"></a>PowerShell

Size başlamanıza yönelik temel komutlar aşağıda verilmiştir. PowerShell kullanarak bir depolama hesabına günlük kaydı ayarlamaya yönelik adım adım yardım istiyorsanız, bu makalenin ilerleyen kısımlarında yer alan öğreticiye bakın.

PowerShell kullanarak ölçümleri ve tanılama günlüğünü etkinleştirmek için aşağıdaki komutları kullanın:

- Bir depolama hesabında tanılama günlüklerinin depolanmasını etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Depolama hesabı KIMLIĞI, günlükleri göndermek istediğiniz depolama hesabının kaynak KIMLIĞIDIR.

- Tanılama günlüklerinin bir olay hub 'ına akışını etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus kural KIMLIĞI Şu biçimdeki bir dizedir:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Tanılama günlüklerini bir Log Analytics çalışma alanına göndermeyi etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Aşağıdaki komutu kullanarak Log Analytics çalışma alanınızın kaynak KIMLIĞINI elde edebilirsiniz:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Birden çok çıkış seçeneğini etkinleştirmek için bu parametreleri birleştirebilirsiniz.

### <a name="rest-api"></a>REST API

[Azure izleyici REST API kullanarak tanılama ayarlarını değiştirmeyi](https://docs.microsoft.com/rest/api/monitor/)öğrenin. 

### <a name="resource-manager-template"></a>Resource Manager şablonu

[Kaynak Yöneticisi şablonu kullanarak, kaynak oluşturma sırasında tanılama ayarlarını nasıl etkinleştireceğinizi](../azure-monitor/platform/diagnostic-settings-template.md)öğrenin. 

## <a name="manage-your-logs"></a>Günlüklerinizi yönetme

Günlükler genellikle günlüğe kaydetme kurulumu için birkaç saat içinde kullanılabilir. Depolama hesabınızdaki günlüklerinizi yönetmek size bağlıdır:

* Günlüklerinize erişebilecek kişileri kısıtlayarak güvenliklerini sağlamak için standart Azure erişim denetimi yöntemlerini kullanın.
* Artık depolama hesabınızda tutmak istemediğiniz günlükleri silin.
* Depolama hesabınızdan eski günlüklerin silindiği için bir bekletme dönemi ayarladığınızdan emin olun.

## <a name="view-logs-in-log-analytics-workspace"></a>Log Analytics çalışma alanındaki günlükleri görüntüleme

Ölçümler ve sunucu olayları, yan yana analize yönelik Log Analytics çalışma alanı kaynağınızın xEvents ile tümleşiktir. Log Analytics çalışma alanı, mimariniz genelinde tanılama günlüğü verilerinin bir bütünsel görünümünü sağlayan diğer Azure hizmetlerinden olayları alacak şekilde de yapılandırılabilir.

Tanılama verilerinizi görüntülemek için, Log Analytics çalışma alanında, sol menüden **Günlükler** ' i açın.

![Azure portal günlük arama seçenekleri](./media/analysis-services-logging/aas-logging-open-log-search.png)

Sorgu tasarımcısında, **Logmanagement** > **AzureDiagnostics**' ı genişletin. AzureDiagnostics, altyapı ve hizmet olaylarını içerir. Bir sorgunun açık bir şekilde oluşturulduğunu fark edin. EventClass\_s alanı xEvent adlarını içerir, bu da şirket içi günlüğe kaydetme için xEvents kullandıysanız tanıdık görünebilir. **EventClass\_s** ' e veya olay adlarından birine tıklayın ve Log Analytics çalışma alanı bir sorgu oluşturma devam eder. Sorgularınızı daha sonra yeniden kullanmak üzere kaydettiğinizden emin olun.

### <a name="example-queries"></a>Örnek sorgular

#### <a name="example-1"></a>Örnek 1

Aşağıdaki sorgu, bir model veritabanı ve sunucusu için her sorgu bitiş/yenileme bitiş olayı için süreleri döndürür. Ölçeği ölçeklenirse, çoğaltma numarası ServerName_s ' ye eklendiğinden sonuçlar çoğaltma tarafından bölünür. RootActivityId_g tarafından gruplandırma, Azure Tanılama REST API alınan satır sayısını azaltır ve [Log Analytics oran sınırları](https://dev.loganalytics.io/documentation/Using-the-API/Limits)bölümünde açıklandığı gibi limitlerin içinde kalmalarına yardımcı olur.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Örnek 2

Aşağıdaki sorgu, bir sunucu için bellek ve QPU kullanımını döndürür. Ölçeği ölçeklenirse, çoğaltma numarası ServerName_s ' ye eklendiğinden sonuçlar çoğaltma tarafından bölünür.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Örnek 3

Aşağıdaki sorgu, bir sunucu için Okuma/sn Analysis Services altyapısı performans sayaçlarını döndürür.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Kullanabileceğiniz yüzlerce sorgu vardır. Sorgular hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorgularını kullanmaya başlama](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>PowerShell kullanarak günlüğü açma

Bu hızlı öğreticide, Analysis Service sunucunuz ile aynı abonelikte ve kaynak grubunda bir depolama hesabı oluşturursunuz. Ardından, yeni depolama hesabına çıkış göndererek tanılama günlüğünü açmak için set-AzDiagnosticSetting ' i kullanabilirsiniz.

### <a name="prerequisites"></a>Ön koşullar
Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklara sahip olmanız gerekir:

* Mevcut bir Azure Analysis Services sunucusu. Sunucu kaynağı oluşturma hakkında yönergeler için, bkz. [Azure Portal sunucu oluşturma](analysis-services-create-server.md)veya [PowerShell kullanarak Azure Analysis Services sunucusu oluşturma](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a>aboneliklerinize bağlanmak </a>

Bir Azure PowerShell oturumu başlatın ve aşağıdaki komutla Azure hesabınızda oturum açın:  

```powershell
Connect-AzAccount
```

Açılır tarayıcı penceresinde Azure hesabı kullanıcı adınızı ve parolanızı girin. Azure PowerShell bu hesapla ilişkili tüm abonelikleri alır ve varsayılan olarak birinciyi kullanır.

Birden çok aboneliğiniz varsa Azure Anahtar Kasanızı oluşturmak için kullanılan belirli bir tanesini belirtmeniz gerekebilir. Hesabınız için abonelikleri görmek üzere aşağıdakini yazın:

```powershell
Get-AzSubscription
```

Daha sonra, günlük kaydı yaptığınız Azure Analysis Services hesapla ilişkili aboneliği belirtmek için şunu yazın:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Hesabınızla ilişkili birden fazla aboneliğiniz varsa, aboneliğin belirtilmesi önemlidir.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Günlükleriniz için yeni bir depolama hesabı oluşturma

Günlükleriniz için mevcut bir depolama hesabını kullanabilirsiniz. Bu, sunucunuz ile aynı abonelikte yer verilir. Bu öğreticide, Analysis Services günlüklerine adanmış yeni bir depolama hesabı oluşturursunuz. Daha kolay hale getirmek için, depolama hesabı ayrıntılarını **sa**adlı bir değişkende depolarsınız.

Ayrıca, Analysis Services sunucunuzu içeren kaynakla aynı kaynak grubunu da kullanırsınız. `awsales_resgroup`, `awsaleslogs`ve `West Central US` değerlerini kendi değerlerinizle değiştirin:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Günlüklerinizin sunucu hesabını tanımla

Hesap adını hesap adlı bir değişkene **ayarlayın, burada**resourceName hesabın adıdır.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Günlü kaydını etkinleştir

Günlüğe kaydetmeyi etkinleştirmek için, set-AzDiagnosticSetting cmdlet 'ini yeni depolama hesabı, sunucu hesabı ve kategori değişkenleriyle birlikte kullanın. Aşağıdaki komutu çalıştırın, **-Enabled** bayrağını **$true**olarak ayarlar:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Çıkış şu örneğe benzer olmalıdır:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Bu çıktı, günlüğe kaydetme işleminin artık sunucu için etkinleştirildiğini onaylar ve depolama hesabına bilgi kaydedilir.

Günlüklerinizin bekletme ilkesini, eski günlüklerin otomatik olarak silinmesi için de ayarlayabilirsiniz. Örneğin, **$true**için **-RetentionEnabled** bayrağını kullanarak bekletme ilkesi ayarlayın ve **-retentionındays** parametresini **90**olarak ayarlayın. 90 günden daha eski Günlükler otomatik olarak silinir.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Kaynak tanılama günlüğü](../azure-monitor/platform/resource-logs-overview.md)hakkında daha fazla bilgi edinin.

PowerShell Yardımı 'nda [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) bölümüne bakın.
