---
title: PowerShell ile & oluşturma Log Analytics yapılandırma
description: Azure Izleyici 'de Log Analytics çalışma alanları, şirket içi veya bulut altyapınızdaki sunuculardaki verileri depolar. Azure tanılama tarafından oluşturulduğunda, Azure Storage 'dan makine verileri toplayabilirsiniz.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: a1abe79b4ab1caad04a088f659c0afceb2668eac
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106930"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>PowerShell kullanarak Azure Izleyici 'de Log Analytics çalışma alanı oluşturma ve yapılandırma
Bu makale, Azure Izleyici 'de bir Log Analytics çalışma alanı oluşturmayı ve yapılandırmayı gösteren iki kod örneği sunar.  


> [!NOTE]
> Log Analytics daha önce Işletimsel içgörüler olarak adlandırılmıştı. Bu, cmdlet 'lerde kullanılan addır.


## <a name="prerequisites"></a>Önkoşullar
Bu örnekler, az. Operationalınsights modülünün Version 1.0.0 veya üzeri sürümleriyle çalışır.

## <a name="create-workspace"></a>Çalışma alanı oluşturma
Aşağıdaki örnek betik, veri kaynağı yapılandırmasına sahip olmayan bir çalışma alanı oluşturur. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>Çalışma alanı oluşturma ve veri kaynaklarını yapılandırma

Aşağıdaki örnek betik örneği bir çalışma alanı oluşturur ve birden çok veri kaynağını yapılandırır. Bu veri kaynakları yalnızca [Log Analytics aracısını](../agents/log-analytics-agent.md)kullanarak sanal makineleri izliyorsanız gereklidir.

Bu betik aşağıdaki işlevleri gerçekleştirir:

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
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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

> [!NOTE]
> Özel bir günlük için yapılandırmayı tanımlayan **Customlograwjson** parametresinin biçimi karmaşık olabilir. Mevcut bir özel günlüğün yapılandırmasını almak için [Get-Azoperationalınsightsdatasource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) komutunu kullanın. **Properties** özelliği, **Customlograwjson** parametresi için gereken yapılandırmadır.

Yukarıdaki örnekte regexDelimiter, \\ yeni satır için "n" olarak tanımlandı. Günlük sınırlayıcısı de bir zaman damgası olabilir.  Desteklenen biçimler şunlardır:

| Biçimlendir | `\\` `\` Bir Regex uygulamasında test edilmesi durumunda, JSON Regex biçimi her bir Standart Regex için iki tane kullanır. `\\``\` |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> AAA sonrasında iki boşluk | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> Burada + ya da a- <br> Burada zzzz zaman kayması | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> T, sabit bir harf T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>Sorun giderme
Son 14 gün içinde silinen bir çalışma alanı oluşturduğunuzda ve [geçici silme durumunda](../logs/delete-workspace.md#soft-delete-behavior)işlem, çalışma alanı yapılandırmanıza bağlı olarak farklı bir sonuca sahip olabilir:
1. Silinen çalışma alanında aynı çalışma alanı adı, kaynak grubu, abonelik ve bölge sağlarsanız, çalışma alanınız veri, yapılandırma ve bağlı aracılar dahil kurtarılacak.
2. Çalışma alanı adı her kaynak grubu için benzersiz olmalıdır. Zaten var olan bir çalışma alanı adı kullanırsanız, kaynak grubunuzda de geçici silme sırasında, *' Workspace-Name ' çalışma alanı adı benzersiz değil* veya *Çakışma* olan bir hata alırsınız. Geçici silme işlemini geçersiz kılmak ve çalışma alanınızı kalıcı olarak silmek ve aynı ada sahip yeni bir çalışma alanı oluşturmak için, önce çalışma alanını kurtarmak ve kalıcı silme gerçekleştirmek için şu adımları izleyin:
   * Çalışma alanınızı [kurtarma](../logs/delete-workspace.md#recover-workspace)
   * Çalışma alanınızı [kalıcı olarak silme](../logs/delete-workspace.md#permanent-workspace-delete)
   * Aynı çalışma alanı adını kullanarak yeni bir çalışma alanı oluştur


## <a name="next-steps"></a>Sonraki adımlar
* PowerShell 'i Log Analytics yapılandırması için kullanma hakkında daha fazla bilgi için [Log Analytics PowerShell cmdlet 'Lerini gözden geçirin](/powershell/module/az.operationalinsights/) .
