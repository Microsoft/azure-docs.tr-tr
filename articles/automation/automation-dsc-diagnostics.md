---
title: Azure Izleyici günlükleriyle tümleştirme
description: Bu makalede, Istenen durum yapılandırması raporlama verilerinin Azure Automation durum yapılandırmasından Azure Izleyici günlüklerine nasıl gönderileceği açıklanır.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834813"
---
# <a name="integrate-with-azure-monitor-logs"></a>Azure Izleyici günlükleriyle tümleştirme

Azure Otomasyonu durum yapılandırması, düğüm durumu verilerini 30 gün boyunca tutar. Bu verileri daha uzun bir süre için tutmayı tercih ediyorsanız, Log Analytics çalışma alanınıza düğüm durumu verileri gönderebilirsiniz. Uyumluluk durumu, düğümler ve düğüm yapılandırmalarında tek DSC kaynakları için Azure portal veya PowerShell ile görünür. 

Azure Izleyici günlükleri, otomasyon durumu yapılandırma verilerinize daha fazla işletimsel görünürlük sağlar ve olayların daha hızlı bir şekilde ele sağlanmasına yardımcı olabilir. Azure Izleyici günlükleri ile şunları yapabilirsiniz:

- Yönetilen düğümler ve bireysel kaynaklar için uyumluluk bilgileri alın.
- Uyumluluk durumuna göre bir e-posta veya uyarı tetikleyin.
- Yönetilen düğümleriniz genelinde gelişmiş sorgular yazın.
- Otomasyon hesapları arasında uyumluluk durumu ile bağıntı kurun.
- Runbook sonuçlarınızı, runbook iş durumunu ve diğer ilgili anahtar göstergelerini veya ölçümlerini görselleştirmek için özel görünümleri ve arama sorgularını kullanın.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Automation durum yapılandırma raporlarınızı Azure Izleyici günlüklerine göndermeye başlamak için şunlar gerekir:

- [Azure PowerShell](/powershell/azure/) Kasım 2016 veya sonraki bir sürümü (v 2.3.0).
- Azure Otomasyonu hesabı. Daha fazla bilgi için bkz. [Azure Otomasyonu 'na giriş](automation-intro.md).
- Otomasyon & denetim hizmeti teklifiyle Log Analytics çalışma alanı. Daha fazla bilgi için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md).
- En az bir Azure Otomasyonu durum yapılandırması düğümü. Daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md).
- [Xdscdiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modülü, sürüm 2.7.0.0 veya üstü. Yükleme adımları için bkz. [Azure Otomasyonu Istenen durum yapılandırması sorunlarını giderme](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile tümleştirmeyi ayarlama

Azure Otomasyonu durum yapılandırmasından Azure Izleyici günlüklerine veri aktarmaya başlamak için aşağıdaki adımları izleyin:

1. PowerShell 'de Azure hesabınızda oturum açın. Bkz. [Azure PowerShell oturum açma](/powershell/azure/authenticate-azureps).
1. Aşağıdaki PowerShell cmdlet 'ini çalıştırarak Otomasyon hesabınızın kaynak KIMLIĞINI alın. Birden fazla otomasyon hesabınız varsa, yapılandırmak istediğiniz hesabın kaynak KIMLIĞINI seçin.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Aşağıdaki PowerShell cmdlet 'ini çalıştırarak Log Analytics çalışma alanınızın kaynak KIMLIĞINI alın. Birden fazla çalışma alanınız varsa, yapılandırmak istediğiniz çalışma alanının kaynak KIMLIĞINI seçin.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Aşağıdaki PowerShell cmdlet 'ini çalıştırarak `<AutomationResourceId>` ve `<WorkspaceResourceId>` `ResourceId` değerlerini önceki adımların her biri ile değiştirin.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Azure Otomasyonu durum yapılandırmasından Azure Izleyici günlüklerine veri aktarmayı durdurmak istiyorsanız aşağıdaki PowerShell cmdlet 'ini çalıştırın.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Durum yapılandırma günlüklerini görüntüleme

Otomasyon durumu yapılandırma verilerinize yönelik Azure Izleyici günlükleri ile tümleştirmeyi ayarladıktan sonra, durum yapılandırması (DSC) sayfasının sol bölmesindeki **izleme** bölümünde **Günlükler** ' i seçerek bunları görüntüleyebilirsiniz.

![Günlükler](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Günlük araması bölmesi, Otomasyon hesabı kaynağınız kapsamındaki bir sorgu bölgesi ile açılır. Azure Izleyici günlüklerinde arama yaparak DSC işlemlerine yönelik durum yapılandırma günlüklerinde arama yapabilirsiniz. DSC işlemlerine yönelik kayıtlar `AzureDiagnostics` tabloda depolanır. Örneğin, uyumlu olmayan düğümleri bulmak için aşağıdaki sorguyu yazın.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filtreleme ayrıntıları:

* `DscNodeStatusData`Her durum yapılandırma düğümü için işlemleri döndürmek üzere filtreleyin.
* `DscResourceStatusData`Bu kaynağa uygulanan düğüm yapılandırmasında çağrılan her DSC kaynağına yönelik işlemleri döndürmek için filtreleyin. 
* `DscResourceStatusData`Başarısız olan tüm DSC kaynakları için hata bilgilerini döndürmek üzere filtreleyin.

Verileri bulmak için günlük sorguları oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Bir durum yapılandırması uyumluluk denetimi başarısız olduğunda e-posta gönder

En popüler müşteri isteklerimizden biri, bir DSC yapılandırmasıyla bir sorun olduğunda e-posta veya metin gönderebilme özelliğine yöneliktir.

Uyarı kuralı oluşturmak için, uyarıyı çağırması gereken durum yapılandırma raporu kayıtları için bir günlük araması oluşturarak başlayın. Uyarı kuralını oluşturmak ve yapılandırmak için **Yeni uyarı kuralı** düğmesine tıklayın.

1. Log Analytics çalışma alanına genel bakış sayfasında **Günlükler**' e tıklayın.
1. Sorgu alanına aşağıdaki aramayı yazarak Uyarınız için bir günlük arama sorgusu oluşturun:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Çalışma alanınıza birden fazla otomasyon hesabından veya aboneliğine ait Günlükler ayarladıysanız, uyarılarınızı abonelik ve otomasyon hesabına göre gruplandırabilirsiniz. Otomasyon hesabı adını `Resource` kayıtları aramadaki alandan türetirsiniz `DscNodeStatusData` .
1. **Kural oluştur** ekranını açmak için sayfanın üst kısmındaki **Yeni uyarı kuralı** ' na tıklayın. 

Uyarıyı yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [bir uyarı kuralı oluşturma](../azure-monitor/alerts/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Tüm düğümlerde başarısız DSC kaynaklarını bul

Azure Izleyici günlüklerini kullanmanın avantajlarından biri, düğümler genelinde başarısız denetimleri arayabilmenizi sağlayabilir. Başarısız olan DSC kaynaklarının tüm örneklerini bulmak için:

1. Log Analytics çalışma alanına genel bakış sayfasında **Günlükler**' e tıklayın.
1. Sorgu alanına aşağıdaki aramayı yazarak Uyarınız için bir günlük arama sorgusu oluşturun:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Geçmiş DSC düğümü durumunu görüntüle

DSC düğüm durumu geçmişinizi zaman içinde görselleştirmek için bu sorguyu kullanabilirsiniz:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Bu sorgu zaman içinde düğüm durumunun bir grafiğini görüntüler.

## <a name="azure-monitor-logs-records"></a>Azure Izleyici günlük kayıtları

Azure Otomasyonu tanılama, Azure Izleyici günlüklerinde iki kayıt kategorisi oluşturur:

* Düğüm durumu verileri ( `DscNodeStatusData` )
* Kaynak durumu verileri ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Uyumluluk denetiminin çalıştırıldığı tarih ve saat. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Düğümün uyumlu olup olmadığını gösteren değer. |
| NodeName_s |Yönetilen düğümün adı. |
| NodeComplianceStatus_s |Düğümün uyumlu olup olmadığını belirten durum değeri. |
| DscReportStatus |Uyumluluk denetiminin başarıyla çalıştırılıp çalıştırılmadığını gösteren durum değeri. |
| ConfigurationMode | Yapılandırmayı düğüme uygulamak için kullanılan mod. Olası değerler şunlardır: <ul><li>`ApplyOnly`: DSC yapılandırmayı uygular ve hedef düğüme yeni bir yapılandırma itilemez veya bir sunucudan yeni bir yapılandırma çekilmediği takdirde hiçbir şey yapmaz. Yeni yapılandırmanın ilk uygulamasından sonra DSC, daha önce yapılandırılmış bir durumdan DRFT 'yi denetlemez. DSC, değer yürürlüğe girmeden önce başarılı olana kadar yapılandırmayı uygulamaya çalışır `ApplyOnly` . </li><li>`ApplyAndMonitor`: Bu, varsayılan değerdir. LCM, yeni yapılandırma uygular. Yeni yapılandırmanın ilk uygulamasından sonra, hedef düğüm istenen durumdan Drifts, DSC, günlüklerde tutarsızlığı raporlar. DSC, değer yürürlüğe girmeden önce başarılı olana kadar yapılandırmayı uygulamaya çalışır `ApplyAndMonitor` .</li><li>`ApplyAndAutoCorrect`: DSC yeni yapılandırma uygular. Yeni bir yapılandırmanın ilk uygulamasından sonra, hedef düğüm istenen durumdan Drifts, DSC günlüklerde tutarsızlığı raporlar ve ardından geçerli yapılandırmayı yeniden uygular.</li></ul> |
| HostName_s | Yönetilen düğümün adı. |
| IPAddress | Yönetilen düğümün IPv4 adresi. |
| Kategori | `DscNodeStatus`. |
| Kaynak | Azure Otomasyonu hesabının adı. |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| NodeId_g | Yönetilen düğümü tanımlayan GUID. |
| DscReportId_g | Raporu tanımlayan GUID. |
| LastSeenTime_t | Raporun en son görüntülendiği tarih ve saat. |
| ReportStartTime_t | Raporun başlatıldığı tarih ve saat. |
| ReportEndTime_t | Raporun tamamlandığı tarih ve saat. |
| NumberOfResources_d | Düğümde uygulanan yapılandırmada çağrılan DSC kaynaklarının sayısı. |
| SourceSystem | Azure Izleyici günlüklerinin verileri nasıl topladığını tanımlayan kaynak sistem. Her zaman `Azure` Azure Tanılama için. |
| ResourceId |Azure Otomasyonu hesabının kaynak tanımlayıcısı. |
| ResultDescription | Bu işlem için kaynak açıklaması. |
| SubscriptionId | Otomasyon hesabı için Azure abonelik KIMLIĞI (GUID). |
| adlı yönetilen örnek, | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT. Otomasyon. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Uyumluluk raporunun bağıntı tanımlayıcısı olan bir GUID. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Uyumluluk denetiminin çalıştırıldığı tarih ve saat. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Kaynağın uyumlu olup olmadığı. |
| NodeName_s |Yönetilen düğümün adı. |
| Kategori | DscNodeStatus. |
| Kaynak | Azure Otomasyonu hesabının adı. |
| Tenant_g | Çağıran için kiracıyı tanımlayan GUID. |
| NodeId_g |Yönetilen düğümü tanımlayan GUID. |
| DscReportId_g |Raporu tanımlayan GUID. |
| DscResourceId_s |DSC kaynak örneğinin adı. |
| DscResourceName_s |DSC kaynağının adı. |
| DscResourceStatus_s |DSC kaynağının uyumlu olup olmadığı. |
| DscModuleName_s |DSC kaynağını içeren PowerShell modülünün adı. |
| DscModuleVersion_s |DSC kaynağını içeren PowerShell modülünün sürümü. |
| DscConfigurationName_s |Düğüme uygulanan yapılandırmanın adı. |
| ErrorCode_s | Kaynak başarısız olursa hata kodu. |
| ErrorMessage_s |Kaynak başarısız olursa hata iletisi. |
| DscResourceDuration_d |DSC kaynağının çalıştırıldığı saniye cinsinden süre. |
| SourceSystem | Azure Izleyici günlükleri verileri nasıl topladı. Her zaman `Azure` Azure Tanılama için. |
| ResourceId |Azure Otomasyonu hesabının tanımlayıcısı. |
| ResultDescription | Bu işlemin açıklaması. |
| SubscriptionId | Otomasyon hesabı için Azure abonelik KIMLIĞI (GUID). |
| adlı yönetilen örnek, | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT. Otomasyon. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Uyumluluk raporunun bağıntı KIMLIĞI olan GUID. |

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md).
- Başlamak için bkz. [Azure Otomasyonu durum yapılandırmasını kullanmaya başlama](automation-dsc-getting-started.md).
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum YAPıLANDıRMASıNDA DSC yapılandırmalarını derleme](automation-dsc-compile.md).
- PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Chocolatey ile sürekli dağıtımı ayarlama](automation-dsc-cd-chocolatey.md).
- Farklı arama sorguları oluşturma ve Azure Izleyici günlükleriyle Otomasyon durumu yapılandırma günlüklerini İnceleme hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde günlük aramaları](../azure-monitor/logs/log-query-overview.md).
- Azure Izleyici günlükleri ve veri toplama kaynakları hakkında daha fazla bilgi edinmek için bkz. Azure [izleyici günlüklerine Azure depolama verilerini toplama genel bakış](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
