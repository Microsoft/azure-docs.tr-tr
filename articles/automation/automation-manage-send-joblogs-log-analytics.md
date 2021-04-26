---
title: Azure Otomasyonu iş verilerini Azure İzleyici günlüklerine iletme
description: Bu makalede, Azure Izleyici günlüklerine iş durumu ve runbook iş akışlarının nasıl gönderileceği açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c24c38368ef20dadd0dc19b1804f9d27ad68bd27
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833697"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Otomasyonu iş verilerini Azure İzleyici günlüklerine iletme

Azure Otomasyonu, Log Analytics çalışma alanınıza runbook iş durumu ve iş akışları gönderebilir. Bu işlem, çalışma alanı bağlamayı içermez ve tamamen bağımsızdır. İş günlükleri ve iş akışları, bireysel işler için Azure portal veya PowerShell ile görünür ve bu, basit araştırmalar gerçekleştirmenize olanak tanır. Artık Azure Izleyici günlükleri ile şunları yapabilirsiniz:

* Otomasyon işlerinizin durumuyla ilgili Öngörüler elde edin.
* Runbook iş durumunuz (örneğin, başarısız veya askıya alındı) temelinde bir e-posta veya uyarı tetikleyin.
* İş akışlarınız genelinde gelişmiş sorgular yazın.
* Otomasyon hesaplarında işlerin ilişkilendirilmesi.
* Runbook sonuçlarınızı, runbook iş durumunu ve diğer ilgili anahtar göstergelerini veya ölçümlerini görselleştirmek için özel görünümleri ve arama sorgularını kullanın.

## <a name="prerequisites"></a>Önkoşullar

Otomasyon günlüklerinizi Azure Izleyici günlüklerine göndermeye başlamak için şunlar gerekir:

* [Azure PowerShell](/powershell/azure/)en son sürümü.

* Bir Log Analytics çalışma alanı ve kaynak KIMLIĞI. Daha fazla bilgi için bkz. [Azure izleyici günlüklerini kullanmaya başlama](../azure-monitor/overview.md).

* Azure Otomasyonu hesabınızın kaynak KIMLIĞI.

## <a name="how-to-find-resource-ids"></a>Kaynak kimliklerini bulma

1. Azure Otomasyonu hesabınızın kaynak KIMLIĞINI bulmak için aşağıdaki komutu kullanın:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. **RESOURCEID** değerini kopyalayın.

3. Log Analytics çalışma alanınızın kaynak KIMLIĞINI bulmak için aşağıdaki komutu kullanın:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. **RESOURCEID** değerini kopyalayın.

Belirli bir kaynak grubundan sonuçları döndürmek için `-ResourceGroupName` parametresini ekleyin. Daha fazla bilgi için bkz. [Get-AzResource](/powershell/module/az.resources/get-azresource).

Yukarıdaki komutların çıktısında birden fazla otomasyon hesabınız veya çalışma alanınız varsa, aşağıdaki işlemi gerçekleştirerek Otomasyon hesabınızın tam kaynak KIMLIĞININ bir parçası olan adı ve diğer ilgili özellikleri bulabilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portal **Otomasyon hesapları** sayfasından Otomasyon hesabınızı seçin.
1. Seçilen Otomasyon hesabının sayfasında, **Hesap ayarları** altında **Özellikler**' i seçin.
1. **Özellikler** sayfasında aşağıda gösterilen ayrıntıları göz önünde bulabilirsiniz.

    ![Otomasyon hesabı özellikleri](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Tanılama ayarlarını yapılandırma

Otomasyon Tanılama ayarları aşağıdaki platform günlüklerinin ve ölçüm verilerinin iletilmesini destekler:

* JobLogs
* JobStreams
* DSCNodeStatus
* Ölçümler-toplam Iş, toplam güncelleştirme dağıtım makinesi çalıştırmaları, toplam güncelleştirme dağıtımı çalıştırmaları

Otomasyon günlüklerinizi Azure Izleyici günlüklerine göndermeye başlamak için, tanılama ayarlarını platform günlüklerini gönderecek şekilde yapılandırmak üzere kullanılabilecek özellik ve yöntemleri anlamak için [tanılama ayarlarını oluşturma](../azure-monitor/essentials/diagnostic-settings.md) ' yı gözden geçirin.

## <a name="azure-monitor-log-records"></a>Azure Izleyici günlük kayıtları

Azure Otomasyonu tanılama, Azure Izleyici günlüklerinde olarak etiketlenen iki tür kayıt oluşturur `AzureDiagnostics` . Sonraki bölümlerdeki tablolar, Azure Otomasyonu tarafından oluşturulan kayıt örnekleri ve günlük araması sonuçlarında görünen veri türleridir.

### <a name="job-logs"></a>İş günlükleri

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Runbook işinin yürütüldüğü tarih ve saat. |
| RunbookName_s |Runbook’un adı. |
| Caller_s |İşlemi başlatan arayan. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir. |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| JobId_g |Runbook işini tanımlayan GUID. |
| ResultType |Runbook işinin durumudur. Olası değerler şunlardır:<br>-Yeni<br>-Oluşturuldu<br>- Başlatıldı<br>- Durduruldu<br>- Askıya alındı<br>- Başarısız oldu<br>-Tamamlandı |
| Kategori | Veri türü sınıflandırması. Otomasyon için değer JobLogs olacaktır. |
| OperationName | Azure 'da gerçekleştirilen işlem türü. Otomasyon için değer Iş olur. |
| Kaynak | Otomasyon hesabının adı |
| SourceSystem | Azure Izleyici günlüklerinin verileri toplamak için kullandığı sistem. Azure Tanılama için değer her zaman Azure ' dır. |
| ResultDescription |Runbook işi sonuç durumu. Olası değerler şunlardır:<br>- İş başlatıldı<br>- İş Başarısız Oldu<br>- İş Tamamlandı |
| CorrelationId |Runbook işinin bağıntı GUID 'SI. |
| ResourceId |Runbook 'un Azure Otomasyonu hesabı kaynak KIMLIĞI. |
| SubscriptionId | Otomasyon hesabı için Azure aboneliği GUID 'ı. |
| adlı yönetilen örnek, | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | Kaynak sağlayıcısı. Değer MICROSOFT. Otomasyon. |
| ResourceType | Kaynak türü. Değer AUTOMATIONACCOUNTS değeridir. |

### <a name="job-streams"></a>İş akışları
| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Runbook işinin yürütüldüğü tarih ve saat. |
| RunbookName_s |Runbook’un adı. |
| Caller_s |İşlemi başlatan arayan. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir. |
| StreamType_s |İş akışı türü. Olası değerler şunlardır:<br>- İlerleme durumu<br>- Çıktı<br>- Uyarı<br>- Hata<br>- Hata ayıklama<br>- Ayrıntılı |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| JobId_g |Runbook işini tanımlayan GUID. |
| ResultType |Runbook işinin durumudur. Olası değerler şunlardır:<br>-Devam ediyor |
| Kategori | Veri türü sınıflandırması. Otomasyon için değer JobStreams olacaktır. |
| OperationName | Azure 'da gerçekleştirilen işlem türü. Otomasyon için değer Iş olur. |
| Kaynak | Otomasyon hesabının adı. |
| SourceSystem | Azure Izleyici günlüklerinin verileri toplamak için kullandığı sistem. Azure Tanılama için değer her zaman Azure ' dır. |
| ResultDescription |Runbook 'tan çıkış akışını içeren açıklama. |
| CorrelationId |Runbook işinin bağıntı GUID 'SI. |
| ResourceId |Runbook 'un Azure Otomasyonu hesabı kaynak KIMLIĞI. |
| SubscriptionId | Otomasyon hesabı için Azure aboneliği GUID 'ı. |
| adlı yönetilen örnek, | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | Kaynak sağlayıcısı. Değer MICROSOFT. Otomasyon. |
| ResourceType | Kaynak türü. Değer AUTOMATIONACCOUNTS değeridir. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde Otomasyon günlüklerini görüntüleme

Artık Otomasyon iş akışlarınızı ve günlüklerinizi Azure Izleyici günlüklerine göndermeye başladığınıza göre, Azure Izleyici günlükleri içinde bu günlüklerle neler yapabileceğinizi görelim.

Günlükleri görmek için aşağıdaki sorguyu çalıştırın: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Bir runbook işi başarısız olduğunda veya askıya aldığında e-posta gönder

Aşağıdaki adımlarda, bir runbook işiyle ilgili bir sorun olduğunda sizi bilgilendirmek üzere Azure Izleyici 'de uyarıların nasıl ayarlanacağı gösterilmektedir.

Uyarı kuralı oluşturmak için, uyarıyı çağırması gereken runbook iş kayıtları için bir günlük araması oluşturarak başlayın. Uyarı kuralını oluşturmak ve yapılandırmak için **Uyarı** düğmesine tıklayın.

1. Log Analytics çalışma alanına genel bakış sayfasında **günlükleri görüntüle**' ye tıklayın.

2. Sorgu alanına aşağıdaki aramayı yazarak Uyarınız için bir günlük arama sorgusu oluşturun: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Ayrıca, kullanarak runbook adına göre gruplandırabilirsiniz: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Çalışma alanınıza birden fazla otomasyon hesabından veya aboneliğine ait Günlükler ayarlarsanız, uyarılarınızı abonelik ve otomasyon hesabına göre gruplandırabilirsiniz. Otomasyon hesabı adı `Resource` , arama içindeki alanda bulunabilir `JobLogs` .

3. **Kural oluştur** ekranını açmak için sayfanın üst kısmındaki **Yeni uyarı kuralı** ' na tıklayın. Uyarıyı yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Azure 'Da günlük uyarıları](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hatalarla tamamlanan tüm işleri bul

Hatalarda uyarı verme işleminin yanı sıra, bir runbook işinin Sonlandırılmamış bir hata olduğunu fark edebilirsiniz. Bu durumlarda, PowerShell bir hata akışı üretir, ancak Sonlandırılmamış hatalar işinizin askıya alınmasına veya başarısız olmasına neden olmaz.

1. Log Analytics çalışma alanınızda **Günlükler**' e tıklayın.

2. Sorgu alanına, yazın `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. **Ara** düğmesine tıklayın.

### <a name="view-job-streams-for-a-job"></a>İş akışlarını görüntüleme

Bir iş hata ayıklaması yaparken iş akışlarına da bakmak isteyebilirsiniz. Aşağıdaki sorguda GUID ile tek bir iş için tüm akışlar gösterilmektedir `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Geçmiş iş durumunu görüntüle

Son olarak, zaman içinde iş geçmişinizi görselleştirmek isteyebilirsiniz. Bu sorguyu, zaman içinde işlerinizin durumunu aramak için kullanabilirsiniz.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics geçmiş Iş durumu grafiği](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>JSON nesnesine dönüştürülen iş durumu çıkışını filtrele

Son olarak, Otomasyon günlüğü verilerinin, Log Analytics hizmetindeki tabloya nasıl yazıldığı ve bu durumda `AzureDiagnostics` JSON özelliklerinin ayrı alanlara artık bölünememesi gibi bir davranışı değiştirdik. Runbook 'unuzu JSON biçimindeki nesneleri ayrı sütunlar olarak biçimlendirmek üzere yapılandırdıysanız, bu özelliklere erişmek için sorgularınızın bu alanı bir JSON nesnesine ayrıştırmak üzere yeniden yapılandırılması gerekir. Bu, bilinen bir yoldaki belirli bir JSON öğesine erişmek için [parseJSON](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) kullanılarak gerçekleştirilir.

Örneğin, bir runbook, çıkış akışındaki *Resultdescription* özelliğini birden çok alan içeren JSON biçiminde biçimlendirir. **Durum** adlı bir alanda belirtilen başarısız durumda olan işlerinizin durumunu aramak Için, *Resultdescription* durumunu **başarısız** olarak aramak üzere bu örnek sorguyu kullanın:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics geçmiş Iş akışı JSON biçimi](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Sonraki adımlar

* Arama sorguları oluşturma ve Azure Izleyici günlükleriyle Otomasyon iş günlüklerini İnceleme hakkında bilgi edinmek için bkz. [Azure izleyici günlüklerinde günlük aramaları](../azure-monitor/logs/log-query-overview.md).
* Runbook 'lardan çıkış ve hata iletilerinin oluşturulmasını ve alınmasını anlamak için bkz. [runbook çıkışını izleme](automation-runbook-output-and-messages.md).
* Runbook yürütmesi, runbook işlerini izleme ve diğer teknik ayrıntılar hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'Nda runbook yürütme](automation-runbook-execution.md).
* Azure Izleyici günlükleri ve veri toplama kaynakları hakkında daha fazla bilgi edinmek için bkz. Azure [izleyici günlüklerine Azure depolama verilerini toplama genel bakış](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
* Log Analytics sorun giderme konusunda yardım için bkz. [Log Analytics artık veri toplamamasının neden olduğu sorun giderme](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
