---
title: Log Analytics çalışma alanı oluşturmak ve yapılandırmak için PowerShell 'i kullanma | Microsoft Docs
description: Azure Izleyici 'de Log Analytics çalışma alanları, şirket içi veya bulut altyapınızdaki sunuculardaki verileri depolar. Azure tanılama tarafından oluşturulduğunda, Azure Storage 'dan makine verileri toplayabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 9d5bbaf02798c0fd87c40f1d952db19aac7b0b7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932088"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>PowerShell kullanarak Azure Izleyici 'de Log Analytics çalışma alanını yönetme

[Log Analytics PowerShell cmdlet 'lerini](https://docs.microsoft.com/powershell/module/az.operationalinsights/) , Azure izleyici 'deki bir Log Analytics çalışma alanında bir komut satırından veya bir betiğin parçası olarak çeşitli işlevler gerçekleştirmek için kullanabilirsiniz.  PowerShell ile gerçekleştirebileceğiniz görevlere örnekler şunlardır:

* Çalışma alanı oluşturma
* Çözüm ekleme veya kaldırma
* Kaydedilmiş aramaları içeri ve dışarı aktarma
* Bilgisayar grubu oluşturma
* Windows aracısının yüklü olduğu bilgisayarlardan IIS günlükleri toplamayı etkinleştir
* Linux ve Windows bilgisayarlarından performans sayaçlarını toplayın
* Linux bilgisayarlarda Syslog 'tan olay topla
* Windows olay günlüklerinden olay topla
* Özel olay günlüklerini topla
* Log Analytics aracısını bir Azure sanal makinesine ekleme
* Log Analytics 'i Azure tanılama kullanılarak toplanan verileri dizinleyecek şekilde yapılandırma

Bu makale, PowerShell 'den gerçekleştirebileceğiniz bazı işlevleri gösteren iki kod örneği sunar.  Diğer işlevler için [Log Analytics PowerShell cmdlet başvurusuna](https://docs.microsoft.com/powershell/module/az.operationalinsights/) başvurabilirsiniz.

> [!NOTE]
> Log Analytics daha önce Işletimsel içgörüler olarak adlandırılmıştı. Bu, cmdlet 'lerde kullanılan addır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu örnekler, az. Operationalınsights modülünün Version 1.0.0 veya üzeri sürümleriyle çalışır.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics çalışma alanı oluşturma ve yapılandırma
Aşağıdaki betik örneği, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Çalışma alanı oluşturma
2. Kullanılabilir çözümleri listeleyin
3. Çalışma alanına çözümler ekleme
4. Kaydedilmiş aramaları içeri aktar
5. Kaydedilmiş aramaları dışarı aktar
6. Bilgisayar grubu oluşturma
7. Windows aracısının yüklü olduğu bilgisayarlardan IIS günlükleri toplamayı etkinleştir
8. Linux bilgisayarlardan mantıksal disk performans sayaçlarını toplayın (% kullanılan ınomdes; Boş megabayt; % Kullanılan alan; Disk aktarımı/sn; Disk Okuma/sn; Disk yazma/sn)
9. Linux bilgisayarlardan Syslog olaylarını topla
10. Windows bilgisayarlardan uygulama olay günlüğünden hata ve uyarı olaylarını toplayın
11. Windows bilgisayarlarından bellek kullanılabilir MBayt performans sayacını topla
12. Özel bir günlük topla

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
Yukarıdaki örnekte regexDelimiter, yeni satır için "\\n" olarak tanımlanmıştır. Günlük sınırlayıcısı de bir zaman damgası olabilir.  Desteklenen biçimler şunlardır:

| Biçimlendir | JSON RegEx biçimi her bir standart RegEx için iki \\ kullanır, bu nedenle bir RegEx uygulamasında test etmek \\ \ ' a düşürüyorsam | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> AAA sonrasında iki boşluk | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> Burada + ya da a- <br> Burada zzzz zaman kayması | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T, sabit bir harf T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Azure tanılama göndermek için Log Analytics yapılandırma
Azure kaynaklarının aracısız izlenmesi için, kaynakların Azure tanılama 'nın etkinleştirilmesi ve bir Log Analytics çalışma alanına yazmak üzere yapılandırılması gerekir. Bu yaklaşım, verileri doğrudan çalışma alanına gönderir ve verilerin depolama hesabına yazılmasına gerek yoktur. Desteklenen kaynaklar şunlardır:

| Kaynak Türü | Günlükler | Ölçümler |
| --- | --- | --- |
| Application Gatewayler    | Yes | Yes |
| Otomasyon hesapları     | Yes | |
| Batch hesapları          | Yes | Yes |
| Data Lake analytics     | Yes | |
| Data Lake deposu         | Yes | |
| Elastik SQL havuzu        |     | Yes |
| Olay hub'ı ad alanı     |     | Yes |
| IoT Hub’ları                |     | Yes |
| Key Vault               | Yes | |
| Yük Dengeleyiciler          | Yes | |
| Logic Apps              | Yes | Yes |
| Ağ Güvenlik Grupları | Yes | |
| Redis için Azure Cache             |     | Yes |
| Hizmet ara         | Yes | Yes |
| Service Bus ad alanı   |     | Yes |
| SQL (V12)               |     | Yes |
| Web Siteleri               |     | Yes |
| Web sunucusu grupları        |     | Yes |

Kullanılabilir ölçümlerin ayrıntıları için [Azure izleyici ile desteklenen ölçümler](../../azure-monitor/platform/metrics-supported.md)bölümüne bakın.

Kullanılabilir günlüklerin ayrıntıları için bkz. [tanılama günlükleri için desteklenen hizmetler ve şema](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Ayrıca, farklı aboneliklerdeki kaynaklardan günlükleri toplamak için önceki cmdlet 'ini de kullanabilirsiniz. Her iki kaynağın KIMLIĞI ve günlüklerin gönderildiği çalışma alanını sağladığından, cmdlet abonelikler arasında çalışabilir.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Depolama alanından Azure tanılama toplamak için Log Analytics çalışma alanı yapılandırma
Klasik bir bulut hizmeti veya bir Service Fabric kümesinin çalışan bir örneği içinden günlük verilerini toplamak için, önce verileri Azure depolama 'ya yazmanız gerekir. Daha sonra bir Log Analytics çalışma alanı, günlükleri depolama hesabından toplayacak şekilde yapılandırılır. Desteklenen kaynaklar şunlardır:

* Klasik bulut Hizmetleri (Web ve çalışan rolleri)
* Service Fabric kümeleri

Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Mevcut depolama hesaplarını ve çalışma alanının verileri dizinleyecek konumları listeleyin
2. Depolama hesabından okumak için bir yapılandırma oluşturma
3. Yeni oluşturulan yapılandırmayı ek konumlardan veri dizinleyecek şekilde Güncelleştir
4. Yeni oluşturulan yapılandırmayı Sil

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Farklı aboneliklerdeki depolama hesaplarından günlükleri toplamak için yukarıdaki betiği de kullanabilirsiniz. Depolama hesabı kaynak KIMLIĞI ve buna karşılık gelen bir erişim anahtarı sağlamaktan bu yana betik abonelikler arasında çalışabilir. Erişim anahtarını değiştirdiğinizde, depolama öngörülerini yeni anahtara sahip olacak şekilde güncelleştirmeniz gerekir.


## <a name="next-steps"></a>Sonraki adımlar
* PowerShell 'i Log Analytics yapılandırması için kullanma hakkında daha fazla bilgi için [Log Analytics PowerShell cmdlet 'Lerini gözden geçirin](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

