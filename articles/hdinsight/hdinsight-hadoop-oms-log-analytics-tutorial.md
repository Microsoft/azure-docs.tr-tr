---
title: Azure Izleme günlüklerini kullanarak Azure HDInsight kümelerini izleme
description: HDInsight kümesinde çalışan işleri izlemek için Azure Izleyici günlüklerini nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: a693b14bb61eb52a09ab1f1ecd5d00b339357d5d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240381"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight kümelerini izlemek için Azure Izleyici günlüklerini kullanma

HDInsight 'ta Hadoop kümesi işlemlerini izlemek için Azure Izleyici günlüklerinin nasıl etkinleştirileceğini ve bir HDInsight izleme çözümünün nasıl ekleneceğini öğrenin.

[Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md) , Azure izleyici 'de bulut ve şirket içi ortamlarınızı izleyip kullanılabilirlik ve performanslarını korumalarına yönelik bir hizmettir. Birden fazla kaynak arasında analiz sağlamak üzere bulut ve şirket içi ortamlarınızdaki kaynaklar ile diğer izleme araçları tarafından oluşturulan verileri toplar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

* **Bir Log Analytics çalışma alanı**. Bu çalışma alanını kendi veri deposu, veri kaynakları ve çözümleri olan benzersiz bir Azure Izleyici günlükleri ortamı olarak düşünebilirsiniz. Yönergeler için bkz. [Log Analytics çalışma alanı oluşturma](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Bir Azure HDInsight kümesi**. Şu anda Azure Izleyici günlüklerini aşağıdaki HDInsight kümesi türleriyle kullanabilirsiniz:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Bir HDInsight kümesi oluşturma hakkında yönergeler için bkz: [Azure HDInsight ile çalışmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Az module Azure PowerShell**.  Bkz. [yeni Azure PowerShell az modüle giriş](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> HDInsight küme hem de Log Analytics çalışma alanı, daha iyi performans için aynı bölgede yerleştirmek için önerilir. Azure Izleyici günlükleri tüm Azure bölgelerinde kullanılamaz.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Portalı kullanarak Azure Izleyici günlüklerini etkinleştirme

Bu bölümde, bir Azure Log Analytics çalışma alanı işleri, hata ayıklama günlükleri izlemek üzere kullanmak için mevcut bir HDInsight Hadoop kümesi yapılandırın.

1. [Azure Portal](https://portal.azure.com/), kümenizi seçin.  Yönergeler için bkz. [liste ve kümeleri gösterme](./hdinsight-administer-use-portal-linux.md#showClusters) . Küme yeni bir portal sayfasında açılır.

1. Soldan, **izleme**altında, **Operations Management Suite**' i seçin.

1. Ana görünümden, **OMS izleme**altında **Etkinleştir**' i seçin.

1. **Çalışma alanı seç** aşağı açılan listesinden, mevcut bir Log Analytics çalışma alanını seçin.

1. **Kaydet**’i seçin.  Ayarı kaydetmek için birkaç dakika sürer.

    ![HDInsight kümeleri için izlemeyi etkinleştirin](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight kümeleri için izlemeyi etkinleştir")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure PowerShell kullanarak Azure Izleyici günlüklerini etkinleştirme

Azure PowerShell az Module [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet 'Ini kullanarak Azure izleyici günlüklerini etkinleştirebilirsiniz.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Devre dışı bırakmak için [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet 'ini kullanın:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight küme yönetim çözümlerini yükleme

HDInsight, Azure Izleyici günlükleri için ekleyebileceğiniz kümeye özgü yönetim çözümleri sağlar. [Yönetim çözümleri](../log-analytics/log-analytics-add-solutions.md) , ek veri ve analiz araçları sağlayarak Azure izleyici günlüklerine işlevsellik ekler. Bu çözümlerin, HDInsight kümelerinizi önemli performans ölçümlerini toplamak ve ölçümlerini arama için araçlar sağlar. Bu çözümleri ayrıca görselleştirmeler ve panolar için HDInsight içinde desteklenen çoğu küme türleri sağlar. Topladığınız ölçümleri çözümle birlikte kullanarak, özel izleme kuralları ve uyarılar oluşturabilirsiniz.

Var olan HDInsight çözümlerinin şunlardır:

* HDInsight Hadoop İzleme
* HDInsight HBase İzleme
* HDInsight etkileşimli sorgu izleme
* HDInsight Kafka İzleme
* HDInsight Spark İzleme
* HDInsight Storm Monitoring

Bir yönetim çözümü yüklemek yönergeler için bkz. [Azure yönetim çözümlerine](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Denemek için bir HDInsight Hadoop Izleme çözümü yüklemeyi deneyin. İşlem tamamlandığında, gördüğünüz bir **HDInsightHadoop** kutucuğu altında listelenen **özeti**. Seçin **HDInsightHadoop** Döşe. HDInsightHadoop çözüm şuna benzer:

![HDInsight izleme çözüm görünümü](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Kümeye yeni bir küme olduğundan, herhangi bir etkinlik raporu göstermez.

## <a name="configuring-performance-counters"></a>Performans sayaçlarını yapılandırma

Azure İzleyici ayrıca kümenizdeki düğümlerin performans ölçümlerini toplamayı ve çözümlemeyi destekler. Bu özelliği etkinleştirme ve yapılandırma hakkında daha fazla bilgi için bkz. [Azure izleyici 'de Linux performans veri kaynakları](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Küme denetimi

HDInsight, Azure Izleyici günlükleri ile küme denetimini destekler ve aşağıdaki türlerde Günlükler içeri aktarabilirsiniz:

* `log_gateway_audit_CL`-Bu tablo, başarılı ve başarısız oturum açma girişimlerini gösteren küme ağ geçidi düğümlerinden denetim günlükleri sağlar.
* `log_auth_CL`-Bu tablo, başarılı ve başarısız oturum açma girişimleri ile SSH günlükleri sağlar.
* `log_ambari_audit_CL`-Bu tablo, ambarı 'ndan denetim günlükleri sağlar.
* `log_ranger_audti_CL`-Bu tablo, ESP kümelerinde Apache Ranger 'tan denetim günlükleri sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama](hdinsight-hadoop-oms-log-analytics-use-queries.md)
