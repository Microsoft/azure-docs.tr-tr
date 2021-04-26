---
title: Azure Etkinlik Günlüğü
description: Azure etkinlik günlüğünü görüntüleyin ve Azure Izleyici günlüklerine, Azure Event Hubs ve Azure depolama 'ya gönderin.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.openlocfilehash: 7583b4037d350b9190d6eae30c28b907b1d41d86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722863"
---
# <a name="azure-activity-log"></a>Azure Etkinlik Günlüğü
Etkinlik günlüğü Azure'da abonelik düzeyi olaylarıyla ilgili içgörüler sağlayan bir [platform günlüğüdür](./platform-logs-overview.md). Bir kaynağın ne zaman değiştirildiği veya sanal makinenin ne zaman başlatıldığı gibi bilgileri içerir. Etkinlik günlüğünü Azure portal görüntüleyebilir veya PowerShell ve CLı ile girdileri alabilirsiniz. Ek işlevsellik için, etkinlik günlüğü 'nü Azure [Izleyici günlüklerine](../logs/data-platform-logs.md), Azure Event Hubs Azure 'da veya arşivleme Için Azure depolama 'ya iletecek şekilde göndermek üzere bir tanılama ayarı oluşturmanız gerekir. Bu makale, etkinlik günlüğünü görüntüleme ve farklı hedeflere gönderme hakkında ayrıntılar sağlar.

Tanılama ayarı oluşturma hakkında ayrıntılı bilgi için bkz. [Platform günlüklerini ve ölçümleri farklı hedeflere göndermek için Tanılama ayarları oluşturma](./diagnostic-settings.md) .

> [!NOTE]
> Etkinlik Günlüğündeki girdiler sistem tarafından oluşturulur ve değiştirilemez veya silinemez.

## <a name="view-the-activity-log"></a>Etkinlik günlüğünü görüntüleme
Azure portaldaki menülerin çoğundan Etkinlik günlüğüne erişebilirsiniz. Günlüğün başlangıçtaki filtresi, günlüğü hangi menüden açtığınıza göre belirlenir. Bunu **izleyici** menüsünden açarsanız, abonelik üzerinde tek filtre olur. Bunu bir kaynağın menüsünden açarsanız, filtre o kaynağa ayarlanır. Tüm diğer girdileri görüntülemek için filtreyi her zaman değiştirebilirsiniz. Filtreye ek özellikler eklemek için **Filtre Ekle** ' ye tıklayın.

![Etkinlik günlüğünü görüntüle](./media/activity-log/view-activity-log.png)

Etkinlik günlüğü kategorilerinin açıklaması için bkz. [Azure etkinlik günlüğü olay şeması](activity-log-schema.md#categories).

## <a name="download-the-activity-log"></a>Etkinlik günlüğünü indirin
Geçerli görünümdeki olayları indirmek için **CSV olarak indir** ' i seçin.

![Etkinlik günlüğünü indir](media/activity-log/download-activity-log.png)

### <a name="view-change-history"></a>Değişiklik geçmişini görüntüle

Bazı olaylar için, bu olay süresi boyunca gerçekleşen değişiklikleri gösteren değişiklik geçmişini görüntüleyebilirsiniz. Daha ayrıntılı olarak görmek istediğiniz etkinlik günlüğünden bir olay seçin. Söz konusu olayla ilişkili tüm değişiklikleri görüntülemek için **değişiklik geçmişi (Önizleme)** sekmesini seçin.

![Olay için değişiklik geçmişi listesi](media/activity-log/change-history-event.png)

Olayla ilişkili herhangi bir değişiklik varsa, seçebileceğiniz değişikliklerin bir listesini görürsünüz. Bu, **değişiklik geçmişi (Önizleme)** sayfasını açar. Bu sayfada, kaynakta yapılan değişiklikleri görürsünüz. Aşağıdaki örnekte, yalnızca VM 'nin boyutlarının değiştiğini, ancak önceki VM boyutunun değişiklikten önce ne olduğunu ve ne değiştiği hakkında bilgi alabilirsiniz. Değişiklik geçmişi hakkında daha fazla bilgi edinmek için bkz. [kaynak değişikliklerini alma](../../governance/resource-graph/how-to/get-resource-changes.md).

![Farkları gösteren değişiklik geçmişi sayfası](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Etkinlik günlüğü olaylarını almak için diğer Yöntemler
Etkinlik günlüğü olaylarına aşağıdaki yöntemleri kullanarak da erişebilirsiniz.

- PowerShell 'den etkinlik günlüğünü almak için [Get-AzLog](/powershell/module/az.monitor/get-azlog) cmdlet 'ini kullanın. Bkz. [Azure Izleyici PowerShell örnekleri](../powershell-samples.md#retrieve-activity-log).
- CLı 'dan etkinlik günlüğünü almak için [az Monitor Activity-Log](/cli/azure/monitor/activity-log) kullanın.  Bkz. [Azure IZLEYICI CLI örnekleri](../cli-samples.md#view-activity-log).
- REST istemcisinden etkinlik günlüğünü almak için [Azure izleyici REST API](/rest/api/monitor/) kullanın. 


## <a name="send-to-log-analytics-workspace"></a>Log Analytics çalışma alanına gönderme
 Aşağıdakiler de dahil olmak üzere [Azure Izleyici günlüklerinin](../logs/data-platform-logs.md) özelliklerini etkinleştirmek için etkinlik günlüğünü bir Log Analytics çalışma alanına gönderin:

- Etkinlik günlüğü verilerinin Azure Izleyici tarafından toplanan diğer izleme verileriyle ilişkilendirilmesi.
- Birden çok Azure aboneliği ve kiracısından günlük girişlerini analiz için tek bir konuma birleştirin.
- Karmaşık analizler gerçekleştirmek ve etkinlik günlüğü girişleriyle ilgili ayrıntılı Öngörüler elde etmek için günlük sorgularını kullanın.
- Daha karmaşık uyarı mantığına izin veren etkinlik girişleriyle birlikte günlük uyarılarını kullanın.
- Etkinlik günlüğü girdilerini 90 günden daha uzun bir süre için depolayın.
- Log Analytics çalışma alanında depolanan etkinlik günlüğü verileri için veri alma ücreti yok.
- Log Analytics çalışma alanında depolanan etkinlik günlüğü verileri için 90 güne kadar veri saklama ücreti ücretlendirmez.

Etkinlik günlüğünü bir Log Analytics çalışma alanına göndermek için [bir tanılama ayarı oluşturun](./diagnostic-settings.md) . Etkinlik günlüğünü herhangi bir tek abonelikten beş çalışma alanına gönderebilirsiniz. Kiracılar arasında günlükleri toplamak için [Azure Lighthouse](../../lighthouse/index.yml) gerekir.

Log Analytics çalışma alanındaki etkinlik günlüğü verileri, [Log Analytics](../logs/log-analytics-tutorial.md)bir [günlük sorgusuyla](../logs/log-query-overview.md) alabileceğiniz, *AzureActivity* adlı bir tabloda depolanır. Bu tablonun yapısı, [günlük girişinin kategorisine](activity-log-schema.md)bağlı olarak değişir. Tablo özelliklerinin açıklaması için bkz. [Azure izleyici veri başvurusu](/azure/azure-monitor/reference/tables/azureactivity).

Örneğin, her bir kategorinin etkinlik günlüğü kayıtlarının sayımını görüntülemek için aşağıdaki sorguyu kullanın.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Yönetim kategorisindeki tüm kayıtları almak için aşağıdaki sorguyu kullanın.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs gönder
Azure 'un dışında, örneğin bir üçüncü taraf SıEM veya diğer Log Analytics çözümlerine giriş göndermek için etkinlik günlüğünü Azure Event Hubs 'a gönderin. Olay Hub 'larından etkinlik günlüğü olayları `records` , her yükündeki kayıtları içeren bir öğesi Ile JSON biçiminde kullanılır. Şema kategoriye bağlıdır ve [depolama hesabı ve Olay Hub 'Larından şema](activity-log-schema.md)bölümünde açıklanır.

Aşağıda bir etkinlik günlüğü için Event Hubs örnek çıktı verileri verilmiştir:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Azure depolama 'ya gönder
Günlük verilerinizi denetim, statik analiz veya yedekleme için 90 günden daha uzun bir süre içinde bekletmek istiyorsanız, etkinlik günlüğünü bir Azure depolama hesabına gönderin. Yalnızca 90 gün boyunca olaylarınızı tutmanız gerekiyorsa veya daha az etkinlik günlüğü olayları, 90 gün boyunca Azure platformunda korunduğundan, bir depolama hesabına arşiv ayarlamanız gerekmez.

Etkinlik günlüğü 'nü Azure 'a gönderdiğinizde, bir olay meydana geldiğinde depolama hesabında bir depolama kapsayıcısı oluşturulur. Kapsayıcıdaki Bloblar aşağıdaki adlandırma kuralını kullanır:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Örneğin, belirli bir blob şuna benzer bir ada sahip olabilir:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Her PT1H.json blobu, blob URL’sinde belirtilen saat (örneğin, h=12) içinde gerçekleşen bir JSON olay blobu içerir. Mevcut saat boyunca, olaylar meydana geldikçe PT1H.json dosyasına eklenir. Kaynak günlüğü olayları saat başına ayrı blob 'lara bölündüğü için, dakika değeri (e = 00) her zaman 00 ' dır.

Her olay, ortak bir en üst düzey şemayı kullanan, ancak her kategori için  [etkinlik günlüğü şeması](activity-log-schema.md)' nda açıklandığı gibi benzersiz olan aşağıdaki biçimdeki PT1H.jsdosya üzerinde depolanır.

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Eski koleksiyon yöntemleri
Bu bölümde, tanılama ayarlarından önce kullanılan etkinlik günlüğünü toplamaya yönelik eski yöntemler açıklanmaktadır. Bu yöntemleri kullanıyorsanız, kaynak günlükleriyle daha iyi işlevsellik ve tutarlılık sağlayan tanılama ayarlarına geçiş yapmayı göz önünde bulundurmanız gerekir.

### <a name="log-profiles"></a>Günlük profilleri
Günlük profilleri, Azure depolama veya Olay Hub 'larına etkinlik günlüğü göndermek için eski yöntemdir. Bir günlük profiliyle çalışmaya devam etmek veya bir tanılama ayarına geçiş hazırlığı sırasında devre dışı bırakmak için aşağıdaki yordamı kullanın.

1. Azure portal **Azure izleyici** menüsünde **etkinlik günlüğü**' nü seçin.
3. **Tanılama ayarları**'na tıklayın.

   ![Tanılama ayarları](media/activity-log/diagnostic-settings.png)

4. Eski deneyimin mor başlığına tıklayın.

    ![Eski deneyim](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>PowerShell kullanarak günlük profilini yapılandırma

Zaten bir günlük profili varsa, önce mevcut günlük profilini kaldırmalı ve ardından yeni bir tane oluşturmanız gerekir.

1. `Get-AzLogProfile`Bir günlük profilinin mevcut olup olmadığını belirlemek için kullanın.  Bir günlük profili varsa, *Name* özelliğine göz önünde varın.

1. `Remove-AzLogProfile` *Ad* özelliğinden değeri kullanarak günlük profilini kaldırmak için kullanın.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`Yeni bir günlük profili oluşturmak için kullanın:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Özellik | Gerekli | Açıklama |
    | --- | --- | --- |
    | Ad |Yes |Günlük profilinizin adı. |
    | Storageaccountıd |No |Etkinlik günlüğünün kaydedilmesi gereken depolama hesabının kaynak KIMLIĞI. |
    | Servicebusruleıd |No |İçinde Olay Hub 'larının oluşturulmasını istediğiniz Service Bus ad alanı için kural KIMLIĞI Service Bus. Bu şu biçimde bir dizedir: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Konum |Evet |Etkinlik günlüğü olaylarını toplamak istediğiniz bölgelerin virgülle ayrılmış listesi. |
    | Retentionındays |Yes |Depolama hesabında olayların saklanacağı gün sayısı (1 ile 365 arasında). Sıfır değeri, günlükleri süresiz olarak depolar. |
    | Kategori |No |Toplanması gereken olay kategorilerinin virgülle ayrılmış listesi. Olası değerler _yazma_, _silme_ ve _eylem_. |

### <a name="example-script"></a>Örnek betik
Aşağıda, etkinlik günlüğünü hem depolama hesabına hem de Olay Hub 'ına yazan bir günlük profili oluşturmak için örnek bir PowerShell betiği verilmiştir.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLı kullanarak günlük profilini yapılandırma

Zaten bir günlük profili varsa, önce mevcut günlük profilini kaldırmanız ve ardından yeni bir günlük profili oluşturmanız gerekir.

1. `az monitor log-profiles list`Bir günlük profilinin mevcut olup olmadığını belirlemek için kullanın.
2. `az monitor log-profiles delete --name "<log profile name>` *Ad* özelliğinden değeri kullanarak günlük profilini kaldırmak için kullanın.
3. `az monitor log-profiles create`Yeni bir günlük profili oluşturmak için kullanın:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Özellik | Gerekli | Açıklama |
    | --- | --- | --- |
    | name |Yes |Günlük profilinizin adı. |
    | depolama hesabı-kimliği |Yes |Etkinlik günlüklerinin kaydedileceği depolama hesabının kaynak KIMLIĞI. |
    | yerlerini |Yes |Etkinlik günlüğü olaylarını toplamak istediğiniz bölgelerin boşlukla ayrılmış listesi. Kullanarak aboneliğiniz için tüm bölgelerin bir listesini görüntüleyebilirsiniz `az account list-locations --query [].name` . |
    | gün |Yes |Olayların saklanacağı gün sayısı, 1 ile 365 arasında. Sıfır değeri günlükleri süresiz olarak depolar (süresiz).  Sıfır ise, etkin parametre false olarak ayarlanmalıdır. |
    |enabled | Yes |Doğru veya Yanlış.  Bekletme ilkesini etkinleştirmek veya devre dışı bırakmak için kullanılır.  True ise Days parametresi 0 ' dan büyük bir değer olmalıdır.
    | kategoriler |Yes |Toplanması gereken olay kategorilerinin boşlukla ayrılmış listesi. Olası değerler yazma, silme ve eylem. |


### <a name="log-analytics-workspace"></a>Log Analytics çalışma alanı
Etkinlik günlüğünü bir Log Analytics çalışma alanına göndermek için eski yöntem, günlüğü çalışma alanı yapılandırmasında bağyor. 

1. Azure portal **Log Analytics çalışma alanları** menüsünde, etkinlik günlüğünü toplamak için çalışma alanını seçin.
1. Çalışma alanının menüsündeki **çalışma alanı veri kaynakları** bölümünde **Azure etkinlik günlüğü**' nü seçin.
1. Bağlanmak istediğiniz aboneliğe tıklayın.

    ![Ekran görüntüsünde bir Azure etkinlik günlüğü seçiliyken Log Analytics çalışma alanı görüntülenir.](media/activity-log/workspaces.png)

2. Abonelikteki etkinlik günlüğünü seçili çalışma alanına bağlamak için **Bağlan** ' a tıklayın. Abonelik zaten başka bir çalışma alanına bağlıysa bağlantıyı kesmek için önce **bağlantıyı kes** ' e tıklayın.

    ![Çalışma alanlarını bağlama](media/activity-log/connect-workspace.png)


Ayarı devre dışı bırakmak için aynı yordamı gerçekleştirin ve aboneliği çalışma alanından kaldırmak için **bağlantıyı kes** ' e tıklayın.

### <a name="data-structure-changes"></a>Veri yapısı değişiklikleri
Tanılama ayarları, etkinlik günlüğünü *AzureActivity* tablosunun yapısına bazı değişikliklerle birlikte göndermek için kullanılan eski yöntemle aynı verileri gönderir.

Aşağıdaki tablodaki sütunlar güncelleştirilmiş şemada kullanımdan kaldırılmıştır. Hala *AzureActivity* içinde bulunur, ancak hiçbir veri içermez. Bu sütunların yerini değiştirme yeni değil, ancak kullanım dışı sütunuyla aynı verileri içerir. Farklı bir biçimde olduklarından, bunları kullanan günlük sorgularını değiştirmeniz gerekebilir. 

| Kullanım dışı sütun | Değiştirme sütunu |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| Etkinlik alt durumu | ActivitySubstatusValue |
| Kategori          | CategoryValue          |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Bazı durumlarda bu sütunlardaki değerler tümüyle büyük harf olabilir. Bu sütunları içeren bir sorgunuz varsa, büyük/küçük harfe duyarsız bir karşılaştırma yapmak için [=~ işlecini](/azure/kusto/query/datatypes-string-operators) kullanmalısınız.

Güncelleştirilmiş şemada *AzureActivity* 'e aşağıdaki sütun eklenmiştir:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Etkinlik Günlüğü Analizi izleme çözümü
Azure Log Analytics izleme çözümü yakında kullanımdan kalkmış ve Log Analytics çalışma alanındaki güncelleştirilmiş şemayı kullanarak bir çalışma kitabı tarafından değiştirilmeyecektir. Hala etkinse çözümü kullanabilirsiniz, ancak yalnızca eski ayarları kullanarak etkinlik günlüğü topluyorsanız kullanılabilir. 



### <a name="use-the-solution"></a>Çözümü kullanma
**İzleme çözümlerine Azure Portal izleme menüsünden erişilir** . **Genel bakış** sayfasını çözüm kutucukları ile açmak için **Öngörüler** bölümünde **daha fazla** ' yı seçin. **Azure etkinlik günlükleri** kutucuğu, çalışma alanınızdaki **AzureActivity** kayıt sayısının sayısını görüntüler.

![Azure etkinlik günlükleri kutucuğu](media/activity-log/azure-activity-logs-tile.png)


Azure **etkinlik günlükleri görünümünü açmak** için Azure etkinlik **günlükleri** kutucuğuna tıklayın. Görünüm, aşağıdaki tablodaki görselleştirme parçalarını içerir. Her bölümde, belirtilen zaman aralığı için parçaların ölçütlerine uyan 10 ' a kadar öğe listelenir. Bölümün en altında bulunan **Tümünü görüntüle ' ye** tıklayarak eşleşen tüm kayıtları döndüren bir günlük sorgusu çalıştırabilirsiniz.

![Azure etkinlik günlükleri panosu](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Yeni abonelikler için çözümü etkinleştirin
Yakında etkinlik günlüğü Analizi çözümünü Azure portal kullanarak aboneliğinize ekleyemeyeceksiniz. Bir Kaynak Yöneticisi şablonuyla aşağıdaki yordamı kullanarak ekleyebilirsiniz. 

1. Aşağıdaki JSON *öğesini Activitylogtemplate*. JSON adlı bir dosyaya kopyalayın.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Aşağıdaki PowerShell komutlarını kullanarak şablonu dağıtın:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Sonraki adımlar

* [Platform günlüklerine Genel Bakış bölümünü okuyun](./platform-logs-overview.md)
* [Etkinlik günlüğü olay şemasını gözden geçirin](activity-log-schema.md)
* [Diğer hedeflere etkinlik günlükleri göndermek için tanılama ayarı oluştur](./diagnostic-settings.md)
