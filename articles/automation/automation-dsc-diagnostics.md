---
title: Azure Otomasyonu durumu Azure İzleyici günlüklerine veri raporlama yapılandırma ilet
description: Bu makalede Desired State Configuration (Azure Otomasyonu durumu yapılandırma verileri Azure İzleyici günlüklerine raporlama hakkındaki ek bilgiler ve yönetim sağlamak için DSC) gönderme işlemini gösterir.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dad74f75fd7b73e7dab0b2dddbdfda193d5b2ec
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445792"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Otomasyonu durumu Azure İzleyici günlüklerine veri raporlama yapılandırma ilet

Azure Otomasyonu durumu yapılandırma düğümü durumu verileri 30 gün boyunca tutar.
İsterseniz bu verileri korumak için daha uzun bir süre Log Analytics çalışma alanınıza düğüm durumu veriler gönderebilir.
Uyumluluk durumu Azure portalında veya PowerShell ile ve DSC düğüm yapılandırmaları kaynaklarında tek tek düğümleri için görülebilir.
Azure İzleyici günlüklerine ile şunları yapabilirsiniz:

- Yönetilen düğümler ve ayrı kaynakları için uyumluluk bilgilerini alma
- Bir e-posta veya uyumluluk durumuna bağlı olarak uyarıyı Tetikle
- Gelişmiş sorgular, yönetilen düğümlere yazma
- Otomasyon hesapları arasında uyumluluk durumu ilişkilendirin
- Zaman içinde düğüm uyumluluk geçmişinizi görselleştirin

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure İzleyici günlüklerine Otomasyon durum yapılandırması raporlarınızı göndermeye başlamak için ihtiyacınız vardır:

- Kasım 2016 veya sonraki sürümünün [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Azure Otomasyonu hesabı. Daha fazla bilgi için [Azure Otomasyonu ile çalışmaya başlama](automation-offering-get-started.md)
- Log Analytics çalışma alanıyla bir **otomasyon ve Denetim** hizmet teklifi. Daha fazla bilgi için [Azure İzleyici günlüklerine ile çalışmaya başlama](../log-analytics/log-analytics-get-started.md).
- En az bir Azure Otomasyonu durumu yapılandırma düğümü. Daha fazla bilgi için [makineleri Azure Otomasyon durum yapılandırması tarafından Yönetim için hazırlama](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure İzleyici günlüklerine ile tümleştirmesini ayarlama

Azure İzleyici günlüklerine Azure Automation DSC veri almaya başlamak için aşağıdaki adımları tamamlayın:

1. PowerShell Azure hesabınızda oturum açın. Bkz: [oturum Azure PowerShell ile oturum açma](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Alma _ResourceId_ Otomasyon hesabınızı aşağıdaki PowerShell komutunu çalıştırarak: (birden fazla Otomasyon hesabınız varsa, seçin _ResourceId_ yapılandırmak istediğiniz hesap için).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Alma _ResourceId_ aşağıdaki PowerShell komutunu çalıştırarak bir Log Analytics çalışma alanınızın: (birden fazla çalışma alanı varsa, seçin _ResourceId_ yapılandırmak istediğiniz çalışma alanı için).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Aşağıdaki PowerShell komutunu çalıştırın değiştirerek `<AutomationResourceId>` ve `<WorkspaceResourceId>` ile _ResourceId_ değerleri önceki adımların her biri:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Azure İzleyici günlüklerine Azure Otomasyonu durumu yapılandırmasından veri almayı durdurmak istiyorsanız, aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Durum yapılandırması günlüklerini görüntüleme

Azure İzleyici günlüklerine ile tümleştirme, Otomasyon durumu yapılandırma verilerinizi ayarladıktan sonra bir **günlük araması** düğmesi görünür **DSC düğümleri** Otomasyon hesabınızın dikey. Tıklayın **günlük araması** DSC düğümü veriler için günlükleri görüntülemek için düğme.

![Günlük arama düğmesi](media/automation-dsc-diagnostics/log-search-button.png)

**Günlük araması** dikey penceresi açılır ve gördüğünüz bir **DscNodeStatusData** her durum yapılandırması düğümü için işlemi ve **DscResourceStatusData** her işlemi[ DSC kaynak](/powershell/dsc/resources) bu düğüme uygulanan düğüm yapılandırma olarak adlandırılır.

**DscResourceStatusData** işlemi başarısız olan tüm DSC kaynakları için hata bilgilerini içerir.

Bu işlem için verileri görmek için listenin her bir işlemde tıklayın.

Ayrıca, Azure İzleyici günlüklerine arayarak günlükleri görüntüleyebilirsiniz.
Bkz: [Bul günlük aramaları kullanarak verileri](../log-analytics/log-analytics-log-searches.md).
Durum yapılandırması günlüklerinizi bulmak için aşağıdaki sorguyu yazın: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Ayrıca, sorgu işlem adına göre daraltabilirsiniz. Örneğin, `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Durum yapılandırması uyumluluk denetim başarısız olduğunda bir e-posta Gönder

Müşterilerimizin en iyi müşteri istekleri için bir e-posta veya metin bir şeyler ile bir DSC yapılandırması yanlış gittiğinde gönderme olanağı biridir.

Bir uyarı kuralı oluşturmak için bir günlük araması uyarı çağırması gereken durum yapılandırması rapor kayıtlar için oluşturarak başlayın. Tıklayın **+ yeni uyarı kuralı** düğmesine oluşturmak ve uyarı kuralını yapılandırın.

1. Log Analytics çalışma alanı genel bakış sayfasında **günlükleri**.
1. Aşağıdaki arama sorgu alanına yazarak Uyarınız için günlük arama sorgusu oluşturun:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Günlüklerini birden fazla Otomasyon hesabını veya aboneliğini çalışma alanınıza ayarladıysanız, uyarılarınızı aboneliği ve Automation hesabı göre gruplandırabilirsiniz.  
   Otomasyon hesabı adı DscNodeStatusData arama kaynak alanından türetilir.  
1. Açmak için **oluşturma kuralı** ekranında **+ yeni uyarı kuralı** sayfanın üstünde. Uyarı yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [uyarı kuralı oluşturma](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Tüm düğümlerde başarısız DSC kaynakları Bul

Başarısız denetimler için düğümlerde arayabilirsiniz Azure İzleyici günlüklerine kullanmanın avantajlarından biri.
Başarısız olan DSC kaynakları tüm örneklerini bulmak için:

1. Log Analytics çalışma alanı genel bakış sayfasında **günlükleri**.
1. Aşağıdaki arama sorgu alanına yazarak Uyarınız için günlük arama sorgusu oluşturun:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>DSC düğümü durumu geçmiş görüntüle

Son olarak, zaman içinde DSC düğümü durumu geçmişi görselleştirmek isteyebilirsiniz.  
Bu sorgu, zaman içinde DSC düğümü durumu durumu için aramak için kullanabilirsiniz.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Bu düğüm durumu içeren bir grafik, zaman içinde görüntüler.

## <a name="azure-monitor-logs-records"></a>Azure İzleyici kayıtları günlüğe kaydeder.

Azure Otomasyonu tanılamadan Azure İzleyici günlüklerine iki kategoriye kayıt oluşturur.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Tarih ve saat uyumluluk denetimi ne zaman çalıştırıldığı. |
| OperationName |DscNodeStatusData |
| ResultType |Düğüm uyumlu olup olmadığı. |
| NodeName_s |Yönetilen düğümün adı. |
| NodeComplianceStatus_s |Düğüm uyumlu olup olmadığı. |
| DscReportStatus |Uyumluluk denetimi olmadığını başarıyla çalıştı. |
| ConfigurationMode | Nasıl yapılandırma düğüme uygulanır. Olası değerler __"ApplyOnly"__,__"ApplyandMonitior"__, ve __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC yapılandırmasını uygular ve yeni bir yapılandırma, hedef düğüme veya bir sunucudan yeni bir yapılandırma çekildiğinde gönderildiğinde sürece başka hiçbir şey yapmaz. Yeni yapılandırma ilk uygulamadan sonra önceden yapılandırılmış bir durumdan kayması için DSC denetlemez. DSC denemeden önce başarılı oluncaya kadar yapılandırmayı uygulamak __ApplyOnly__ etkinleşir. </li><li> __ApplyAndMonitor__: Varsayılan değer budur. LCM herhangi bir yeni yapılandırmalar geçerlidir. Hedef düğüm istenen durumundan drifts sonra ilk uygulama yeni bir yapılandırma günlüklerini tutarsızlık DSC bildirir. DSC denemeden önce başarılı oluncaya kadar yapılandırmayı uygulamak __ApplyAndMonitor__ etkinleşir.</li><li>__ApplyAndAutoCorrect__: DSC, herhangi bir yeni yapılandırmalar geçerlidir. Yeni yapılandırma ilk uygulamadan sonra hedef düğüm istenen durumundan drifts DSC günlükleri tutarsızlık raporları ve sonra geçerli yapılandırmasını yeniden uygular.</li></ul> |
| HostName_s | Yönetilen düğümün adı. |
| IPAddress | Yönetilen düğüme IPv4 adresi. |
| Category | DscNodeStatus |
| Resource | Azure Otomasyon hesabı adı. |
| Tenant_g | Kiracı için çağıranın tanımlayan GUID. |
| NodeId_g |Yönetilen düğümde tanımlayan GUID. |
| DscReportId_g |Rapor tanımlayan GUID. |
| LastSeenTime_t |Tarih ve saat raporun son görüntülenen zaman. |
| ReportStartTime_t |Tarih ve saat raporun başlatıldığı. |
| ReportEndTime_t |Tarih ve saat raporun tamamlanmış olduğunda. |
| NumberOfResources_d |DSC kaynak sayısı düğüme uygulanan yapılandırma olarak bilinir. |
| SourceSystem | Azure İzleyici nasıl günlüğe yazacağını veri toplanmadı. Her zaman *Azure* Azure tanılama için. |
| ResourceId |Azure Otomasyonu hesabını belirtir. |
| ResultDescription | Bu işlem için açıklama. |
| SubscriptionId | Otomasyon hesabı için Azure aboneliği kimliğini (GUID). |
| ResourceGroup | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Uyumluluk raporu bağıntı kimliği olan GUID. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Özellik | Açıklama |
| --- | --- |
| TimeGenerated |Tarih ve saat uyumluluk denetimi ne zaman çalıştırıldığı. |
| OperationName |DscResourceStatusData|
| ResultType |Kaynak uyumlu olup olmadığı. |
| NodeName_s |Yönetilen düğümün adı. |
| Category | DscNodeStatus |
| Resource | Azure Otomasyon hesabı adı. |
| Tenant_g | Kiracı için çağıranın tanımlayan GUID. |
| NodeId_g |Yönetilen düğümde tanımlayan GUID. |
| DscReportId_g |Rapor tanımlayan GUID. |
| DscResourceId_s |DSC kaynak örnek adı. |
| DscResourceName_s |DSC kaynak adı. |
| DscResourceStatus_s |DSC kaynak uyumlu olup olmadığı. |
| DscModuleName_s |DSC kaynağı içeren PowerShell modülünün adı. |
| DscModuleVersion_s |DSC kaynağı içeren bir PowerShell modülü sürümü. |
| DscConfigurationName_s |Düğüme uygulanan yapılandırmasının adı. |
| ErrorCode_s | Kaynak başarısız olursa hata kodu. |
| ErrorMessage_s |Kaynak başarısız olursa hata iletisi. |
| DscResourceDuration_d |DSC kaynak çalıştığı saniye cinsinden süre. |
| SourceSystem | Azure İzleyici nasıl günlüğe yazacağını veri toplanmadı. Her zaman *Azure* Azure tanılama için. |
| ResourceId |Azure Otomasyonu hesabını belirtir. |
| ResultDescription | Bu işlem için açıklama. |
| SubscriptionId | Otomasyon hesabı için Azure aboneliği kimliğini (GUID). |
| ResourceGroup | Otomasyon hesabı için kaynak grubunun adı. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Uyumluluk raporu bağıntı kimliği olan GUID. |

## <a name="summary"></a>Özet

Otomasyon durumu yapılandırma verilerinizi Azure İzleyici günlüklerine göndererek Otomasyon durum yapılandırması düğümlerinizi durumunu daha iyi bir anlayış alabilirsiniz:

- Bir sorun olduğunda bildirimde bulunacak uyarılar ayarlama
- Runbook sonuçlarınızı görselleştirmek için özel görünümlerinizi ve arama sorgularını kullanarak runbook iş durumu ve diğer önemli göstergeleri veya ölçümleri ilgili.  

Azure İzleyici günlüklerine Otomasyon durum yapılandırması verileriniz için daha fazla operasyonel görünürlük sağlar ve olaylara daha hızlı bir şekilde yardımcı olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure Otomasyon durum yapılandırması](automation-dsc-overview.md)
- Başlamak için bkz: [Azure Otomasyon durum yapılandırması ile çalışmaya başlama](automation-dsc-getting-started.md)
- Hedef düğümleri atayabilirsiniz böylece, DSC yapılandırmaları derleme hakkında bilgi edinmek için [yapılandırmaları Azure Automation durumu yapılandırma derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durumu yapılandırma cmdlet'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz: [Azure Otomasyon durum yapılandırması için fiyatlandırma](https://azure.microsoft.com/pricing/details/automation/)
- Bir sürekli dağıtım işlem hattı, Azure Otomasyonu durum yapılandırmasını kullanarak bir örnek görmek için bkz: [sürekli dağıtımı kullanarak Azure Otomasyon durum yapılandırması ve Chocolatey](automation-dsc-cd-chocolatey.md)
- Azure İzleyici günlükleri ile Otomasyon durumu yapılandırma günlüklerini gözden geçirin ve farklı arama sorguları oluşturma hakkında daha fazla bilgi için bkz: [Azure İzleyici günlüklerine günlük aramaları](../log-analytics/log-analytics-log-searches.md)
- Azure İzleyici günlüklerine ve veri toplama kaynakları hakkında daha fazla bilgi için bkz: [Azure depolama verileri toplamaya Azure İzleyici'de oturum genel bakış](../azure-monitor/platform/collect-azure-metrics-logs.md)
