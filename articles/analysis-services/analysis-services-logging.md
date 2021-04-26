---
title: Azure Analysis Services için tanılama günlüğü | Microsoft Docs
description: Azure Analysis Services sunucunuzu izlemek için günlüğü ayarlamayı açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769220"
---
# <a name="setup-diagnostic-logging"></a>Tanılama günlüğüne kaydetmeyi ayarlama

Herhangi bir Analysis Services çözümünün önemli bir kısmı, sunucularınızın nasıl çalıştığını izlerdir. Azure Analysis Services, Azure Izleyici ile tümleşiktir. [Azure izleyici kaynak günlükleri](../azure-monitor/essentials/platform-logs-overview.md)Ile [Azure depolama](https://azure.microsoft.com/services/storage/)'ya günlükleri izleyip gönderebilir, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)akışa alabilir ve [Azure izleyici günlüklerine](../azure-monitor/overview.md)aktarabilirsiniz.

![Depolama, Event Hubs veya Azure Izleyici günlüklerine kaynak günlüğü](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Günlüğe neler kaydedilir?

**Motor**, **hizmet** ve **ölçüm** kategorilerini seçebilirsiniz.

### <a name="engine"></a>Altyapı

**Motor** seçildiğinde tüm [XEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)günlüğe kaydedilir. Ayrı olayları seçemezsiniz. 

|XEvent kategorileri |Olay adı  |
|---------|---------|
|Güvenlik Denetimi    |   Audit Login      |
|Güvenlik Denetimi    |   Audit Logout      |
|Güvenlik Denetimi    |   Denetim sunucusu başlatılır ve duraklar      |
|İlerleme raporları     |   İlerleme raporu başlangıç      |
|İlerleme raporları     |   İlerleme raporu sonu      |
|İlerleme raporları     |   İlerleme raporu geçerli      |
|Sorgular     |  Query Begin       |
|Sorgular     |   Query End      |
|Komutlar     |  Komut başlangıcı       |
|Komutlar     |  Komut sonu       |
|Hatalar & uyarıları     |   Hata      |
|Bulma     |   Bulma bitişi      |
|Bildirim     |    Bildirim     |
|Oturum     |  Session Initialize       |
|Kilitler    |  Kilitlenme       |
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

Ölçüm kategorisi, AzureMetrics tablosuna aynı [Sunucu ölçümlerini](analysis-services-monitor.md#server-metrics) günlüğe kaydeder. Sorgu [ölçeği](analysis-services-scale-out.md) genişletme kullanıyorsanız ve her bir okuma çoğaltması için ölçümleri ayırmanız gerekiyorsa, bunun yerine AzureDiagnostics tablosunu kullanın; burada, **OperationName** , **logmetric** öğesine eşittir.

## <a name="setup-diagnostics-logging"></a>Tanılama günlüğünü ayarlama

### <a name="azure-portal"></a>Azure portalı

1. [Azure portal](https://portal.azure.com) > sunucusu ' nda, sol gezinti bölmesinde **Tanılama ayarları** ' na tıklayın ve ardından **tanılamayı aç**' a tıklayın.

    ![Azure portal Azure Cosmos DB için kaynak günlüğünü açma](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **Tanılama ayarları** sayfasında aşağıdaki seçenekleri belirtin: 

    * **Ad**. Oluşturulacak Günlükler için bir ad girin.

    * **Bir depolama hesabına arşivleme**. Bu seçeneği kullanmak için, bağlanmak üzere var olan bir depolama hesabının olması gerekir. Bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md). Yönergeleri izleyerek Kaynak Yöneticisi, genel amaçlı bir hesap oluşturun ve ardından portalda bu sayfaya dönüp depolama hesabınızı seçin. Yeni oluşturulan depolama hesaplarının açılan menüde görünmesi birkaç dakika sürebilir.
    * **Bir olay hub 'ına akış**. Bu seçeneği kullanmak için, bağlanmak için mevcut bir olay hub 'ı ad alanı ve Olay Hub 'ı gerekir. Daha fazla bilgi için bkz. [Azure portalı kullanarak Event Hubs ad alanı ve olay hub'ı oluşturma](../event-hubs/event-hubs-create.md). Ardından, Olay Hub 'ı ad alanını ve ilke adını seçmek için portalda bu sayfaya geri dönün.
    * **Azure izleyici 'ye (Log Analytics çalışma alanı) gönderin**. Bu seçeneği kullanmak için, var olan bir çalışma alanını kullanın ya da portalda [Yeni bir çalışma alanı kaynağı oluşturun](../azure-monitor/logs/quick-create-workspace.md) . Günlüklerinizi görüntüleme hakkında daha fazla bilgi için bu makaledeki [Log Analytics çalışma alanındaki günlükleri görüntüle](#view-logs-in-log-analytics-workspace) bölümüne bakın.

    * **Altyapı**. XEvents 'i günlüğe kaydetmek için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, kaynak günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.
    * **Hizmeti**. Hizmet düzeyi olaylarını günlüğe kaydetmek için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, kaynak günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.
    * **Ölçümler**. Ayrıntılı verileri [ölçümlerde](analysis-services-monitor.md#server-metrics)depolamak için bu seçeneği belirleyin. Bir depolama hesabına arşivleme yapıyorsanız, kaynak günlükleri için bekletme dönemini seçebilirsiniz. Günlükler, saklama süresi dolduktan sonra tekrar silinir.

3. **Kaydet**’e tıklayın.

    "Tanılamayı güncelleştirme hatası" belirten bir hata alırsanız \<workspace name> . Abonelik, \<subscription id> Microsoft. Insights 'ı kullanmak için kayıtlı değil. " hesabı kaydetmek için [sorun giderme Azure tanılama](../azure-monitor/essentials/resource-logs.md) yönergeleri izleyin ve bu yordamı yeniden deneyin.

    Kaynak günlüklerinizin gelecekte herhangi bir noktada nasıl kaydedildiğini değiştirmek istiyorsanız, ayarları değiştirmek için bu sayfaya geri dönebilirsiniz.

### <a name="powershell"></a>PowerShell

Size başlamanıza yönelik temel komutlar aşağıda verilmiştir. PowerShell kullanarak bir depolama hesabına günlük kaydı ayarlamaya yönelik adım adım yardım istiyorsanız, bu makalenin ilerleyen kısımlarında yer alan öğreticiye bakın.

PowerShell kullanarak ölçümleri ve kaynak günlüğünü etkinleştirmek için aşağıdaki komutları kullanın:

- Bir depolama hesabında kaynak günlüklerinin depolanmasını etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Depolama hesabı kimliği, günlükleri göndermek istediğiniz depolama hesabının kaynak kimliğidir.

- Kaynak günlüklerinin bir olay hub 'ına akışını etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus kural kimliği, şu biçime sahip bir dizedir:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Kaynak günlüklerinin Log Analytics çalışma alanına gönderilmesini etkinleştirmek için şu komutu kullanın:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Log Analytics çalışma alanının kaynak kimliğini almak için aşağıdaki komutu kullanabilirsiniz:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Bu parametreleri bir arada kullanarak birden fazla çıkış seçeneği oluşturabilirsiniz.

### <a name="rest-api"></a>REST API'si

[Azure İzleyici REST API'sini kullanarak tanılama ayarlarını değiştirmeyi](/rest/api/monitor/) öğrenin. 

### <a name="resource-manager-template"></a>Resource Manager şablonu

[Resource Manager şablonu kullanarak kaynak oluşturma sırasında tanılama ayarını etkinleştirmeyi](../azure-monitor/essentials/resource-manager-diagnostic-settings.md) öğrenin. 

## <a name="manage-your-logs"></a>Günlüklerinizi yönetme

Günlükler genellikle günlüğe kaydetme kurulumu için birkaç saat içinde kullanılabilir. Depolama hesabınızdaki günlükleri istediğiniz şekilde yönetebilirsiniz:

* Günlüklerinize erişebilecek kişileri kısıtlayarak güvenliklerini sağlamak için standart Azure erişim denetimi yöntemlerini kullanın.
* Depolama hesabınızda tutmak istemediğiniz günlükleri silebilirsiniz.
* Depolama hesabınızdan eski günlüklerin silindiği için bir bekletme dönemi ayarladığınızdan emin olun.

## <a name="view-logs-in-log-analytics-workspace"></a>Log Analytics çalışma alanındaki günlükleri görüntüleme

Ölçümler ve sunucu olayları, yan yana analize yönelik Log Analytics çalışma alanı kaynağınızın xEvents ile tümleşiktir. Log Analytics çalışma alanı, mimariniz genelinde tanılama günlüğü verilerinin bir bütünsel görünümünü sağlayan diğer Azure hizmetlerinden olayları alacak şekilde de yapılandırılabilir.

Tanılama verilerinizi görüntülemek için, Log Analytics çalışma alanında, sol menüden **Günlükler**  ' i açın.

![Azure portal günlük arama seçenekleri](./media/analysis-services-logging/aas-logging-open-log-search.png)

Sorgu tasarımcısında, **logmanagement**  >  **AzureDiagnostics**' ı genişletin. AzureDiagnostics, altyapı ve hizmet olaylarını içerir. Bir sorgunun açık bir şekilde oluşturulduğunu fark edin. EventClass \_ s alanı xEvent adlarını içerir, bu da şirket içi günlüğe kaydetme Için xEvents kullandıysanız tanıdık görünebilir. **EventClass \_ s** ' e veya olay adlarından birine tıklayın ve Log Analytics çalışma alanı bir sorgu oluşturma devam eder. Sorgularınızı daha sonra kullanmak üzere kaydetmeyi unutmayın.

### <a name="example-queries"></a>Örnek sorgular

#### <a name="example-1"></a>Örnek 1

Aşağıdaki sorgu, bir model veritabanı ve sunucusu için her sorgu bitiş/yenileme bitiş olayı için süreleri döndürür. Ölçeği ölçeklenirse, çoğaltma numarası ServerName_s eklendiğinden sonuçlar çoğaltma tarafından bölünür. RootActivityId_g gruplama, Azure Tanılama REST API alınan satır sayısını azaltır ve [Log Analytics hız sınırları](https://dev.loganalytics.io/documentation/Using-the-API/Limits)bölümünde açıklandığı gibi limitlerin içinde kalmalarına yardımcı olur.

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

Aşağıdaki sorgu, bir sunucu için bellek ve QPU kullanımını döndürür. Ölçeği ölçeklenirse, çoğaltma numarası ServerName_s eklendiğinden sonuçlar çoğaltma tarafından bölünür.

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

Kullanabileceğiniz yüzlerce sorgu vardır. Sorgular hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorgularını kullanmaya başlama](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>PowerShell kullanarak günlüğü açma

Bu hızlı öğreticide, Analysis Service sunucunuz ile aynı abonelikte ve kaynak grubunda bir depolama hesabı oluşturursunuz. Ardından, yeni depolama hesabına çıkış göndermek için tanılama günlüğünü açmak üzere Set-AzDiagnosticSetting kullanırsınız.

### <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklara sahip olmanız gerekir:

* Mevcut bir Azure Analysis Services sunucusu. Sunucu kaynağı oluşturma hakkında yönergeler için, bkz. [Azure Portal sunucu oluşturma](analysis-services-create-server.md)veya [PowerShell kullanarak Azure Analysis Services sunucusu oluşturma](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Aboneliklerinize bağlanma

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

Günlükleriniz için mevcut bir depolama hesabını kullanabilirsiniz. Bu, sunucunuz ile aynı abonelikte yer verilir. Bu öğreticide, Analysis Services günlüklerine adanmış yeni bir depolama hesabı oluşturursunuz. Daha kolay hale getirmek için, depolama hesabı ayrıntılarını **sa** adlı bir değişkende depolarsınız.

Ayrıca, Analysis Services sunucunuzu içeren kaynakla aynı kaynak grubunu da kullanırsınız. `awsales_resgroup`, `awsaleslogs` Ve değerlerini `West Central US` kendi değerlerinizle değiştirin:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Günlüklerinizin sunucu hesabını tanımla

Hesap adını hesap adlı bir değişkene **ayarlayın, burada** resourceName hesabın adıdır.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Günlü kaydını etkinleştir

Günlüğe kaydetmeyi etkinleştirmek için, yeni depolama hesabı, sunucu hesabı ve kategori değişkenleriyle birlikte Set-AzDiagnosticSetting cmdlet 'ini kullanın. Aşağıdaki komutu çalıştırın, **-Enabled** bayrağını **$true** olarak ayarlar:

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

Günlüklerinizin bekletme ilkesini, eski günlüklerin otomatik olarak silinmesi için de ayarlayabilirsiniz. Örneğin, **$true** için **-RetentionEnabled** bayrağını kullanarak bekletme ilkesi ayarlayın ve **-retentionındays** parametresini **90** olarak ayarlayın. 90 günden daha eski Günlükler otomatik olarak silinir.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure izleyici kaynak günlüğü](../azure-monitor/essentials/platform-logs-overview.md)hakkında daha fazla bilgi edinin.

PowerShell Yardımı 'nda [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) bölümüne bakın.
