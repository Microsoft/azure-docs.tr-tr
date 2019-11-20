---
title: Azure Izleyici günlükleriyle Azure Site Recovery izleme
description: Azure Izleyici günlükleriyle Azure Site Recovery izlemeyi öğrenin (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133426"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure İzleyici Günlükleriyle Site Recovery’yi izleme

Bu makalede Azure [Site Recovery](site-recovery-overview.md)tarafından çoğaltılan makinelerin [Azure izleyici günlükleri](../azure-monitor/platform/data-platform-logs.md)kullanılarak ve [Log Analytics](../azure-monitor/log-query/log-query-overview.md)nasıl izleneceği açıklanır.

Azure Izleyici günlükleri, diğer izleme verileriyle birlikte etkinlik ve tanılama günlüklerini toplayan bir günlük veri platformu sağlar. Azure Izleyici günlükleri içinde, günlük sorgularını yazmak ve test etmek ve günlük verilerini etkileşimli olarak analiz etmek için Log Analytics kullanırsınız. Günlük sonuçlarını görselleştirebilir ve sorgulayabilir ve izlenen verilere göre eylemleri gerçekleştirmek için uyarıları yapılandırabilirsiniz.

Site Recovery için, aşağıdakileri yapmanıza yardımcı olması için Azure günlüklerini Izleyebilirsiniz:

- **Site Recovery durumunu ve durumunu izleyin**. Örneğin, çoğaltma durumunu, test yük devretme durumunu, Site Recovery olayları, korunan makineler için kurtarma noktası hedeflerini (RPOs) ve disk/veri değişim hızlarını izleyebilirsiniz.
- **Site Recovery için uyarıları ayarlayın**. Örneğin, makine sistem durumu, test yük devretme durumu veya Site Recovery iş durumu için uyarıları yapılandırabilirsiniz.

Azure Izleyici günlüklerini Site Recovery ile Azure 'da Azure **'a** çoğaltma, **VMware VM/fiziksel sunucusu ise Azure** 'a çoğaltma için desteklenir.

> [!NOTE]
> VMware ve fiziksel makinelere yönelik dalgalanma veri günlüklerini ve karşıya yükleme hızı günlüklerini almak için Işlem sunucusuna bir Microsoft Monitoring Agent yüklemeniz gerekir. Bu aracı, çoğaltılan makinelerin günlüklerini çalışma alanına gönderir. Bu özellik yalnızca 9,30 Mobility Agent sürümü için geçerlidir.

## <a name="before-you-start"></a>Başlamadan önce

İşte gerekenler:

- Bir kurtarma hizmetleri kasasında korunan en az bir makine.
- Site Recovery günlüklerini depolamak için bir Log Analytics çalışma alanı. Çalışma alanı ayarlama [hakkında bilgi edinin](../azure-monitor/learn/quick-create-workspace.md) .
- Log Analytics 'de günlük sorgularının nasıl yazılacağı, çalıştırılacağı ve analiz edileceği hakkında temel bir anlama. [Daha fazla bilgi edinin](../azure-monitor/log-query/get-started-portal.md).

Başlamadan önce [yaygın izleme sorularını](monitoring-common-questions.md) incelemenizi öneririz.

## <a name="configure-site-recovery-to-send-logs"></a>Günlükleri göndermek için Site Recovery yapılandırma

1. Kasada, tanılama **ayarları** > **Tanılama ayarı Ekle**' ye tıklayın.

    ![Tanılama günlüğünü seçin](./media/monitoring-log-analytics/add-diagnostic.png)

2. **Tanılama ayarları**' nda bir ad belirtin ve kutuyu **Log Analytics gönder**' i işaretleyin.
3. Azure Izleyici günlük aboneliğini ve Log Analytics çalışma alanını seçin.
4. Geçiş **Azure tanılama** seçin.
5. Günlük listesinden, **Azuresterecovery**ön ekine sahip tüm günlükleri seçin. Daha sonra, **Tamam**'a tıklayın.

    ![Çalışma alanını seçme](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery Günlükler seçili çalışma alanındaki bir tabloya (**AzureDiagnostics**) akışa başlar.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Işlem sunucusunda, değişim ve karşıya yükleme hızı günlükleri göndermek için Microsoft Monitoring Agent 'ı yapılandırma

Şirket içinde VMware/fiziksel makineleriniz için veri değişim oranı bilgilerini ve kaynak verilerini karşıya yükleme hızı bilgilerini yakalayabilirsiniz. Bunu etkinleştirmek için, Işlem sunucusuna bir Microsoft İzleme aracısının yüklenmesi gerekir.

1. Log Analytics çalışma alanına gidin ve **Gelişmiş ayarlar**' a tıklayın.
2. **Bağlı kaynaklar** sayfasına tıklayın ve **Windows Server**' ı seçin.
3. Işlem sunucusuna Windows Agent 'ı (64 bit) indirin. 
4. [Çalışma alanı KIMLIĞINI ve anahtarını alma](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Aracıyı TLS 1,2 kullanacak şekilde yapılandırma](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. Alınan çalışma alanı KIMLIĞINI ve anahtarını sağlayarak [Aracı yüklemesini doldurun](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) .
7. Yükleme tamamlandıktan sonra, Log Analytics çalışma alanına gidin ve **Gelişmiş ayarlar**' a tıklayın. **Veri** sayfasına gidin ve **Windows performans sayaçları**' na tıklayın. 
8. Aşağıdaki iki sayacı 300 saniyelik örnek aralığıyla eklemek için **' + '** düğmesine tıklayın:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Dalgalanma ve karşıya yükleme hızı verileri, çalışma alanına beslemeyi başlatacak.


## <a name="query-the-logs---examples"></a>Günlükleri sorgulama-örnekler

[Kusto sorgu diliyle](../azure-monitor/log-query/get-started-queries.md)yazılmış günlük sorgularını kullanarak günlüklerden veri alırsınız. Bu bölümde, Site Recovery izlemek için kullanabileceğiniz yaygın sorgulara yönelik birkaç örnek verilmiştir.

> [!NOTE]
> Örneklerden bazıları **A2A**olarak ayarlanmış **replicationProviderName_s** kullanır. Bu, Site Recovery kullanılarak ikincil bir Azure bölgesine çoğaltılan Azure VM 'lerini alır. Bu örneklerde, Site Recovery kullanarak Azure 'a çoğaltılan şirket içi VMware VM 'lerini veya fiziksel sunucuları almak istiyorsanız **A2A** ile **InMageAzureV2**değiştirebilirsiniz.


### <a name="query-replication-health"></a>Sorgu çoğaltma durumu

Bu sorgu, tüm korumalı Azure VM 'lerinin geçerli çoğaltma durumu için bir pasta grafiği çizer ve üç duruma bölünür: normal, uyarı veya kritik.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Mobility hizmeti sürümünü sorgula

Bu sorgu, Site Recovery ile çoğaltılan Azure VM 'lerinin, çalıştırdığı Mobility Aracısı sürümüne göre ayrılmış bir pasta grafiği çizer.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Sorgu RPO saati

Bu sorgu, kurtarma noktası hedefi (RPO) tarafından ayrılmış, Site Recovery ile çoğaltılan Azure VM 'lerinin çubuk grafiğini çizer: 15 dakikadan az, 30 dakikadan uzun bir süre 15-30.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Sorgu RPO 'SU](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery işleri sorgula

Bu sorgu, Son 72 saat içinde tetiklenen tüm Site Recovery işleri (tüm olağanüstü durum kurtarma senaryoları için) ve tamamlanma durumlarını alır.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Sorgu Site Recovery olayları

Bu sorgu, Son 72 saat içinde oluşturulan tüm Site Recovery olaylarını (tüm olağanüstü durum kurtarma senaryoları için) önem derecesine sahip olarak alır. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Sorgu testi yük devretme durumu (pasta grafik)

Bu sorgu, Site Recovery ile çoğaltılan Azure VM 'lerinin test yük devretme durumu için bir pasta grafik çizer.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Sorgu testi yük devretme durumu (tablo)

Bu sorgu, Site Recovery ile çoğaltılan Azure VM 'lerinin test yük devretme durumu için bir tablo çizer.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Makine RPO 'SU sorgula

Bu sorgu, Son 72 saat boyunca belirli bir Azure VM 'nin (ContosoVM123) RPO 'sunu izleyen bir eğilim grafiğini çizer.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Makine RPO 'SU sorgula](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Bir Azure VM için sorgu verileri değişiklik hızı (karmaşıklık) ve karşıya yükleme oranı

Bu sorgu, veri değişim hızını (saniye başına yazılan bayt) ve veri yükleme oranını temsil eden belirli bir Azure VM (ContosoVM123) için bir eğilim grafiği çizer. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Sorgu verileri değişikliği](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware veya fiziksel makine için sorgu veri değişim oranı (karmaşıklık) ve karşıya yükleme oranı

> [!Note]
> Bu günlükleri getirmek için Işlem sunucusunda izleme aracısını oluşturduğunuzdan emin olun. [İzleme aracısını yapılandırma adımları](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)bölümüne bakın.

Bu sorgu, veri değişim oranını (saniye başına yazılan bayt) ve veri yükleme oranını temsil eden **Win-9r7sfh9enru**öğesinin çoğaltılan bir öğesi **Disk0** için bir eğilim grafiği çizer. Disk adını, kurtarma hizmetleri kasasındaki çoğaltılan öğenin **diskler** dikey penceresinde bulabilirsiniz. Sorguda kullanılacak örnek adı, makinenin DNS adıdır ve ardından _ ve disk adı bu örnekte olur.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
İşlem sunucusu bu verileri her 5 dakikada bir Log Analytics çalışma alanına iter. Bu veri noktaları, 5 dakika boyunca hesaplanan ortalama süreyi temsil eder.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Sorgu olağanüstü durum kurtarma Özeti (Azure 'dan Azure 'a)

Bu sorgu, ikincil bir Azure bölgesine çoğaltılan Azure VM 'Leri için bir Özet tablosu çizer.  VM adı, çoğaltma ve koruma durumu, RPO, test yük devretme durumu, Mobility Aracısı sürümü, etkin çoğaltma hataları ve kaynak konumu gösterir.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Sorgu olağanüstü durum kurtarma Özeti (VMware/fiziksel sunucular)

Bu sorgu, VMware VM 'Leri ve Azure 'a çoğaltılan fiziksel sunucular için bir Özet tablosu çizer.  Makine adı, çoğaltma ve koruma durumu, RPO, test yük devretme durumu, Mobility Aracısı sürümü, etkin çoğaltma hataları ve ilgili işlem sunucusu gösterilir.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Uyarıları ayarlama-örnekler

Azure Izleyici verilerine göre Site Recovery uyarıları ayarlayabilirsiniz. Günlük uyarılarını ayarlama hakkında [daha fazla bilgi edinin](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) . 

> [!NOTE]
> Örneklerden bazıları **A2A**olarak ayarlanmış **replicationProviderName_s** kullanır. Bu, ikincil bir Azure bölgesine çoğaltılan Azure VM 'Leri için uyarıları ayarlar. Bu örneklerde, Azure 'a çoğaltılan şirket içi VMware VM 'Leri veya fiziksel sunucular için uyarılar ayarlamak istiyorsanız **A2A** ile **InMageAzureV2** değiştirebilirsiniz.

### <a name="multiple-machines-in-a-critical-state"></a>Kritik durumdaki birden çok makine

20 ' den fazla çoğaltılan Azure VM 'si kritik bir duruma gittiği takdirde uyarı ayarlayın.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Uyarı için **eşik değerini** 20 olarak ayarlayın.

### <a name="single-machine-in-a-critical-state"></a>Kritik durumdaki tek makine

Belirli bir çoğaltılan Azure VM 'si kritik duruma geçtiğinde bir uyarı ayarlayın.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Uyarı için **eşik değerini** 1 olarak ayarlayın.

### <a name="multiple-machines-exceed-rpo"></a>Birden çok makine RPO 'yu aşıyor

20 ' den fazla Azure VM 'ye yönelik RPO 30 dakikayı aşarsa bir uyarı ayarlayın.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Uyarı için **eşik değerini** 20 olarak ayarlayın.

### <a name="single-machine-exceeds-rpo"></a>Tek makine RPO 'yu aşıyor

Tek bir Azure VM için RPO 30 dakikayı aşarsa bir uyarı ayarlayın.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Uyarı için **eşik değerini** 1 olarak ayarlayın.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Birden çok makine için yük devretme testi 90 günü aşıyor

20 ' den fazla VM için son başarılı test yük devretmesi 90 günden daha fazla olursa bir uyarı ayarlayın. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Uyarı için **eşik değerini** 20 olarak ayarlayın.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Tek makine için yük devretme testi 90 günü aşıyor

Belirli bir sanal makine için son başarılı test yük devretmesi 90 günden daha önce olursa bir uyarı ayarlayın.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Uyarı için **eşik değerini** 1 olarak ayarlayın.

### <a name="site-recovery-job-fails"></a>Site Recovery iş başarısız

Son gün içinde bir Site Recovery işi (Bu durumda yeniden koruma işi) herhangi bir Site Recovery senaryosunda başarısız olursa bir uyarı ayarlayın. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Uyarı için **eşik değerini** 1 olarak ayarlayın ve son gün içindeki hataların denetlenmesi için 1440 dakikaya kadar olan **süreyi** belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yerleşik Site Recovery izleme [hakkında bilgi edinin](site-recovery-monitor-and-troubleshoot.md) .
