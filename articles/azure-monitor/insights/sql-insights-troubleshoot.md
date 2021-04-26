---
title: SQL Insights sorunlarını giderme (Önizleme)
description: Azure Izleyici 'de SQL Insights sorunlarını giderme
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 4d4a801d0cf0a2355334272053ff86dd846b6bbf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030313"
---
# <a name="troubleshooting-sql-insights-preview"></a>SQL Insights sorunlarını giderme (Önizleme)
SQL Insights 'ta veri toplama sorunlarını gidermek için, **profili Yönet** sekmesinde izleme makinesinin durumunu kontrol edin. Bu, aşağıdaki durumlardan birine sahip olacaktır:

- Toplama 
- Toplanmıyor 
- Hatalarla toplama 
 
Günlükleri ve diğer ayrıntıları görmek için detaya gitme **durumuna** tıklayarak sorunu çözmenize yardımcı olabilirsiniz. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Makine durumunu izleme":::

## <a name="not-collecting-state"></a>Durum toplanmıyor 
Son 10 dakika içinde SQL için *ınsightsölçümler* 'de veri yoksa, izleme makinesi *toplanmıyor* durumuna sahiptir. 

SQL Insights, bu bilgileri almak için aşağıdaki sorguyu kullanır:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Telegraf 'dan, sorunun kökünü belirlemek için size yardımcı olan Günlükler olup olmadığını denetleyin. Günlük girişleri varsa, genel sorunlar için *toplanmıyor* ' e tıklayabilir ve günlükleri ve sorun giderme bilgilerini kontrol edebilirsiniz. 


Günlük yoksa, iki sanal makine uzantısı tarafından yüklenen aşağıdaki hizmetler için izleme sanal makinesindeki günlükleri denetlemeniz gerekir:

- Microsoft. Azure. Monitor. AzureMonitorLinuxAgent 
  - Hizmet: MDSD 
- Microsoft. Azure. Monitor. Iniş yükleri. Workload. Wzaınuxextension 
  - Hizmet: wli 
  - Hizmet: MS-telegraf 
  - Hizmet: TD-Agent-bit-wli 
  - Yüklemesi başarısızlıklarını denetlemek için uzantı günlüğü:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>WLI hizmet günlükleri 

Hizmet günlükleri: `/var/log/wli.log`

Son günlükleri görmek için: `tail -n 100 -f /var/log/wli.log`
 

Aşağıdaki hata günlüğünü görürseniz, **MDSD** hizmetiyle ilgili bir sorun olduğunu gösterir.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Telegraf hizmeti günlükleri 

Hizmet günlükleri: `/var/log/ms-telegraf/telegraf.log`

Son günlükleri görmek için: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` en son hata ve uyarı günlüklerini görmek için: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Telegraf tarafından kullanılan yapılandırma wli hizmeti tarafından oluşturulur ve şu şekilde yerleştirilir: `/etc/ms-telegraf/telegraf.d/wli`
 
Hatalı bir yapılandırma oluşturulursa MS-telegraf hizmeti başlayamayabilir, şu komutla birlikte MS-telegraf hizmetinin çalışıp çalışmadığını kontrol edin: `service ms-telegraf status`

Telegraf hizmetinden hata iletilerini görmek için aşağıdaki komutla el ile çalıştırın: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>MDSD hizmeti günlükleri 

Azure Izleyici aracısının [geçerli sınırlamalarını](../agents/azure-monitor-agent-overview.md#current-limitations) denetleyin. 


Hizmet günlükleri:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Son hataları görmek için: `tail -n 100 -f /var/log/mdsd.err`

 Desteğe başvurmanız gerekirse, aşağıdaki bilgileri toplayın: 

- Oturum açan `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Oturum aç `/var/log/waagent.log` 
- Oturum açan `/var/log/mdsd*`
- İçindeki dosyalar `/etc/mdsd.d/`
- Dosyasýný `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Geçersiz izleme sanal makine yapılandırması

*Toplama* durumunun bir nedeni, geçersiz bir izleme sanal makine yapılandırmasına sahip olduğunuz durumdur.  Varsayılan yapılandırma aşağıda verilmiştir:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Bu yapılandırma, izleme sanal makinenizde profil yapılandırmasında kullanılacak değiştirme belirteçlerini belirler. Ayrıca, Azure Key Vault parolalara başvurmanızı sağlar, böylece gizli olmayan herhangi bir yapılandırmada gizli değerleri saklamayın ve kesinlikle önerilir.

#### <a name="secrets"></a>Gizli Diziler
Gizli dizileri, değerleri bir Azure Key Vault çalışma zamanında alınan belirteçlerdir. Gizli anahtar, bir Key Vault başvurusu ve gizli dizi adı çifti tarafından tanımlanır. Bu, Azure Izleyici 'nin gizli anahtar dinamik değerini almasına ve bunun aşağı akış yapılandırma başvuruları kullanmasına izin verir.

Ayrı anahtar kasalarında depolanan gizlilikler da dahil olmak üzere, yapılandırmada gereken sayıda gizli dizi tanımlayabilirsiniz.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Key Vault erişim izinleri, izleme sanal makinesindeki bir Yönetilen Hizmet Kimliği sağlanır. Azure Izleyici Key Vault, sanal makine için en az gizli dizi Al izinleri sağlamasını bekliyor. Azure portal, PowerShell, CLı veya Kaynak Yöneticisi şablonundan etkinleştirebilirsiniz.

#### <a name="parameters"></a>Parametreler
Parametreler, JSON şablon oluşturma aracılığıyla profil yapılandırmasında başvurulabilen belirteçlerdir. Parametrelerin bir adı ve değeri vardır. Değerler, nesneler ve diziler dahil olmak üzere herhangi bir JSON türü olabilir. Bu kural içindeki adı kullanılarak profil yapılandırmasında bir parametreye başvurulur `.Parameters.<name>` .

Parametreler, aynı kuralı kullanarak Key Vault gizli dizilerme başvurabilir. Örneğin, `sqlAzureConnections` kuralını kullanarak gizli dizi başvurusu `telegrafPassword` `$telegrafPassword` .

Çalışma zamanında, tüm parametreler ve gizli dizileri çözülür ve makinede kullanılacak gerçek yapılandırmayı oluşturmak için profil yapılandırmasıyla birleştirilir.

> [!NOTE]
> `sqlAzureConnections`, `sqlVmConnections` Ve parametrelerinin `sqlManagedInstanceConnections` bazıları için sağlayacaksınız bağlantı Dizeleriniz olmasa bile, bu parametre adları yapılandırmada gereklidir.


## <a name="collecting-with-errors-state"></a>Hata durumuyla toplama
En az bir *ınsightsmetrik* günlüğü, ancak *işlem* tablosunda da hatalar varsa, Izleme makinesi *hatalarla birlikte toplanacaktır* .

SQL Insights, bu bilgileri almak için aşağıdaki sorguları kullanır:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> ' Workloadagnoçıkartlogs ' veri türünde herhangi bir veri görmüyorsanız, bu verileri depolamak için izleme profilinizi güncelleştirmeniz gerekebilir.  SQL Insights UX içinden ' profili Yönet ' seçeneğini belirleyin, ardından ' Profili Düzenle ' seçeneğini belirleyin ve ardından ' izleme profilini Güncelleştir ' seçeneğini belirleyin.


Yaygın durumlarda, günlüklerimizde sorun giderme bilgisi sağlıyoruz: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Sorun giderme günlükleri görünümü.":::



## <a name="next-steps"></a>Sonraki adımlar

- [SQL Insights 'ı etkinleştirme](sql-insights-enable.md)hakkında ayrıntılı bilgi edinin.
