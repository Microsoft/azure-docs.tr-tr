---
title: Azure Izleme günlüklerini kullanarak Azure HDInsight kümelerini izleme
description: HDInsight kümesinde çalışan işleri izlemek için Azure Izleyici günlüklerini nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 2a81b25b08708a878fc8ff83cf19c643036b8f90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770336"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure İzleyici günlüklerini kullanarak HDInsight kümelerini izleme

HDInsight 'ta Hadoop kümesi işlemlerini izlemek için Azure Izleyici günlüklerini nasıl etkinleştireceğinizi öğrenin. Ve bir HDInsight izleme çözümü ekleme.

[Azure izleyici günlükleri](../azure-monitor/logs/log-query-overview.md) , bulutunuzu ve şirket içi ortamlarınızı Izleyen bir Azure izleyici hizmetidir. İzleme, kullanılabilirlik ve performansının bakımını sağlar. Bulutta, şirket içi ortamlarınızdaki ve diğer izleme araçlarından kaynaklar tarafından oluşturulan verileri toplar. Veriler, birden çok kaynak genelinde analiz sağlamak için kullanılır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

* Log Analytics çalışma alanı. Bu çalışma alanını kendi veri deposu, veri kaynakları ve çözümleri olan benzersiz bir Azure Izleyici günlükleri ortamı olarak düşünebilirsiniz. Yönergeler için bkz. [Log Analytics çalışma alanı oluşturma](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace).

* Bir Azure HDInsight kümesi. Şu anda Azure Izleyici günlüklerini aşağıdaki HDInsight kümesi türleriyle kullanabilirsiniz:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight kümesi oluşturma yönergeleri için bkz. [Azure HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* PowerShell kullanıyorsanız [az Module](/powershell/azure/)gerekecektir. En son sürüme sahip olduğunuzdan emin olun. Gerekirse, öğesini çalıştırın `Update-Module -Name Az` .

* Azure CLı 'yi kullanmak istiyorsanız ve henüz yüklemediyseniz, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

> [!NOTE]  
> Daha iyi performans için hem HDInsight kümesini hem de Log Analytics çalışma alanını aynı bölgeye yerleştirmeniz önerilir. Azure Izleyici günlükleri tüm Azure bölgelerinde kullanılamaz.

## <a name="enable-azure-monitor-using-the-portal"></a>Portalı kullanarak Azure Izleyicisini etkinleştirme

Bu bölümde, var olan bir HDInsight Hadoop kümesini işleri izlemek için bir Azure Log Analytics çalışma alanı kullanacak şekilde yapılandırırsınız, günlükleri hata ayıklamaktır.

1. [Azure Portal](https://portal.azure.com/), kümenizi seçin. Küme yeni bir portal sayfasında açılır.

1. Soldan, **izleme** altında **Azure izleyici**' yi seçin.

1. Ana görünümden **Azure Izleyici tümleştirmesi** altında **Etkinleştir**' i seçin.

1. **Çalışma alanı seç** aşağı açılan listesinden, mevcut bir Log Analytics çalışma alanını seçin.

1. **Kaydet**’i seçin.  Ayarın kaydedilmesi birkaç dakika sürer.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="HDInsight kümeleri için izlemeyi etkinleştir":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell kullanarak Azure Izleyicisini etkinleştirme

Azure PowerShell az Module [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet 'Ini kullanarak Azure izleyici günlüklerini etkinleştirebilirsiniz.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Devre dışı bırakmak için [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) cmdlet 'ini kullanın:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLı kullanarak Azure Izleyicisini etkinleştirme

Azure Izleme günlüklerini Azure CLı `[az hdinsight monitor enable` ] (/cli/Azure/hdinsight/Monitor # az_hdinsight_monitor_enable) komutunu kullanarak etkinleştirebilirsiniz.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Devre dışı bırakmak için komutunu kullanın [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) .

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight kümesi yönetim çözümlerini yükler

HDInsight, Azure Izleyici günlükleri için ekleyebileceğiniz kümeye özgü yönetim çözümleri sağlar. [Yönetim çözümleri](../azure-monitor/insights/solutions.md) , ek veri ve analiz araçları sağlayarak Azure izleyici günlüklerine işlevsellik ekler. Bu çözümler, HDInsight kümelerinizdeki önemli performans ölçümlerini toplar. Ve ölçümleri aramak için araçlar sağlar. Bu çözümler, HDInsight 'ta desteklenen çoğu küme türü için görsel öğeler ve panolar de sağlar. Çözümle topladığınız ölçümleri kullanarak, özel izleme kuralları ve uyarılar oluşturabilirsiniz.

Kullanılabilir HDInsight çözümleri:

* HDInsight Hadoop Izleme
* HDInsight HBase İzleme
* HDInsight etkileşimli sorgu Izleme
* HDInsight Kafka Izleme
* HDInsight Spark Izleme
* HDInsight fırtınası Izleme

Yönetim çözümü yönergeleri için bkz. [Azure 'Da yönetim çözümleri](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Denemek için bir HDInsight Hadoop Izleme çözümü yüklemeyi deneyin. İşlem tamamlandığında **Özet** altında listelenmiş bir **HDInsightHadoop** kutucuğu görürsünüz. **HDInsightHadoop** kutucuğunu seçin. HDInsightHadoop çözümü şöyle görünür:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight izleme çözüm görünümü":::

Küme yeni bir küme olduğundan, rapor hiçbir etkinliği göstermez.

## <a name="configuring-performance-counters"></a>Performans sayaçlarını yapılandırma

Azure izleyici, kümenizdeki düğümlerin performans ölçümlerini toplamayı ve çözümlemeyi destekler. Daha fazla bilgi için bkz. [Azure izleyici 'de Linux performansı veri kaynakları](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Küme denetimi

HDInsight, Azure Izleyici günlükleri ile küme denetimini destekler ve aşağıdaki türlerde Günlükler içeri aktarabilirsiniz:

* `log_gateway_audit_CL` -Bu tablo, başarılı ve başarısız oturum açma girişimlerini gösteren küme ağ geçidi düğümlerinden denetim günlükleri sağlar.
* `log_auth_CL` -Bu tablo, başarılı ve başarısız oturum açma girişimleri ile SSH günlükleri sağlar.
* `log_ambari_audit_CL` -Bu tablo, ambarı 'ndan denetim günlükleri sağlar.
* `log_ranger_audti_CL` -Bu tablo, ESP kümelerinde Apache Ranger 'tan denetim günlükleri sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Apache ambarı ve Azure Izleyici günlükleri ile küme kullanılabilirliğini izleme](./hdinsight-cluster-availability.md)