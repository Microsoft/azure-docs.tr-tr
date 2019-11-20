---
title: Azure Otomasyonu iş verilerini Azure İzleyici günlüklerine iletme
description: Bu makalede, ek Öngörüler ve yönetim sağlamak için iş durumu ve runbook iş akışlarının Azure Izleyici günlüklerine gönderilmesi gösterilmektedir.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff455ed355d4412bcf042208d2fd1e7a2a11b965
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186772"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Otomasyon 'dan Azure Izleyici günlüklerine iş durumu ve iş akışları iletme

Otomasyon, Log Analytics çalışma alanınıza runbook iş durumu ve iş akışları gönderebilir. Bu işlem, çalışma alanı bağlamayı içermez ve tamamen bağımsızdır. İş günlükleri ve iş akışları, bireysel işler için Azure portal veya PowerShell ile görünür ve bu, basit araştırmalar gerçekleştirmenize olanak tanır. Artık Azure Izleyici günlükleri ile şunları yapabilirsiniz:

* Otomasyon işlerinize ilişkin içgörü edinin.
* Runbook iş durumunuz (örneğin, başarısız veya askıya alındı) temelinde bir e-posta veya uyarı tetikleyin.
* İş akışlarınızda gelişmiş sorgular yazın.
* Farklı Otomasyon hesaplarındaki işleri bağıntılandırın.
* Zaman içinde iş geçmişinizi görselleştirin.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Önkoşullar ve dağıtım konuları

Otomasyon günlüklerinizi Azure Izleyici günlüklerine göndermeye başlamak için şunlar gerekir:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)en son sürümü.
* Log Analytics çalışma alanı. Daha fazla bilgi için bkz. [Azure izleyici günlüklerini kullanmaya başlama](../log-analytics/log-analytics-get-started.md).
* Azure Otomasyonu hesabınız için RESOURCEID.

Azure Otomasyonu hesabınızın RESOURCEID 'sini bulmak için:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Log Analytics çalışma alanınızın RESOURCEID 'sini bulmak için aşağıdaki PowerShell 'i çalıştırın:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Birden fazla otomasyon hesabınız veya çalışma alanınız varsa, önceki komutların çıkışında, yapılandırmanız gereken *adı* bulun ve *RESOURCEID*değerini kopyalayın.

Otomasyon hesabınızın *adını* bulmanız gerekiyorsa Azure Portal Otomasyon hesabı dikey penceresinden Otomasyon hesabınızı seçin ve **Tüm ayarlar**' ı seçin. **Tüm ayarlar** dikey penceresindeki **Hesap Ayarları** altında **Özellikler**’i seçin.  **Özellikler** dikey penceresinde bu değerleri fark edebilirsiniz.<br> ![Otomasyon hesabı özellikleri](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile tümleştirmeyi ayarlama

1. Bilgisayarınızda, **Başlangıç** ekranından **Windows PowerShell** ' i başlatın.
2. Aşağıdaki PowerShell 'i çalıştırın ve değerini `[your resource id]` önceki adımdaki değerlerle ve `[resource id of the log analytics workspace]` değerlerini düzenleyin.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Bu betiği çalıştırdıktan sonra, yeni JobLogs veya JobStreams 'nin yazıldığı Azure Izleyici günlüklerinde kayıtları görmeniz için bir saat sürebilir.

Günlükleri görmek için Log Analytics günlük aramasında aşağıdaki sorguyu çalıştırın:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Yapılandırmayı doğrulama

Otomasyon hesabınızın Log Analytics çalışma alanınıza günlük gönderdiğini doğrulamak için, aşağıdaki PowerShell kullanılarak tanılamaların Otomasyon hesabında doğru şekilde yapılandırılıp yapılandırılmadığını denetleyin:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Çıktıda şunları doğrulayın:

* *Günlükler*altında, *etkin* değeri *true*'dur.
* Çalışma alanı *kimliği* değeri, Log Analytics çalışma alanınızın RESOURCEID değerine ayarlanır.

## <a name="azure-monitor-log-records"></a>Azure Izleyici günlük kayıtları

Azure Otomasyonu tanılama, Azure Izleyici günlüklerinde iki tür kayıt oluşturur ve **AzureDiagnostics**olarak etiketlenir. Aşağıdaki sorgular yükseltilen sorgu dilini Azure Izleyici günlüklerine kullanır. Eski sorgu dili ve yeni Azure kusto sorgu dili arasındaki ortak sorgular hakkında daha fazla bilgi için, [eski-yeni Azure kusto sorgu Dil](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) listesini ziyaret edin

### <a name="job-logs"></a>İş günlükleri

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Runbook işinin yürütüldüğü tarih ve saat. |
| RunbookName_s |Runbook’un adı. |
| Caller_s |İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir. |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| JobId_g |Runbook işinin Kimliği olan GUID. |
| ResultType |Runbook işinin durumudur. Olası değerler şunlardır:<br>-Yeni<br>-Oluşturuldu<br>- Başlatıldı<br>- Durduruldu<br>- Askıya alındı<br>- Başarısız oldu<br>-Tamamlandı |
| Category | Veri türü sınıflandırması. Otomasyon için değer JobLogs olacaktır. |
| OperationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur. |
| Resource | Otomasyon hesabının adı |
| SourceSystem | Azure Izleyici günlükleri verileri nasıl topladı. Azure için her zaman *Azure* tanılama. |
| ResultDescription |Runbook iş sonucu durumunu açıklar. Olası değerler şunlardır:<br>- İş başlatıldı<br>- İş Başarısız Oldu<br>- İş Tamamlandı |
| CorrelationId |Runbook işinin Bağıntı Kimliği olan GUID. |
| RESOURCEID |Runbook 'un Azure Otomasyonu hesabı kaynak kimliğini belirtir. |
| SubscriptionId | Otomasyon hesabı için Azure abonelik kimliği (GUID). |
| ResourceGroup | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT. OTOMASYON |
| KaynakTürü | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>İş akışları
| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Runbook işinin yürütüldüğü tarih ve saat. |
| RunbookName_s |Runbook’un adı. |
| Caller_s |İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir. |
| StreamType_s |İş akışı türü. Olası değerler şunlardır:<br>\- İlerleme durumu<br>- Çıktı<br>- Uyarı<br>- Hata<br>- Hata ayıklama<br>- Ayrıntılı |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| JobId_g |Runbook işinin Kimliği olan GUID. |
| ResultType |Runbook işinin durumudur. Olası değerler şunlardır:<br>-Devam ediyor |
| Category | Veri türü sınıflandırması. Otomasyon için değer JobStreams olacaktır. |
| OperationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur. |
| Resource | Otomasyon hesabının adı |
| SourceSystem | Azure Izleyici günlükleri verileri nasıl topladı. Azure için her zaman *Azure* tanılama. |
| ResultDescription |Runbook’un çıktı akışını içerir. |
| CorrelationId |Runbook işinin Bağıntı Kimliği olan GUID. |
| RESOURCEID |Runbook 'un Azure Otomasyonu hesabı kaynak kimliğini belirtir. |
| SubscriptionId | Otomasyon hesabı için Azure abonelik kimliği (GUID). |
| ResourceGroup | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT. OTOMASYON |
| KaynakTürü | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde Otomasyon günlüklerini görüntüleme

Artık Otomasyon iş günlüklerinizi Azure Izleyici günlüklerine göndermeye başladığınıza göre, Azure Izleyici günlükleri içinde bu günlüklerle neler yapabileceğinizi görelim.

Günlükleri görmek için aşağıdaki sorguyu çalıştırın:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Bir runbook işi başarısız olduğunda veya askıya aldığında e-posta gönder
Bir runbook işiyle ilgili bir sorun olduğunda bir e-posta veya metin gönderebilme özelliği, en üst müşteri sorisidir.

Bir uyarı kuralı oluşturmak için, uyarıyı çağırması gereken runbook iş kayıtları için bir günlük araması oluşturarak başlayın. Uyarı kuralını oluşturmak ve yapılandırmak için **Uyarı** düğmesine tıklayın.

1. Log Analytics çalışma alanına genel bakış sayfasında **günlükleri görüntüle**' ye tıklayın.
2. Sorgu alanına aşağıdaki aramayı yazarak Uyarınız için bir günlük arama sorgusu oluşturun: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`Ayrıca, şunu kullanarak runbook adına göre gruplandırabilirsiniz:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Çalışma alanınıza birden fazla otomasyon hesabından veya aboneliğine ait Günlükler ayarlarsanız, uyarılarınızı abonelik ve otomasyon hesabına göre gruplandırabilirsiniz. Otomasyon hesabı adı, JobLogs aramasında kaynak alanında bulunabilir.
3. **Kural oluştur** ekranını açmak için sayfanın üst kısmındaki **+ Yeni uyarı kuralı** ' na tıklayın. Uyarıyı yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Azure 'Da günlük uyarıları](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hatalarla tamamlanan tüm işleri bul
Hatalarda uyarı verme işleminin yanı sıra, bir runbook işinin Sonlandırılmamış bir hata olduğunu fark edebilirsiniz. Bu durumlarda, PowerShell bir hata akışı üretir, ancak Sonlandırıcı olmayan hatalar işinizin askıya alınmasına veya başarısız olmasına neden olmaz.

1. Log Analytics çalışma alanınızda **Günlükler**' e tıklayın.
2. Sorgu alanına yazın `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` ve ardından **Ara** düğmesine tıklayın.

### <a name="view-job-streams-for-a-job"></a>İş akışlarını görüntüleme
Bir iş hata ayıklaması yaparken iş akışlarına da bakmak isteyebilirsiniz. Aşağıdaki sorgu, GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 ile tek bir işin tüm akışlarını gösterir:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Geçmiş iş durumunu görüntüle
Son olarak, zaman içinde iş geçmişinizi görselleştirmek isteyebilirsiniz. Bu sorguyu, zaman içinde işlerinizin durumunu aramak için kullanabilirsiniz.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics geçmiş Iş durumu grafiği](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Tanılama ayarlarını kaldır

Otomasyon hesabından tanılama ayarını kaldırmak için aşağıdaki komutları çalıştırın:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Özet

Otomasyon iş durumunuzu ve akış verilerinizi Azure Izleyici günlüklerine göndererek, Otomasyon işlerinizin durumu hakkında daha iyi öngörüler elde edebilirsiniz:
+ Sorun olduğunda sizi bilgilendirmek için Uyarılar ayarlama.
+ Runbook sonuçlarınızı, runbook iş durumunu ve diğer ilgili anahtar göstergelerini veya ölçümlerini görselleştirmek için özel görünümler ve arama sorguları kullanma.

Azure Izleyici günlükleri, Otomasyon işleriniz için daha fazla işletimsel görünürlük sağlar ve olayların daha hızlı sağlanmasına yardımcı olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Log Analytics sorun giderme konusunda yardım için bkz. [Log Analytics artık veri toplamamasının neden olduğu sorun giderme](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Farklı arama sorguları oluşturma ve Azure Izleyici günlükleriyle Otomasyon iş günlüklerini İnceleme hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde günlük aramaları](../log-analytics/log-analytics-log-searches.md).
* Runbook 'lardan çıkış ve hata iletilerinin nasıl oluşturulduğunu ve alınacağını anlamak için bkz. [runbook çıktısı ve iletileri](automation-runbook-output-and-messages.md).
* Runbook yürütme, runbook işlerini izleme ve diğer teknik ayrıntılar hakkında daha fazla bilgi edinmek için bkz. [Runbook işi izleme](automation-runbook-execution.md).
* Azure Izleyici günlükleri ve veri toplama kaynakları hakkında daha fazla bilgi edinmek için bkz. Azure [izleyici günlüklerine Azure depolama verilerini toplama genel bakış](../azure-monitor/platform/collect-azure-metrics-logs.md).

